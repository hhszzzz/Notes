# Web安全威胁

## 2.1 Web安全威胁

- **1、Web安全威胁**

![在这里插入图片描述](https://img-blog.csdnimg.cn/3b1f768920f740a8a9c7417142688802.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/53e205d929e643c799f2eb63194aa6be.png)

- **2、基于应用层实现Web安全**

![在这里插入图片描述](https://img-blog.csdnimg.cn/94255bca1b8b44a9a78c3b7d03cd16c2.png)

- **3、基于传输层实现Web安全**

![在这里插入图片描述](https://img-blog.csdnimg.cn/e36a00d5822e4f9788c5f1b017a92a1d.png)

- **4、基于网络层实现Web安全**  
    在这里插入图片描述

![在这里插入图片描述](https://img-blog.csdnimg.cn/067496e7b1cd4b72b83744ae62441bca.png)

## 2.2 安全套接字层（SSL）

> - 可以分别通过应用层，传输层，网络层解决Web安全问题
> - SSL就是传输层与应用层之间的安全套接字层

- **1、SSL：Secure Sockets Layer**

![在这里插入图片描述](https://img-blog.csdnimg.cn/dbea7ff9c7ff48f491a83ffdd9e5c2b1.png)

- **2、SSL和TCP/IP**

![在这里插入图片描述](https://img-blog.csdnimg.cn/977f21c75a03459094752baa377efe04.png)

- **3、可以像PGP那样实现某些安全功能**

![在这里插入图片描述](https://img-blog.csdnimg.cn/83ddc96582c84b198be4f00f880d994d.png)

> - PGP不适用于字节流和交互数据；
> - SSL需要一组密钥用于整个连接；

### 2.2.1 简化的SSL

- **1、简化的（Toy）SSL：一个简单的安全信道**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/3a0c1c9828d046debadc2ca23f5e6983.png)
    
- **2、简化的SSL：一个简单的握手过程**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/6296a5a7a668462fb7cf76c53d6e39f0.png)

- **3、简化的的SSL：密钥派生**

![在这里插入图片描述](https://img-blog.csdnimg.cn/2f4212018a944f2fa98b8a8cc7f63120.png)

- **4、简化的SSL：数据记录**

> - MAC报文完整性检验

![在这里插入图片描述](https://img-blog.csdnimg.cn/8ca3654673ee4e169dbd7e3647518a1e.png)

- **5、简化的SSL：序列号**

![在这里插入图片描述](https://img-blog.csdnimg.cn/6a9231b3839c46978d15ac73f1443ca8.png)

- **6、简化的SSL：控制信息**

![在这里插入图片描述](https://img-blog.csdnimg.cn/345b2b6b423e489b95870c8a5161759e.png)

- **7、简化的SSL：总结**

![在这里插入图片描述](https://img-blog.csdnimg.cn/69f87c88f2ed4359b978a7dccbb05243.png)

### 2.2.2 SSL协议栈

![在这里插入图片描述](https://img-blog.csdnimg.cn/88840231f4b44d89a2bd101caf7c9b12.png)

- **1、SSL密码组（cipher suite)**

![在这里插入图片描述](https://img-blog.csdnimg.cn/db55bd4472a7481f8e073dc3f28e0d3b.png)

- **2、SSL更改密码规格协议**

![在这里插入图片描述](https://img-blog.csdnimg.cn/7762f858bcba4f9f843f017c527b07e4.png)

- **3、SSL警告协议**

![在这里插入图片描述](https://img-blog.csdnimg.cn/541a9654f8174b4a8d8a20c54b7e56df.png)

- **4、SSL握手协议**

![在这里插入图片描述](https://img-blog.csdnimg.cn/3de39efb49e74aa9ac955ec3d93e9bd0.png)

- **5、SSL记录协议**

![在这里插入图片描述](https://img-blog.csdnimg.cn/e31a44d01e3b46ad999993b2ace84abb.png)

### 2.2.3 SSL握手过程

> - 握手协议与记录协议

- **1、SSL握手过程**

![在这里插入图片描述](https://img-blog.csdnimg.cn/4ea4efaf9e27472e854722d077eff927.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/5c28590b79c9442b90c6cc04e62498d9.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/90cfaf9479684132b1578afba49a946b.png)

- **2、SSL握手协议**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/1078841fb94b4479856dd7f536212fdd.png)
    
- **3、SSL握手协议工作过程**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/7dbc9156429147a78f1f14200862511d.png)

### 2.2.4 SSL记录协议

![在这里插入图片描述](https://img-blog.csdnimg.cn/a67fcc88bd274e58b25873163a63e324.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/cc06038d248742aca4f508983db44c6e.png)

- **1、SSL记录格式**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/cfc7d661f28c45bf9e8497f684b3f8d8.png)
    
- **2、实际的SSL连接**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/425f03b445f54546a64497ed5db93637.png)

> - SSL在TCP协议之上，在SSL握手之前，TCP已经完成三次握手；
> - SSL安全断连之后才是TCP断开连接；

- **3、密钥派生**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/813fde47499641639bb8e8704824c875.png)

> - 这个过程对于客户端和服务端都是统一的，因此产生的密钥均相同；

## 2.3 虚拟专用网（VPN）

- **1、专用网（PN）**

![在这里插入图片描述](https://img-blog.csdnimg.cn/7c911ebf030044febd57dfc4ee9f7072.png)

- **2、虚拟专用网（VPN）**

![在这里插入图片描述](https://img-blog.csdnimg.cn/b0c5a121e6644a85808c3d81c5d6b3e4.png)

- **3、典型VPN应用**

![在这里插入图片描述](https://img-blog.csdnimg.cn/c69de5e75505425aba4c28bc8aacd986.png)

> - 建立逻辑上的安全通道，不独占网络资源；

- **4、VPN的功能**

![在这里插入图片描述](https://img-blog.csdnimg.cn/d973635064b04e03a9ad5c344a090bd1.png)

- **5、VPN关键技术**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/d67fab9505564714885195abd8ff6a63.png)
    
- **6、隧道技术**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/b909dfd068ac49189c5ffc1be4b17802.png)

- **7、隧道协议**

![在这里插入图片描述](https://img-blog.csdnimg.cn/4e2ff3fc53cc4d3aa92e0576bd3b63d5.png)

- **8、典型VPN实现技术**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/d3414fb58b3347b3973b4d6453d3615d.png)

## 2.4 IP安全（[IPsec](https://so.csdn.net/so/search?q=IPsec&spm=1001.2101.3001.7020)）

> - 网络层

### 2.4.1 IPsec体系结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/92f5594c00704afb957cbb84be9d4686.png)

- **1、IPsec服务**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/3060100d134f4cc5b9d651fee375609b.png)
    
- **2、IPsec的传输（transport）模式**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/37d636d51eb44b7e8c18e117f2f77061.png)

- **3、IPsec的隧道（tunneling）模式**

![在这里插入图片描述](https://img-blog.csdnimg.cn/3134e4ad77614f2bb0173c1d6435b8c7.png)

- **4、两个IPsec协议**

![在这里插入图片描述](https://img-blog.csdnimg.cn/54b3b5cab0e240839e8e0601d837ad5e.png)

- **5、IPsec模式与协议的4中**

![在这里插入图片描述](https://img-blog.csdnimg.cn/311cf0e14c794d208f917088d82d8098.png)

### 2.4.2 安全关联（SA）

- **1、安全SA**

![在这里插入图片描述](https://img-blog.csdnimg.cn/5ee20ed60dbd45d4ac778b30518662c1.png)

> - 双向的IPsec通信需要两个SA；
> - IPsec看做网络层的逻辑安全连接；

![在这里插入图片描述](https://img-blog.csdnimg.cn/8da5dd6496b948d1b5025d3cf3075fec.png)

- **2、SA举例**

![在这里插入图片描述](https://img-blog.csdnimg.cn/87cb313e902d48f2bddfa8af8ff2c502.png)

- **3、安全关联数库（SAD）**

![在这里插入图片描述](https://img-blog.csdnimg.cn/c2446cd2cd2144c3a8ae82aed9b28df4.png)

- **4、安全策略数据库（SPD）**

> - 不是所有数据报都需要经过SA，SPD就记录这个信息；

![在这里插入图片描述](https://img-blog.csdnimg.cn/8108454e89dd4183a3f3a6706d6fbf7f.png)

### 2.4.3 IPsec协议（AH,ESP）

- **1、IPsec数据报**

![在这里插入图片描述](https://img-blog.csdnimg.cn/4a9e1828cecb4cac85aaa1af6e75509e.png)

> - 对整个IP头、AH头、原IP数据报载荷进行认证，不包括IP头中的可变字段；

![在这里插入图片描述](https://img-blog.csdnimg.cn/3cf96068dd124939ad8145e792918467.png)

> - 隧道模式要构造新的IP头；

![在这里插入图片描述](https://img-blog.csdnimg.cn/a83b6a10d5124fde85b9226321ee3256.png)

> - ESP要提供机密性，因此有加密；分组密钥因此有填充；

![在这里插入图片描述](https://img-blog.csdnimg.cn/2ea8f07d1a254c26bfc81eb2a62f9730.png)

> - 隧道模式的ESP应用最广泛；最重要；

- **2、IPsec的传输模式**

![在这里插入图片描述](https://img-blog.csdnimg.cn/5d94a9eda79648eca3852286fb25474c.png)

- **3、IPsec的隧道模式**

![在这里插入图片描述](https://img-blog.csdnimg.cn/32f90415ed3141f8b3ca4e83a63b734e.png)

### 2.4.4 数据报处理过程

![在这里插入图片描述](https://img-blog.csdnimg.cn/c03e01a0135b47e79a527c42cc63a36e.png)

> - 隧道ESP；

> - 总部网络中一台主机向分支结构发送IP数据报；R1路由器；

- **1、R1：将原IP数据报转换为IPsec数据报**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/6c1a912f8656438bab49d8a1069e433e.png)
    
- **2、R2：解封IPsec数据报**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/f8640c2835f944a7a5a2a191689aa8f0.png)

- **3、在enchilada内部**

![在这里插入图片描述](https://img-blog.csdnimg.cn/1090a7cf23094ed3a37336e4c43b6bfb.png)

- **4、IPsec序列号**

![在这里插入图片描述](https://img-blog.csdnimg.cn/9e35f963e3ff4ce49a228865e35bf614.png)

> - IPsec建立连接中需要确定加密算法和加密密钥；

### 2.4.5 SA的建立和密钥管理

![在这里插入图片描述](https://img-blog.csdnimg.cn/01e51e5460b842ef882faf0a31484ea5.png)

- **1、INternet密钥交换（IKE）**

![在这里插入图片描述](https://img-blog.csdnimg.cn/48db0d62389c4395af070fe514b0c29e.png)

- **2、Internet密钥交换（IKE）**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/98d45d1da72344c5996db0129311ba73.png)
    
- **3、IKE和IPsec**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/dc47eb1cd25b4406b10246850956fc18.png)

- **4、IKE:PSK与PKI**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/df489f350ca7440d92fd670845b0b8ea.png)
- **5、IKE的阶段**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/131de75ac12246648a363f44aa514b6d.png)

> - 第一阶段使用公钥建立安全连接（效率低），在基础上就可以安全的进行IPsec SA效率高（不必再使用公钥）；

- **6、IPsec总结**

> - 网络层通讯实体建立安全连接；

![在这里插入图片描述](https://img-blog.csdnimg.cn/043eb949b6bd4fe799cdf642c2097a5b.png)

## 2.5 无线局域网的安全

> - 信息报文在无线局域网内传播，更加容易被嗅探；

- **1、WEP的设计目标**

![在这里插入图片描述](https://img-blog.csdnimg.cn/5738cb815eb54c19aefece2bc5531fdc.png)

> - 分组之间没有关联；可以单独加密；

- **2、回顾：对称流密码**

![在这里插入图片描述](https://img-blog.csdnimg.cn/59186e5b81f84bc6948b3cc54ff62a50.png)

- **3、流密码与分组独立性**

![在这里插入图片描述](https://img-blog.csdnimg.cn/82d7152dd5334cd1b906fe339a42767e.png)

- **4、WEP加密**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20cecfbf3f6040888f824449fbdfcd09.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/b50fdb2f81c049fe8460fe207fd38537.png)

- **5、WEP解密概述**

![在这里插入图片描述](https://img-blog.csdnimg.cn/314a5f2f8d3c4eddae7ccf1d74e6b12e.png)

> - Web流密码报文数据这里的完整性认证会差很多比之前的MAC；

- **6、利用一次性随机数进行断点认证**

![在这里插入图片描述](https://img-blog.csdnimg.cn/02d9ec3e22a54daca9bed40a499b4543.png)

- **7、WEP的身份认证**

> - 使用之前已经确认的共享密钥；

![在这里插入图片描述](https://img-blog.csdnimg.cn/55cdaf488e6a48d78bed22d43de4243a.png)

> - WEP的安全性较差；

- **8、破解802.11 WEP加密**

![在这里插入图片描述](https://img-blog.csdnimg.cn/3ce7dd62d71b41339a5ba6d6c9457a16.png)

- **9、802.11 i：改进的安全**

![在这里插入图片描述](https://img-blog.csdnimg.cn/b557e3cc518f4e6bbc6959cd05b6577f.png)

- **10、802.11i：运行的4个阶段**

![在这里插入图片描述](https://img-blog.csdnimg.cn/ec19c4bcc8d444e68f1fcfe0addf5a77.png)

- **11、EAP：扩展认证协议**

![在这里插入图片描述](https://img-blog.csdnimg.cn/16e0dcf69b2c468589296269b438c567.png)

> - 两段完全不同的链路，左侧为无线802.11，右侧是UDP/IP；

## 2.6 防火墙

> - 软件/硬件/软硬件结合；

![在这里插入图片描述](https://img-blog.csdnimg.cn/9e2c6e4a794a4ad3ba1d204227d57f90.png)

- **1、防护墙的作用**

![在这里插入图片描述](https://img-blog.csdnimg.cn/1d56efc9da1c408c88347f1743d51b97.png)

- **2、无状态分组过滤**

![在这里插入图片描述](https://img-blog.csdnimg.cn/be7376ea6c01479fa37aeadab3329679.png)

- **无状态分组过滤：举例**

![在这里插入图片描述](https://img-blog.csdnimg.cn/39fc409645d0473f81594736f501966a.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/b16633036f5640c29cf5f137cec6f0b0.png)

- **访问控制列表**

> - 通过路由器的访问控制列表实现防火墙功能；

![在这里插入图片描述](https://img-blog.csdnimg.cn/868c9dd3494e4fc381094f6330c7fffe.png)

- **3、有状态分组过滤**

> - 之前基于静态信息，笨拙

![在这里插入图片描述](https://img-blog.csdnimg.cn/a7941b4f8080480596ef98e9f91f4f0a.png)

> - 也通过ACL实现，不同的是增加了一列`check connection`；同时检测当前连接状态；

![在这里插入图片描述](https://img-blog.csdnimg.cn/daad1c21da614dfe92dcbc7713e46e98.png)

- **4、应用网关**

> - 可以基于应用层信息/用户信息来决定是否放行；

![在这里插入图片描述](https://img-blog.csdnimg.cn/4174e5bf3cbe411f8639eb72f594f5ce.png)

- **5、防火墙、应用网关的局限性**

![在这里插入图片描述](https://img-blog.csdnimg.cn/abf9bb2be145477587e4ccadcf0d8460.png)