# 前言

**trap的触发条件**

> 有三种情况会导致CPU搁置普通指令的执行，并强制将控制权转移到处理事件的特殊代码。 
> ①系统调用：当用户程序执行`ecall`指令以要求内核为其做一些事情时。 
> ②异常：一条用户或内核指令做了一些非法的事情时，例如除以0或使用无效的虚拟地址。 
> ③设备中断：当设备发出需要注意的信号时，例如当磁盘硬件完成读取或写入请求时。

**trap的工作流程**

> 本书使用_trap_作为这些情况的通用术语。通常，在_trap_发生时正在执行的任何代码稍后都需要恢复，并且不需要知道发生的任何特殊情况。也就是说，我们经常希望_trap_是透明的。这一点来说对于中断尤其重要，这是因为中断代码通常是无法预料的。[trap](https://so.csdn.net/so/search?q=trap&spm=1001.2101.3001.7020)通常的顺序是： 
>
> ①_trap_强制将控制权转移到内核中。 
> ②内核保存寄存器和其他状态，以便后续可以恢复运行。 
> ③内核执行适当的处理程序代码（例如系统调用实现或设备驱动程序）
> ④内核恢复保存的状态并从*trap*中返回。 
> ⑤原始代码从中断的地方继续执行。

**xv6 trap的意义**

> [xv6](https://so.csdn.net/so/search?q=xv6&spm=1001.2101.3001.7020)内核处理所有trap，trap不传递给user code。 
> ①这对于系统调用来说是很自然的。 
> ②这对于中断来说是有意义的，因为隔离性要求用户进程不直接使用设备，而且内核是一种在多个进程之间共享设备的方便机制。
> ③这对于异常来说是有意义的，因为xv6通过杀死(kill)违规的程序来响应来自用户空间的所有异常。

**xv6 trap的处理流程**

> xv6 trap的处理流程分四个阶段进行： 
>
> ①RISC-V CPU采取的一系列**硬件操作** 
> ②为内核C代码、C函数（决定如何处理trap）、系统调用或设备驱动服务例程，执行而准备的**汇编指令**。 
>
> 虽然三种_trap_类型之间的共同点表明内核可以使用单个代码路径处理所有*trap*，但对于三种不同情况使用单独的汇编向量和 C *trap*处理程序很方便：来自用户空间的*trap*、来自内核空间的trap、定时器中断。
>
> 处理*trap*的内核代码（汇编程序 or C）通常被称为处理程序（handler），第一个处理程序指令通常用汇编语言编写（而不是C），有时被称为`vector`

  

# 一、RISC-V的trap机制

**重要的控制寄存器**

> 每个RISC-V CPU都有一组控制寄存器，内核写入这些寄存器来告诉CPU如何处理traps，并且内核还可以读取这些寄存器以找出已发生的trap。**riscv.h**(kernel/riscv.h:1)包含xv6使用的寄存器的定义。以下是最重要的一些**寄存器**的概述： 
>
> ①`stvec`：内核在此处写入其trap处理程序的地址，RISC-V跳转到这里来处理trap。 
> ②`sepc`：当trap发生时，RISC-V将程序计数器保存在这里(因为`pc`（程序计数器）会被`stvec`覆盖)。`sret`(特权指令，用于从内核态返回用户态，从_trap_返回)指令将`sepc`复制到`pc`。内核可以写入`sepc`来控制`sret`的去向。 
> ③`scause`：RISC-V在此处放置了一个数字来描述trap的原因。 
> ④`sscratch`：内核在此处放置一个值，该值在trap处理程序的一开始就派上用场。 
> ⑤`sstatus`：`sstatus`中的`SIE`位控制是否启用设备中断。如果内核清除`SIE`，RISC-V将推迟设备中断，直到内核设置`SIE`。`SPP`位指示trap是来自user模式还是supervisor模式，并控制`sret`返回到何种模式。 
> 		上述寄存器与在supervisor模式下处理的trap有关，在用户模式下不能读写这些寄存器。对于在机器模式下处理的trap，有一组等效的控制寄存器。xv6仅将它们用于定时器中断的特殊情况。 
> 		多核芯片上的每个CPU都有自己的一组寄存器，在任何给定时间都可能有多个CPU同时处理trap。 
> 当需要强制trap时，RISC-V硬件对所有类型的trap(定时器中断除外)执行以下操作： 
> ①如果trap是设备中断并且`sstatus SIE`位清零，请不要进行以下任何操作。 
> ②通过清除`SIE`禁用中断。 
> ③将`pc`复制给`sepc`。 
> ④将当前模式(user或supervisor)保存在`sstatus`的`SPP`位中。 
> ⑤设置`scause`以反映trap的原因。 
> ⑥将模式设置为supervisor。 
> ⑦将`stvec`复制给`pc`。 
> ⑧在新`pc`处开始执行。 
> 		注意CPU不会切换到内核页表，不会切换到内核中的栈，也不会保存除`pc`以外的任何寄存器。内核软件必须来执行这些任务。CPU在trap期间做最少工作的原因之一是为软件提供了灵活性。例如，某些操作系统在某些情况下不需要页表切换，这可以提高性能。 
> 		你可能想知道是否可以进一步简化CPU硬件的trap处理序列。例如，假设CPU没有切换程序计数器。然后一个trap可以在运行用户指令的同时切换到supervisor模式。这些用户指令可能会破坏用户/内核隔离，例如通过修改`satp`寄存器以指向允许访问所有物理内存的页表。因此CPU切换到内核指定的指令地址(`stvec`)是很重要的。

  

# 二、来自用户空间的trap

> xv6处理trap的方式不同，这取决于它是在内核中执行还是在用户代码中执行。以下是用户代码中trap的故事；第4.5节描述了内核代码中的trap。
>
> **用户空间trap的执行与返回顺序**
> 		如果用户程序进行系统调用(`ecall`指令)、执行非法操作、设备中断，则在用户空间执行时可能会发生trap。来自用户空间的trap的高级路径是`uservec`(kernel/trampoline.S:16)，然后是`usertrap`(kernel/trap.c:37)。返回时，先是`usertrapret`(kernel/trap.c:90)，然后`userret`(kernel/trampoline.S:88)。 
> **注：** 来自用户代码的trap比来自内核的trap更具挑战性，因为`satp`指向不映射内核的用户页表，栈指针可能包含无效甚至恶意值。
>
> **uservec在用户页表与内核页表中的映射** 
> 		由于RISC-V硬件在trap期间不切换页表，所以用户页表必须包含`uservec`的映射（<u>当处理完trap之后需要返回用户模式，此时必须将`stvec`寄存器重新设置为用户模式下的陷阱处理程序地址，如果这个地址在用户页表中没有有效的映射，处理器就会访问一个无效地址导致系统崩溃</u>），即`stvec`指向的trap向量指令。`uservec`必须切换`satp`以指向内核页表。为了在切换后继续执行指令，`uservec`必须映射到内核页表中与用户页表中相同的地址。 
>
> Q：为什么内核页表也会需要映射`stvec`呢？
>
> A：首先用户模式切换到内核模式后，页表也会切换，此时原先用户页表的`stvec`的映射就失效了，有的人可能会说：内核不需要映射也可以直接访问该寄存器啊！没错，的确可以访问，但是在第三章中，os采用了虚拟内存机制，所以如果绕过虚拟地址直接使用物理地址进行访问，可能会导致一些错误
>
> **trampoline在用户页表与内核页表中的映射** 
> 		xv6使用包含`uservec`的trampoline页面来满足这些约束。xv6在内核页表和每个用户页表中将trampoline页映射到相同的虚拟地址。这个虚拟地址是`TRAMPOLINE`，位于虚拟地址空间的最顶端)。trampoline的内容在**trampoline.S**中设置，并且(在执行用户代码时)`stvec`设置为`uservec`(kernel/trampoline.S:16)。 因为trampoline页面映射在用户页面表中，使用PTE_U标志，陷阱可以在主管模式下开始执行。因为蹦床页面被映射到内核地址中的同一地址空间，陷阱处理程序可以在切换到内核页表后继续执行
>
> **sscratch寄存器的作用** 
> 		当`uservec`启动时，所有32个寄存器都包含被中断代码拥有的值。但是`uservec`需要能够修改一些寄存器才能设置`satp`并生成保存寄存器的地址。RISC-V以`sscratch`寄存器的形式提供了帮助。`uservec`开头的`csrrw`指令交换`a0`和`sscratch`的内容。现在用户代码的`a0`被保存了。`uservec`有一个寄存器(`a0`)可以使用。`a0`包含内核先前放在`sscratch`中的值。 
>
> **trapframe保存用户寄存器** 
> 		`uservec`的下一个任务是保存用户寄存器。在进入用户空间之前，内核之前将`sscratch`设置为指向每个进程的trapframe，该trapframe(除其他外)有空间保存所有用户寄存器(kernel/proc.h:44)。因为`satp`仍然指向用户页表，所以`uservec`需要将trapframe映射到用户地址空间。在创建每个进程时，xv6为进程的trapframe分配一个页面，并安排它始终映射到用户虚拟地址`TRAPFRAME`，该地址位于`TRAMPOLINE`的下方。尽管使用物理地址，该进程的`p->trapframe`也指向trapframe，因此内核可以通过内核页表使用它。 
>
> ​		因此，在交换`a0`和`sscratch`之后，`a0`持有一个指向当前进程trapframe的指针。`uservec`现在将所有用户寄存器保存在那里，包括用户的`a0`(从`sscratch`读取)。 
> ​		trapframe包含指向当前进程的内核栈、当前CPU的`hartid`、`usertrap()`函数地址和内核页表地址。`uservec`检索这些值，将`satp`切换到内核页表，并调用`usertrap`。 
>
> **usertrap的工作** 
> `usertrap`的工作是确定trap的原因，对其进行处理，然后返回(kernel/trap.c:37)。 
> 		如上所述，它首先更改`stvec`，以便内核中的trap将由`kernelvec`处理。 
> 		它保存了 `sepc`(保存的用户程序计数器)，因为`usertrap`中可能调用`yield()`进行进程切换，这可能导致`sepc`被覆盖。 
> 		如果trap是系统调用，则由系统调用处理；如果设备中断，则由`devintr`处理；否则它是一个异常，内核会杀死出错的进程。 
> 		系统调用路径向保存的用户`pc`添加了4，因为在系统调用的情况下，RISC-V会留下指向ecall指令的程序指针(返回后需要执行ecall之后的下一条指令)。 
> 		在退出的过程中，`usertrap`检查进程是已经被杀死还是应该让出CPU(如果这个陷阱是计时器中断)。 
>
> **usertrapret的工作** 
> 		返回用户空间的第一步是调用`usertrapret`(kernel/trap.c:90)。该函数设置RISC-V 控制寄存器，为来自用户空间的未来trap做准备。这涉及将`stvec`更改为引用 `uservec`，准备`uservec`所依赖的trapframe字段，并将`sepc`设置为先前保存的用户程序计数器。 
> 		最后，`usertrapret`在映射到用户和内核页表的trampoline页上调用`userret`；原因是`userret`中的汇编代码会切换页表。 
>
> **userret的工作** 
> 		`usertrapret`对`userret`的调用将`TRAPFRAME`(kernel/trampoline.S:88)传递给`a0`，并在`a1`中传递指向用户页表的指针的。 
> 		`userret`将`satp`切换到进程的用户页表。回想一下，用户页表映射了trampoline页和`TRAPFRAME`，但内核中没有其他内容。同样，trampoline页映射到用户和内核页表中的相同虚拟地址这一事实允许 `uservec`在更改`satp`后继续执行。 
> 		`userret`将trapframe保存的用户`a0`复制到`sscratch`以准备以后与`TRAPFRAME`交换。从此刻开始，`userret`唯一可以使用的数据是寄存器内容和trapframe的内容。 
> 		下一个`userret`从trapframe中恢复保存的用户寄存器，最后交换`a0`和`sscratch`以恢复用户`a0`并为下一个trap保存`TRAPFRAME`，并使用`sret`返回用户空间。

  

# 三、代码：调用系统调用

**调用系统调用**

> 第2章以`initcode.S`调用`exec`系统调用(user/initcode.S:11) 结束。让我们看看用户调用如何在内核中实现`exec`系统调用。 
>
> ①用户代码将`exec`的参数放在寄存器`a0`和`a1`中，并将系统调用号放在`a7`中。系统调用号与`syscalls`数组中的条目相匹配(`syscalls`数组是一个函数指针表 (kernel/syscall.c:108))。`ecall`指令trap进内核并执行`uservec`、`usertrap`，然后是 `syscall`，正如我们在上面看到的**（这里可能就是发生*trap*用户到内核的转变）**。 
>
> ②`syscall`(kernel/syscall.c:133)从trampoline帧中保存的`a7`中检索系统调用号，并使用它来索引`syscalls`。对于第一个系统调用，`a7`包含`SYS_exec`(kernel/syscall.h:8)，导致调用系统调用接口函数`sys_exec`。 
>
> ③当系统调用接口函数返回时，`syscall`将其返回值记录在`p->trapframe->a0`中。这将导致原始用户空间对`exec()`的调用返回该值，因为RISC-V上的C调用约定将返回值放在`a0`中。系统调用通常返回负数表示错误，返回零或正数表示成功。如果系统调用号无效，则`syscall`打印错误并返回-1

  

# 四、代码：系统调用参数

**系统调用参数**

> ①内核中的系统调用接口需要找到用户代码传递的参数。因为用户代码调用系统调用封装函数，所以参数被放置在RISC-V C调用所约定的位置：寄存器。内核trap代码将用户寄存器保存到当前进程的`trapframe`中，内核代码可以在其中找到它们。函数`argint`、`argaddr`和`argfd`从`trapframe`中检索第`n`个系统调用参数作为整数、指针或文件描述符。它们都调用`argraw`来检索相应的的已保存的用户寄存器(kernel/syscall.c:35)。 
>
> ②一些系统调用将指针作为参数传递，内核必须使用这些指针来读取或写入用户内存。例如，`exec`系统调用向内核传递一个指针数组，该数组指向用户空间中的字符串参数。这些指标提出了两个挑战。首先，用户程序可能有缺陷或恶意，并且可能向内核传递无效指针或旨在欺骗内核访问内核内存而不是用户内存的指针。其次，xv6内核页表映射与用户页表映射不同，因此内核不能使用普通指令从用户提供的地址加载或存储。 
>
> ③内核实现了安全地将数据传入和传出用户提供的地址的功能。`fetchstr`就是一个例子(kernel/syscall.c:25)。诸如`exec`之类的文件系统调用使用`fetchstr`从用户空间检索字符串文件名参数。`fetchstr`调用`copyinstr`来做这件事工作。 
>
> ![image-20230409091947010](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230409091947010.png)
>
> ④`copyinstr`(kernel/vm.c:406)从用户页表`pagetable`中的虚拟地址`srcva`复制最多`max`字节到`dst`。由于`pagetable`不是现在的页表（现在是虚拟地址映射的，而非物理地址），所以它使用`walkaddr`(调用`walk`)（通过虚拟地址查找物理地址）在软件中遍历页表以确定`srcva`的物理地址 `pa0`。由于内核将所有物理RAM地址映射到同一个内核虚拟地址（xv6采用的是直接映射，物理地址和虚拟地址相同），因此`copyinstr`可以直接将字符串字节从`pa0`复制到`dst`。`walkaddr`(kernel/vm.c:95)检查用户提供的虚拟地址是否为进程用户地址空间的一部分，因此程序无法欺骗内核读取其他内存。(行：113~118)
>
> ![image-20230409091840485](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230409091840485.png)
>
> 一个类似的函数，函数`copyout`将数据从内核复制到用户提供的地址。



# 五、来自内核空间的trap

**来自内核空间的trap**

> **xv6中CPU的trap寄存器** 
> xv6对CPU的trap寄存器的配置略有不同，具体取决于执行的是用户代码还是内核代码。当内核在CPU上执行时，内核将`stvec`指向`kernelvec`(kernel/kernelvec.S:10)处的汇编代码。由于`xv6`已经在内核中，`kernelvec`可以依靠已经设置了内核页表的`satp`，以及指向有效内核栈的栈指针。`kernelvec`保存所有寄存器，以便中断的代码最终可以不受干扰地恢复。 
>
> **kernelvec的工作** 
> 		`kernelvec`将寄存器保存在中断的内核线程的栈上，这是有道理的，因为寄存器值属于该线程。如果trap导致切换到不同的线程，这一点尤其重要—在这种情况下，trap实际上将返回新线程的栈，将被中断线程保存的寄存器安全地留在其栈中。 
>
> **kerneltrap的工作** 
> 		`kernelvec`在保存寄存器后跳转到`kerneltrap`(kernel/trap.c:134)。`kerneltrap`准备了两种类型的trap：设备中断和异常。它调用`devintr`(kernel/-trap.c:177)来检查和处理前者。如果trap不是设备中断，那它一定是一个异常，内核中的异常将是一个致命的错误；内核调用`panic`并停止执行。 
> 		如果由于定时器中断而调用`kerneltrap`，并且进程的内核线程正在运行(而不是调度程序线程)，则`kerneltrap`调用`yield`以让其他线程有机会运行。在某个时候，其中一个线程将让步，并让我们的线程及其内核trap再次恢复。第7章解释了yield中发生的事情。 
> 		当`kerneltrap`的工作完成后，它需要返回任何被trap中断的代码。因为`yield`可能干扰了`sstatus`中保存的`sepc`和保存的先前的状态模式，所以`kerneltrap`在启动时会保存它们。它现在恢复那些控制寄存器并返回到`kernelvec`(kernel/kernelvec.S:48)。`kernelvec`从栈中弹出保存的寄存器并执行`sret`，它将`sepc`复制到`pc`并恢复中断的内核代码。
> 		如果`kerneltrap`由于定时器中断而调用`yield`，那么trap返回是如何发生的，这是值得思考的。 
> **注：** 当CPU从用户空间进入内核时，xv6将CPU的`stvec`设置为`kernelvec`。你可以在`usertrap`(kernel/trap.c:29)中看到这一点。内核执行时有一个时间窗口，但`stvec`设置为`uservec`，在该窗口期间禁用设备中断至关重要。幸运的是，RISC-V 在开始捕获trap时总是禁用中断，而`xv6`直到设置`stvec`之后才再次启用它们。

  

# 六、页面错误异常

**xv6与其它系统的page fault**

> xv6对异常的响应非常无聊：如果用户空间发生异常，内核会杀死出错的进程。如果内核中发生异常，内核会触发panic。真正的操作系统通常以更有趣的方式做出响应。例如，许多内核使用page fault来实现写时复制版本的fork——`copy-on-write (COW)` fork。要解释COW fork，请考虑第3章中描述的xv6的fork。`fork`通过调用`uvmcopy`(kernel/vm.c:309)为子进程分配物理内存，从而使子进程拥有与父进程相同的内存内容。如果子进程和父进程可以共享父进程的物理内存，效率会更高。然而，一个简单的实现是行不通的，因为它会导致父子进程通过对共享栈和堆的写入来中断彼此的执行。

**RISC-V的三种page fault**

> 通过适当使用页面表权限和页面错误，父级和子级可以安全地共享物理内存。当使用的虚拟地址在页面表中没有映射，或者具有PTE_V标志已经被清除的映射，或者其许可位（PTE_R、PTE_W、PTE_X、PTE_U）禁止尝试操作的映射时，CPU会引发页面错误异常。
>
> 父进程和子进程可以使用由页面错误驱动的COW fork安全地共享物理内存。当CPU无法将虚拟地址转换为物理地址时，CPU会生成page fault异常。RISC-V有3种不同类型的page fault：加载页面错误(当加载指令无法翻译其虚拟地址时)、存储页面错误(当存储指令无法翻译其虚拟地址时)和指令页面错误(当指令的地址无法转换时)。`scause`寄存器中的值指示页面错误的类型，而`stval`寄存器包含无法转换的地址。

**COW fork**

> ​		COW fork的基本策略是父子进程最初共享所有的物理页面，但将它们映射为只读（清楚PTE_W标志）。父子可以从共享的物理内存中进行读取。因此，当子进程或父进程执行存储指令时（写入给定页面），RISC-V CPU 会引发page fault异常。为响应此异常，内核*trap*处理程序分配一个新的物理内存页，并将错误地址映射到的页面复制进去。内核更改故障进程的页表中相关的PTE，以指向该副本并允许写入和读取，更新页表后，内核在导致故障的指令处恢复故障进程的执行。由于内核已经更新了相关的PTE以允许写入，所以错误指令现在将正确地执行。写时复制需要记账，以帮助决定何时可以释放物理页面，因为根据forks，page faults，execs，and exits的历史，每个页面都可以被不同数量的页面表引用。这种记账允许进行重要的优化：如果<ins>一个进程</ins>出现存储页面错误，并且物理页面只从该进程的页面表中引用，则不需要复制。可以避免不必要的复制操作，提高系统的性能。
>
> ​		写时复制使fork更快，因为fork不需要复制内存。有些内存在以后写入时必须复制，但通常情况下，大多数内存都不必复制。例如，fork后面跟着exec：可能会在fork后面写一些页面，但随后子级的exec会释放从父级继承的大部分内存。COW fork消除了复制此内存的需要。
>
> ​		这个COW策略对`fork`很有效，因为子进程经常在`fork`之后立即调用`exec`，用新的地址空间替换它的地址空间。在那种常见的情况下，子进程只会遇到较少的page fault，内核可以避免拷贝父进程内存完整的副本。此外，COW fork是透明的，无需对应用程序进行任何修改即可受益。 
>
> **page fault的应用** 
>
> - ***lazy allocation***
>
> ​		页表和页错误的结合开辟了除COW fork之外的广泛有趣的可能性。另一个广泛使用的特性称为懒惰分配（*lazy allocation*），它有两个部分。首先，当应用程序调用`sbrk`请求更多内存时，内核标记增加的地址空间，但是不分配物理内存，并且不为新的虚拟地址范围创建PTE。其次，在其中一个新地址出现页面错误时，内核分配一页物理内存并将其映射到页表中。由于应用程序经常要求比他们需要的更多的内存，因此惰性分配是一个胜利：内核仅在应用程序实际使用它时才分配内存。与 COW fork 一样，内核可以对应用程序透明地实现此功能。
> ​		此外，如果应用程序要求大幅增加地址空间，那么没有延迟分配的sbrk是昂贵的：如果应用程序需要千兆字节的内存，内核必须分配并清零262144个4096字节的页面。懒惰的分配允许这种成本随着时间的推移而分散。另一方面，延迟分配会导致页面错误的额外开销，这涉及到内核/用户转换。操作系统可以通过为每个页面错误而不是一个页面分配一批连续的页面，并通过专门针对此类页面错误的内核进入/退出代码来降低这一成本。
>
> - ***demand paging***
>
> ​		另一个广泛使用的利用页面错误的功能是需求分页。在exec中，xv6将应用程序的所有文本和数据急切地加载到内存中。由于应用程序可能很大，并且从磁盘读取的成本很高，因此用户可能会注意到这种启动成本：当用户从shell启动大型应用程序时，可能需要很长时间才能看到响应。为了提高响应时间，现代内核为用户地址空间创建页面表，但将页面的PTE标记为无效。在出现页面错误时，内核从磁盘读取页面的内容，并将其映射到用户地址空间（当操作系统发现这个页面不在内存中时，就会触发一个异常，即所谓的page fault，操作系统会通过异常处理程序来处理这个异常，将对应的页面从磁盘中读入内存，并且将页面映射到进程的地址空间中，使得进程可以继续执行）。与COW fork和延迟分配一样，内核可以对应用程序透明地实现这一功能。在计算机上运行的程序可能需要比计算机的RAM更多的内存。为了正常处理，操作系统可以实现对磁盘的分页。其想法是将用户页面的一小部分存储在RAM中，并将其余部分存储在磁盘上的页面区域中。内核将与存储在分页区域（因此不在RAM中）中的存储器相对应的PTE标记为无效。如果应用程序试图使用已分页到磁盘的页面之一，则该应用程序将发生页面错误，并且该页面必须被分页：内核陷阱处理程序将分配一页物理RAM，将该页面从磁盘读取到RAM，并修改相关PTE以指向RAM。
>
> ​		如果页面需要分页，但没有可用的物理RAM，会发生什么？在这种情况下，内核必须首先释放一个物理页面，方法是将其分页或驱逐到磁盘上的分页区域，并将引用该物理页面的PTE标记为无效。逐出是昂贵的，所以如果分页不频繁，它的性能最好：如果应用程序只使用其内存页的一个子集，并且这些子集的并集适合RAM。这种性质通常被称为具有良好的参考位置。与许多虚拟内存技术一样，内核通常以对应用程序透明的方式实现对磁盘的分页。
>
> ​		磁盘分页。如果应用程序需要比可用物理RAM更多的内存，内核可以驱逐一些页面：将它们写入磁盘等存储设备并将它们的PTE标记为无效。如果应用程序读取或写入被逐出的页面，CPU将遇到page fault。然后内核可以检查故障地址。如果地址属于磁盘上的一个页面，则内核分配一个物理内存页面，将该页面从磁盘读取到该内存，将PTE更新为有效并引用该内存，然后恢复应用程序。为了给页面腾出空间，内核可能不得不驱逐另一个页面。此功能不需要对应用程序进行任何更改，并且如果应用程序具有引用的地址(即它们在任何给定时间仅使用其内存的子集)，则效果很好。 
>
> ​		结合分页和页面错误异常的其他功能包括自动扩展栈空间和内存映射文件。

  

# 七、现实世界

如果内核内存被映射到每个进程的用户页表(带有适当的PTE权限标志)，则可以消除对特殊trampoline page的需求。当从用户空间进入内核时，这也将消除对页表切换的需要。这反过来也将允许内核中的系统调用实现利用当前进程正在映射的用户内存，允许内核代码直接取消引用用户指针。 许多操作系统已经使用这些想法来提高效率。xv6避免使用它们是为了减少由于无意使用用户指针而导致内核中出现安全漏洞的机会，并降低了确保用户和内核虚拟地址不重叠所需的一些复杂性。