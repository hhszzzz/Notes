# Page table

## xv6页表的逻辑视图

Q：如何根据虚拟地址来查询物理地址？

A：如下图，首先我们有一个虚拟地址，然后根据虚拟地址的前27位的index（后面有讲EXT）索引到页表的页表项<a href="#pte">PTE</a>（PPN+Flags），然后找到<a href="#ppn">PPN</a>，最后根据<a href="#ppn">PPN</a>和虚拟地址的偏移量Offset组合成物理地址。

- 图1

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678711780769.png"  />

有的人可能会问那虚拟地址最前面的25位是什么？在RISC-V中，虚拟地址的EXT部分时用于扩展虚拟地址的。

------

Q：那么这里我就又有一个问题了，页表是存在哪里的呢？虚拟地址怎么访问页表的呢？虚拟地址的前27位是页表的物理地址吗？

A：好，那么Figure3.2图也给出了解释（如下图），可以观察到页表已经分为了三级，一级页表是存储在<a href="#satp-register"><a href="#satp-register">satp寄存器</a></a>中的，此时通过satp找到一级页表，然后通过高九位（L2）查找到PTE，根据<a href="#ppn">PPN</a>的地址定位到二级页表，同上，最后找到三级页表的PTE，取出PPN，再根据虚拟地址的偏移量Offset（低12位）组合成最终的物理地址。

那么现在就可以解释上面的问题了。

首先第一个问题，**一级页表是存储在<a href="#satp-register">satp寄存器</a>中的（当然这里只能存一个当前运行进程的页表），也就是缓存（cache），二级和三级页表一般是存在内存中的。**

第二问，已经在上文给出了答案，但是再补充一点！**在CPU需要查找虚拟地址对应的物理地址时，会用到<a href="#mmu">MMU</a>，在<a href="#mmu">MMU</a>中存储了一张虚拟地址与物理地址映射的表单，通过<a href="#mmu">MMU</a>进行转换后，在<a href="#satp-register">satp寄存器</a>中定位到一级目录，继而**。

第三问，并不是！**虚拟地址的前27位的其中9位是用于索引某一级页表的页表项**，得到<a href="#ppn">PPN</a>，通过<a href="#ppn">PPN</a>定位到下一级页表，再用其中9位索引该页表的页表项，从而找到下一级的页表。依我所见，`PTE2PA(最后一级的PTE)`才是物理地址

- 图2

<img src="https://img-blog.csdnimg.cn/6dbf6d3ce0424bff84502ae0f5c65796.png" alt="在这里插入图片描述"  />

这里我发现每一级的页表只有512个页表项，经过chatGPT大法，得到原来是由RISC-V架构规定的。

然后我又发现一个问题，为什么这里只有`Page Directory`？我的Page table去哪里了！经查阅发现，每一级页表都有不同的称呼，级别从高到低分别为`Page Directory`，`Page Middle Directory`和`Page table`。原来如此！那么三级页表才叫做`Page table`。

在这张表还存有<a href="#pte">PTE</a>的结构，flags标志位的每位都代表着不同的意思，这里不做过多叙述。

------

## 内核地址空间

根据书上描述"plus a single page table that describes the kernel’s address space"，可以得出，内核的运行是需要一个页表的，就只有一个！继续阅读下去，发现在这个页表中，内核配置了地址空间的布局，以便在虚拟地址上访问对应的物理地址和各种硬件资源

文件（kernel/memlayout.h）中也有提到xv6的地址空间的布局（从第1行往下看），就和下面的图3一样

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679584308523.png"  />

在xv6中，内核采用**直接映射**的方式来获取RAM和<a href="#memory-mapped-control-registers">内存映射设备寄存器</a>，也就是说，内核空间中虚拟地址和物理地址是相同的。

> 解释一下上图出现的各种不知名的东西
>
> 1. `CLINT`：CLINT is used to implement the timer and interprocessor interrupts (IPIs).
>
> 2. `PLIC`：PLIC is used to implement device interrupts.
>
>    很显然`CLINT`是实现时钟中断的，而`PLIC`是实现设备中断的
>
> 3. `UART0`：uart0 is used to print debug information. 
>
>    qemu puts UART registers here in physical memory.
>
> 4. `VIRTIO0`：virtio mmio (mmio is memory-mapped I/O.) interface. 
>
>    virtio mmio is a standard interface for device drivers(设备驱动程序) to talk to virtual devices.
>
>    `VIRTIO0`提供磁盘I/O功能的虚拟设备，模拟一个磁盘控制器，通过virtio协议与宿主机进行通信，实现对虚拟磁盘的读写操作，`VIRTIO0`设备驱动程序在(kernel/virtio_disk.c)实现
>
>    此外，`VIRTIO0`设备并不是 xv6 操作系统中唯一的虚拟设备，还包括了 `VIRTCON`（用于提供虚拟终端）、`VIRTIO-RNG`（用于提供随机数生成器）等。这些虚拟设备都是为了方便在虚拟化环境中进行操作系统和应用程序的开发和测试而设计的。

如下图，内核在虚拟地址空间和物理内存都位于`KERNBASE = 0x80000000`上，直接简化了读取或写入内存的内核代码。

- 图3

<img src="https://img-blog.csdnimg.cn/70d921a29cac46338058aed58576b36d.png" alt="在这里插入图片描述"  />

从图上可以观察到，`kernel data`和`kernel text`也是直接映射到物理地址中的，而且在虚拟地址上，`PHYSTOP`到`MAXVA`之间存放着内核的数据段和内核的堆。具体来说，这段空间中包括了内核数据结构、内存分配器、系统调用参数和返回值缓冲区、各种缓冲区以及其他内核需要数据等等。

> 总结
>
> 通过上述物理地址`0~KERNBASE`，是各种设备，驱动，`KERNBASE~PHYSTOP`是物理内存。
>
> 可以得出，实际上，设备上存储的都是**硬件地址**，但是这里出现了硬件地址和物理内存地址两种，为什么呢？因为物理内存其实是一个抽象的概念，是全体RAM存储单元的集合
>
> 所以，设备存储的都是硬件的地址，本质上并没有物理内存地址的概念。

QEMU模拟一台包含RAM（物理内存）的计算机，从图上可看出，RAM从物理地址0x80000000开始并持续到0x86400000（PHYSTOP），同时，QEMU还模拟了I/O设备，例如磁盘接口。QEMU将设备接口作为<a href="#memory-mapped-control-registers">内存映射设备寄存器</a>暴露给软件，这些寄存器位于物理地址中0x80000000以下。内核可以通过读/写这些特殊的物理地址与设备进行交互，此类读写是与设备的硬件通信而不是与RAM进行通信（第4章有讲到）

又例如，当`fork`（kernel/proc.c）为子进程分配用户内存时，分配器返回该内存的物理地址，于是`fork`将父进程的用户内存复制到子进程时，直接将该地址作为虚拟地址。

凡事都要讲个证据，直接映射具体代码在哪呢？还有`fork`真的是这样吗？<a href="#fork-answer">传送</a>

### fork函数

下面贴出代码

```c
// Create a new process, copying the parent.
// Sets up child kernel stack to return as if from fork() system call.
int
fork(void)
{
  int i, pid;
  struct proc *np; // son pointer
  struct proc *p = myproc();

  // Allocate process.
  if((np = allocproc()) == 0){
    return -1;
  }

  // Copy user memory from parent to child.
  if(uvmcopy(p->pagetable, np->pagetable, p->sz) < 0){
    freeproc(np);
    release(&np->lock);
    return -1;
  }
  np->sz = p->sz;

  // copy saved user registers.
  *(np->trapframe) = *(p->trapframe);

  // Cause fork to return 0 in the child.
  // 将trapframe的a0寄存器的值设置为0
  // 父进程返回子进程的进程ID，子进程返回0，因此，a0设置为0将会导致子进程返回0
  np->trapframe->a0 = 0;

  // increment reference counts on open file descriptors.
  // 打开文件描述符的引用计数（应该在文件系统那章才有的讲）
  for(i = 0; i < NOFILE; i++)
    if(p->ofile[i])
      np->ofile[i] = filedup(p->ofile[i]);
  np->cwd = idup(p->cwd);
  // name在代码是用来debug的（下面的proc结构体有讲）
  safestrcpy(np->name, p->name, sizeof(p->name));

  pid = np->pid;

  release(&np->lock);

  acquire(&wait_lock);
  np->parent = p;
  release(&wait_lock);

  acquire(&np->lock);
  np->state = RUNNABLE; // 更新状态RUNNABLE
  release(&np->lock);

  return pid;
}
```

11行申请一个进程，继续追

#### allocproc函数

```c
// Look in the process table for an UNUSED proc.
// If found, initialize state required to run in the kernel,
// and return with p->lock held.
// If there are no free procs, or a memory allocation fails, return 0.
static struct proc*
allocproc(void)
{
  struct proc *p;

  for(p = proc; p < &proc[NPROC]; p++) {
    acquire(&p->lock);
    if(p->state == UNUSED) { // 找到一个可以分配的进程(UNUSED)
      goto found;
    } else {
      release(&p->lock);
    }
  }
  return 0;

// allocate something

found:
  p->pid = allocpid();
  p->state = USED;

  // Allocate a trapframe page.
  if((p->trapframe = (struct trapframe *)kalloc()) == 0){
    freeproc(p);
    release(&p->lock);
    return 0;
  }

  // An empty user page table.
  p->pagetable = proc_pagetable(p); // 分配一个页表
  // 用户有用户的分配(proc_pagetable)，内核有内核的分配(kvmmake)
  if(p->pagetable == 0){
    freeproc(p);
    release(&p->lock);
    return 0;
  }

  // Set up new context to start executing at forkret,
  // which returns to user space.
  memset(&p->context, 0, sizeof(p->context));
  p->context.ra = (uint64)forkret;
  p->context.sp = p->kstack + PGSIZE;

  return p;
}
```

[todo][]forkret

##### freeproc函数

下面贴出代码

![](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679622057217.png)

> 不难看出，该函数的作用是 "是恢复出厂设置"，将该进程设置为`UNUSED`状态。

##### context结构体

![](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230324195949045.png)

> `context`结构体是用于在内核上下文切换过程中保存寄存器状态的。该结构体保存的成员变量是一些在内核上下文切换中需要保存的寄存器状态，具体来说：
>
> - `ra`：保存返回地址寄存器的值，指示从函数调用中返回时应返回的地址。
> - `sp`：保存栈指针寄存器的值，指示当前栈的位置。
> - `s0 - s11`：保存调用者保存的寄存器的值，这些寄存器在函数调用过程中需要被保存，以便在函数返回时可以恢复它们的值。

#### uvmcopy函数

上文提到的问题的<span name="fork-answer">答案</span>应该就在这里了！在代码24行已经标识出来了

```c
// Given a parent process's page table, copy
// its memory into a child's page table.
// Copies both the page table and the
// physical memory.
// returns 0 on success, -1 on failure.
// frees any allocated pages on failure.
int
uvmcopy(pagetable_t old, pagetable_t new, uint64 sz)
{
  pte_t *pte;
  uint64 pa, i;
  uint flags;
  char *mem;

  for(i = 0; i < sz; i += PGSIZE){
    if((pte = walk(old, i, 0)) == 0)
      panic("uvmcopy: pte should exist");
    if((*pte & PTE_V) == 0)
      panic("uvmcopy: page not present");
    pa = PTE2PA(*pte);
    flags = PTE_FLAGS(*pte);
    if((mem = kalloc()) == 0)
      goto err;
    memmove(mem, (char*)pa, PGSIZE); // in this! 完全复制了父进程的地址
    if(mappages(new, i, PGSIZE, (uint64)mem, flags) != 0){
      kfree(mem);
      goto err;
    }
  }
  return 0;

 err:
  uvmunmap(new, 0, i / PGSIZE, 1);
  return -1;
}
```

#### trapframe结构体

```c
// per-process data for the trap handling code in trampoline.S.
// sits in a page by itself just under the trampoline page in the
// user page table. not specially mapped in the kernel page table.
// the sscratch register points here.
// uservec in trampoline.S saves user registers in the trapframe,
// then initializes registers from the trapframe's
// kernel_sp, kernel_hartid, kernel_satp, and jumps to kernel_trap.
// usertrapret() and userret in trampoline.S set up
// the trapframe's kernel_*, restore user registers from the
// trapframe, switch to the user page table, and enter user space.
// the trapframe includes callee-saved user registers like s0-s11 because the
// return-to-user path via usertrapret() doesn't return through
// the entire kernel call stack.
struct trapframe {
  /*   0 */ uint64 kernel_satp;   // kernel page table
  /*   8 */ uint64 kernel_sp;     // top of process's kernel stack
  /*  16 */ uint64 kernel_trap;   // usertrap()
  /*  24 */ uint64 epc;           // saved user program counter
  /*  32 */ uint64 kernel_hartid; // saved kernel tp
  /*  40 */ uint64 ra;
  /*  48 */ uint64 sp;
  /*  56 */ uint64 gp;
  /*  64 */ uint64 tp;
  /*  72 */ uint64 t0;
  /*  80 */ uint64 t1;
  /*  88 */ uint64 t2;
  /*  96 */ uint64 s0;
  /* 104 */ uint64 s1;
  /* 112 */ uint64 a0; // 保存函数的第一个参数
  /* 120 */ uint64 a1;
  /* 128 */ uint64 a2;
  /* 136 */ uint64 a3;
  /* 144 */ uint64 a4;
  /* 152 */ uint64 a5;
  /* 160 */ uint64 a6;
  /* 168 */ uint64 a7;
  /* 176 */ uint64 s2;
  /* 184 */ uint64 s3;
  /* 192 */ uint64 s4;
  /* 200 */ uint64 s5;
  /* 208 */ uint64 s6;
  /* 216 */ uint64 s7;
  /* 224 */ uint64 s8;
  /* 232 */ uint64 s9;
  /* 240 */ uint64 s10;
  /* 248 */ uint64 s11;
  /* 256 */ uint64 t3;
  /* 264 */ uint64 t4;
  /* 272 */ uint64 t5;
  /* 280 */ uint64 t6;
};
```

这里有各种寄存器...就不深入讲解了，应该留到第四章trap来讲hh

好了`fork`函数讲完了

---

在下图中提到了<a href="#kernel-data-and-text">Kernel data和Kernel text</a>。

- 图4

![](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678923652486.png)

紧接上图啊！有些内核虚拟地址不是直接映射的：

- `trampoline page`：内核和用户进程之间的一个共享的虚拟页面，并且用户进程和内核使用相同的映射来访问`trampoline page`，它被映射到虚拟地址空间的最高地址处。

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678878464998.png"  />

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678878579724.png"  />

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678878610691.png"  />

  查阅代码可以发现这个`trampoline page`确实是在虚拟地址空间的最高地址处，`PGSIZE = 4096`，实际上`MAXVA`并没有这么大，这么大只是为了避免符号扩展的问题。实际上我也不太清楚是多少，不过在`Sv39`中最高位的地址是$2^{38}$，用16进制表示就是`0x4000000000`。并且，`trampoline page`也是一个页表的大小，**而且是最后一个页表！**

  

  当用户进程执行系统调用时，内核只需要切换页表，然后就可以直接访问`trampoline page`，获取系统调用参数并执行系统调用。

  好处就是，内核可以通过访问`trampoline page`来实现对用户进程的访问，无需再次切换到用户进程的虚拟地址空间。虽然执行系统调用时进行了页表切换，但是不需要进行地址空间切换，因此提高了系统调用的性能和效率

> Q：到这里我其实又有一个疑问，地址空间切换和页表切换有什么区别？为什么有了`trampoline page`就不用进行地址空间切换了？
>
> A：第一个问题，地址空间切换指的是进程与进程之间的切换，而页表切换是pagetable之间的切换，当进程切换时，页表切换会同时进行
>
> 第二个问题，由于[todo][]的原因，现在只能比较笼统地解释。
>
> 从chatGPT给出的答案总结：是因为`trampoline page`包含了一些内核代码，用于执行系统调用，当用户进程执行系统调用时，只需要切换一次页表，将页表映射到`trampoline page`上，然后直接执行内核代码，刚刚提到这里面包含了内核代码，同时这里也包含了用户进程地址空间的映射，所以执行系统调用时，可以无需进行地址空间切换。
>

据我理解，就是不需要切换到内核进程，只需要跳转到`trampoline page`直接执行有关系统调用的代码即可。

- `kernel stack page`：

  每个进程都有自己的内核栈，内核栈被映射到足够高到xv6在它下面可以留下一个未被映射的保护页，**保护页的<a href="#pte">PTE</a>无效**（感觉很重要，待会做lab可能会参考到）

  

## 创建地址空间

### 通用函数操作

在阅读到`copyout`和`copyin`后（kernel/vm.c），可以解释这两个函数的作用。

在内核空间和用户空间之间进行数据传输，具体来说，它们将数据从内核缓存区（内核与用户之间的一种隔离isolation）复制到用户空间的虚拟地址，或者将数据从用户空间的虚拟地址到内核缓冲区。

- #### `copyin`

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678886214034.jpg"  />

  `copyin`函数的参数包括一个页表（pagetable_t）、一个目标地址（dst）、一个源地址（srcva）和要复制的字节数（len），提供了用户进程向内核传递数据的方法。

  <a  href="#walkaddr">walkaddr</a>函数的解析可以往这里看，在这里我画了一个流程图辅助理解

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/c553f841ff4aa42f3ef7e7f01ca3c5c.jpg" style="zoom: 50%;" />

- #### `copyout`

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678886292017.jpg"  />

  同`copyin`相同，只不过这次互换角色了而已

---

Q：既然一个进程对应一个页表，而且页表有许多页表项，那么页表都存了啥？可能是指令之类的，每个指令都有一个或者多个<a href="#pte">PTE</a>？

A：一个进程确实是对应一个页表，但是页表不是用来存数据的，**页表存的是页面**（这个好像是一个新概念）**的虚拟地址和物理地址的映射关系**。即一个进程对应一个页表，而一个页表对应多个页面，页面存放的都是有关该进程的数据。

实际上这些映射关系指向的物理地址中存放了实际的数据。因此，数据时存放在页面中，而不是存放在页表中。

> tips：**页面和页表的大小都是4KB**

---

- #### `kvminithart()`

  ```cpp
  // Switch h/w page table register to the kernel's page table,
  // and enable paging.
  void
  kvminithart()
  {
    w_satp(MAKE_SATP(kernel_pagetable));
    sfence_vma();
  }
  ```

  > 注释的意思就是，将硬件页表寄存器切换到内核页表，并启用分页机制，以便内核能够访问自己的代码和数据，并能够对进程地址空间进行管理和保护。

  相关代码：

  ```c
  // supervisor address translation and protection; 主管地址转换和保护
  // holds the address of the page table. 保护页表的地址
  static inline void 
  w_satp(uint64 x)
  {
    asm volatile("csrw satp, %0" : : "r" (x));
    // csrw：RISC-V汇编语言的指令，用于将一个寄存器的值写入到指定的系统寄存器中
    // 在这里，"csrw satp, %0" 的意思是将"%0"（即 "x"）的值写入到 SATP 寄存器中。
    // "volatile"关键字表示该指令是一个内存屏障，即必须立即执行，并且不能被编译器优化或重排。
  }
  ```

  ```c
  #define MAKE_SATP(pagetable) (SATP_SV39 | (((uint64)pagetable) >> 12))
  // SATP_SV39对应`8L << 60`
  // 根据高位编码值公式是`(N/8 - 1)L`，因此该模式高位编码的位数为4
  // 而高位编码的方式是固定的，不同的高位编码表示不同的虚拟地址模式。
  ```

  > 综合来看`w_satp(MAKE_SATP(kernel_pagetable));`就是将内核页表写入<a href="#satp-register">satp寄存器</a>。从现在开始，内核页表全面接管！！！

  ```cpp
  // flush the TLB.
  static inline void
  sfence_vma()
  {
    // the zero, zero means flush all TLB entries.
    asm volatile("sfence.vma zero, zero");
  }
  ```

  啊，那么TLB是什么呢？我们接着来看书吧！

  在文章也有讲到 TLB（Translation Look-aside Buffer），那么TLB是用来存储最近访问的虚拟地址到物理地址的转换结果（用于加速内存访问）。

  xv6如果在更改页表的时候没有指定旧的TLB条目无效，那么稍后TLB可能会用旧的缓存映射，指向一个已经分配给其他进程的物理页。那么结果就是，**一个进程就有能力去在其他进程的内存中进行修改**，后果十分严重。

  那么，如何刷新呢？RISC-V给出了一条指令`sfence.vma`，用于刷新当前CPU的TLB

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679294379619.png"  />

  xv6在重新加载<a href="#satp-register">satp寄存器</a>后在`kvminithart`中执行`sfence.vma`，在返回用户空间之前在切换到用户页表的 `trampoline` 代码中执行 `sfence.vma`(kernel/trampoline.S:79)。

  以上在代码中均能找到

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679294458976.png" style="zoom:85%;" />

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679294338724.png" style="zoom:80%;" />

- #### `procinit`

  好，那么回到原文，`main`调用`procinit`为每个进程分配内核堆栈。为每个堆栈映射到由`KSTACK`生成的虚拟地址，从而为保护页（invalid）留出空间。

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679296431415.png"  />

  ##### <span name="proc">结构体</span>`proc`：

  ```c
  // Per-process state
  struct proc {
      
    // 这里有一个锁
    struct spinlock lock;
  
    // 当使用这些元素必须要获取锁
    // p->lock must be held when using these:
    enum procstate state;        // Process state
    void *chan;                  // If non-zero, sleeping on chan
    int killed;                  // If non-zero, have been killed
    int xstate;                  // Exit status to be returned to parent's wait
    int pid;                     // Process ID
  
    // what is wait_lock?
    // wait_lock must be held when using this:
    struct proc *parent;         // Parent process
  
    // 下面的元素对进程来说是私有的，所以不需要获取锁
    // these are private to the process, so p->lock need not be held.
    uint64 kstack;               // Virtual address of kernel stack
    uint64 sz;                   // Size of process memory (bytes)
    pagetable_t pagetable;       // User page table
    // 这个kpagetable是自己加的...
    pagetable_t kpagetable;	   // Kernel page table
    struct trapframe *trapframe; // data page for trampoline.S
    struct usyscall *usyscall;   // data page for USYSCALL
    // context结构体通常包含了进程的寄存器信息以及堆栈指针等信息
    // 用于在进程切换时保存当前进程的状态（与cpu相配合），以便在之后重新调度该进程时能够恢复其之前的执行状态
    struct context context;      // swtch() here to run process
    struct file *ofile[NOFILE];  // Open files
    struct inode *cwd;           // Current directory
    char name[16];               // Process name (debugging)
  };
  ```

  ---

  回到`procinit`函数

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/7aa9465457e9ce7135f310dc054b67a.png"  />

  根据上图，以及下图

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679307091346.png"  />

  **不难猜测出`pid_lock`是一个用于创建新进程的锁，只有获取了它之后，才能够进行创建新进程的操作。**

  ---

  ##### 提到了一点锁

  那么`procinit`的`initlock`(:51)是干什么的呢？让我们继续翻看源码

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/952f039f6456b9869fa35398009fe558_.png"  />

  这里初始化了锁`lk`的名字（name）、状态（`locked`）、被哪个cpu持有（0表示没有）

  那么为什么要起一个`spinlock`呢，继续追

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1a2fa0fda608de00e20c058ba3bb8eae_.png"  />

  噢，原来是互斥锁，好吧，等后面的章节再来解析。

  噢对了，别忘了`wait_lock`

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679308522004.png"  />

  经典阅读理解......根据注释，不难理解`wait_lock`可以确保父进程在等待子进程退出时，不会错过任何子进程退出的通知（如果不使用该锁，当多个子进程同时退出时，父进程可能会错过其中的一些通知，从而导致等待超时或者其他的问题）

  另外，`wait_lock`必须比任何`p->lock`更先获取，如果反过来，那么可能会导致死锁或者其他问题（详情请参考`wait`函数）。

  综上，`initlock`就是初始化锁的数据，说远了......厚礼谢。

  ---

  那么接下来就是对每个进程进行循环，`NPROC`也是老朋友了（**允许的最大进程数64**），初始化每个进程，然后自然是看不懂`p->kstack = KSTACK((int) (p - proc));`，没关系，继续冲

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679311090180.png" style="zoom:80%;" />

  从这里就可以看出来了，**内核页表就只是一个页表的大小而已！！！**（我好像之前写错了），`TRAMPOLINE`所代表的就是用户与内核直接的隔阂，也是用户的最高地址

  然后`KSTACK`宏定义的注释解释了内核栈是在`trampoline`下的，每一个都被保护页包裹着，其实代码也能看出来，每次都是`(p)*2`，应该是留出给保护页（这里的保护页好像不需要再申请之类的，因为没有定义，就是无效的，就可以提供保护的作用）的空间。所以！`KSTACK`的作用就是计算进程的内核栈起始地址，而`trampoline`是**内核栈顶**。

  那么这段代码就好解释了，`(p-proc)`是进程在进程（[todo][]有这个东西嘛？）表中的索引号，综合来说，这行代码的作用是：根据该进程在进程表中的索引号，将当前进程的内核栈起始地址计算出来，并将其存储在进程控制块中的`kstack`字段中。这样就可以通过使用`p->kstack`来使用内核栈了！！！

  好，终于，`procinit`结束了！

  ---

激动地进入3.4

## 四、物理内存分配

  xv6使用内核末端和`PHYSTOP`（操作系统所支持的物理内存的上限）之间的物理内存用于运行时分配，一次分配一个page

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679312647837.png"  />

  通过维护一个物理页的链表来寻找哪些内存页面当前可用和哪些已经被分配，**噢噢！这里就是内存**。分配新内存时，os需要从链表中移除一个**可用页面**（这里我一开始理解为移除别的进程正在使用的，原来是一个可用的）来分配给该进程使用，这个过程被称为"Allocation"（分配）；相反，当一个进程释放一个页面时，os会将该页面添加回链表中以便下次可以被重新分配给其他进程使用，这个过程被称为"Freeing"（释放）

  好耶，3.4结束了

---

  现在苦逼的3.5，算了，明天再看吧！

  ## 五、物理内存分配器

  ### `kinit`函数

在`kinit`函数中，初始化锁`kmem`，然后进入`freerange(end, (void*)PHYSTOP)`函数

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679399471206.png"  />

那么这里的PHYSTOP代表了最高的物理地址，`end`又表示什么呢？于是我找到了这段。

  ![](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679399574722.png)

注释说明了是继内核之后的第一个地址，即内核的终点

于是我又找到了文件 kernel.ld

  ```c
  OUTPUT_ARCH( "riscv" )
  ENTRY( _entry )
  
  SECTIONS
  {
    /*
     * ensure that entry.S / _entry is at 0x80000000,
     * where qemu's -kernel jumps.
     */
    . = 0x80000000;
  
    .text : {
      *(.text .text.*)
      . = ALIGN(0x1000);
      _trampoline = .;
      *(trampsec)
      . = ALIGN(0x1000);
      ASSERT(. - _trampoline == 0x1000, "error: trampoline larger than one page");
      PROVIDE(etext = .);
    }
  
    .rodata : {
      . = ALIGN(16);
      *(.srodata .srodata.*) /* do not need to distinguish this from .rodata */
      . = ALIGN(16);
      *(.rodata .rodata.*)
    }
  
    .data : {
      . = ALIGN(16);
      *(.sdata .sdata.*) /* do not need to distinguish this from .data */
      . = ALIGN(16);
      *(.data .data.*)
    }
  
    .bss : {
      . = ALIGN(16);
      *(.sbss .sbss.*) /* do not need to distinguish this from .bss */
      . = ALIGN(16);
      *(.bss .bss.*)
    }
  
    PROVIDE(end = .);
  }
  
  ```

虽然我看不懂链接器脚本（Linker Script），但是GPT可以啊！

在这个链接脚本中，`.text`部分定义了操作系统内核代码段（可能就是图4中的`kernel text`），`.rodata`部分定义了只读数据段，`.data`部分定义了读写数据段（`kernle data`），`.bss`部分定义了未初始化的数据段

> 综上，`kinit`是初始化从内核末尾到最高物理地址的空间

  ### `freerange`函数

然后深入`freerange`函数

  ![](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679400638241.png)

前面的都能理解，（根据偏移量）向上取整`pa_start`复制给p（这里向上取整是有讲究的，因为如果下取整到了内核的数据代码等，就有可能会出问题。而且还有一个与之相对的函数`PGROUNDDOWN`在我的`trace_userinit`文章中有讲到），然后从`pa_start`到`pa_end`进行`kfree`函数

  #### `kfree`函数

  ![](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679401337901.png)

free `v` 指向的物理内存页（这里注释应该有问题，应该将`v`改为`pa`），该物理内存页一般通过`kalloc`函数调用返回（如果初始化分配器异常，查看上面的`kinit`）

那么经过向上取整之后的`pa`（freerange的p）应该不太可能会出现51行if前两个条件，那么如果有了，就报错呗~

55行，注释解释了，在代码中填充一些随机数据，以便在程序中引用一个未初始化或已经释放的内存时，可以更容易地发现这些问题，从而避免程序崩溃或出现其他错误。小tips：这样的填充的数据通常被称为"垃圾值"（`junk value`）或"毒草"（`poison`）

57-62行的意图很明显，就是和`kalloc()`函数相反的操作（参考我的文章`trace_userinit`）

那现在的`kinit`就详解完了

---

在书上还提到，分配器开始没有内存，正是对`kfree`的调用将可用内存交给了分配器来管理。

**分配器代码中的类型转换：**

分配器有时将地址视为整数以便对其执行算术运算(例如，遍历`freerange`中的所有page)，有时将地址用作读写内存的指针(例如，操作存储在每个页面中的`run`结构体)。这种对地址的双重使用是分配器代码充满C类型转换的主要原因。另一个原因是释放和分配内存固有地改变了内存的类型。 

---

终于结束了3.5！！

今天3.6（进程地址空间）

## 进程地址空间

**进程申请用户内存**：当用户进程向xv6请求更多用户内存时，xv6先调用`kalloc`来分配物理页。然后将<a href="#pte">PTE</a>添加到进程的页表中，（<a href="#pte">PTE</a>）指向新的物理页。xv6在<a href="#pte">PTE</a>中设置各种标示（`PTE_W`、`PTE_X`等等），大多数进程都不会用到整个用户地址空间，xv6将未使用的<a href="#pte">PTE</a>中的`PTE_V`清除。

那么，具体代码体现，我也找到了，嘿嘿（kernel/vm.c:233）`uvmalloc`函数。

### `uvmalloc`函数

```c
// Allocate <a href="#pte">PTE</a>s and physical memory to grow process from oldsz to
// newsz, which need not be page aligned.  Returns new size or 0 on error.
// 为增长进程（的内存）分配<a href="#pte">PTE</a>s和物理内存，从oldsz增长到newsz
// 还不需要页面对齐？为啥?待会解释
// 返回一个 new size/0(error)
uint64
uvmalloc(pagetable_t pagetable, uint64 oldsz, uint64 newsz)
{
  char *mem;
  uint64 a;

  if(newsz < oldsz)
    return oldsz;

  oldsz = PGROUNDUP(oldsz);
  for(a = oldsz; a < newsz; a += PGSIZE){
    mem = kalloc();
    // 如果某次循环申请不到内存，就从现在的(a)Deallocate到(oldsz)，恢复原来的位置
    if(mem == 0){
      uvmdealloc(pagetable, a, oldsz);
      return 0;
    }
    memset(mem, 0, PGSIZE);
    // 申请到了，就初始化，然后映射到pagetable中，给予四个权限，a是va，mem是pa
    // 同样的如果不能正确映射，就取消分配mem内存，并且(a)Deallocate到(oldsz)
    // 其实这里我有个疑问，之前申请的mem怎么办？不能像uvmdealloc一样取消吗？
    if(mappages(pagetable, a, PGSIZE, (uint64)mem, <a href="#pte">PTE</a>_W|<a href="#pte">PTE</a>_X|<a href="#pte">PTE</a>_R|<a href="#pte">PTE</a>_U) != 0){
      kfree(mem);
      uvmdealloc(pagetable, a, oldsz);
      return 0;
    }
  }
  return newsz;
}
```

个人总结：**xv6中是用某个进程的pagetable通过mappages将内存映射到三级页表的某个<a href="#pte">PTE</a>当中，就在`PTE = PA2PTE(pa) | parm | PTE_V`这句代码中体现**

这句话绝对是精华，值得细品，整合了`mappages`、`walk`、`kalloc`等函数，阐明了分配内存和页表之间的关系，`*pte`表示了<a href="#pte">PTE</a>的地址就是这个物理地址（内存的地址）附加上各种标志（<a href="#pte">PTE</a>_xxx）

所以，`uvmalloc`用于为进程分配内存，它可以在进程虚拟地址空间中分配任意大小的内存，而不需要对齐到页边界，在os中，虚拟内存管理是以页为单位进行的，这意味着os可以将进程的虚拟地址空间中的任意内存地址映射到物理内存页中的任意位置。

当进程需要分配内存时，os会分配足够的物理内存页，并将这些页映射到进程的虚拟地址空间中。因此，**对于进程来说，内存分配的单位不是物理内存页，而是虚拟地址空间中的任意内存地址。**

在`uvmalloc`函数中，所需内存大小通过参数传递，该函数会尝试分配足够的物理内存页，并将这些页映射到进程的虚拟地址空间中，由于os可以任意内存地址映射到物理页总的任意位置，因此不需要对齐到页边界。

### `uvmdealloc`函数

这段代码多次提到了`uvmdealloc`函数，下面贴出该函数的代码

```c
// Deallocate(释放) user pages to bring the process size from oldsz to
// newsz.  oldsz and newsz need not be page-aligned, nor does newsz
// need to be less than oldsz.  oldsz can be larger than the actual
// process size.  Returns the new process size.

// 释放用户页，调整进程的内存空间的大小。oldsz和newsz不需要对齐到页边界，newsz也不需要比oldsz小，代码都有处理，oldsz可以大于真正的进程大小
// 返回一个新的进程大小

uint64
uvmdealloc(pagetable_t pagetable, uint64 oldsz, uint64 newsz)
{
  if(newsz >= oldsz)
    return oldsz;
  // 这里规范了newsz和oldsz页的大小，使得uvmunmao得以正确处理映射关系
  if(PGROUNDUP(newsz) < PGROUNDUP(oldsz)){
    int npages = (PGROUNDUP(oldsz) - PGROUNDUP(newsz)) / PGSIZE;
    uvmunmap(pagetable, PGROUNDUP(newsz), npages, 1);
  }

  return newsz;
}
```

解释为什么不需要对齐到页边界：该函数主要是用来释放不再被进程使用的内存，而不是分配新的内存。具体来说，该函数会通过迭代页表，将`oldsz`到`newsz`之间的内存范围内的所有页面释放。这意味着，如果某个页面的一部分在该范围内，那么该页面就需要被释放，即使它的起始地址不是页边界对齐的。

[todo-finish][]这里`uvmdealloc()`还没讲完呢，还有`uvmunmap()`还没追，可能这里可以解答我前面在`uvmalloc()`注释里面留下的一个疑惑

这里贴出`uvmunmap`函数的代码，该函数用于取消页面映射

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679541507774.png"  />

这里提到了一个概念：<a href="#leaf-PTE">叶子页表项</a>

那么在看完该函数之后，我明白了原来`uvmunmap`函数的工作就像`uvmdealloc`一样（相当于撤回操作）

---

**页表使用案例：**

内核在用户地址空间的顶部映射一个带有`trampoline code` 的页面，这样所有的地址空间都可以看到一个单独的物理内存页面（好像前面也有提到，`trampoline`可以用于系统调用，提高os效率）

![在这里插入图片描述](https://img-blog.csdnimg.cn/ed2dd5a8fadf4f21a65ae55635a378d1.png)

下面来介绍xv6的用户内存布局，如上图

栈是一个page，显示的是`exec`创建后的初始内容。

栈的顶部，有命令行参数的字符串，以及指向它们的指针

再往下是允许程序在`main`处启动的值（`main`的`argc`和`argv`），就像`main(argc, argv)`被调用一样。

我们注意到用户内存布局中还存在着保护页（在栈的正下方），用于检测用户栈是否溢出了已分配的堆栈内存。如果用户栈溢出并且进程试图使用栈下方的地址，由于映射无效，硬件将生成页面错误异常。事实上，操作系统可能会在用户栈溢出的时候自动为其分配更多内存。

---

## `sbrk` and `exec`

今天来学3.7 and 3.8（主要是围绕着代码来讲的）

- ### `sbrk`

  `sbrk`是用来增加或缩小进程的内存的一个系统调用，具体实现为函数`growproc`，在代码上可以观察到，发现又有很多不清楚的函数，唉~又是一段深深代码路~开干吧！

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679532358753.png"  />

  那么282:`myproc()`应该是返回一个cpu上运行的进程指针，我们继续追

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679532484302.png"  />

  [todo-finish][]我这里还想研究一下cpu呢，别急，待会就有机会了

  那么直接观察到`push_off()`以及`pop_off()`，可以联想到栈，对吧，啊其实不是，是有关中断的，下面放它们的代码，我的注释先别急着看，先看看下面的代码追踪~

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679538346236.png"  />

  很不幸的是，还有这么多我们不认识的函数出现，没办法，继续追呗~

  - #### `intr_get()`（原子操作）

    <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679533856341.png"  />

    该函数的作用是：**检查当前处理器中断是否启用**

    从`r_sstatus()`中读取状态寄存器的值，储存在64位无符号整数变量x中

    通过`x & (状态寄存器的)SIE`，检查SIE是否为1，如果为1，则表示中断已启用，否则没有。该函数返回一个整数值，为1表示中断已启用，为0表示中断未启用

  - #### `intr_off()`（原子操作）

    <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679533195822.png"  />

    好家伙，直接到了汇编，虽然我看不懂，但是我可以问gpt233

    这段代码是**用来禁用设备中断**的，使用了内联汇编（嵌套函数？）的方式来执行指令。

    这段代码使用了`r_sstatus()`和`w_sstatus()`两个函数（这个就别深究了，跟那个讨厌的`w_satp()`一样，都看不明白-_-），分别是用来读取和写入状态寄存器（特殊的寄存器，用于保存处理器cpu的各种状态信息，包括中断使能位、当前特权级、异常处理模式等）的。

    通过将状态寄存器的SIE字段（上面提到的中断使能位）设置为0来禁用设备中断。具体来说，代码通过调用`r_sstatus()`来读取当前状态寄存器的值，然后通过& ~位运算来将SIE字段设置为0。最后，代码通过调用`w_sstatus()`将修改后的状态寄存器的值写回到寄存器中。

  - #### `mycpu()`

    <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679534323684.png"  />

    首先调用`cpuid()`函数获取当前cpu的id号

    <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679534400792.png"  />

    调用`cpuid()`函数时，必须先禁用中断，以防止进程被移动到不同的cpu上，从而产生竞态条件问题，这是因为当进程被移动到不同的cpu上时，他的cpuid也会发生变化，因此在中断被禁止的情况下调用`cpuid()`可以保证返回的是当前cpu的正确id。

    

    `mycpu()`接下来通过全局数组cpus找到第id个cpu，并返回该cpu结构体的指针

    需要注意的是，调用`mycpu()`函数时，必须先禁用中断，否则可能会出现<a href="#race-condition">竞态条件</a>（race condition）的问题

  - #### `struct cpu`结构体

    <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679536341487.png"  />

    - `proc`：在该cpu上运行的进程，多个、单个、或者没有
    - `context`：（与上面的<a href="#proc">proc</a>相配合）保存当前进程的一些信息
    - `noff`：用于记录当前禁用中断的嵌套深度，每执行一次`push_off()`函数，noff就增加1，表示中断禁用的嵌套深度增加了1，当执行`pop_off()`函数时，noff变量的值会减少1，表明中断禁用的嵌套深度减少了1。
    - `intena`：用于记录在调用`push_off()`函数之前，中断是否被禁用。如果中断被禁用，那么intena的值为0，否则为1。在执行`pop_off()`时，如果intena的值为1，则会重新开启中断，否则不开启（这段话表明了，只有在禁用(开启)中断的条件下，才可以禁用(开启)更高优先级的中断）。确保中断的嵌套禁用和开启能够正确地工作，保证系统的稳定性和安全性。

  - #### `intr_on()`（原子操作）

    <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679538054871.png"  />

    与`intr_off()`相反，该函数用于开启设备中断，和上面一样，就不细说了。

  现在又回到了`growproc`函数，不过也没啥好讲的了，就是看增长还是缩小了n bytes

  xv6使用进程的页表，不仅告诉硬件是如何映射到用户虚拟地址的，而且还提供了物理内存页分配给该进程的唯一记录。这就是在释放用户内存（在`uvmunmap`中）时需要检查用户页表的原因。

- ### `exec`：这是一个很重要的函数

  在(kernel/exec:13)找到了exec函数，我超...怎么这么长啊...

  先贴出代码哈，可能会有点多

  ```c
  int
  exec(char *path, char **argv)
  {
    char *s, *last;
    int i, off;
    uint64 argc, sz = 0, sp, ustack[MAXARG], stackbase;
    struct elfhdr elf;
    struct inode *ip;
    struct proghdr ph;
    pagetable_t pagetable = 0, oldpagetable;
    struct proc *p = myproc();
  
    begin_op();
  
    if((ip = namei(path)) == 0){
      end_op();
      return -1;
    }
    ilock(ip);
  
    // Check ELF header
    if(readi(ip, 0, (uint64)&elf, 0, sizeof(elf)) != sizeof(elf))
      goto bad;
    if(elf.magic != ELF_MAGIC)
      goto bad;
  
    if((pagetable = proc_pagetable(p)) == 0)
      goto bad;
  
    // Load program into memory.
    for(i=0, off=elf.phoff; i<elf.phnum; i++, off+=sizeof(ph)){
      if(readi(ip, 0, (uint64)&ph, off, sizeof(ph)) != sizeof(ph))
        goto bad;
      if(ph.type != ELF_PROG_LOAD)
        continue;
      if(ph.memsz < ph.filesz)
        goto bad;
      if(ph.vaddr + ph.memsz < ph.vaddr)
        goto bad;
      uint64 sz1;
      if((sz1 = uvmalloc(pagetable, sz, ph.vaddr + ph.memsz)) == 0)
        goto bad;
      sz = sz1;
      if((ph.vaddr % PGSIZE) != 0)
        goto bad;
      if(loadseg(pagetable, ph.vaddr, ip, ph.off, ph.filesz) < 0)
        goto bad;
    }
    iunlockput(ip);
    end_op();
    ip = 0;
  
    p = myproc();
    uint64 oldsz = p->sz;
  
    // Allocate two pages at the next page boundary.
    // Use the second as the user stack.
    sz = PGROUNDUP(sz);
    uint64 sz1;
    if((sz1 = uvmalloc(pagetable, sz, sz + 2*PGSIZE)) == 0)
      goto bad;
    sz = sz1;
    uvmclear(pagetable, sz-2*PGSIZE);
    sp = sz;
    stackbase = sp - PGSIZE;
  
    // Push argument strings, prepare rest of stack in ustack.
    for(argc = 0; argv[argc]; argc++) {
      if(argc >= MAXARG)
        goto bad;
      sp -= strlen(argv[argc]) + 1;
      sp -= sp % 16; // riscv sp must be 16-byte aligned
      if(sp < stackbase)
        goto bad;
      if(copyout(pagetable, sp, argv[argc], strlen(argv[argc]) + 1) < 0)
        goto bad;
      ustack[argc] = sp;
    }
    ustack[argc] = 0;
  
    // push the array of argv[] pointers.
    sp -= (argc+1) * sizeof(uint64);
    sp -= sp % 16;
    if(sp < stackbase)
      goto bad;
    if(copyout(pagetable, sp, (char *)ustack, (argc+1)*sizeof(uint64)) < 0)
      goto bad;
  
    // arguments to user main(argc, argv)
    // argc is returned via the system call return
    // value, which goes in a0.
    p->trapframe->a1 = sp;
  
    // Save program name for debugging.
    for(last=s=path; *s; s++)
      if(*s == '/')
        last = s+1;
    safestrcpy(p->name, last, sizeof(p->name));
      
    // Commit to the user image.
    oldpagetable = p->pagetable;
    p->pagetable = pagetable;
    p->sz = sz;
    p->trapframe->epc = elf.entry;  // initial program counter = main
    p->trapframe->sp = sp; // initial stack pointer
    proc_freepagetable(oldpagetable, oldsz);
  
    if (p->pid == 1) vmprint(p->pagetable);
  
    return argc; // this ends up in a0, the first argument to main(argc, argv)
  
   bad:
    if(pagetable)
      proc_freepagetable(pagetable, sz);
    if(ip){
      iunlockput(ip);
      end_op();
    }
    return -1;
  }
  ```

  那这次我就不全读了，因为书上有提示后面hh，文件系统的部分就留到第八章吧！

  `exec`使用存储在文件系统中的文件，初始化地址空间的用户空间那部分，可以观察到，`exec`函数使用`namei`(kernel/exec.c:26)打开二进制path（第八章有解释！），然后，它读取ELF头...后面看书。

  #### `proc_pagetable`函数

  `exec`使用`proc_pagetable`(kernel/exec.c:38)分配一个没有用户映射的新页表

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679553539253.png" style="zoom:80%;" />

  注释有大部分的理解，看到了`uvmcreate()`，继续追

  ##### `uvmcreate`函数

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679553613234.png"  />

  `uvmcreate()`函数是用于创建用户页表的，调用了`kalloc()`，让页表拥有一段内存空间，后面就是分配的处理

  ##### `uvmfree`函数

  然后200行又看到了`uvmfree()`，很显然这是一个与`uvmcreate()`相反的操作，抵消了前面创建的页表，一切都回归于平静~（啥也没干）

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679553899575.jpg"  />

  从代码上看来确实是这样，如果有该页表已经分配了大小`sz`的内存，那么会先调用`uvmunmap`释放内存，然后再调用`freewalk`销毁页表

  ###### `freewalk`函数

  `freewalk`字面意思上看来，就是跟`walk`相反的操作嘛，而且还是递归操作，那么放上代码

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679555626098.png"  />

  那么具体解释都在注释里了。

  好耶！`proc_pagetable()`就分析完毕啦！

  ---

  `exec`使用`uvmalloc`(kernel/exec.c:52)为每个ELF段分配内存，并使用`loadseg`(kernel/exec.c:10)将每个段加载进内存。`loadseg`使用`walkaddr`查找已分配内存的物理地址，并在该物理地址处写入ELF段的每个page，并使用`readi`从文件中读取

  #### `loadseg`函数

  `loadseg`函数：在虚拟地址va上加载程序段进页表中，va必须页对齐，va~va+sz必须已经被映射，成功返回0，失败返回-1

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679556214445.png"  />

  #### `walkaddr`函数

  `walkaddr`<span name="walkaddr">函数</span>：同一个进程当中，通过虚拟地址返回物理地址

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679556066269.png" style="zoom:80%;" />

  <a href="#init">/init</a>是一个程序段头，它是通过`exec`创建的第一个用户程序。
  
  ![](https://img-blog.csdnimg.cn/0e111cbcadd0498ea109d5ddb96c41ed.png)
  
  程序段头的`filesz`可能小于`memsz`。
  
  ③`exec`分配并初始化用户栈。它只分配一个栈页
  
  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679557879192.png" style="zoom:80%;" />
  
  `exec`一次将参数中的字符串复制到栈顶，并将指向它们的指针存在`ustack`中。并且在`ustack`的末尾放置一个空指针。`ustack`的前三个条目分别是：虚假返回程序计数器、`argc`和`argv`指针。
  
  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679558054481.png" style="zoom:80%;" />
  
  [todo][]有机会一定要详解`exec`（学到第8章）

自此，第三章Page Table全部结束！！！



---

<div name="race-condition">
    &emsp;&emsp;竞态条件（Race Condition）是指当多个进程或线程同时访问共享资源时，由于各自的执行顺序不确定，导致最终的结果依赖于执行顺序的随机性和不可预测性的一种情况。
    <br>
    &emsp;&emsp;竞态条件会导致程序的行为变得不确定，可能会导致程序崩溃或产生错误的结果。例如，在多个线程同时访问某个共享变量时，如果其中一个线程修改了该变量的值，而其他线程同时也在读取或修改该变量，那么最终的结果可能是不确定的，可能会导致程序出现问题。
    <br>
    &emsp;&emsp;为了避免竞态条件，通常需要使用同步机制来确保多个进程或线程之间的互斥访问。这包括使用锁、信号量、互斥量等机制来保证共享资源的原子性访问，以避免多个进程或线程同时访问共享资源而导致的竞态条件问题。
</div>

------

<div name="leaf-PTE">
    &emsp;&emsp;可以通过PTE中的一些其他标志位来推断该PTE是否为叶子页表项。例如，PTE中的"Present"标志位指示该页表项指向的物理页是否被映射到了虚拟地址空间中。如果这个标志位被设置为 1，那么该PTE指向一个有效的物理页，这个PTE就不是叶子页表项。相反，如果这个标志位被设置为 0，那么该PTE指向的物理页没有映射到虚拟地址空间中，这个PTE就是叶子页表项。
	<br>
    &emsp;&emsp;另外，PTE中的"Page Size"标志位也可以用于判断该PTE是否为叶子页表项。如果这个标志位被设置为 1，那么该PTE指向的是一个大页（通常为4KB的倍数），这个PTE就不是叶子页表项。相反，如果这个标志位被设置为 0，那么该PTE指向的是一个小页（通常为4KB），这个PTE就是叶子页表项。
</div>



------

<div name="init">
    &emsp;&emsp;/init通常是一个特殊的进程，它是系统引导过程中的第一个用户空间进程。在系统启动时，内核会执行/init程序，/init程序会负责初始化系统并启动其他用户进程
</div>

------

<div name="memory-mapped-control-registers">
    内存映射控制寄存器（memory-mapped control registers）：
<br>
    &emsp;&emsp;一种硬件结构，可以访问内存中访问控制硬件设备的寄存器。
<br>
    &emsp;&emsp;通常用于与外围设备进行通信，如磁盘驱动器、网络接口卡等交互。
<br>
	&emsp;&emsp;用于控制硬件的各种操作和功能。
<br>
	&emsp;&emsp;软件可以像内存一样访问这些寄存器，通过对它们的读写来与硬件设备进行交互和控制。
<br>
	&emsp;&emsp;这种方式比直接访问硬件设备的寄存器更加灵活和方便。
</div>


---

<div name="satp-register">
    satp：
<br>
    &emsp;&emsp;satp寄存器是RISC-V架构中用于设置地址转换的寄存器
<br>
	&emsp;&emsp;该寄存器包含了当前正在运行的进程或操作系统所使用的页表信息，它将虚拟地址映射到物理地址，并提供了安全保护的功能。
</div>


---

<div name="<a href="#pte">PTE</a>">
    PTE（page table entries）：页表条目
	<br>
    &emsp;&emsp;每个进程都有一个页表，页表中包含了每个虚拟页面对应的物理页面的信息，其中一个条目就是一个PTE
	<br>
    &emsp;&emsp;PTE一般包含以下信息：
    <br>
    &emsp;&emsp;①物理页面的地址：用于指示该虚拟页面所对应的物理页面的地址。
    <br>
    &emsp;&emsp;②权限信息：用于指示该虚拟页面的读写权限、是否可执行等。
    <br>
    &emsp;&emsp;③标志位：用于指示该虚拟页面是否被映射到物理页面，是否被修改过。
</div>


----

<div name="ppn">
    PPN（physical page number）：物理页面号，存储物理地址
<br>
	&emsp;&emsp;当系统需要将虚拟地址转换为物理地址时，会根据页面映射关系找到对应的PTE，然后从PTE中提取出PPN，再将虚拟地址的页偏移量和PPN组合成物理地址
</div>


---

<div name="mmu">
    MMU（memory management unit）：
    <br>
    &emsp;&emsp;作用是实现虚拟内存到物理内存的映射，提供了一种从逻辑上连续的虚拟地址空间到物理地址空间的映射机制。
    <br>
	&emsp;&emsp;MMU通过在内存中维护一个页表来管理虚拟地址空间和物理地址空间之间的映射关系。
    <br>
    <br>
    &emsp;&emsp;Q：那么这里其实有一个关于MMU和satp之间是怎么运作的问题
	<br>
	&emsp;&emsp;A：实际上正如上文MMU的解释的那样，存储着虚拟地址和物理地址的映射关系，它负责将虚>拟地址转换成物理地址。
	<br>
  	&emsp;&emsp;那么satp是用来指定当前正在使用的页表，而且最多只能存一个页表的物理地址和模式（S模式 和U模式），在RISC-V架构中，satp寄存器的最高位（63位）为模式位，代表当前页表的模式，接下来的44位为页表物理地址（48位物理地址中的高44位），可能吧~
</div>


---

<div name="kernel-data-and-text">
    &emsp;&emsp;Kernel data和Kernel text是操作系统内核的两个主要部分，它们分别用于不同的目的。
    <br>
    &emsp;&emsp;Kernel text是操作系统内核的代码部分，包括处理器指令、中断处理程序、设备驱动程序等。它是操作系统内核的核心，负责管理系统资源、调度进程、提供系统服务等。Kernel text通常是只读的，因为它需要始终保持不变，以确保操作系统的稳定性和可靠性。
    <br>
    &emsp;&emsp;Kernel data是操作系统内核的数据部分，包括内核数据结构、进程控制块、中断向量表等。它用于存储内核运行时所需的数据，例如正在运行的进程的状态、系统中各种资源的使用情况等。Kernel data通常是可读写的，因为它需要随着系统状态的变化而动态更新。
    <br>
    &emsp;&emsp;Kernel data是操作系统内核的数据部分，包括内核数据结构、进程控制块、中断向量表等。它用于存储内核运行时所需的数据，例如正在运行的进程的状态、系统中各种资源的使用情况等。Kernel data通常是可读写的，因为它需要随着系统状态的变化而动态更新。
</div>

---







