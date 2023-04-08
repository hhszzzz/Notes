# Operating system organization

## kernel organization

本节探讨了宏内核与微内核的不同之处和各自的优缺点

> 宏内核：整个操作系统都驻留在内核中，所有的系统调用都在supervisor mode中运行
>
> 微内核：最大限度地减少在supervisor mode下运行的`os`代码，在用户模式下运行大部分`os`代码
>
> <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230403193417425.png" alt="image-20230403193417425" style="zoom:67%;" />
>
> 在上图可以看到，shell如果想要读取或写入文件，它会向文件服务发送消息并等待响应，[so how to achieve?][]

## xv6 organization（code）

xv6内核源代码位于`kernel/`子目录中，源代码按照模块化的概念分为多个文件。下表列出了这些文件，模块间的接口在`defs.h(kernel/defs.h)`中定义。

![image-20230403195159094](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230403195159094.png)

## Process overview

"The mechanisms used by the kernel to implement processes include the user/supervisor mode flag, address spaces, and time-slicing of threads"，那么内核是如何实现线程的时间切片的呢？[todo][]

xv6使用页表（由硬件实现）为每个进程提供自己的地址空间。RISC-V页表将虚拟地址（RISC-V指令操作的地址）转换（映射）为物理地址（CPU芯片发送到主存储器的地址）。

下面展示一个进程的页表

![image-20230403200806549](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230403200806549.png)

地址空间是从虚拟地址0开始的。首先是指令区（Instructions），然后是全局变量区（global variables），然后是栈区（stack），最后是进程可以根据需要拓展的堆区（heap）（用malloc）。

硬件在页表中查找虚拟地址时只使用低39位，而xv6仅使用这39位中的38位。因此，最大地址为 $\rm{2^{38} - 1 = 0x3fffffffff}$，即`MAXVA`(kernel/riscv.h:348)。

线程的大部分状态(局部变量、函数调用返回地址)都存储在线程的栈中。

> 每个进程有两个栈：用户栈和内核栈(`p->kstack`)。当进程在执行用户指令时，只使用它的用户栈，它的内核栈是空的。当进程进入内核(用于系统调用或中断)时，内核代码在进程的内核栈上执行。当一个进程在内核中运行时，它的用户栈仍然包含保存的数据，但没有被使用。一个进程的线程在主动使用它的用户栈和内核栈之间交替执行。内核栈是独立的(并且不受用户代码的影响)，因此即使进程破坏了其用户栈，内核也可以执行。
>
> `p->state`表示进程是否已分配、准备运行、正在运行、等待I/O、退出，等等。
>
> ![image-20230403203416190](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230403203416190.png)

>书上这里详细介绍了系统调用的具体实现，代码我还没查...
>
>进程可以通过RISC-V的`ecall`指令来执行系统调用。该指令提高了硬件权限级别并将程序计数器更改为内核定义的入口点。入口点的代码切换到内核栈中并执行实现系统调用的内核指令。系统调用完成后，内核切换回用户栈，通过调用`sret`指令返回用户空间，降低硬件权限，在系统调用指令执行后恢复执行用户指令。进程的线程可以在内核中阻塞以等待I/O，并在I/O完成后从中断处继续执行。

## Code: starting xv6, the first process and system call

貌似这节我在(Trace/trace-userinit)中写过，好像又不是

当RISC-V计算机开机时，它会自行初始化并运行存储在只读存储器中的引导加载程序，引导加载程序将xv6内核加载到内存中。然后在机器模式下，CPU从`_entry (kernel/entry.S:6)`开始执行xv6。RISC-V从禁用分页硬件开始：虚拟地址直接映射到物理地址。

![image-20230403204321255](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230403204321255.png)

加载程序将xv6内核加载到物理地址`0x80000000`的内存中。将内核放置在`0x80000000`而不是`0x0`的原因是因为地址范围`0x0 ~ 0x80000000`包含I/O设备。

`_entry`处的指令设置了一个栈，以便xv6可以运行C代码。xv6在文件`start.c`(kernel/start.c:11)中为初始堆栈`stack0`声明了空间。

![image-20230403204625739](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230403204625739.png)

`_entry`处的代码将栈顶地址`stack0+4096`加载到栈指针寄存器`sp`，

![image-20230403204726252](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230403204726252.png)

这是因为RISC-V的栈是向下扩展的。现在内核有了栈区，`_entry`调用C代码`start`(kernel/start.c:21)。

![image-20230403204753598](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230403204753598.png)

函数`start`执行了一些些仅在机器模式下允许的配置，然后切换到管理员模式。要进入管理员模式，RISC-V 提供了指令`mret`。该指令最常用于从前面将管理员模式切换机器模式的调用中返回。

![image-20230403204916443](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230403204916443.png)

但是实际上并不是这样，下面介绍运行过程

1. `start`函数在寄存器`mstatus`中将先前的权限模式设置为管理员模式(23~27)

2. 并通过将`main`函数的地址写入寄存器`mepc`将返回地址设置为`main`(31)

3. 通过将0写入页表寄存器`satp`来禁用管理员模式下的虚拟地址转换(34)，并将所有中断和异常委托给管理员模式(37~39)

4. 跳转到管理员模式之前，还执行了另外一项任务：对时钟芯片进行编程以产生定时器中断。处理完这些事务后，通过调用`mret`返回到管理员模式。这会导致程序计数器的值变为`main`(kernel/main.c:11)函数地址。

5. 在`main`(kernel/main.c:11)初始化几个设备和子系统后，它通过调用`userinit `(kernel/proc.c:212)创建第一个进程。

6. 第一个进程执行一个用RISC-V汇编编写的小程序`initcode.S`(user/initcode.S:1)，通过调用`exec`系统调用重新进入内核。

   ![image-20230403210948396](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230403210948396.png)

7. 正如我们在第1章中看到的，`exec`用一个新程序(在本例中为`/init`)替换了当前进程的内存和寄存器。一旦内核完成`exec`，它就会返回`/init`进程中的用户空间。

8. 如果需要，`init`(user/main.c:15)创建一个新的控制台设备文件，然后将其作为文件描述符0、1和2打开。然后它在控制台上启动一个 shell，系统就这样启动起来了。

   ![image-20230403211122146](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230403211122146.png)

