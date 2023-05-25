# 分析`userinit`代码

在内核加载完毕之后创建的第一个进程就是这个：

```cpp
// Set up first user process.
void
userinit(void)
{
    struct proc *p;

    p = allocproc();			//xv6的第一个进程，其pid = 1
    initproc = p;

    // allocate one user page and copy init's instructions
    // and data into it.
    uvminit(p->pagetable, initcode, sizeof(initcode));
    p->sz = PGSIZE;

    // prepare for the very first "return" from kernel to user.
    p->trapframe->epc = 0;      // user program counter
    p->trapframe->sp = PGSIZE;  // user stack pointer

    safestrcpy(p->name, "initcode", sizeof(p->name));
    p->cwd = namei("/");

    p->state = RUNNABLE;

    release(&p->lock);
}
```

## alloproc函数

从第7行`alloproc()`开始：

- 图1

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678707798281.png"  />

可以发现这个函数是为了给状态为`UNUSED`（表示该进程表项PTE当前未被使用）的进程使用的，遍历每一个进程，在xv6中，`NPROC`宏定义为64，如果找到状态为`UNUSED`的，就给该进程分配一个表单和初始化进程控制块等等。

- 图2

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678708440209.png"  />

#### [todo][]（查询trapframe）我找到了！

- (kernel/proc.h:44)，不过现在不是研究这个的时候哈

## uvminit函数

随后来到12行，跟踪`uvminit`，到达`vm.c`

```cpp
// Load the user initcode into address 0 of pagetable,
// for the very first process.
// sz must be less than a page.
void
uvminit(pagetable_t pagetable, uchar *src, uint sz)
{
    char *mem;

    if(sz >= PGSIZE)
        panic("inituvm: more than a page");
    mem = kalloc();
    memset(mem, 0, PGSIZE);
    mappages(pagetable, 0, PGSIZE, (uint64)mem, PTE_W|PTE_R|PTE_X|PTE_U);
    memmove(mem, src, sz);
}
```

看完注释之后结合代码大概明白了，该函数是为第一个进程加载一个`initcode`（初始化代码），然后创建一个页表，并且将用户程序的初始化代码加载到页表的地址0处，由于该初始化代码的大小通常比较小，因此注释中还指出了一个限制条件：sz必须小于一个页的大小。这是因为操作系统在加载用户程序时会使用页粒度（page granularity），而一个页的大小通常为4KB或者更大。如果初始化代码的大小超过一个页的大小，那么操作系统需要将其分割成多个页来加载，这会增加操作系统的复杂性，因此通常需要遵守这个限制条件。

### kalloc函数

阅读源码，发现出现了`kalloc()`，于是继续深入，翻看源码

- 图3

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679396744443.png"  />

通过注释（这里68行注释应该是头插法）不难看出，这个函数是分配页表的，但是本着深究的精神，我们来详细看看

```cpp
struct run {
    struct run *next;
};

struct {
    struct spinlock lock;
    struct run *freelist;
} kmem;
```

在xv6的物理内存分配器中，`struct run`结构体是用来维护一条可用的内存块链表（注意这个`kmem.freslist`的大小是固定的。在xv6中，物理内存管理器不支持动态增加或减少物理内存）。

`kmem`结构体

综上可以发现，原来`kalloc()`74-76行是**为了找到一个可用的内存块并返回！**

[todo-finished][]在这里我又有疑问了`(void*)`是什么意义，为什么这里的`r`就一定是PGSIZE呢？为什么84行又要转化为`(char*)`呢？

- A：`(void*)`是一个未知类型的指针，可以指向任何类型的数据，可以显示或隐式地转换为其他类型的指针，以便程序中访问所分配的内存块！

PS：可能这里会有人对`panic`有疑问，这个函数就是用来报错的，涉及到中断处理程序，目前先搁置一旁。

### mappages函数

继续阅读`uvminit`，查看`mappages`函数：

```cpp
// Create PTEs for virtual addresses starting at va that refer to
// physical addresses starting at pa. va and size might not
// be page-aligned. Returns 0 on success, -1 if walk() couldn't
// allocate a needed page-table page.
int
mappages(pagetable_t pagetable, uint64 va, uint64 size, uint64 pa, int perm)
{
    uint64 a, last;
    pte_t *pte;

    if(size == 0)
        panic("mappages: size");

    a = PGROUNDDOWN(va);
    // 这里a得到的是PPN+12位的0
    last = PGROUNDDOWN(va + size - 1);
    // 这里last(原来的va再加上12位的1,然后再PGROUNDDOWN运算)
    // last 表示本次映射的最后一个虚拟地址
    // 如果va一开始的偏移量就是12个0, 那么last就等于a !!!
    // 但是如果一开始va不是规律的(va的偏移量不是12个0),那么a != last,last = a + PGSIZE
    for(;;){
        if((pte = walk(pagetable, a, 1)) == 0)
            return -1;
        if(*pte & PTE_V)
            panic("mappages: remap");
        // perm就是像PTE_()一样的东西，并且可能不止一个，比如说PTE_R|PTE_W
        *pte = PA2PTE(pa) | perm | PTE_V;
        if(a == last)
            break;
        a += PGSIZE;
        pa += PGSIZE;
    }
    return 0;
}
```

注释的意思就是创建一些PTE，使得从虚拟地址`va`开始的一段内存可以映射到从物理地址`pa`开始的一段内存。同时强调了虚拟地址`va`和内存大小`size`不一定是按页对齐的（实际应用中，虚拟地址和内存大小往往不会正好是页大小的倍数），最后就是通过`walk()`函数来决定是否能分配，能返回0（创建成功！），否则返回-1。

#### 宏定义PGROUNDDOWN

再来看看源码，`PGROUNDDOWN`是什么鬼？！于是我直接跳转，发现竟然是`riscv.h`里面的

```cpp
#define PGROUNDDOWN(a) (((a)) & ~(PGSIZE-1))
```

大眼瞪小眼...通过计算器发现PGSIZE-1 = 4095 =$$(111111111111)_2$$，那么取反之后全部为0，原来如此！原来这个宏定义就是为了清除给定地址的低12位，从而得到物理地址的具体位置，为什么我会知道呢？这就不得不扯到xv6手册的一张图

- 图4

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678711780769.png"  />

如图4，就可以解释了。

在这里出现了`walk()`，继续追

#### walk函数

```cpp
// Return the address of the PTE in page table pagetable
// that corresponds to virtual address va.  If alloc!=0,
// create any required page-table pages.
//
// The risc-v Sv39 scheme has three levels of page-table
// pages. A page-table page contains 512 64-bit PTEs.
// A 64-bit virtual address is split into five fields:
//   39..63 -- must be zero.
//   30..38 -- 9 bits of level-2 index.
//   21..29 -- 9 bits of level-1 index.
//   12..20 -- 9 bits of level-0 index.
//    0..11 -- 12 bits of byte offset within the page.
pte_t *
walk(pagetable_t pagetable, uint64 va, int alloc)
{
    if(va >= MAXVA)
        panic("walk");

    for(int level = 2; level > 0; level --) {
        // 求得该级页表的PTE
        pte_t *pte = &pagetable[PX(level, va)];
        if(*pte & PTE_V) {
            pagetable = (pagetable_t)PTE2PA(*pte);
        } else {
            if(!alloc || (pagetable = (pde_t*)kalloc()) == 0)
                return 0;
            memset(pagetable, 0, PGSIZE);
            // 给一个valid的标识
            *pte = PA2PTE(pagetable) | PTE_V;
        }
    }
    return &pagetable[PX(0, va)];
}
```

这注释很容易可以看出`walk()`是返回虚拟地址对应的三级页表的PTE，如果参数alloc（分配）不为0，则给该虚拟地址进行分配

##### 宏定义PX(level, va)

19行循环从一级页表开始，21行看不懂没关系，继续追代码

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678967703386.png"  />

可以发现`PX(level, va)`是为了得到某一级页表的某个PTE！于是我们返回

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678967838469.png"  />

`pagetable_t`其实就是某一级页表，结合起来`&pagetable[PX(level, va)]`就是返回该级页表的一个PTE

进入22行判断语句，如果PTE存在且拥有PTE_V标志，就将该PTE的地址转化为物理地址，这里要稍稍解释一下，如果你得到了一个最终的物理地址，那么真正物理页的数据是存在在地址高位中的。

```cpp
// shift a physical address to the right place for a PTE.
#define PA2PTE(pa) ((((uint64)pa) >> 12) << 10)

#define PTE2PA(pte) (((pte) >> 10) << 12)
```

`pa >> 12`代表 右移去除PGSIZE大小的内容(因为PTE不需要offset) , 左移10bits 是给PTE flags 留下的空间.  而pte2PA的话 就是先去除10bits(代表PTE flags), 再左移12bits (PGSIZE的大小) 用来索引这个Page的具体内容

好了，那么再看25行，如果`alloc!=0`并且可以分配，那么就继续，清空page，给定PTE_V标识。

最后`walk`返回最后一级页表的页表项！over



继续来看mappages函数，结合注释17~20，不难发现，a是分配页表的起点，last是分配页表的终点，一般来说page都是对齐的所以va是规律的（offset全是0），那么分配完毕之后呢，就会退出，然后返回0（表示成功！）

### memmove函数

论解释还得是chatGPT啊！

`memmove()`函数是C语言标准库中的一个内存拷贝函数，用于将一段内存区域的数据复制到另一个内存区域。和`memcpy()`函数类似，`memmove()`函数也可以进行内存复制，但是相对于`memcpy()`函数，`memmove()`函数在某些情况下更加灵活和安全。下面是`memmove()`函数的函数原型：

```cpp
void *memmove(void *dest, const void *src, size_t n);
```

其中，`dest`是目标内存区域的起始地址，`src`是源内存区域的起始地址，`n`是需要复制的字节数。`memmove()`函数的作用就是将源内存区域中的`n`个字节复制到目标内存区域中，如果源内存区域和目标内存区域存在重叠，那么`memmove()`函数仍然可以保证正确的复制结果。

`memmove()`函数的实现通常是通过字节复制来实现的，即将源内存区域中的每个字节逐个复制到目标内存区域中。但是由于源内存区域和目标内存区域可能存在重叠，因此需要特别处理这种情况。

在实现`memmove()`函数时，通常采用以下两种策略来处理源内存区域和目标内存区域的重叠情况：

1. 如果源内存区域的起始地址小于目标内存区域的起始地址，那么从前往后逐个复制字节。这种情况下，如果源内存区域和目标内存区域存在重叠，那么复制的过程中不会覆盖源内存区域中尚未被复制的数据，从而保证了正确性。
   
2. 如果源内存区域的起始地址大于目标内存区域的起始地址，那么从后往前逐个复制字节。这种情况下，如果源内存区域和目标内存区域存在重叠，那么复制的过程中也不会覆盖源内存区域中尚未被复制的数据，从而保证了正确性。
   

需要注意的是，由于`memmove()`函数涉及到内存的操作，因此在使用该函数时需要保证源内存区域和目标内存区域的地址是合法的，并且具有足够的访问权限。

- 灵魂提问：为第一个进程加载`initcode`（初始化代码）做什么？

- A：`memmove()`函数将`initcode`的内容从内核空间的`initcode`位置复制到新地址空间的0地址位置，也就是用户进程的起始地址。这样，当用户进程开始执行时，它会从0地址处开始执行初始化代码。

  由于用户进程的地址空间是新创建的，而且内核空间和用户空间是相互独立的，因此需要通过`memmove()`函数将`initcode`从内核空间复制到用户进程的地址空间中，才能让用户进程执行初始化代码。如果直接跳转到内核空间的`initcode`位置，用户进程是无法访问该位置的，因为内核空间和用户空间是相互独立的。

  这样就可以让用户进程从0地址处开始执行初始化代码，进而启动用户进程的执行。

  `initcode`的作用：其实就是一段汇编语言

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679233103067.png"  />
  
  但是我在proc.c当中又发现了一个initcode数组，目前还不知道是做什么的
  
  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679296431415.png"  />

# 现在终于又能分析userinit的代码了
