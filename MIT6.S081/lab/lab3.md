## speed up system calls（easy）

需要对`USYSCALL`进行映射（就像`trapframe`一样需要map）

以及取消映射，必须十分小心

## print a page table（easy）

需要注意，`pagetable_t`本身就是一个`uint64`**指针**，所以我在第一步输出就搞错了...

## Detecting which pages have been accessed（hard）

被坑了两次woc

- 需要注意的是，`walk`函数竟然是不外露的，所以需要在内部`vm.c`上实现查看`pte`是否被`accessed`了
- 然后对于`PTE_x`的机制，竟然是`risc-v`自动处理的，所以你根本就不需要考虑什么时候要添加`PTE_A`！！
- 题意也说的比较模糊捏



最后贴上一张胜利的照片！

![82ca5da22cff37df6e1ca3bf61ce1e9](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/82ca5da22cff37df6e1ca3bf61ce1e9.png)