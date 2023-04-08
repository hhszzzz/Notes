# Operating system interfaces

当一个进程需要调用内核服务，它会调用system call（操作系统的某个接口），然后system call会进入内核，内核执行服务并返回。于是进程实现了在user space和kernel space中轮换

![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/214ce9452654477f8e5a5f994f565c63.png)

`xv6 shell`本质上是`Unix Bourne shell`的简单实现，下面是<span name="shell">shell</span>的代码

![image-20230404202634226](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230404202634226.png)

在(user/sh.c:145)处执行，shell的主体结构很简单。

> 1. 主循环使用getcmd从用户那里读取一行输入
>
> 2. 然后它调用fork创建一个shell子进程
>
> 3. 父进程调用wait，子进程执行用户输入的命令。

## Processes and memory

xv6为各进程提供了分时特性：xv6不断切换到可用的CPU去执行等待队列中的进程，下面给出代码具体实现

### scheduler函数

```c
// Per-CPU process scheduler.
// Each CPU calls scheduler() after setting itself up.
// Scheduler never returns. It loops, doing:
//  - choose a process to run.
//  - swtch to start running that process.
//  - eventually that process transfers control
//    via swtch back to the scheduler.
void
scheduler(void)
{
  struct proc *p;
  struct cpu *c = mycpu();
  
  c->proc = 0;
  for(;;){
    // Avoid deadlock by ensuring that devices can interrupt.
    intr_on();

    for(p = proc; p < &proc[NPROC]; p++) {
      acquire(&p->lock);
      if(p->state == RUNNABLE) {
        // Switch to chosen process.  It is the process's job
        // to release its lock and then reacquire it
        // before jumping back to us.
        p->state = RUNNING;
        c->proc = p;
        swtch(&c->context, &p->context);

        // Process is done running for now.
        // It should have changed its p->state before coming back.
        c->proc = 0;
      }
      release(&p->lock);
    }
  }
}
```

事实上，在这段代码中，并没有出现分时的操作，也没有看到多少秒会进行一次切片操作，具体代码在第四章trap会有看到。具体来说，`trap()`函数是中断处理程序的入口点，它会根据中断类型（包括时钟中断）来调用相应的处理函数。`lapictr1()`函数是时钟中断的处理函数，它会调用`scheduler()`函数来进行进程调度。在这里不作过多描述

当一个进程没有被执行时，xv6会保存它的CPU寄存器，并在下一次执行该进程时恢复它们。内核使用PID(process identifier)标识各个进程。

浅谈fork函数，下面给出一个<span name="forkexample">例子</span>

```c
int pid = fork();
if(pid > 0) {
	printf("parent: child=%d\n", pid);
	pid = wait((int *) 0);
	printf("child %d is done\n", pid);
} else if(pid == 0) {
	printf("child: exiting\n");
	exit(0);
} else {
	printf("fork error\n");
}
```

1. `exit`系统调用会使调用它的进程停止运行，并释放相应的资源(如内存和打开的文件)。exit接受一个整数作为参数，通常0表示成功，1表示失败。
2. `wait`系统调用返回当前进程中已退出(或已终止)的子进程的PID。[了解exit和wait函数][]

### exit函数

```c
// Exit the current process.  Does not return.
// An exited process remains in the zombie state
// until its parent calls wait().
void
exit(int status)
{
  struct proc *p = myproc(); // Note: 获取cpu正在运行的进程

  if(p == initproc) // Note: initproc is the first user process
    panic("init exiting");

  // Close all open files.
  for(int fd = 0; fd < NOFILE; fd++){ 
    // Note: NOFILE is max number of open files
    // Note: ofile is an array of pointers to files
    if(p->ofile[fd]){
      struct file *f = p->ofile[fd];
      fileclose(f);
      p->ofile[fd] = 0;
    }
  }

  // Note: release all the pages
  begin_op();
  iput(p->cwd);
  end_op();
  p->cwd = 0;

  // 
  acquire(&wait_lock);

  // Give any children to init.
  reparent(p);

  // Parent might be sleeping in wait().
  wakeup(p->parent);
  
  acquire(&p->lock);

  p->xstate = status;
  p->state = ZOMBIE;

  release(&wait_lock);

  // Jump into the scheduler, never to return.
  sched();
  panic("zombie exit");
}
```

解释都在注释中了，下面只用来继续深究其他函数

#### reparent函数

```c
// Pass p's abandoned children to init.
// Caller must hold wait_lock.
// Note: reparent() is called to change the parent of the child process to initproc
void
reparent(struct proc *p)
{
  struct proc *pp;

  for(pp = proc; pp < &proc[NPROC]; pp++){
    if(pp->parent == p){
      pp->parent = initproc;
      wakeup(initproc);
    }
  }
}
```

> `reparent`用于将子进程的父进程更新为`initproc`
>
> 在exit函数中，由于进程p即将要退出了，所以将进程p的所有子进程都reparent

#### wakeup函数

```c
// Note: update the process's state to runnable
// Wake up all processes sleeping on chan.
// Must be called without any p->lock.
void
wakeup(void *chan)
{
  struct proc *p;

  for(p = proc; p < &proc[NPROC]; p++) {
    if(p != myproc()){
      acquire(&p->lock);
      // 判断进程的等待通道是否为指定的chan
      // 是，就说明该进程正在等待chan所代表的事件的发生，需要被唤醒
      // 否，说明该进程不需要被唤醒
      if(p->state == SLEEPING && p->chan == chan) {
        p->state = RUNNABLE;
      }
      release(&p->lock);
    }
  }
}
```

> 每个进程都有一个等待通道（wait channel），用于等待某个事件的发生

那么，回到正题，`wakeup(p->parent)`(exit:36)是用来做什么的呢？

结合注释，这段code是用来**唤醒**可能正在等待当前进程结束的**父进程**，让父进程继续执行，回收当前子进程的资源。当父进程接收到子进程结束的信号后，需要通过调用wait()系统调用来回收子进程的资源，并将子进程的状态从`ZOMBIE`改为`UNUSED`。

> Q：可能这里有些人会有疑惑，为什么wakeup在设置子进程状态之前呢？好问题！
>
> 在父进程调用wait函数时，wait函数会先获取`wait_lock`，再看(exit:43)，现在明白了，从wakeup到release这一系列的操作对于wait来说可以看成是一个整体，所以代码顺序可以交换！
>
> ![image-20230405104124919](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230405104124919.png)

> Q：先前我们介绍了`scheduler`函数，现在又出现了`sched`函数，那么两者之间到底有什么区别呢？
>
> 下面贴出`sched`函数的代码
>
> ![image-20230405111625886](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230405111625886.png)
>
> 概括地说，`sched()`函数负责将当前执行的进程（或线程）切换到另一个准备好运行的进程。这个函数只能在调用线程持有当前进程的锁并已经改变了当前进程的状态时调用。
>
> 还解释了保存和恢`intena`变量（第三章有讲到，用于判断中断是否被禁用，0-禁用，1-没有）的原因，`intena`代表内核线程的中断使能状态。这是必要的，因为`intena`是与内核线程相关的，而不是与当前CPU相关。（因为在多核系统中，每个CPU都可能在同时运行不同的内核线程，因此在保存和恢复`intena`变量时，需要考虑当前内核线程的中断使能状态，而不是当前CPU的中断使能状态。）
>
> 注释还指出，`proc->intena`和`proc->noff`更适合用作变量，但在某些情况下，锁被持有但没有进程的情况下，这将破坏代码。（因为没有进程时proc所指向为null）
>
> 好，现在来回答刚刚的问题，两者有什么区别
>
> - **`sched()`函数是用于实现进程切换的函数。**
>
> - **调度器（`scheduler`）函数是用于决策下一个要运行的进程的函数。**
>
> 在`exit()`函数中为什么调用`sched()`函数呢？因为`exit()`函数只需要切换进程并释放资源，而不需要决定下一个要运行的进程。而`scheduler`函数会在系统的其他部分被调用，以决定下一个要运行的进程。

### wait函数

```c
// Wait for a child process to exit and return its pid.
// Return -1 if this process has no children.
int
wait(uint64 addr)
{
  struct proc *np;
  int havekids, pid;
  struct proc *p = myproc();

  acquire(&wait_lock);

  for(;;){
    // Scan through table looking for exited children.
    havekids = 0;
    for(np = proc; np < &proc[NPROC]; np++){
      if(np->parent == p){
        // make sure the child isn't still in exit() or swtch().
        acquire(&np->lock);

        havekids = 1;
        if(np->state == ZOMBIE){
          // Found one.
          pid = np->pid;
          // copy the exit status to the address passed as argument
          // addr receives the exit status of the child process
          if(addr != 0 && copyout(p->pagetable, addr, (char *)&np->xstate,
                                  sizeof(np->xstate)) < 0) {
            release(&np->lock);
            release(&wait_lock);
            return -1;
          }
          freeproc(np);
          release(&np->lock);
          release(&wait_lock);
          return pid;
        }
        release(&np->lock);
      }
    }

    // No point waiting if we don't have any children.
    if(!havekids || p->killed){
      release(&wait_lock);
      return -1;
    }
    
    // Wait for a child to exit.
    sleep(p, &wait_lock);  //DOC: wait-sleep
    // if the child exits, the parent process is woken up
  }
}
```

> 大部分解释都写在注释上了，`copyout`在第3章有讲到，经过copyout操作之后，退出的进程状态的地址已经传递到addr上了，之后可以通过访问addr来查询已退出的子进程的状态。
>
> wait接收一个参数，子进程在调用exit时会将exit的参数传递到父进程的wait的参数（这里的参数应该是指`xstate`）。
>
> 如果子进程没有退出，wait会一直等待直到有一个子进程退出（即在最后出现的sleep函数）
>
> 如果没有子进程，则会立即返回-1。
>
> 如果父进程不关心子进程的退出状态，则可以将`(int*) 0`作为wait的参数。

在<a href="#forkexample">上述例子</a>中，下面的两行输出可能以任意顺序被打印，具体取决于父进程和子进程谁先调用printf。

```c
parent: child=1234
child: exiting
```

子进程退出后，父进程的wait返回，于是父进程会打印

```c
parent: child 1234 is done
```

**注意：** 虽然最初子进程与父进程具有相同的内存内容，但是父子进程使用不同的内存空间和寄存器存储，修改一个进程中的变量不会影响另一个进程。例如，当wait的返回值被存储到父进程的pid中时，它不会改变子进程中的pid变量。子进程中pid的值仍然为0。

### exec函数

`exec`和`fork`很像，但是`exec`是使用一个新的内存映像（从文件系统储存的文件中加载）**替换**当前调用`exec`的进程的内存，**注意是替换！**

两个参数：可执行文件的文件名、字符串参数数组

大多数程序都会忽略`argv`的第一个参数，因为它通常是程序的名称

### shell的工作流程

让我们回到<a href="#shell">刚才</a>，再次浏览一下shell的函数。

## I/O and File descriptors

shell确保每个进程都有3个打开的文件描述符(user/sh.c:151)，这3个是控制台的默认文件描述符

- 0表示标准输入，进程从文件描述符0开始读取
- 1表示标准输出，将输出写入到文件描述符1
- 2表示标准错误，将错误信息写道文件描述符2

`close`系统调用负责释放一个文件描述符，如果某个时刻需要分配新的文件描述符，那么该新文件描述符总是当前进程中编号最低的未使用的文件描述符。

## Pipes

管道操作符：`|`

管道是一个小的内核缓冲区，以一对文件描述符的形式暴露给进程，一个用于读取，另一个用于写入。

将数据写入管道的一端，可以从管道的另一端读取数据。管道为进程提供了一种通信方式。

这里有一段代码可以前往译文查看，解释的很详细

## File system

`links`：同一个文件（**文件称为`inode`**）

每个链接由目录中的一个条目组成，该条目包含文件名和对inode的引用。inode保存有关文件的相关数据，包括文件类型(文件或目录或设备)、文件长度、文件内容在磁盘上的位置以及文件链接的数量。  

### fstat函数

并没有找到相关代码...

fstat可以获取一个文件描述符指向的文件信息。

填充一个名为stat的结构体

![image-20230405212849324](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230405212849324.png)

### link函数

link创建一个新的文件系统名称，与现有文件指向同一个inode。

下面创建一个又叫a又叫b的新文件。

```c
open("a", O_CREATE|O_WRONLY);
link("a", "b");
```

读写a相当于读写b。每个inode都由一个唯一的inode编号标识。在上面的代码中，我们可以通过fstat知道a和b都指向同样的内容，a和b都会返回同样的inode号(ino)，并且nlink（number of links to file）数会设置为2。

### ulink函数

unlink从文件系统中删除一个名称，我们添加 `ulink("a")`到上述代码最后一行，会使inode和文件内容仅能通过b访问。

文件的inode和保存其内容的磁盘空间仅在文件的链接数为0（即没有文件描述符引用它）时才被释放。下面代码是创建一个没有名称的临时inode的惯用方式，当进程关闭fd或退出时将清理它。 

```c
fd = open("/tmp/xyz", O_CREATE|O_RDWR);
unlink("/tmp/xyz");
```

