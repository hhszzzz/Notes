# 计算机网络之危机四伏

## 1.1 网络安全

### 1.1.1 网络安全基本概念

- **1、网络安全**

![在这里插入图片描述](https://img-blog.csdnimg.cn/fdf3cd46ca3949b5ba3271ccc4117fce.png)

- **2、网络安全基本属性**

![在这里插入图片描述](https://img-blog.csdnimg.cn/d3291b159fe64b548fac6a1e9747ac8a.png)

- **3、网络安全的基本特征**

![在这里插入图片描述](https://img-blog.csdnimg.cn/1307981ce9fc4669a21221bc448381e9.png)

- **4、网络安全**

![在这里插入图片描述](https://img-blog.csdnimg.cn/dd32def89e9845f9bf565b3616cb4cc8.png)

### 1.1.2 网络安全拟人模型

- **1、拟人场景**

![在这里插入图片描述](https://img-blog.csdnimg.cn/fd1b072329e244bc9065e26a59ffb969.png)

### 1.1.3 网络安全威胁

![在这里插入图片描述](https://img-blog.csdnimg.cn/dc547b15bac54d95b22a7ade7977ec37.png)

- **1、Internet安全威胁**
- **“探路”**

![在这里插入图片描述](https://img-blog.csdnimg.cn/6f30a2c852834e5bb973f8529bffd948.png)

- **2、分组“嗅探”（sniffing）**

> - 不根据地址匹配，接收/记录所有经过的分组/帧；

![在这里插入图片描述](https://img-blog.csdnimg.cn/f2ef926193934650b45538855190dc33.png)

- **3、IP欺骗（Spoofing）**

> - 源IP地址写成B的，而不用自己的IP地址，隐藏痕迹；

![在这里插入图片描述](https://img-blog.csdnimg.cn/97ec5b77b469430caa3dcd11dd8916fe.png)

> - 路由器检查，src是否属于C的子网地址范围内，不转发源IP地址无效的IP分组；
> - 但是目前Internet网络并没有强制执行这一检查；

![在这里插入图片描述](https://img-blog.csdnimg.cn/6a33be5c97144851963689f2905f1bd4.png)

- **4、拒绝服务DOS（Denial of service)**

> - 恶意泛洪，消耗资源，无法给正常用户提供资源；

![在这里插入图片描述](https://img-blog.csdnimg.cn/aaa4fce060e04b5c8053215332399bd1.png)

- **DDoS攻击过程**

> - 直接式攻击

![在这里插入图片描述](https://img-blog.csdnimg.cn/0f392239d9154d029b4708d726162648.png)

> - 反射式DDoS攻击—利用ip欺骗

![在这里插入图片描述](https://img-blog.csdnimg.cn/0487207291634093b0076af3e2000b25.png)

- **DOS：对策**

![在这里插入图片描述](https://img-blog.csdnimg.cn/0fc37198cc934d06adfdad6952ac3147.png)

- **SYN cookie**

> - 服务器第一次握手，不预先分配资源，一直到第三次握手才分配资源；
> - SYN cookie就是为了给服务器在不预先分配资源的情况下，第三次握手时清楚这是哪一个连接；使用哈希产生随机序列号；

![在这里插入图片描述](https://img-blog.csdnimg.cn/5b6102c2ffdb43fda0e81e98ef3b4aa3.png)

## 1.2 密码学基础

### 1.2.1 密码学（cryptography)术语

![](https://img-blog.csdnimg.cn/5008c59007ea4a8b9c777d82acf550e9.png)

- **1、对称密钥加密（加密和解密是同一个密钥）**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/d72c9fb4f1a84d4488b50e63e3aeda0f.png)
    
- **2、公开密钥加密（两个密钥）**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/f63fc6ae395646d0823e7649eb9cdeab.png)

- **3、破解加密方法**

![在这里插入图片描述](https://img-blog.csdnimg.cn/1a89e0b6d226466b9f9cb861e4817fcd.png)

> - 选择明文攻击：例如对于字母替换，就选择包含26字母的明文，获得其密文就解密了；

### 1.2.2 传统加密方法

- **1、替代密码（substitution cipher）**

![在这里插入图片描述](https://img-blog.csdnimg.cn/124b1d39cdd247f8bd0dac93899a2ebf.png)

- **2、换位（transpositions）密码**

![在这里插入图片描述](https://img-blog.csdnimg.cn/41ce461f1596471c9e69aed83de1fb78.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/207f7075dec143cca9d7464af787e116.png)

### 1.2.3 现代加密技术

![在这里插入图片描述](https://img-blog.csdnimg.cn/44b9852cdbd64170a89aeb970049d66f.png)

- **1、流密码**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/085257acd3ea4553ae7af9e507b119f5.png)
    
- **2、分组密码（应用更广泛）**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/29799f397f4343e7b5aec8dae0cb2974.png)

- **3、Feistel分组密码结构**

![在这里插入图片描述](https://img-blog.csdnimg.cn/1b36cf1e0ec2447d8421a0a550fe3658.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/06ed8f95ae7541d99c6537e5026d12c8.png)

### 1.2.4 数据加密标准：DES

![在这里插入图片描述](https://img-blog.csdnimg.cn/027326ea740547eea560bae55892b6e3.png)

#### 1.2.4.1 DES算法结构

- **1、DES算法结构（Data Encryption Standard）**

![在这里插入图片描述](https://img-blog.csdnimg.cn/690f392cc806444382ecd30da7ddc328.png)

- **2、初始置换IP（Initial Permutation）**

![在这里插入图片描述](https://img-blog.csdnimg.cn/154db1c4776c423a817c48e425c7ad58.png)

> - 58就是对应1，以此类推

- **3、一轮DES加密过程**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/5519a4d93a694daba21160ab6d2b279c.png)

> - 密钥是56bit，子密钥是48bit，要经过压缩变换得到；

- **4、DES：f函数结构**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/ab3ebd1b374b493b824919f85ee288a7.png)
    
- **5、f函数的基本操作**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/c76f30f13e2f4cf9bf00db4e683f68a3.png)

- **6、逆初始置换（Inverse Initial Permutaion）**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/1012eeec38a34751a943eb6560bc321f.png)
    
- **7、每轮子密钥的生成**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/1d73f1660f744d92a2ff7d694ae31968.png)

#### 1.2.4.2 DES的安全性

![在这里插入图片描述](https://img-blog.csdnimg.cn/dc0ca7c9f42d4301bd381aa9cd6a3b73.png)

- **1、DES的改进**

> - 相同的明文分组，不会出现相同的密文；防止破译规律；

![在这里插入图片描述](https://img-blog.csdnimg.cn/4ec3bd5c836c4e6d8de4ddefe25fbdf0.png)

- **2、高级加密标准AES**

> - AES取代了DES；

![在这里插入图片描述](https://img-blog.csdnimg.cn/7d6c88e3264d4c0d85ccf7ce47441a00.png)

- **3、Rijndael加密算法简介**

![在这里插入图片描述](https://img-blog.csdnimg.cn/b405b38612cc4ee8a76df87d3e8d274d.png)

### 1.2.5 公钥密码学

![在这里插入图片描述](https://img-blog.csdnimg.cn/7a83df16936648978c98a64782d3eea5.png)

- **1、公钥加密算法**

![在这里插入图片描述](https://img-blog.csdnimg.cn/dd9d8c4899de4385846d3c40df280f32.png)

- **2、前提条件：模运算**

![在这里插入图片描述](https://img-blog.csdnimg.cn/e6a3a377799646b2a5bfe3481bd3d345.png)

- **3、RSA:预备知识**

![在这里插入图片描述](https://img-blog.csdnimg.cn/5125e90d894a4971acf83a94fc2c63d4.png)

- **4、RSA:生成公钥/私钥对**

![在这里插入图片描述](https://img-blog.csdnimg.cn/94714373e7a44652b28cf51bd755b192.png)

- **5、RSA:加密、解密**

![在这里插入图片描述](https://img-blog.csdnimg.cn/c246d589a9e64b2abcaebe31b0af788a.png)

- **6、RSA举例**

![在这里插入图片描述](https://img-blog.csdnimg.cn/d7af2a7f6aa1433cb9ea60e6404150f5.png)

### 1.2.6 RSA的理论依据

- **1、公式推导**

![在这里插入图片描述](https://img-blog.csdnimg.cn/df240b4fae7747069add1027cae1bc48.png)

- **2、RSA：另一个重要性质**

> - 公钥与私钥是相对的；

![在这里插入图片描述](https://img-blog.csdnimg.cn/a4c3675f95c6411888428781944923d0.png)

- **3、RSA的安全性**

![在这里插入图片描述](https://img-blog.csdnimg.cn/2a06b8df64584feeb9182ed1d8a5bbd1.png)

- **4、RSA的实际应用（问题）**

> - 幂计算量太大，通过公钥加密建立安全连接，然后利用第二个对称密钥，加密传输数据；

![在这里插入图片描述](https://img-blog.csdnimg.cn/c80ad9159908444dac6568b93c233a84.png)

## 1.3 身份认证

> - 协议ap1.0

![在这里插入图片描述](https://img-blog.csdnimg.cn/6b33dad35e1b43c9a766c95419570749.png)

> - 协议ap2.0：IP源地址-----IP欺骗

![在这里插入图片描述](https://img-blog.csdnimg.cn/391dfcf7837244abbef7cf8ea89b133f.png)

> - 协议ap3.0：秘密口令-----嗅探分组

![在这里插入图片描述](https://img-blog.csdnimg.cn/4e8c727fee5144498ef9532f78aff1a3.png)

> - 协议ap3.1：秘密口令加密----直接回放复制攻击

![在这里插入图片描述](https://img-blog.csdnimg.cn/a64104aabed34da596b3c2b83136b298.png)

> - 协议4.0：一次性随机数，避免回放攻击
> - 缺点：需要双方提前达成一个共享密钥；

![在这里插入图片描述](https://img-blog.csdnimg.cn/82896107440d4deaa6fc3b5175bfdac1.png)

> - 协议5.0：利用公钥

![在这里插入图片描述](https://img-blog.csdnimg.cn/03fbcf9cf46a42c1b071c830d6fafec2.png)

> - ap5.0的安全漏洞：中间人攻击

![在这里插入图片描述](https://img-blog.csdnimg.cn/9d1d0144bd9944dcb0d2769e4776f18d.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/1332edbcb2264e5db5fa903169c469ae.png)

> - 中间人攻击难以检测；

## 1.4 报文完整性

![在这里插入图片描述](https://img-blog.csdnimg.cn/53a35a7c54f6494c8cb35ef8b987db43.png)

- **1、密码散列函数**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/eb989f3400fa427ba35f16dce57ee030.png)
    
- **2、Iternet校验和–不能作为散列函数**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/723550b8e1bb40c5a4fdddb39d1ad8c7.png)
    
- **3、散列函数算法**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/d7701ed22b6e435daf4f6fb43971ca04.png)

- **4、报文摘要（Message digests）**

![在这里插入图片描述](https://img-blog.csdnimg.cn/22834176d064413682e6bd2541f11e15.png)

- **5、报文认证**

![在这里插入图片描述](https://img-blog.csdnimg.cn/c492aa7fb91441a3a55bff1d9cabbef2.png)

> - 只能说明报文没有更改过，但是存在缺陷，不能区分身份认证；
> - 改进：报文认证码MAC

![在这里插入图片描述](https://img-blog.csdnimg.cn/3c8495d392d94cce98b6e4421b52ddac.png)

## 1.5 数字签名

> - 简单的MAC存在的问题；

![在这里插入图片描述](https://img-blog.csdnimg.cn/eb9b10d4f5f94aa5a9d34866d4c17800.png)

- **1、数字签名**

> - 用自己的私有的密钥来形成密文，作为数字签名；

![在这里插入图片描述](https://img-blog.csdnimg.cn/00e6ab3c0fdb412eae450d509871d91f.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/3fb6339720a7490eabb1286742c1d62a.png)

- **2、签名报文摘要**

> - 不对原来所有报文数据进行签名，而是对摘要签名；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20ae2f7965f94c3bb333781aa0b34a21.png)

## 1.6 密钥分发中心（KDC)

> - 回顾身份认证协议：ap 4.0

![在这里插入图片描述](https://img-blog.csdnimg.cn/41cde06f35e4406b9b63084f9186f438.png)

- **1、对称密钥问题？**

![在这里插入图片描述](https://img-blog.csdnimg.cn/e431b80b9dd04633a28829daa8223056.png)

- **2、密钥分发中心（KDC）**

![在这里插入图片描述](https://img-blog.csdnimg.cn/84fa59b2f3cc4c5d856a78985273329d.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/4916038597024ccd926aef6365e13703.png)

## 1.7 认证中心（CA）

> - 回顾身份认证协议：ap5.0

![在这里插入图片描述](https://img-blog.csdnimg.cn/3ba901a7f06845c1abdf9d765fbbc01f.png)

- **1、比萨恶作剧**

![在这里插入图片描述](https://img-blog.csdnimg.cn/4feb825dc28e4ef0bebb5792221e6778.png)

- **2、公钥问题**

> - 使用的假的公钥；

![在这里插入图片描述](https://img-blog.csdnimg.cn/464703aa4d0d412e99023cd4acfb453b.png)

- **3、认证中心**

> - 将实体与公钥一一对应；

![在这里插入图片描述](https://img-blog.csdnimg.cn/39cc31309f854a8780ce3ddfef371076.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/c4be75acf1a74527bac4b963f090910e.png)

- **4、公钥证书主要内容**

![在这里插入图片描述](https://img-blog.csdnimg.cn/285dba54450c441485d99585a081ba08.png)

## 1.8 安全电子邮件

### 1.8.1 安全电子邮件基本原理

- **1、电子邮件安全威胁**

![在这里插入图片描述](https://img-blog.csdnimg.cn/0b4b4bbe033a494e82f175798ba0e3c5.png)

- **2、电子邮件安全需求**

![在这里插入图片描述](https://img-blog.csdnimg.cn/70df330bd4c94c4f9771125e00746a1e.png)

- **3、安全电子邮件基本原理**

> - 公开密钥（建立安全连接）+对称密钥（信息主题）
> - 公开密钥计算量太大，不适合用来加密邮件本身；
> - 用公钥加密传送对称密钥；

![在这里插入图片描述](https://img-blog.csdnimg.cn/43f15d215b6640e7bf86bb144add6af1.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/f96dfd3628d64a80b23aadf065c4d2dd.png)

> - 汇总（数字签名）认证与加密传送；

![在这里插入图片描述](https://img-blog.csdnimg.cn/3158783fe7584c57a9cdda3008f5e73a.png)

### 1.8.2 安全电子邮件标准

- **1、PEM标准（基石，但没有广泛配置）**

![在这里插入图片描述](https://img-blog.csdnimg.cn/072bca8857384d6fa36d4c8831a2c8fc.png)

- **2、PGP标准（广泛之一）**  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/284a2dbfa9cd438db41f41ca7c3bc129.png)
    
- **3、PGP标准**
  

![在这里插入图片描述](https://img-blog.csdnimg.cn/fe32bf1f4f604695a549667ed5c3b10b.png)

- **4、PGP功能框架**

![在这里插入图片描述](https://img-blog.csdnimg.cn/cb6838d6ddfd41698963dfb5c835f0f4.png)

> - 首先SHA-1是散列函数，RSA是公钥加密；利用KA-私钥对散列结果加密；邮件m本身一起压缩，选一个对称密钥Ks加密，再使用接收方的公钥对对称密钥Ks进行加密，

- **5、PGP报文格式**

![在这里插入图片描述](https://img-blog.csdnimg.cn/0db45df36ec44d2ba53795efc27356ed.png)

> - 利用发送方私钥进行签名加密；
> - 利用接收方公钥进行对称密钥（会话密钥）的加密；
> - 1.报文主体是对称密钥加密；

- **6、PGP密钥**

![在这里插入图片描述](https://img-blog.csdnimg.cn/29110f56d194490ea605683c5ef01164.png)

- **7、S/MIME标准**

![在这里插入图片描述](https://img-blog.csdnimg.cn/4825bfb1f81941be83d00697bc866e76.png)