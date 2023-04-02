## 存储器

1）存储器所处位置分类：内存和外存

> CPU
>
> Cache：高速缓存
>
> 内存（主存）
>
> 外存（辅存）

![image-20230401110310784](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230401110310784.png)

2）按工作方式分类

![image-20230401110620581](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230401110620581.png)

![image-20230401111156021](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230401111156021.png)

> 闪存：
>
> - 以块为单位删除
>
> - 信息不会丢失
> - 代替ROM
> - 不能代替主存

---

> 虚拟存储器：由主存和辅存构成
>
> 相联存储器是一种按内容访问的存储器

### 空间局部性与时间局部性

> 空间局部性：访问一个存储单元的时候，其附近的存储单元还有可能要被访问
>
> 时间局部性：访问一个存储单元的时候，下一次还有可能访问该存储单元

## Cache

> - Cache与主存地址的映射是**由硬件自动完成**
> - 用来存最活跃的程序和数据
>
> - 对程序员来说是透明的
>
> - 存放主存的部份拷贝（副本）信息
> - 查找时先查询是否在cache中，未命中再去主存中寻找，找到时按照替换原则决定主存的一块信息放到cache存储器当中

## Cache地址映像

![](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230401165559756.png)

3）组相联映像

> 将直接映像和全相联影响组合，两种方式的特点都有
>
> 1）主存划分为第x组必须分配到cache第x块，是直接映像
>
> 2）主存每一组的块是全相联映像
>
> 冲突多少排序：直接>组相联映像>全相联映像

