# 十八、Session

## 001-什么是Session会话

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/b5ab8df3517b4cfe9b674e48f9356cf6.png)  
>
> - Cookie在浏览器端，Session在服务器端

## 002-如何创建和获取Session

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/28fff8c02869435c98af3c8d02e5feee.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)  

![在这里插入图片描述](https://img-blog.csdnimg.cn/5615ebf4694647be819e68892fd14dea.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

## 003-Session域数据的存储和获取

![在这里插入图片描述](https://img-blog.csdnimg.cn/9be2fa0a4b924cb482e0bb6b3ae76980.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

## 004-Session生命周期控制

![在这里插入图片描述](https://img-blog.csdnimg.cn/27e1d69afc2b4e6d9060528adda32753.png)

> 如果`session`设置为永不超时，服务器这边就要越来越多的session，占用内存。
>
> 如何让当前`session`会话马上超时无效？
> 				不是像`cookie `设置为0，而是使用另一个API，即另一个方法：`public void invalidate()` 让当前` Session` 会话马上超时无效

![在这里插入图片描述](https://img-blog.csdnimg.cn/fa808f6ea40f4ddca86f5aa482b65c93.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/f82a235e9545400fa9306811349ab0e1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)
>

---

![在这里插入图片描述](https://img-blog.csdnimg.cn/16ca3fe51198453d80e4a07227b45366.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

---

![在这里插入图片描述](https://img-blog.csdnimg.cn/aff20cc1e2524c57a38d237198b8e79f.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/0c573ec2e4644aedaa9409a62fe33205.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

---

![在这里插入图片描述](https://img-blog.csdnimg.cn/82ae2e5ef9414abca98fa58976b44f71.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

> 点一下，等3秒，`session`被销毁。
>
> 一秒点一下，`timeout`一直是3，`session`无法被销毁。

## 005-浏览器和 Session 之间是怎么关联的？

> 问题：在`Session`的超时时长内，关闭浏览器，再打开浏览器，怎么`Session`就没了？
> 			因为一关浏览器 `Cookie` 就没了，那么`Sessison`也就没了，要创建一个新的`Session`。
>
> 
>
> - `Session` 技术，底层其实是基于 `Cookie` 技术来实现的。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2a179498c982421991d30b9f92d4a00f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)