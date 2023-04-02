# 十八、Session

## 001-什么是Session会话

> ![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/b5ab8df3517b4cfe9b674e48f9356cf6.png)  
>
> - Cookie和Session的区别：
>
>   Cookie在浏览器端，Session在服务器端（安全性更高）
>
>   ![1679917773352](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679917773352.png)

## 002-如何创建和获取Session

> - 不同的浏览器在访问服务器时，服务器会创建不同的`Session`
>
> ![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/28fff8c02869435c98af3c8d02e5feee.png)
>
> 还有一个带参数的`getSession(boolean create)`  ，如果参数为true，在相关的HttpSession对象不存在时创建并返回（相当于`getSession()`），否则不创建新的HttpSession对象，而是**返回null**
>
> - 一般存储登录信息用无参的，如果仅仅是获取登录信息，就用`getSession(false)`

![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/5615ebf4694647be819e68892fd14dea.png)

## 003-Session域数据的存储和获取

![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/9be2fa0a4b924cb482e0bb6b3ae76980.png)

## 004-Session生命周期控制

![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/27e1d69afc2b4e6d9060528adda32753.png)

> 如果`session`设置为永不超时，服务器这边就要越来越多的session，占用内存。
>
> 如何让当前`session`会话马上超时无效？
> 				不是像`cookie `设置为0，而是使用另一个API，即另一个方法：`public void invalidate()` 让当前` Session` 会话马上超时无效

![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/fa808f6ea40f4ddca86f5aa482b65c93.png)

> ![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/f82a235e9545400fa9306811349ab0e1.png)
>

---

![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/16ca3fe51198453d80e4a07227b45366.png)

---

![在这里插入图片描述](https://img-blog.csdnimg.cn/aff20cc1e2524c57a38d237198b8e79f.png)  
![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/0c573ec2e4644aedaa9409a62fe33205.png)

---

![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/82ae2e5ef9414abca98fa58976b44f71.png)

> 点一下，等3秒，`session`被销毁。
>
> 一秒点一下，`timeout`一直是3，`session`无法被销毁。

## 005-浏览器和 Session 之间是怎么关联的？

> 问题：在`Session`的超时时长内，关闭浏览器，再打开浏览器，怎么`Session`就没了？
> 			因为一关浏览器 `Cookie` 就没了，那么`Sessison`也就没了，要创建一个新的`Session`。
>
> - `Session` 技术，底层其实是基于 `Cookie` 技术来实现的。

![在这里插入图片描述](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/2a179498c982421991d30b9f92d4a00f.png)