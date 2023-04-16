1、`C type`在寄存器中，如果`Bytes`不够寄存器的位数，那么高位就会全部被符号位所代替。

2、C进行参数传递时，对于整数参数，使用a0-a7这8个整数寄存器来传递，对于浮点数参数，则使用fa0-fa7这8个浮点寄存器来传递。

3、如果参数少于等于8个，将会用影子参数寄存器存储，类似与一个结构体指针引用，包含了8个参数，可以避免将整个结构体压入堆栈，提高函数调用的效率。

4、如果浮点参数存储器不够用，将会把浮点参数也传递到整数寄存器中，不过会损失精度

5、浮点数如何存储到整数寄存器的问题：

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678524648212.png" style="zoom: 80%;" />

6、如果参数大于8会怎么样？A：超出的参数会按照一定顺序被传递到堆栈上（超出的第9个将被存储在栈帧中偏移量位0的位置，第10个参数将被存储在偏移量为4的位置，以此类推。），在函数中访问这些参数时，需要使用**基址寄存器**（通常是sp，栈指针寄存器）加上适当的偏移量来访问它们。

7、RISC-V参数传递规则：

- 如果一个参数大小小于一个指针字，则该参数将被传递到参数寄存器的最低有效位，如果是通过栈传递，因为RISC-V具有小端内存系统，在小端内存系统中，较低的子节存储在较低的地址中。
- 如果一个参数大小刚好是指针字的两倍时，如果是通过栈传递，它会自然地对齐。如果存储在寄存器，某个函数只有两个参数，且第二个是64位长整型，在RV32架构中，这个函数的第一个参数存储在a0、而第二个参数存储在寄存器a2和a3中，在这种情况下，a1会被浪费。
- 如果参数的大小超过了指针字的两倍，则会通过引用传递，即参数在栈上分配内存，通过传递指向该内存的指针来传递参数值。

8、未在参数寄存器中传递的结构体的一部分将通过栈传递，栈指针sp指向第一个未在寄存器中传递的参数。

9、函数返回值被传递到整数寄存器a0和a1（a0和a1既作为参数寄存器又作为返回值寄存器）以及浮点寄存器fa0和fa1中。仅当返回值是基本数据类型或结构体只包含一个或两个浮点数值时，浮点值才会在浮点寄存器中返回。如果存在更大的返回值，会存储在内存中。

10、除了参数和返回值寄存器外，还有七个整数寄存器t0-t6和十二个浮点寄存器ft0-ft11，它们是临时寄存器。另外有十二个整数寄存器s0-s11和十二个浮点寄存器fs0-fs11是跨函数调用保持的，如果使用必须由被调用方保存。

11、Soft-float是一种浮点数运算的实现方式，它在不依赖硬件浮点数运算指令的情况下，通过使用纯软件的方法来进行浮点数计算。

---

[todo][]查找一下用户进程和内核之间的切换

## RISC-V trap machinery

介绍几个重要的寄存器：

- `stvec (Trap-Vector)`

  内核在此处写入*trap*处理程序的地址，RISC-V跳转到该寄存器的地址来处理*trap*

  储存中断处理程序的入口地址

  ![image-20230414211155644](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230414211155644.png)

- `sepc (supervisor exception program counter)`

  当*trap*发生时，RISC-V会将pc存在这（因为随后pc会被`stvec`中的值覆盖，很合理，因为要执行中断处理程序）

  `sret (return from trap)`指令将`sepc`复制到`pc`，内核可以写入`sepc`来控制`sret`的去向

  ![image-20230414211220801](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230414211220801.png)

- `scause`

  RISC-V在此处放置了一个数字来描述*trap*的原因

  ![image-20230415103750578](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230415103750578.png)

- `sscratch`

  内核在此处放置一个值，该值在trap处理程序的一开始就派上用场。

  当发生*trap*时，处理器会将当前进程的**内核栈的指针保存**到`sscratch`寄存器中，并将内核栈

  切换到一个预先设置好的内核栈。这个预先设置好的内核栈可以是当前进程的内核栈，也可以

  是另一个进程的内核栈，这样就可以在内核中执行一些操作，比如系统调用等，而**不会破坏当**

  **前进程的内核栈**。当内核中的操作完成后，处理器会将`sscratch`寄存器中保存的内核栈的指

  针恢复回当前进程的内核栈指针，然后继续执行当前进程的代码。

  ![image-20230415104010693](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230415104010693.png) 

- `sstatus`

  在第三章中有讲到，`sstatus`的`SIE`位控制设备中断是否启用，如果内核清除`SIE`，RISC-V将会推迟设备中断知道内核设置`SIE`。

  如下图，`SPP`位指示*trap*是来自user模式（0）还是supervisor模式（1），并控制`sret`返回到何种模式。

  ![image-20230415104849965](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230415104849965.png)

上述寄存器与在supervisor模式下处理的*traps*有关，并且在用户模式下不能读写这些寄存器。对于在machine模式下处理的*trap*，有一组等效的控制寄存器。xv6仅将它们用于**定时器中断**的特殊情况。 

多核芯片上的每个CPU都有自己的一组寄存器，在任何给定时间都可能有多个CPU同时处理一个*trap*。

> 当需要强制*trap*时，RISC-V硬件对所有类型的*trap*(定时器中断除外)执行以下操作：
>
> 1. 如果*trap*是设备中断并且`sstatus SIE`位清零，请不要进行以下任何操作。 
> 2. 通过清除`SIE`禁用中断。 
> 3. 将`pc`复制给`sepc`。 
> 4. 将当前模式(user或supervisor)保存在`sstatus`的`SPP`位中。
> 5. 设置`scause`以反映*trap*的原因。 
> 6. 将模式设置为supervisor。 
> 7. 将`stvec`复制给`pc`。 
> 8. 在新`pc`处开始执行。 

注意CPU不会切换到内核页表，不会切换到内核中的栈，也不会保存除`pc`以外的任何寄存器。内核软件必须来执行这些任务（**这话太晦涩难懂了，这是我的解释：CPU不会主动切换，而是让pc执行到处理trap的入口，让该入口地址对应的处理程序，根据需要选择是否切换到内核页表和内核栈**）。CPU在trap期间做最少工作的原因之一是为软件提供了灵活性。例如，某些操作系统在某些情况下不需要页表切换，这可以提高性能。

## 4.2 Traps from user space

如果用户程序进行系统调用(`ecall`指令)、执行非法操作、设备中断，则在用户空间执行时可能会发生trap。来自用户空间的trap的高级路径（high-level path）经过了哪些代码路径？先是`uservec`(kernel/trampoline.S:16)，然后是`usertrap`(kernel/trap.c:37)。返回时，先是`usertrapret`(kernel/trap.c:90)，然后`userret`(kernel/trampoline.S:16)。 

![image-20230415152520369](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230415152520369.png)

(trampoline.S:16)上图注释说明了一大堆东西，主要是为了介绍`uservec`（汇编代码函数，是user space进入kernel space的入口点），(trap.c)设置了`stvec`在这，从用户空间发生的*traps*从这里开始执行

在某些情况下（系统调用或页面错误等），需要在内核模式下处理*trap*事件，但是需要使用用户模式下的页表来访问用户空间的数据，因此会像20-21那样，"in supervisor mode, but with a user page table"

紧接着是`usertrap`，返回时，先是`usertrapret`（在`usertrap`的最后一段代码）

![image-20230415155828205](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230415155828205.png)

然后从`usertrap`中返回，返回到(trampoline.S)中，继续执行`userret`，最后执行完毕

![image-20230416190002689](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230416190002689.png)