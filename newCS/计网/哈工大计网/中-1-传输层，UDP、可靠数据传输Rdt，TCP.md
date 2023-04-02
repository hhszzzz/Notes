# 1 传输层

> - 主要内容
>     - 理解传输层服务的基本理论和基本机制
>         - 多路复用/分用；
>         - 可靠数据传输机制；
>         - 流量控制机制；
>         - 拥塞控制机制；
>     - 掌握Internet的传输层协议
>         - UDP：无连接传输服务；
>         - TCP：面向连接的传输服务；
>         - TCP拥塞控制；

## 1.1 传输层服务

### 1.1.1 传输层服务概述

> - 传输层协议为运行在不同Host上的进程提供了一种**逻辑通信机制**；

![image-20220707153007481](https://img-blog.csdnimg.cn/img_convert/4df0d8c0a1d935294147f069ce36206f.png)

- **1、传输层 vs 网络层**
    - **传输层是应用进程之间，网络层是主机之间；**

![image-20220707153058136](https://img-blog.csdnimg.cn/img_convert/becd54ae84ae2a42c596e432e6fa936b.png)

- **2、Internet传输层协议**

![image-20220707153422761](https://img-blog.csdnimg.cn/img_convert/7ded775acb769b3f8ac5ca3b37db46d0.png)

### 1.1.2 多路复用和多路分用

> - 传输层是应用进程之间的，一个主机会有多个进程；因此会需要多路复用和多路分用；

![image-20220707153729428](https://img-blog.csdnimg.cn/img_convert/745da4d34d77ee784e4b9d25153323c7.png)

- **1、分用如何工作？**

![image-20220707154252065](https://img-blog.csdnimg.cn/img_convert/4553281be7b6e2747ff34ed4cd666037.png)

> - 传输层根据端口号决定发送到哪一个socket
> - 网络层不关心端口号信息；

- **2、无连接分用（UDP）**

![image-20220707161315461](https://img-blog.csdnimg.cn/img_convert/922165b8bb723ef7a2acdaca4b083e36.png)

> - UDP的socket使用二元组标识；此处有错误，应该是源端口号，目的端口号；

![image-20220707161407391](https://img-blog.csdnimg.cn/img_convert/626ac14dd60496039b472fb8be38eac7.png)

> - SP是源端口号；DP是目的端口号；

- **3、面向连接的分用（TCP）**

![image-20220707161622919](https://img-blog.csdnimg.cn/img_convert/1bc189f91abf37941ec3130a3742507f.png)

![image-20220707161637162](https://img-blog.csdnimg.cn/img_convert/5957bc9a2664fb425cb60412f19def83.png)

> - 针对于`P2-P6`和`P3-P5`，它们仅仅只有源端口号不同；

- **4、面向连接的分用：多线程Web服务器**

![image-20220707162028814](https://img-blog.csdnimg.cn/img_convert/9c39c37d3108f8a6246232b23e07cf46.png)

> - 服务器用一个进程创建多个线程；P4创建了3个线程；

## 1.2 UDP协议

> - UDP:User Datagram Protocol \[RFC 768\],用户数据报协议

![image-20220707163153398](https://img-blog.csdnimg.cn/img_convert/6ae5b3a1e425af4bd0cf004f7a81cebb.png)

![image-20220707163239017](https://img-blog.csdnimg.cn/img_convert/953e00fd7f3cc9efe12bd6902ff9519f.png)

> - 右侧就是多路复用的（源端口号，目的端口号），length是UDP段的长度（包含头部）；
> - checksum是UDP校验和（checksum）；

- **UDP校验和（checksum）**

![image-20220707163639459](https://img-blog.csdnimg.cn/img_convert/a3e218c13dcc9e760aaf0a7b4e2338e8.png)

> - 检测错误的能力有限；

- **校验和计算示例**  
    ![image-20220707164044554](https://img-blog.csdnimg.cn/img_convert/45ff5abde140376eb20b0682cb3c5da5.png)

## 1.3 可靠数据传输协议

### 1.3.1 可靠数据传输概述

- **1、可靠数据传输原理**

![image-20220707165131675](https://img-blog.csdnimg.cn/img_convert/b5d08f22b1c9777e57c57ee959293e13.png)

- **2、可靠数据传输协议基本结构：接口**

![image-20220707165445635](https://img-blog.csdnimg.cn/img_convert/4fc013d5774c11003fa227b9776f208f.png)

> - rdt\_send（）和deliver\_data（）都是单向的；
> - udt\_send（）和rdt\_rcv（）都是双向的，跟不可靠信道数据交互时，双向传送控制信息；

- **3、可靠数据传输协议**

> - 数据传输是单向的，控制信息是双向的；

![image-20220707165625532](https://img-blog.csdnimg.cn/img_convert/3cb54b343db4dbd82f7c7765abb962dd.png)

### 1.3.2 [Rdt](https://so.csdn.net/so/search?q=Rdt&spm=1001.2101.3001.7020) 1.0：可靠信道上的数据传输

![image-20220707170619112](https://img-blog.csdnimg.cn/img_convert/f9de926f792f54fdc97307d4980ca8e0.png)

### 1.3.3 Rdt 2.0：产生位错误的信道

> - 2.0增加的部分
>     - 1、校验和；
>     - 2、接收方返回发送方的控制确认消息ACK\\NAK；
>     - 3、重传机制；

![image-20220707171239317](https://img-blog.csdnimg.cn/img_convert/cb5b4730f8d6ce5e569170334be3023a.png)  
![image-20220707171351579](https://img-blog.csdnimg.cn/img_convert/f7f7f8b784e0f4ed22ef25940f14981a.png)

- **Rdt 2.0: 无错误场景**  
    ![image-20220707172656571](https://img-blog.csdnimg.cn/img_convert/09a16d324373bcefe72065779b88816e.png)
    
- **Rdt 2.0：有错误场景**
  

![image-20220707172638634](https://img-blog.csdnimg.cn/img_convert/d36acc0b0811baeb529b853765d8986e.png)

### 1.3.4 Rdt 2.1 和 Rdt2.2：ACK错误

- **1、Rdt 2.0 缺陷**

> - NAK也可能会坏掉；
> - 简单的重传，会产生重复分组；

![image-20220707195928767](https://img-blog.csdnimg.cn/img_convert/9b84c05f87b7bf24d4fcccd6721137a5.png)

- **1、Rdt 2.1 ： 发送方，应对ACK/NAK破坏**

![image-20220707195945822](https://img-blog.csdnimg.cn/img_convert/ab427738e764d24cdf684d116679c56a.png)

- **2、Rdt 2.1 ：接收方，应对ACK/NAK破坏**

![image-20220707200139833](https://img-blog.csdnimg.cn/img_convert/603000d5c3475612e073204428787c60.png)

- **3、Rdt 2.1 vs Rdt 2.0**

![image-20220707200216910](https://img-blog.csdnimg.cn/img_convert/7a1376b146fe59a8316fd6e3c444daf5.png)

> - 两个序列号足够，因为是停-等协议；

- **4、Rdt 2.2 ：无NAK消息协议**

![image-20220707200416927](https://img-blog.csdnimg.cn/img_convert/ef1b2408da7418151b23a9bb668e8d4c.png)

- **5、Rdt 2.2 状态机（判断）**

![image-20220707200443343](https://img-blog.csdnimg.cn/img_convert/dba39185aa9b73c9ad46d707461f717a.png)

> - Rdt2.0的ACK错误之后，无法处理，2.1和2.2就加入一个重要的序列号机制；

### 1.3.5 Rdt 3.0：丢失分组

![image-20220707201301926](https://img-blog.csdnimg.cn/img_convert/2c0ec022207898d907fe25f313a14a21.png)

- **1、Rdt 3.0 ： 发送方FSM**

![image-20220707201322885](https://img-blog.csdnimg.cn/img_convert/63e07d7fb487f207027cc1b87fbf4b2e.png)

- **2、Rdt 3.0 示例（1）**

![image-20220707201354519](https://img-blog.csdnimg.cn/img_convert/d886394e14f57299c72d81eb38e61255.png)

- **3、Rdt 3.0 示例（2）**  
    ![image-20220707201605504](https://img-blog.csdnimg.cn/img_convert/30c0ab72ae733a9bcf0e6159a474400a.png)

> - 设置这个时间`timeout`是关键；

- **4、Rdt 3.0 性能分析**

> - Rdt3.0能够正确工作，但性能很差；停-等协议；

![image-20220707202222986](https://img-blog.csdnimg.cn/img_convert/7d53dcf04ea92eeab6d9930951646c07.png)

> - 网络协议与硬件配置要协同，软硬件协同设计，才能更好利用；

- **5、Rdt 3.0 ：停等协议—限制性能**

![image-20220707202345759](https://img-blog.csdnimg.cn/img_convert/07599865ab66eee20296df61d2078663.png)

### 1.3.6 流水线机制与滑动窗口协议

> - 以上停等协议造成了效率的降低，引入流水线机制；提升资源利用率；

![image-20220707202843252](https://img-blog.csdnimg.cn/img_convert/45c5a8fb92a0093ea8f4f1ea42269fbd.png)

- **1、流水线协议**

> - 序列号扩充，缓存扩充

![image-20220707202948475](https://img-blog.csdnimg.cn/img_convert/cf9fd979484ce332a4d786e71ba7eb8e.png)

- **2、滑动窗口协议（GBN,SR）**

> - 管理已经发出，但还没有确认的分组；

![image-20220707203137667](https://img-blog.csdnimg.cn/img_convert/377a16eb2a17e63375dff10c4028ca2f.png)

### 1.3.7 滑动窗口协议-GBN协议

> - Go-Back-N(GBN)协议：发送方

![image-20220707204034246](https://img-blog.csdnimg.cn/img_convert/f65940901d65857b06db1218c7cc4ece.png)

> - 1、绿色是已经发送并且成功确认的分组；
>     - 2、黄色是已经发送还没有确认的分组；
>     - 3、蓝色是可以使用用来发送的序列号；
>     - 4、不可以使用的序列号；
> - GBN是一种累积确认的机制；
> - 因为发生超时事件后，会重传所有序列号大于等于n的，因此存在资源浪费的情况；

- **1、GBN：发送方扩展FSM**

![image-20220707204244515](https://img-blog.csdnimg.cn/img_convert/a08ec5d71026a372d03bc0253818f4e9.png)

> - 所谓的窗口滑动，其实就是base变量+1；

- **2、GBN：接收方扩展FSM**

![image-20220707204500875](https://img-blog.csdnimg.cn/img_convert/7af6b9cae7128c272342ce0fe4b1de68.png)

> - 接收方会维护一个变量，代表当前期望收到的序列号expectseqnum,并没有缓存；

- **3、GBN示例**

![image-20220707204648122](https://img-blog.csdnimg.cn/img_convert/6caf0361e2ba5e1744ce94545cddc747.png)

> - 2发生timeout事件后，会重新发送2之后的序列；

- **4、练习题–看接收确定的最大序列号3**

![image-20220707204945958](https://img-blog.csdnimg.cn/img_convert/991e7e2670402932f5459e51d7a90dfd.png)

### 1.3.8 滑动窗口协议-SR协议

> - Selective Repeat协议
> - GBN累积确认（确认以最后一个序列号为准），会造成很多重复的重传分组；
> - SR单独确认，可以接收乱序到达的分组；

![image-20220708163033013](https://img-blog.csdnimg.cn/img_convert/45f4d7f7eb846b567f91131f719335c7.png)

> - 相比于SBN,SR多了一个接收方的窗口；

- **1、Selective Repeat:发送方/接收方窗口**

![image-20220708163907467](https://img-blog.csdnimg.cn/img_convert/9f85259bddda881cc84a9f756762f491.png)

> - 下方为接收滑动窗口，
>     - 灰色表示目前期望收到的但还没有收到的序列分组；
>     - 红色表示已经到达，并返回ack的分组；
>     - 蓝色表示可以接收的窗口内的序列分组；
> - 发送方与接受方的窗口并不是同步的；

- **2、SR协议**

![image-20220708164127224](https://img-blog.csdnimg.cn/img_convert/b2de1b94bbae11f4938f921e832a5814.png)  
\*\* 3、SR协议示例\*\*  
![image-20220708164147179](https://img-blog.csdnimg.cn/img_convert/f545da3064e69f8393a6e89c609df8c6.png)

- **4、SR协议：困境**

![image-20220708164327895](https://img-blog.csdnimg.cn/img_convert/2556ae2ee1151853e297c22ae9115221.png)

> - 注意，右侧两种情况，可以成功区分吗？
> - **因为序列号只有0123个状态，两次0状态不好区分；**
> - 解决方案：就是空间大小与窗口尺寸满足一定要求；

### 1.3.9 可靠数据传输原理与协议回顾

![image-20220708165317775](https://img-blog.csdnimg.cn/img_convert/4b793ad20e8020020a575560a6b8c0f4.png)

## 1.4 TCP协议

### 1.4.1 TCP概述

![image-20220708165911773](https://img-blog.csdnimg.cn/img_convert/014467f3f7004882f5ca49bc7f515427.png)

- **1、TCP段结构**

![image-20220708170625135](https://img-blog.csdnimg.cn/img_convert/e3cffc7d399e2dea004a2a291e5db25c.png)

- **2、TCP：序列号和ACK**  
    ![image-20220708173426729](https://img-blog.csdnimg.cn/img_convert/5e9b598a72efe1231ba6e720c1393b56.png)

> - 序列号指的是segment中第一个字节的编号；
>     - 假如有1K的数据，拆分成两个segment,那么第二个segment的序号号通常是501或者500,是segment中第一个字节的编号；并不是segment个数的编号；
> - ACKs:是希望接收的下一个字节的序列号；
>     - 累积确认像SBN协议；
> - 右图中，段的序列号是42，期望收到的字节序列号为ACK=79；

### 1.4.2 TCP可靠数据传输

![image-20220710204647863](https://img-blog.csdnimg.cn/img_convert/2b8313e6e4b4bc13dfca56f4d953d2a1.png)

- **1、TCP RTT和超时**

![image-20220710204928150](https://img-blog.csdnimg.cn/img_convert/e848b7f7e1c75019180de8aa472348e8.png)

> - 考虑历史信息与当前信息，动态更新RTT的时间设置阈值；

![image-20220710205100527](https://img-blog.csdnimg.cn/img_convert/fe632f10a375c2d00494e14708eae5f2.png)

> - 变化大的话，安全边界也设置增大；

- **2、TCP发送方事件**

![image-20220710205139158](https://img-blog.csdnimg.cn/img_convert/9cbb1c0e19890d6eaeebdf7eb8350eb6.png)

> - 只会重传没有确认的那个；

- **3、TCP发送端程序**

![image-20220710205222926](https://img-blog.csdnimg.cn/img_convert/b9abb4d4c36844f4a8b7b2663fff5ccf.png)

- **4、TCP重传示例**

![image-20220710205329039](https://img-blog.csdnimg.cn/img_convert/ffb591b8639a69383fbfd2ae5d7159f2.png)

> - 累积确认机制，第二次会返回ACK=120;

![image-20220710205556257](https://img-blog.csdnimg.cn/img_convert/8b9b49e144ca7964c3d2b65a50bd00f4.png)

> - 主机A虽然没有收到ACK=100，但是收到了ACK=120就会更新为120；

- **5、快速重传机制**

![image-20220710205825200](https://img-blog.csdnimg.cn/img_convert/a9fa5922ed916911efb0203e974d57d3.png)

> - 利用发送方收到3个同一数据的ACK来开启重传机制

- **6、快速重传算法**

![image-20220710205950752](https://img-blog.csdnimg.cn/img_convert/a02d32fd227aecb37a108f76ac4de295.png)

### 1.4.3 TCP流量控制

![image-20220710210111035](https://img-blog.csdnimg.cn/img_convert/6550b869ccb453d5aadeb5166a86a938.png)

![image-20220710210146610](https://img-blog.csdnimg.cn/img_convert/5a77b9e0cb02638fa5a4ff3f8e208d7d.png)

> - 由接收方通过头部字段告诉发送方，目前可用的buffer大小来限制发送；
> - 即使是0，也可以发送很小的信息，以便响应信息携带信息告知；

### 1.4.4 TCP连接管理

![image-20220710210855794](https://img-blog.csdnimg.cn/img_convert/4a5feb6a28b2d99af91b5de358e7a99f.png)

> - 三次握手：
>     - 1、发送方首先第一次请求，SYN标志位置一，发送初始自己的初始序列号；
>     - 2、接收方返回一个SYNACK，返回自己的初始序列号；
>     - 3、发送方（客户端）收到SYNACK，同时SYN不再置一，回复一个ACK；表示客户端收到了服务端允许建立连接的信息，可以包含信息；

- **1、TCP连接管理：建立**

![image-20220710211244479](https://img-blog.csdnimg.cn/img_convert/ee01e111b28329eb05ae5fedefff304e.png)

- **2、TCP连接管理：关闭**

![image-20220710211407116](https://img-blog.csdnimg.cn/img_convert/d758021f2f980762730238792910e584.png)

> - 当服务器收到客户端的ACK才会真正关闭连接；

- **3、TCP连接管理**

![image-20220710211645411](https://img-blog.csdnimg.cn/img_convert/c73727d6099312479cd6c7499d538b78.png)

### 1.4.5 TCP拥塞控制原理

- **1、拥塞控制**  
    ![image-20220710211930988](https://img-blog.csdnimg.cn/img_convert/f7bc40a623078cd7c0d81a067f72ca9a.png)
    
- **2、拥塞的成因和代价：场景1，无限buffer**
  

![image-20220710212202477](https://img-blog.csdnimg.cn/img_convert/2a9a60f5652a4c9f6fde2e9ec95e4352.png)

> - 假设路由器有无线缓存，都可以逐渐发送，不会发生丢包和重传；
> - 吞吐量与时延跟随输入速率的曲线图；
> - 时延代价大；

**3、拥塞的成因和代价：场景2，有限buffer**

![image-20220710213524293](https://img-blog.csdnimg.cn/img_convert/67c907ae81a63fd7e3f79ccb5951eea0.png)  
![image-20220710213535908](https://img-blog.csdnimg.cn/img_convert/f21b65071d3d071e39303570c1e801e5.png)

> - 由于丢失和重传，造成了资源的浪费；

- **4、拥塞的成因和代价：场景3**

![image-20220710213853470](https://img-blog.csdnimg.cn/img_convert/181901d12e7682826c3722bbb2d920a9.png)

> - 4个路由器，有限缓存；
> - A向C发送，要占用上右路由器，D向B发送，红色和蓝色线会发送竞争；拥塞后会发送丢包；加入R2拥塞丢失造成重传，那么上游的路由资源都被浪费了；

![image-20220710214114685](https://img-blog.csdnimg.cn/img_convert/7982d5865d530a0b5caafa17fddfb156.png)

> - 当更大，说明网络已经瘫痪了，都在发送数据，但是几乎没有被接收到；

- **5、拥塞控制的方法（两种）**

> - 重点：管制发送方的发送速度；

![image-20220710214308266](https://img-blog.csdnimg.cn/img_convert/961778de6a64c275442a0b22e11890db.png)

- **6、ATM ABR拥塞控制**

![image-20220710214442247](https://img-blog.csdnimg.cn/img_convert/5ae9acdf8147903b8d3980758ccde75c.png)

> - 信息经过的一路的路由转发，期间的路由器都可以改变状态，接收方就可以通过这个信息知道拥塞情况；
> - 这个拥塞控制由接收方返回给发送方的；
> - 还有一种是中间网络设备直接向发送方发送控制信息分组的；当前讨论是ATM ABR;

- **7、ATM ABR拥塞控制**

![image-20220710215113611](https://img-blog.csdnimg.cn/img_convert/2d034c164e3f9ce936d8f21968dc0807.png)

> - 红色就是资源管理的控制信息；
> - 接收方返回红色块给发送方；

### 1.4.6 TCP拥塞控制

- **1、TCP拥塞控制的基本原理**

> - 控制发送方的发送速率

![image-20220711113201173](https://img-blog.csdnimg.cn/img_convert/98f5fc9abc62b8a33ae96add488e4575.png)

> - 发生拥塞，就将发送窗口长度变小，来减小发送速率；
> - 通过timeout或者3个重复ACK来判断感知网络拥塞；

- **2、加性增—乘性减：AIMD**

> - 线性加（一个一个的加），乘性减（直接减半，快速减）；

![image-20220711113647827](https://img-blog.csdnimg.cn/img_convert/50f52cb558a7433ba66cb0ae0d204720.png)

- **3、TCP慢启动：SS**

![image-20220711114445623](https://img-blog.csdnimg.cn/img_convert/f10679bd2cfdd1b088094069cb415640.png)

![image-20220711114521371](https://img-blog.csdnimg.cn/img_convert/35da7f0fbff6fe4e85163667428f2d63.png)

- **4、Threshold变量**

> - 线性增长与指数型增长的区别，以及何时使用切换？
> - 以Threshold为界限，指数增长到Threshold后开始线性增长；

![image-20220711114739313](https://img-blog.csdnimg.cn/img_convert/1e35cf3946471d48813acd37b497de24.png)

> - Threshold当发生拥塞，阈值减少为原来的一般；蓝色窗口直接降为1；
> - 改进：蓝色窗口也减少为一半，进入线性增长阶段；

- **5、Loss事件的处理**

> - 不同的事件处理不同
>     - timeout意味着更加严重的拥塞；直接减为1；
>     - 向上图减少一半，说明是3个ACK的Loss事件；

![image-20220711115106561](https://img-blog.csdnimg.cn/img_convert/dbc4766658e0946ccaeee4855e1f606a.png)

- **6、TCP拥塞控制算法**

![image-20220711115405404](https://img-blog.csdnimg.cn/img_convert/232c1c1469708595567de25287e7532f.png)

- **7、例题**

![image-20220711115519720](https://img-blog.csdnimg.cn/img_convert/c2e9cf903a4a7eff1a9f154ebfe39932.png)

### 1.4.7 TCP性能分析

- **1、TCP throughput：吞吐率**

![image-20220711115652530](https://img-blog.csdnimg.cn/img_convert/ca1f6813d4ab94ccdb2cfaed9027d936.png)

- **2、未来的TCP**

> - 目前是否适用；

![image-20220711115712248](https://img-blog.csdnimg.cn/img_convert/4ab488c9e406abbd20d33d3b48945105.png)

![image-20220711115739460](https://img-blog.csdnimg.cn/img_convert/8765ceb6b3d514b62ab37b4e653c9902.png)

- **3、TCP的公平性**

![image-20220711115921536](https://img-blog.csdnimg.cn/img_convert/d9579277cb7244754c6f9ee6c7f6136d.png)

![image-20220711115940647](https://img-blog.csdnimg.cn/img_convert/d569a88127dc5156f60e9af62a008b83.png)

> - 横轴是连接1的，纵轴是连接2的，右上45度是公平的，
> - 最终会收敛到45度公平性；

![image-20220711120104912](https://img-blog.csdnimg.cn/img_convert/2bf5a310df40a8b8acbab3e3246e0389.png)

> - 与UDP一起，就会差，不公平，UDP会一直发，TCP会控制；

![image-20220711120113196](https://img-blog.csdnimg.cn/img_convert/8ff5abc359dd2c26cbd5f0965cdc4bd9.png)

## 1.5 传输层总结

![image-20220711120223273](https://img-blog.csdnimg.cn/img_convert/baa2138307cd0f2a606690d2aecdc864.png)