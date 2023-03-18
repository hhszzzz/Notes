# 2 网络层

## 2.1 网络层服务

> - 网络层不再是端到端的服务，在每一个网络设备（例如路由器）都需要运行网络协议；
> - 路由器也将拆分网络层的数据报，进行下一步的路由和转发；

![image-20220711154144440](https://img-blog.csdnimg.cn/img_convert/d6ec3264adb10dd9cb2089a339ff6450.png)

- **1、网络层核心功能-转发和路由**

> - 路由器根据数据报，将数据报转发到正确输出端口；
> - 每个路由器要维护一个转发表；

![image-20220711154754648](https://img-blog.csdnimg.cn/img_convert/591cb7309b5f5348b3b4733ac708e7a6.png)

- **2、网络层核心功能-连接建立**

![image-20220711154929092](https://img-blog.csdnimg.cn/img_convert/f45ddcdc968923cf0e63365a243b277e.png)

> - 网络层连接：两个主机之间（路径上所有路由器等网络设备参与其中）；
> - 传输层连接：两个应用进程之间（对中间网络设备透明）；

- **3、网络层服务模型**

> - Internet的网络服务就是尽力而为服务；
> - ATM在网络层提供四种服务，分别是CBR\\VBR\\ABR\\UBR;

![image-20220711155149144](https://img-blog.csdnimg.cn/img_convert/1785a6fca248cb59ccab6c42d23ec728.png)

> - 提供两种网络层服务模型
>     - 无连接服务（不预先确定路径，不同分组不同路径，—数据报网络）
>     - 连接服务（路径实现确定，相同路径，—虚电路网络）

![image-20220711155802831](https://img-blog.csdnimg.cn/img_convert/cf4979c539e5e88d6af9ed44646a650b.png)

### 2.1.1 连接服务-虚电路网络

> - 网络层是主机到主机（网络核心），传输层是端系统到端系统的；

- **1、连接服务与无连接服务**

![image-20220711160135725](https://img-blog.csdnimg.cn/img_convert/d93ded34d73eb4d10ebd6301826475fb.png)

- **2、虚电路（Virutal circuits）**

![image-20220711160415754](https://img-blog.csdnimg.cn/img_convert/b3392d63e02cb47dc2630eca123cdbbc.png)

> - 分组交换但是，建立连接，并且占用全部带宽；

![image-20220711160651912](https://img-blog.csdnimg.cn/img_convert/4c093be0b24e658ad84731690fd6f800.png)

> - 携带相同虚电路标识的（VCID）就会沿着相同的路径（每一个路由器都需要建立维护）进行传输；

- **3、VC的具体实现**

![image-20220711161144723](https://img-blog.csdnimg.cn/img_convert/6038d5d7c53a45548aa95a7d71406ad2.png)

> - 由于每一段链路的带宽不同，可以提供的虚电路的数量不同，因此每一条虚电路不是全局统一的一个编号，而是沿路每段链路一个编号；
> - 因此需要转发表记录经过的每条虚电路；

- **4、VC转发表**

![image-20220711161338581](https://img-blog.csdnimg.cn/img_convert/050eb03be0e32fe8bf8df88a56f0dfd3.png)

- **5、虚电路信令协议（signaling protocols)**

![image-20220711162050568](https://img-blog.csdnimg.cn/img_convert/22cb05f3825fc6dd8fdfed7e04d9834b.png)

### 2.1.2 无连接服务-数据报网络

> - Internet网络中，采用的是数据报网络（无连接）；

- **1、数据报网络**

> - 每个分组都要携带目的地址；

![image-20220711162328399](https://img-blog.csdnimg.cn/img_convert/adf1cd202f9a3c43bb897e00a0069a02.png)

> - 如果路由算法，更新可能会导致相同目的地址的分组走不同的路径；

- **2、数据报转发表**

> - 记录所有的IP地址是不可能的，因此转发表记录的是目的地址范围；

![image-20220711162556869](https://img-blog.csdnimg.cn/img_convert/5a0e7cffc46dad150097725096905100.png)  
![image-20220711162611913](https://img-blog.csdnimg.cn/img_convert/922a0359f310805f1ed9945f1f4719da.png)  
![image-20220711162712894](https://img-blog.csdnimg.cn/img_convert/746edae839983c203ec56198b10d305c.png)

- **3、最长前缀匹配优先**  
    ![image-20220711162858280](https://img-blog.csdnimg.cn/img_convert/619bcab820f2e0d75c2e42ed5b400ae1.png)
- **4、数据报网络 vs 虚电路网络**  
    ![image-20220711163100757](https://img-blog.csdnimg.cn/img_convert/b66a7c751938f767f12cd64d42fa3e92.png)

## 2.2 IP协议

### 2.2.1 IP数据报

- **1、Internet网络层**

![image-20220711182753350](https://img-blog.csdnimg.cn/img_convert/33519751686934ae0aa07f13197bf2f2.png)

- **2、IP数据报（分组）格式**

![image-20220711182907315](https://img-blog.csdnimg.cn/img_convert/00eb13fcd4ee9b5cfcfc39fae8a70284.png)

> - 版本号字段占4位：IP协议的版本号；4 -> IPv4;6 -> IPv6;
> - 首部长度字段占4位：IP分组首部长度；（4bit的话最大15），但是视为4字节为单位；
>     - 例如：5->IP首部长度为20(5\*4)字节；
> - 服务类型（TOS）字段占8位：指示期望获得哪种类型的服务
>     - 1998年改名为区分服务；
>     - 只有在网络提供区分服务（DiffServ）时使用；
>     - 一般情况下并不使用，通常IP分组的该字段（第2字节）的值为00H;

> - ![image-20220711184613706](https://img-blog.csdnimg.cn/img_convert/a19086d3761858baf18909765184ee48.png)
> - ![image-20220711184703025](https://img-blog.csdnimg.cn/img_convert/be5bcc48a77f46dfcbecec0241c3a852.png)
> - ![image-20220711184802640](https://img-blog.csdnimg.cn/img_convert/c4ed2884088c426b5e3dfa6f75ccc002.png)
> - ![image-20220711184910357](https://img-blog.csdnimg.cn/img_convert/cbda2a1ea8bd7319c3126df17259b37a.png)
> - ![image-20220711184916643](https://img-blog.csdnimg.cn/img_convert/d9174c100d14e05f52f7c51700238312.png)
> - ![image-20220711184941681](https://img-blog.csdnimg.cn/img_convert/38a633ac624551e1ca7237f4f0a0ffef.png)
> - ![image-20220711190039221](https://img-blog.csdnimg.cn/img_convert/f934b4c906d33096b8fd09ab3e08823f.png)

### 2.2.2 IP数据报分片

- **1、最大传输单元（MTU）**

![image-20220711190251093](https://img-blog.csdnimg.cn/img_convert/7177c4dc573d066d3aeae5ddbf434650.png)

- **2、IP分片与重组**

![image-20220711190555970](https://img-blog.csdnimg.cn/img_convert/1cbbe73a20368ab1a4a814a622d3948b.png)

> - 路由器只管分，不管装，都是统一在目的地进行重组；
> - IP首部需要有字段来标识分片以及确定分片相对顺序；

![image-20220711190605301](https://img-blog.csdnimg.cn/img_convert/3e4bfa231f0b053ddb0d51bf941c8789.png)

> - ![image-20220711190653398](https://img-blog.csdnimg.cn/img_convert/80df6c93b8dd4c286a7e71cbe4eb7c05.png)
> - ![image-20220711191033387](https://img-blog.csdnimg.cn/img_convert/7d2014e9be24ab14984e46d052cbb9d8.png)
> - 说明前面分片的数据大小就是8字节为单位；

- **3、IP分片过程**  
    ![image-20220711191545166](https://img-blog.csdnimg.cn/img_convert/d00dac3363689b1cd20a5145e73c23e6.png)

> - 一个最大分片可封装的数据，向下取整后乘以8，因为片偏移量是8的倍数；
> - 总片数是向上取整；（尽可能分最大片）；  
>     ![image-20220711192124300](https://img-blog.csdnimg.cn/img_convert/91682c6dd06e90b0d62126355e3ed1e4.png)

- **4、例子**  
    ![image-20220711192200493](https://img-blog.csdnimg.cn/img_convert/18a7878716fb41d6ef055de11f86315b.png)

### 2.2.3 IP编址（addressing）

- **1、IP地址**

![image-20220711192451825](https://img-blog.csdnimg.cn/img_convert/cf17fa48bd639b355484ace35ce69972.png)

> - IP编址是针对于实现网络层功能的，交换机不在网络层而在链路层是不编址的，主要是对路由器和端系统进行IP编址；

![image-20220711192815059](https://img-blog.csdnimg.cn/img_convert/478135858d9b94bbb94283cbdbb3ce1b.png)

> - IP地址每8bit为一组，转换为十进制表示；
> - 随意分配IP地址，会给路由转发表造成不能承受的负担；

- **2、IP子网（Subnets）**  
    ![image-20220711193822118](https://img-blog.csdnimg.cn/img_convert/2dc3b1260e412f7b8632b08231b31edb.png)

![image-20220711193937613](https://img-blog.csdnimg.cn/img_convert/da440d1c4869c27775868d6eabd694e6.png)

### 2.2.4 有类IP地址

> - 采用二分法（一共32位）；

![image-20220711194954380](https://img-blog.csdnimg.cn/img_convert/159a4879fd66df58e66a6ea33d6c6fda.png)

> - DE类就不区分主机号，就是一组主机，只作为目的地址；
> - 以下分类都是对于ABC类的（有些特殊地址）；

- **1、特殊IP地址**

![image-20220711195238574](https://img-blog.csdnimg.cn/img_convert/164eaed4a3eec61a86fc386f75ab6425.png)

- **2、私有（Private）IP地址**

![image-20220711195718573](https://img-blog.csdnimg.cn/img_convert/26b4e2fa4f7ab1ad2c5deaffd3b4a846.png)

### 2.2.5 IP子网划分与子网掩码

- **1、子网划分**

![image-20220711200035340](https://img-blog.csdnimg.cn/img_convert/842db82bece1d8e213ae4e0276131478.png)  
![image-20220711200257844](https://img-blog.csdnimg.cn/img_convert/c9a4b6aee5dbb6850bd6af50965230c6.png)

- **2、子网掩码**  
    ![image-20220711200416411](https://img-blog.csdnimg.cn/img_convert/04240b4d46d05528d275a87ceda87dd3.png)

![image-20220711200830054](https://img-blog.csdnimg.cn/img_convert/18da3266e6f401159d8f5710e4f95722.png)

> - 子网掩码，借用主机号的前两位Bit划分为4个子网，128+64=192；

- **3、子网掩码的应用**

![image-20220711201421166](https://img-blog.csdnimg.cn/img_convert/ab705c714f0146b17510eab6da2d4440.png)

> - 可分配地址范围去掉主机域全0和主机域全1的IP地址；

- **4、一个C类网络划分子网举例**

![image-20220711201611146](https://img-blog.csdnimg.cn/img_convert/9b0e88857fe5f06aaf61f0decae570a3.png)

> - 当划分子网时，会造成头尾两个IP地址不能表示主机，前面红色是每个子网的子网地址，绿色是每个子网的网络地址；
> - 划分子网会有一定IP地址损失，但是性能提升；

### 2.2.6 CIDR与路由聚合

> - 子网地址+子网掩码可以准确描述一个子网；

- **1、CIDR**

![image-20220712113125297](https://img-blog.csdnimg.cn/img_convert/bbbaf150a6cdf25826eda5723c44c1e0.png)

> /26表示网络前缀长度是26；

![image-20220712113409879](https://img-blog.csdnimg.cn/img_convert/060a68e6046717a3200c7378074f6a10.png)

> - CIDR提高地址的利用效率；
> - 更大作用在于提高路由效率（将多个子网合并为较大的子网）；
> - **路由聚合**

- **2、路由聚合**

![image-20220712113524349](https://img-blog.csdnimg.cn/img_convert/4f83c09b81674cdb18e6ae34f9e7b694.png)

![](https://img-blog.csdnimg.cn/img_convert/1c27bcdc6976dcfa1d0b3d02acf09442.png)

> - 路由表大大简化；  
>     ![image-20220712113840038](https://img-blog.csdnimg.cn/img_convert/2aef10582b70825037b7f076cd8cf18e.png)

> - 左侧小子网应该是连续的，且是2的次方倍数，
> - 但是，有一个组织1，历史原因，有单独的子网在别的地方，如下方就会增加一个**最长前缀匹配优先**原则，避免路由黑洞的问题；

## 2.3 DHCP（动态主机配置协议）

> - 主机如何获取IP地址？  
>     ![image-20220712114607908](https://img-blog.csdnimg.cn/img_convert/330a87abeeb023b119243b291057ceda.png)

> - 默认网关：这个子网的数据报要离开子网的时候，应该送往的接口地址
> - 图中所示的路由器接口就是这个子网的默认网关；

![image-20220712114837524](https://img-blog.csdnimg.cn/img_convert/d0a5b613177168965e6b1c7dbd88cd9e.png)

- **1、动态主机配置协议（DHCP）**

![image-20220712115028165](https://img-blog.csdnimg.cn/img_convert/468cbdb71de8bb93a4a2428c40528993.png)

- **2、DHCP工作示例**

![image-20220712115612974](https://img-blog.csdnimg.cn/img_convert/740dae5a20e527510eb9ed0fba0ecf74.png)

> - `0.0.0.0`表示本机IP地址；`255.255.255.255`是广播地址；
> - 第一次主机发出的广播（发现报文），所有主机都可以接收到，但是只有DHCP服务器才会进行响应（提供报文）；
>     - 为该主机分配IP地址；
>     - yladdr就是DHCP服务器给该主机分配的IP地址；
>     - 这个提供报文也是通过广播地址发出去的；
> - 第二次主机发出的仍然是广播，也是告知其他的DHCP服务器已经跟现在的这个链接上了；（请求报文）；
> - 在DHCP服务器第二次回应之后，就确认绑定该IP地址的了；

![image-20220712115706041](https://img-blog.csdnimg.cn/img_convert/85c329841881b476c4171ad5b4e967a1.png)

![image-20220712115724086](https://img-blog.csdnimg.cn/img_convert/b66e511df66dbf18880a55c7410f6f7e.png)

> - IP分配是有使用时间限制的；

## 2.4 网络地址转换（NAT）

> - IPv4可用于公共网络的IP地址已经分配殆尽，因此是有主机使用私有地址，这些私有地址需要通过网络地址转换NAT来进入公共互联网；

![image-20220712120823985](https://img-blog.csdnimg.cn/img_convert/68fd3adb0e8028b82b6375a98496937f.png)  
![image-20220712121158332](https://img-blog.csdnimg.cn/img_convert/4a771c5660a52846749d41485a843d23.png)  
![image-20220712121306115](https://img-blog.csdnimg.cn/img_convert/c24e20fe4b54654e89035320f3b03307.png)

- **1、NAT转换过程**

![image-20220712121417827](https://img-blog.csdnimg.cn/img_convert/d0cc8cda6e501bcd58875a84de76bd62.png)

![image-20220712121430018](https://img-blog.csdnimg.cn/img_convert/50ff021a1db23426b7e0f7a0c165f128.png)  
![image-20220712121444916](https://img-blog.csdnimg.cn/img_convert/55d685886785e7f0220f06a638e85363.png)  
![image-20220712121459276](https://img-blog.csdnimg.cn/img_convert/c53d8cec07db2ba3532b5ff3204e5256.png)

- **2、NAT存在问题**

> - 端口号是16bit，最多可以支持60,000多并行连接  
>     ![image-20220712121747159](https://img-blog.csdnimg.cn/img_convert/df44376cbcfeb6c01d6bbf80b6e2d8eb.png)

- **3、NAT穿透问题**

![image-20220712121928276](https://img-blog.csdnimg.cn/img_convert/bdc48f45b044820bf7ceb3644af02b59.png)  
![image-20220712122315425](https://img-blog.csdnimg.cn/img_convert/5224126279b99d9474b47639dd7c1e53.png)  
![image-20220712122504597](https://img-blog.csdnimg.cn/img_convert/4d4b616c6f7f159999d229f3cd15c553.png)

## 2.5 互联网控制报文协议（ICMP)

> - IP协议如果发生错误，就会丢弃该分组，同时通过ICMP向主机发送差错报告；

![image-20220712135118461](https://img-blog.csdnimg.cn/img_convert/670678a4a734f4bb980787f011f65ece.png)

- **1、ICMP报文**

![image-20220712135134174](https://img-blog.csdnimg.cn/img_convert/e22a1b379388267c92ad25776b85e84b.png)

- **2、例外情况**

![image-20220712135553382](https://img-blog.csdnimg.cn/img_convert/ef4ac66fe0b854fce267a224177cef78.png)

- **3、ICMP报文的格式**

> - ICMP报文也是要封装到IP数据报中传输的；

![image-20220712135654828](https://img-blog.csdnimg.cn/img_convert/6265dd7f21b6f7d7775cd491f6edcf03.png)

> - ICMP的头部共8个字节，前4个字节是相同（类型、代码、检验和）；

- **4、ICMP差错报告报文数据**

![image-20220712140038346](https://img-blog.csdnimg.cn/img_convert/085f8d860a30a5121b7600090e8cff7c.png)

- **5、ICMP的应用举例：Traceroute**

> - 利用一个不存在的端口号，进行短期的路径探测；

![image-20220712140308988](https://img-blog.csdnimg.cn/img_convert/5138168ed59918160db065aea9b1469a.png)

## 2.6 IPv6简介

- **1、IPv6：动机**

![image-20220712140851510](https://img-blog.csdnimg.cn/img_convert/caee65112b373761ff21366a9e99cb52.png)

> - IPv4的首部长度一般是20字节，但有可选项，所以理论上不确定；
> - 路由器只需要处理基本首部，而不需要管选项中的扩展首部；

- **2、IPv6数据报格式**

![image-20220712141118168](https://img-blog.csdnimg.cn/img_convert/ca51085c98309eb9f3c30efb2ec158d1.png)

> - "流"从一个特定主机发出到一个目的主机的一系列数据报；

- **3、其他改变 vs IPv4**

![image-20220712141434460](https://img-blog.csdnimg.cn/img_convert/ff20bf093e10db3f35b69a65f2fa23b9.png)

- **4、IPv6地址表示形式**

> - 一共是8组16进制数；冒号：连接；

![image-20220712141911651](https://img-blog.csdnimg.cn/img_convert/1af0549b43b1eca44cdbbdedfac93a1c.png)

> - URL为了防止发生歧义，用\[\]之后再+端口号；

- **5、IPv6基本地址类型**

![image-20220712142151935](https://img-blog.csdnimg.cn/img_convert/386a8c6cdf833462175ace24a14be5a5.png)

- **6、IPv4向IPv6过渡**  
    ![image-20220712142243217](https://img-blog.csdnimg.cn/img_convert/06662ce1a095057ccb97248d9db97657.png)
- **7、隧道（tunneling）**  
    ![image-20220712142321018](https://img-blog.csdnimg.cn/img_convert/24dbb6429b3f0528ca4ba31a742c0300.png)  
    ![image-20220712142331372](https://img-blog.csdnimg.cn/img_convert/6f171989b2754a16e745c8b4e4bdf6c8.png)

## 2.7 路由算法

### 2.7.1 路由与转发

> - 路由算法（协议）求解确定去往目的地网络的最佳路径；

![image-20220713140720053](https://img-blog.csdnimg.cn/img_convert/0b4d96469325c0799092276496803c71.png)

- **1、网络抽象：图**

![image-20220713140852876](https://img-blog.csdnimg.cn/img_convert/eb672b98d67575f6a7deed2291ca1c7d.png)

- **2、图抽象：费用（Costs）**

![image-20220713141239474](https://img-blog.csdnimg.cn/img_convert/c60eb5a6a34f46d1df4407d6724140e0.png)

- **3、路由算法分类**

![image-20220713141652118](https://img-blog.csdnimg.cn/img_convert/a849ae6ea19a0c438e4eda40ff3bbb6e.png)

### 2.7.2 链路状态路由算法

- **1、Dijkstra算法**

![image-20220713142458878](https://img-blog.csdnimg.cn/img_convert/d726488e93cd48cb37b0a39dfe72943f.png)

![image-20220713142710085](https://img-blog.csdnimg.cn/img_convert/f063c75abb9aaeef9e48f45a017b82b2.png)

- **2、Dijkstra算法：例1**

![image-20220713143219699](https://img-blog.csdnimg.cn/img_convert/a8c96266feed1330f3030d5d388494cd.png)

- **3、Dijkstra算法：例2**

![image-20220713143249375](https://img-blog.csdnimg.cn/img_convert/c120cd7716340811f8c9fb6065e3ec15.png)

![image-20220713143325437](https://img-blog.csdnimg.cn/img_convert/b522f14845522e6bc7e1247203d32344.png)

- **4、Dijkstra算法：讨论**

![image-20220713143528809](https://img-blog.csdnimg.cn/img_convert/637e223b70d432e08f845919a800d0d3.png)

> - 由于动态更新，有可能有一个数据报从B刚到D，结果路径改变，有传回B，来回反复，形成震荡；最终形成TTL变成0，分组被丢弃；

### 2.7.3 距离向量路由算法

> - 不需要全局信息，知道局部信息就行路由选择；（迭代）

![image-20220713143905133](https://img-blog.csdnimg.cn/img_convert/979ffcfa2a93fa01a23b7a4889dbc15b.png)

- **1、Bellman-Ford举例**

![image-20220713144224641](https://img-blog.csdnimg.cn/img_convert/a5943f39b5bba4f9e3e2b2c827c0ce91.png)

- **2、距离向量路由算法**

![image-20220713144421028](https://img-blog.csdnimg.cn/img_convert/5820809550fa377d29b32a0dd3f2d821.png)

> - 不是一起迭代更新，异步迭代；

![image-20220713144638372](https://img-blog.csdnimg.cn/img_convert/65369da9100420a73155eba370776c56.png)

- **3、距离向量路由算法：举例**  
    ![image-20220713145146171](https://img-blog.csdnimg.cn/img_convert/e94938e46fd43546f7af4df7fc338d76.png)  
    ![image-20220713145200906](https://img-blog.csdnimg.cn/img_convert/0d55c3aa005b41e56ad76a9f7750dc0b.png)
    
- **4、距离向量DV：链路费用变化**
  

![image-20220713150421102](https://img-blog.csdnimg.cn/img_convert/d2c00da0d11b538c16136e293afb0495.png)  
**5、距离向量DV：无穷计数问题**

> - 以y和z到达x的距离。注意并没有直接变成60，因为根据之前的递推式，z到达x为5，那么min就会选择为y达到x的最小距离为5+1=6；来回反复很多次；
> - 如果有环路，会出现无穷计数的问题；

![image-20220713151027345](https://img-blog.csdnimg.cn/img_convert/3716c7f006ddf8fd9cd8384c181f98fc.png)

- **解决方案如下：**
- **1、毒性逆转**

![image-20220713152248174](https://img-blog.csdnimg.cn/img_convert/53e06282469fecfc43a83d793ac895fa.png)

- **2、定义最大度量**

![image-20220713152541876](https://img-blog.csdnimg.cn/img_convert/e51399be27b3f06366307225aafccc56.png)

### 2.7.4 层次路由

> - 一种路由策略；

![image-20220713154018170](https://img-blog.csdnimg.cn/img_convert/1856761aaa74ca0bc32c2fcaec8d522c.png)

- **1、层次路由**

![image-20220713154018170](https://img-blog.csdnimg.cn/img_convert/83028458659335b1f5b0b136f873871e.png)

> - 网关路由器就是连接交换不同AS之间的路由信息；
> - 分为内部路由和AS之间的路由；

- **2、互连的AS**

![image-20220713154457041](https://img-blog.csdnimg.cn/img_convert/6a725eebe41f85e80245cad4c9d23421.png)

- **2、自治系统间（Inter-AS）路由任务**  
    ![image-20220713154641948](https://img-blog.csdnimg.cn/img_convert/3c88b5db9eebfc150818c2cd87d94efd.png)
    
- **3、路由器1d的转发表设置**
  

![image-20220713154821712](https://img-blog.csdnimg.cn/img_convert/286a1e78f1c4de0f0cdf5961022fd270.png)

- **4、在多AS间选择**  
    ![image-20220713154934506](https://img-blog.csdnimg.cn/img_convert/f338fb9e53a662ef35a2bd9178f5da42.png)

![image-20220713155025744](https://img-blog.csdnimg.cn/img_convert/2688d416c655517f62cb2a8b515ab0d4.png)

> - Internet网络就是采用层次路由的方式；

## 2.8 Internet网络中的路由协议

> - 采用层次路由的方式；

### 2.8.1 RIP协议（距离向量路由）

- **1、AS内部路由**

![image-20220713161321333](https://img-blog.csdnimg.cn/img_convert/a353e71bfa4ccfcfc30e6ca70073c52d.png)

- **2、RIP协议**

![image-20220713161537137](https://img-blog.csdnimg.cn/img_convert/6bc61f93b8677a3ec2bdca0bdf4a7746.png)

- **3、RIP：举例**

> - 这里都是字母表示，实际中是IP地址+掩码；

![image-20220713161807065](https://img-blog.csdnimg.cn/img_convert/54db872b03b72c7b07094a8bc97bbf40.png)

- **4、RIP：链路失效、恢复**

![image-20220713162145737](https://img-blog.csdnimg.cn/img_convert/7ed73e9b667e9847d861a94199846add.png)

> - 设置为16还是小网络，RIP更多适用于小型网络；

- **5、RIP路由表的处理**

> - 应用层进程实现的协议；是根据功能划分层，路由是网络层功能，因此是网络层协议；

![image-20220713162319913](https://img-blog.csdnimg.cn/img_convert/12c94dd373920157b7f07637abf9592e.png)

> - 路由器运行由应用层进程实现的RIP协议，那也会运行有UDP/TCP运输层协议，但是网络参考模型中，路由器只到网络层，因为上面层次的协议也是为了完成网络层的功能；路由器本身并不是端系统到端系统的；

### 2.8.2 OSPF协议（链路状态路由）

> - OSPF(Open Shortest Path First)开放：公共可用
> - 链路状态路由算法；

![image-20220714114731779](https://img-blog.csdnimg.cn/img_convert/fa85fc7b1c015bdb0f3394f570648f41.png)

> - 直接封装到IP数据报，没有经过TCP协议；

- **1、OSPF优点（RIP不具备）**

![image-20220714115246940](https://img-blog.csdnimg.cn/img_convert/fe32bfd42b96f8acf1e06a1f498f44ad.png)

- **2、分层的OSPF**

![image-20220714115246940](https://img-blog.csdnimg.cn/img_convert/1ece1770f4bb357e24755bd4624616bf.png)

![image-20220714120341070](https://img-blog.csdnimg.cn/img_convert/b54614c78e6b9747e98fe7cc0d0a1df4.png)

> - 区边界路由器同时是一个区域的路由器也是主干网络的路由器；

![image-20220714120701148](https://img-blog.csdnimg.cn/img_convert/a22e84e8e75621143f81afc743558070.png)

![image-20220714120744357](https://img-blog.csdnimg.cn/img_convert/52246e7d83dfd1121f9f97868faa7529.png)

### 2.8.3 [BGP](https://so.csdn.net/so/search?q=BGP&spm=1001.2101.3001.7020)（边界网关协议）

> - RIP协议是距离向量路由算法，OSPF协议是链路状态路由算法；
> - Internet是采用层次化路由，而BGP就是AS（自治系统间）的路由协议
> - Border Gateway Protocol，BGP,标准域间路由协议；

- **1、Internet AS间路由协议：BGP**

![image-20220714121402759](https://img-blog.csdnimg.cn/img_convert/e10ebf62c7bd4ec74df885f2ba7f43c3.png)

- **2、BGP基础**

![image-20220714121754084](https://img-blog.csdnimg.cn/img_convert/336cc0f772fb293722daa652da4d43b5.png)

> - BGP协议也通过应用层协议TCP进行实现；

![image-20220715120647330](https://img-blog.csdnimg.cn/img_convert/73a08e43321ff57b5d8d786560049a1f.png)

> - 尽可能的进行路由聚合；

- **3、BGP基础：分发路径信息**  
    ![image-20220715120854928](https://img-blog.csdnimg.cn/img_convert/b27dc191da46ab2ebb30967619c7b1c6.png)
    
- **4、路径属性与BGP路由（route）**
  

> AS:自治系统

![image-20220715121100595](https://img-blog.csdnimg.cn/img_convert/f01bb7597b9d8fb6a704d22527d1d394.png)

> - 就像AS2下一个其他网络，有2c和2b两个方式，这个AS路径是相同的，但是下一跳的端口就不同，指示了具体走那一条链路；

- **5、BGP路由选择**

> - BGP协议是Interet中层次路由自治系统间的路由协议；

![image-20220715121714834](https://img-blog.csdnimg.cn/img_convert/163294162af9ccfcac0ec766de2dcebe.png)

- **6、BGP路由选择策略**

![image-20220715121934634](https://img-blog.csdnimg.cn/img_convert/35dbb6408a58be77c93883df1582f9f5.png)

![image-20220715122215545](https://img-blog.csdnimg.cn/img_convert/221db795d125b241f3b5c396f204c2de.png)

> - 提供商网络只为其客户x提供相关路径；

- **7、为什么采用不同的AS内与AS间路由协议？**

> - intra-AS,AS内的路由，纯计算最优策略，不需要考虑很多因素；
> - inter-AS就需要控制如何被路由，谁路由的问题；

![image-20220715122528455](https://img-blog.csdnimg.cn/img_convert/cd6bccf5a57a80bcaffd7dcd82f7c755.png)