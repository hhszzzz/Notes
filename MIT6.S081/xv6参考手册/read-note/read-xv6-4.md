[todo--cpu how to run a process and where is the kernel?][]

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

## 1、RISC-V trap machinery

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

  实际上就是一个中断和异常的向量表，根据不同的原因码去查找对应的中断处理程序的地址

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

  在第三章中有讲到，`sstatus`的`SIE`位控制设备中断是否启用，如果内核清除`SIE`，RISC-V将会推迟设备中断，直到内核设置`SIE`。

  如下图，`SPP`位指示*trap*是来自user模式（0）还是supervisor模式（1），并控制`sret`返回到何种模式。

  ![image-20230415104849965](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230415104849965.png)

上述寄存器与在supervisor模式下处理的*traps*有关，并且在用户模式下不能读写这些寄存器。对于在machine模式下处理的*trap*，有一组等效的控制寄存器。xv6仅将它们用于**定时器中断**的特殊情况。 

多核芯片上的每个CPU都有自己的一组寄存器，在任何给定时间都可能有多个CPU同时处理一个*trap*。

> 当需要强制*trap*时，RISC-V硬件对所有类型的 *trap*（定时器中断除外）执行以下操作：
>
> 首先：如果*trap*是设备中断并且`sstatus SIE`位清零，请不要进行以下任何操作。 
>
> 1. 通过清除`SIE`禁用中断。 
>
>    ```c
>    intr_off();			// trap.c:97
>    ```
>
> 2. 将`pc`复制到`sepc`。 
>
>    ```c
>    // set S Exception Program Counter to the saved user pc.
>    w_sepc(p->trapframe->epc);			// trap.c:119
>    
>    // epc: saved user program counter
>    ```
>
> 3. 将当前模式( *user* 或 *supervisor* )保存在`sstatus`的`SPP`位中。
>
>    ```c
>    // trap.c: 112-116
>    // set S Previous Privilege mode to User.
>      unsigned long x = r_sstatus();
>      x &= ~SSTATUS_SPP; // clear SPP to 0 for user mode
>      x |= SSTATUS_SPIE; // enable interrupts in user mode
>      w_sstatus(x);
>    ```
>
> 4. 设置`scause`以反映*trap*的原因。 
>
> 5. 将模式设置为 *supervisor*。 
>
> 6. 将`stvec`复制到`pc`。 
>
> 7. 在新`pc`处开始执行。 

注意CPU不会切换到内核页表，不会切换到内核中的栈，也不会保存除`pc`以外的任何寄存器。必须靠内核软件来执行这些任务（**这话太晦涩难懂了，这是我的解释：CPU不会主动切换，而是让pc执行到处理trap的入口，让该入口地址对应的处理程序，根据需要选择是否切换到内核页表和内核栈**）。CPU在trap期间做最少工作的原因之一是为软件提供了灵活性。例如，某些操作系统在某些情况下不需要页表切换，这可以提高性能。

## 2、Traps from user space

如果用户程序进行系统调用(`ecall`指令)、执行非法操作、设备中断，则在用户空间执行时可能会发生*trap*。来自用户空间的*trap*的高级路径（high-level path）经过了哪些代码路径？先是`uservec`(kernel/trampoline.S:16)，然后是`usertrap`(kernel/trap.c:37)。返回时，先是`usertrapret`(kernel/trap.c:90)，然后`userret`(kernel/trampoline.S:16)。 

![image-20230415152520369](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230415152520369.png)

(trampoline.S:16)上图注释说明了一大堆东西，主要是为了介绍`uservec`（汇编代码函数，是 user space 进入 kernel space 的入口点），(trap.c)设置了`stvec`在这，从用户空间发生的*trap*从这里开始执行

在某些情况下（系统调用或页面错误等），需要在内核模式下处理*trap*事件，但是需要使用用户模式下的页表来访问用户空间的数据，因此会像20-21那样—"in supervisor mode, but with a user page table."

当`uservec`启动时，所有的32个寄存器都包含被中断的用户代码所拥有的值。这32个值需要保存在内存中的某个位置，以便在trap返回到用户空间时可以恢复。储存在内存需要使用寄存器来保存地址，这就要通过`sscratch`来提供帮助了（line:29中，`a0`和`sscratch`互换内容，ps：这里的a0储存了`trapframe`的地址）

在进入用户空间之前，内核将sscratch设置为指向每个进程trapframe结构。

### trampoline.S

```assembly
# trampoline.S

# uservec:
# line:27
# 1):保存a0(TRAPFRAME)
# swap a0 and sscratch
        # so that a0 is TRAPFRAME
        csrrw a0, sscratch, a0
# 使得sscratch和a0的值一样
# save the user registers in TRAPFRAME
        sd ra, 40(a0)
        sd sp, 48(a0)
        ...
        sd t6, 280(a0)

# line:63        
# save the user a0 in p->trapframe->a0
        csrr t0, sscratch
        sd t0, 112(a0)
# 将sscratch存储到t0中，再将t0存储到112(a0)中
# 112(a0)即p->trapframe->a0
		...
# jump to usertrap(), which does not return
        jr t0
# 现在的p->trapframe->a0已经指向了TRAPFRAME，跳转到usertrap()进行处理

# userret:
# line:99
# put the saved user a0 in sscratch, so we
# can swap it with our a0 (TRAPFRAME) in the last step.
        ld t0, 112(a0)
        csrw sscratch, t0
# 加载112(a0)到t0中，再将t0写入到sscratch
# restore all but a0 from TRAPFRAME
        ld ra, 40(a0)
		...
		ld t6, 280(a0)

# restore user a0, and save TRAPFRAME in sscratch
		csrrw a0, sscratch, a0
		
		# return to user mode and user pc.
        # usertrapret() set up sstatus and sepc.
        sret		
```

通过上述代码，实现了保存`TRAPFRAME`到恢复的过程

那么上面在`uservec`中出现了这样一行代码：

```assembly
# jump to usertrap(), which does not return
jr t0
```

随后跳转到`usertrap()`，返回时，先执行`usertrapret()`（在`usertrap`的最后一段代码）

### usertrap()

该函数的任务是确定*trap*的原因，并且对齐进行处理并返回

<span id="usertrap">line:10</span>

```c
//
// handle an interrupt, exception, or system call from user space.
// called from trampoline.S
//
void
usertrap(void)
{
  int which_dev = 0;

  // 检查中断是否启用以及是否处于用户模式
  if((r_sstatus() & SSTATUS_SPP) != 0)
    panic("usertrap: not from user mode");

  // send interrupts and exceptions to kerneltrap(),
  // since we're now in the kernel.
  // 将中断和异常的处理转交给内核处理程序kernelvec处理
  w_stvec((uint64)kernelvec);

  struct proc *p = myproc();
  
  // save user program counter.
  p->trapframe->epc = r_sepc();
  
  // scause寄存器被访问了，判断异常类型（这里是syscall）
  if(r_scause() == 8){
    // system calla

    if(p->killed)
      exit(-1);

    // sepc points to the ecall instruction,
    // but we want to return to the next instruction.
    // 表示已经执行了ecall指令，并跳转到CPU中指令的下一条（防止程序陷入无限循环，无法执行下一条指令）
    // RISC-V中，指令长度是4，+4表示指向下一条指令的地址
    p->trapframe->epc += 4;

    // an interrupt will change sstatus &c registers,
    // so don't enable until done with those registers.
    intr_on();

    syscall();
  
  // 如果当前中断是一个设备中断，那么就什么都不做
  } else if((which_dev = devintr()) != 0){
    // ok
  } else {
    printf("usertrap(): unexpected scause %p pid=%d\n", r_scause(), p->pid);
    printf("            sepc=%p stval=%p\n", r_sepc(), r_stval());
    p->killed = 1;
  }

  if(p->killed)
    exit(-1);

  // give up the CPU if this is a timer interrupt.
  if(which_dev == 2)
    yield();

  usertrapret();
}
```

#### yield()

56行中，如果是定时器中断，则调用`yield()`函数，进行进程的切换

![image-20230429104813815](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230429104813815.png)

进程切换的具体实现在`sched()`中。

然后调用`usertrapret()`，返回到(trampoline.S)中，继续执行`userret`，最后通过`sret`指令执行，返回用户态，恢复一切！

![image-20230416190002689](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230416190002689.png)

### usertrapret()

该函数是用于设置RISC-V的控制寄存器，为未来的*trap*(in user space)做准备

```c
//
// return to user space
//
void
usertrapret(void)
{
  struct proc *p = myproc();

  // we're about to switch the destination of traps from
  // kerneltrap() to usertrap(), so turn off interrupts until
  // we're back in user space, where usertrap() is correct.
  intr_off();

  // 将stvec更改为引用uservec（而不是kernelvec）
  // send syscalls, interrupts, and exceptions to trampoline.S
  w_stvec(TRAMPOLINE + (uservec - trampoline));

  // 准备uservec所依赖的trapframe字段
  // set up trapframe values that uservec will need (to recover the status of process) when
  // the process next re-enters the kernel.
  p->trapframe->kernel_satp = r_satp();         // kernel page table
  p->trapframe->kernel_sp = p->kstack + PGSIZE; // process's kernel stack
  p->trapframe->kernel_trap = (uint64)usertrap;
  p->trapframe->kernel_hartid = r_tp();         // hartid for cpuid()

  // set up the registers that trampoline.S's sret will use
  // to get to user space.
  
  // set S Previous Privilege mode to User.
  unsigned long x = r_sstatus();
  x &= ~SSTATUS_SPP; // clear SPP to 0 for user mode
  x |= SSTATUS_SPIE; // enable interrupts in user mode
  w_sstatus(x);

  // 将sepc设置为先前保存的用户程序计数器（在usertrap已经保存了）
  // set S Exception Program Counter to the saved user pc.
  w_sepc(p->trapframe->epc);

  // tell trampoline.S the user page table to switch to.
  // set the user mode page table to the satp, which is p->pagetable.
  uint64 satp = MAKE_SATP(p->pagetable);

  // 在映射到用户和内核页表的trampoline页上调用userret，原因是userret中的汇编代码会切换页表
  // jump to trampoline.S at the top of memory, which 
  // switches to the user page table, restores user registers,
  // and switches to user mode with sret.
  uint64 fn = TRAMPOLINE + (userret - trampoline);
  ((void (*)(uint64,uint64))fn)(TRAPFRAME, satp);
}
```

随后调用回`userret`(trampoline.S)，恢复用户的`a0`并为下一次*trap*保存`TRAPFRAME`，并使用`sret`返回用户空间。

## 3-4、Codes

这部分在`lab2`中有讲到，结合翻译食用

## 5、Traps from kernel space

在内核在CPU上执行，内核将`stvec`指向`kernelvec`，此时`kernelvec`可以依靠已经设置了**内核页表**的`satp`，以及指向**有效内核栈的栈指针**。

![image-20230504213916649](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230504213916649.png)

​		`kernelvec`将寄存器保存在中断的内核线程的栈上，因为，如果因为*trap*导致切换到不同的线程，那么*trap*实际上将返回新线程的栈，将被中断线程保存的寄存器安全地留在其栈中。 

```c
// interrupts and exceptions from kernel code go here via kernelvec,
// on whatever the current kernel stack is.
void 
kerneltrap()
{
  int which_dev = 0;
  uint64 sepc = r_sepc();
  uint64 sstatus = r_sstatus();
  uint64 scause = r_scause();

  // ensure interrupts enabled before handling and in supervisor-mode
  if((sstatus & SSTATUS_SPP) == 0)
    panic("kerneltrap: not from supervisor mode");
  if(intr_get() != 0)
    panic("kerneltrap: interrupts enabled");

  // handle device and timer interrupts.
  if((which_dev = devintr()) == 0){
    printf("scause %p\n", scause);
    printf("sepc=%p stval=%p\n", r_sepc(), r_stval());
    panic("kerneltrap");
  }

  // give up the CPU if this is a timer interrupt.
  if(which_dev == 2 && myproc() != 0 && myproc()->state == RUNNING)
    yield();

  // the yield() may have caused some traps to occur,
  // so restore trap registers for use by kernelvec.S's sepc instruction.
  w_sepc(sepc);
  w_sstatus(sstatus);
}
```

`kerneltrap`准备了两种类型的*trap*：设备中断和异常。它调用`devintr`(kernel/trap.c:177)来检查和处理前者。如果trap不是设备中断，那它一定是一个异常，内核中的异常将是一个致命的错误；内核调用`panic`并停止执行。 

如果由于定时器中断而调用`kerneltrap`，并且进程的内核线程正在运行(而不是调度程序线程)，则`kerneltrap`调用`yield`以让其他线程有机会运行。在某个时候，其中一个线程将让步，并让我们的线程及其内核trap再次恢复。

当`kerneltrap`的工作完成后，它需要返回任何被trap中断的代码。因为`yield`可能干扰了`sstatus`中保存的`sepc`和保存的先前的状态模式，所以`kerneltrap`在启动时会保存它们(kerneltrap:8-9)。它现在恢复那些控制寄存器并返回到`kernelvec`(kernel/kernelvec.S:48)(kerneltrap:30-31)。`kernelvec`从栈中弹出保存的寄存器并执行`sret`（该指令会将内核保存的一些寄存器和处理器的状态恢复到被中断时的状态，其中包括将`sepc`的值拷贝到`pc`中，然后再从中断处理完的位置继续执行），它将`sepc`复制到`pc`并恢复中断的内核代码。

内核执行时有一个时间窗口，但`stvec`设置为`uservec`，在该窗口期间禁用设备中断至关重要。幸运的是，RISC-V 在开始捕获trap时总是禁用中断，而`xv6`直到设置`stvec`之后才再次启用它们。(<a href="#usertrap">跳转</a>)

[timevec][]

## 6、Page-fault exceptions

### 写时复制（`COW`）

*copy-on-write*

> 参考`fork`调用`uvmcopy`为子进程分配内存
>
> 文中提到，如果子进程和父进程可以共享父进程的物理内存，效率会更高。然而，一个简单的实现是行不通的，因为它会导致父子进程通过对共享栈和堆的写入（会覆盖彼此的数据）来中断彼此的执行。

除了`fork`之外，例如`exec`，如果一个进程调用了`exec`函数来加载一个新的程序，那么它将覆盖它的地址空间，包括共享的栈和堆。如果有另一个进程正在读取或写入共享的栈和堆，那么它的执行可能会被中断或崩溃，因为共享的数据已经被改变了。

#### 三种Page-fault

> - 加载页面错误（当加载指令无法翻译其虚拟地址时）
>
> - 存储页面错误（当存储指令无法翻译其虚拟地址时）
>
> - 指令页面错误（当指令的地址无法转换时）
>
> `scause`寄存器中的值指示页面错误的类型，而`stval`寄存器包含无法转换的地址。

#### COW策略

> 基本策略是父子进程最初共享所有的物理页面，但将它们映射为只读（清楚PTE_W标志）。父子可以从共享的物理内存中进行读取。
>
> 这里有一个准确的例子，可以看看原文翻译

#### Page-fault的实际使用

> - *lazy allocation*
> - *demand paging*