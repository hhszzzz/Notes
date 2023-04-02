 

## 中断

![image-20230401171824408](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230401171824408.png)

> 1. 中断向量：提供中断服务程序的入口地址（不同的中断对应不同的入口地址）
> 2. 中断响应时间：发出中断请求开始，到进入中断服务程序
> 3. 保存现场、恢复现场

使用堆栈来保护断点和现场最有效

## 输入输出（I/O）控制方式

1）程序查询方式

![image-20230402082802394](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230402082802394.png)

2）中断驱动方式

![image-20230402083426409](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230402083426409.png)

> ⑤一次只能读/写一个字

**3）直接存储器方式（DMA）**

![image-20230402083824170](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230402083824170.png)

> **①CPU和I/O（外设）可并行工作**
>
> **②仅在传送数据块的开始和结束时才需要CPU的干预**
>
> **③由外设直接将数据放入内存**
>
> ④一次读写的单位为**"块"**而不是字
>
> ⑤