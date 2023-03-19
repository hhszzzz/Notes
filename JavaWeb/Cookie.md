# 十七、Cookie

## 001-什么是 Cookie？

> 1、Cookie 翻译过来是饼干的意思。
> 2、Cookie 是服务器通知客户端保存**键值对**的一种技术。
> 3、客户端有了 Cookie 后，每次请求都发送给服务器。
> 4、每个 Cookie的大小不能超过 4kb

## 002-如何创建 Cookie?

>![在这里插入图片描述](https://img-blog.csdnimg.cn/fc2458167afa4ffeab1d746b5ef083d6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)  

![在这里插入图片描述](https://img-blog.csdnimg.cn/e00b030d20c749e0afdaff4b6a248814.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

## 003-服务器如何获取 Cookie?

> `getCookies()`方法**只能**传递`Cookie[]`数组，不能使用键值对返回指定`Cookie`
>
> `cookie`的键值对通过`getName()`和`getValue()`来获取

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/78190a550c1c4b3e985495059366af4f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)  

![在这里插入图片描述](https://img-blog.csdnimg.cn/1ad54af63155462d961e551cf811aba4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/910e29bdf52d498c9b6e4c31ecd47399.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

## 004-Cookie 值的修改

- **两种方法**：

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/e08f331485cc48f2a9624e305a903b08.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/8ded018e4f4e4191b67dc52447006c88.png)

> `Cookie`的值**不能**是中文

## 005-浏览器查看 Cookie

![在这里插入图片描述](https://img-blog.csdnimg.cn/40f27aa002e741c78470e80c1d876c8f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/cf0d86cd33b04b34bbf7141245a2fff9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

## 006-Cookie 生命控制

> 设置`cookie`的有效存活时间

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/4678ac9e9f4d41b99aef536ed628ef4a.png)  
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/395181d620a54ee59743628f2c1b99ee.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/23f372191ce44462a3a768a811276a6c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/eb63453fda9945d982b687393cb81d92.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

## 007-Cookie 有效路径 Path 的设置

> 如果创建的某个`cookie`对象没有设置`Path`属性，那么该`cookie`只对当前访问路径所属的目录及其子目录有效
>
> 如果想让某个cookie项对所有的目录下的访问路径都有效，应该调用`cookie`对象的`setPath`方法将其`Path`属性设置为 **"/"**

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/b784f646d0744c5f99e0e18355c2c901.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)  

![在这里插入图片描述](https://img-blog.csdnimg.cn/718d5f15f74f42d49fecbce64d360867.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

> 方法`setDomain(String pattern)`和`getDomain()`
>
> **domain属性用于指定浏览器访问的域**，例如，我的域名为："hehuansu.com"。
>
> 设置domain属性时，其值必须以`"."`开头，如`domain = .hehuansu.com`。
>
> 默认情况下，domain属性的值为当前主机名，浏览器在访问当前主机下的资源时，都会将cookie信息发送给服务器（当前主机）。
>
> **注意：domain属性的值不区分大小写**

## 008-Cookie 练习—免输入用户名登录

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/9960e2cd9468418aa0a7c161231bfc8f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)  

![在这里插入图片描述](https://img-blog.csdnimg.cn/e7e408fa8cf742c0b6ddd29c96e4b444.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)