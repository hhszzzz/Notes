## RISC和CISC

![image-20230401101558573](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230401101558573.png)

## 指令流水线

> - 流水线：第1条指令的执行时间+（n-1）×（最长时间段）

最长时间段指的是输入、计算、输出三个步骤中耗时最长的

> - 加速比=$\displaystyle\frac{不采用流水线}{采用流水线}$
>
> - 操作周期=最长时间段
>
> - 吞吐率=$\displaystyle\frac{1}{最长时间段}$
>
> - 执行n条指令的吞吐率=$\displaystyle\frac{n}{流水线}$