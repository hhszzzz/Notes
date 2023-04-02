## Part

### （1）文件的上传

- 基于web.xml

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679212646705.jpg" style="zoom:80%;" />

- 基于“注解”

  <img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679212855236.png" style="zoom:80%;" />

  > 如果不对上传的文件大小加以限制，只需要添加`@MultipartConfig`即可
  >
  > 其目的就是为了区分获取表单信息时存在子节和字符的情况

### （2）Part对象中常用的方法

<img src="https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679212905667.png" style="zoom:80%;" />

> 注意：在form表单中要添加属性enctype（设置二进制数据上传方式）
>
> ```html
> <form action="Test" method="post" enctype="multipart/form-data">
> 
> </form>
> ```

![](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1679215120983.png)

> 如果用来存储的文件夹目前是空的，那么idea在创建project时是不会创建该文件夹的，此时如果使用write方法书写，就会出现路径错误。
>
> 所以一定要往该文件夹内放某个东西，idea在加载该项目时，就会在out文件夹中创建该文件夹，以及相应的资源，此时不会出现路径错误。

## Filter过滤器

### 1. 工作原理

   ![image-20230328085703733](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230328085703733.png)

   > 其实紫色回传部分（响应）并不会进行过滤，而是对数据进行压缩等操作，只有请求才会进行真正意义上的过滤。

### 2. Filter生命周期

   > 由容器管理，当容器启动时会实例化Filter并调用init方法完成初始化动作，当容器关闭时会销毁Filter对象，并在销毁前调用destroy方法
   >
   > 当浏览器发送请求时，容器会启动一个新线程来处理请求：若请求URI被过滤器所匹配，则先调用过滤器中的doFilter方法，再根据是否有chain.doFilter指令，决定是否继续请求目标资源

### 3. Filter对象的创建及执行步骤

   创建class去实现接口Filter并实现其三个抽象方法

   （1）init方法：初始化方法，在创建Filter后立即调用，用于完成初始化动作(调用1次)

   （2）doFilter方法：截请求与响应方法，用于对请求和响应实现预处理(调用多次 每次发出请求时均调用)

   （3）destroy方法：销方法在销Filter之前自动调用，用于完成资源释放等动作(调用1次)

### 4. Filter过滤器的开发模式（这里只讲注解式开发）

   ![image-20230328090352753](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230328090352753.png)

   > 需要注意的是，当同一请求满足多个Filter定义的匹配规则时，这些Filter的执行顺序：
   >
   > 1）web.xml中的优先级高于注解中的；
   >
   > 2）同在web.xml中，则按照定义时的编写顺序；
   >
   > 3）同为注解式开发，则根据Filter名称的字典排序顺序

## Listener监听器

> 用于监听web应用中某些对象的创建、销毁、增加、修改、删除等动作的发生，然后作出相应的响应处理

### 1. 监听器分类

1) `ServletContext`对象生命周期监听器或属性操作监听器
2) `ServletRequest`对象生命周期监听器或属性操作监听器
3) `HttpSession`对象生命周期监听器或属性操作监听器

### 2. 基于注解式开发

> 通过`@WebListener`注解代替web.xml中Listener的配置

### 3.细分监听器

1) XXX对象生命周期监听器

   > XXXListener接口定义了XXX对象生命周期的监听行为
   >
   > - `void contextInitialized `/ `requestlnitialized` / `sessionCreated`方法
   >
   >   XXX对象创建后会触发该监听方法，并**将已创建的XXX对象通过参数传递到该方法中**
   >
   > - `void xxxDestroyed`方法
   >   XXX对象在销毁之前会触发该监听方法，并将XXX对象通过参数传递到该方法中

2) XXX对象属性操作监听器

   > XXXAttributeListener接口定义了对于XXX对象属性操作的监听行为
   >
   > - `void attributeAdded`方法
   >
   >   向XX对象中添加属性时会触发该监听方法，并将XX对象通过参数传递到该方法中
   >
   >   触发事件方法为`setAttribute("key","value")`;
   >
   > - `void attributeRemoved`方法
   >
   >   当从XX对象中删除属性时会触发该监听方法，并将XX对象通过参数传递到该方法中
   >
   >   触发事件方法为`removeAttribute("key")`;
   >
   > - `void attributeReplaced`方法
   >
   >   当XX对象中属性的值发生替换时会触发该监听方法，并将XX对象通过参数传递到该方法中
   >
   >   触发事件方法为`setAttribute("key""value")`;

### 4. Filter的设计模式

- 责任链设计模式

  ![image-20230328091718147](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230328091718147.png)

  ![image-20230328091738787](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230328091738787.png)

- 观察者设计模式

  ![image-20230328091800368](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230328091800368.png)

