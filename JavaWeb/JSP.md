虽然说这是一个过时的东西，但是学校要求~没办法啦

> （JSP）Java Server Pages

 ### JSP文件被访问时的执行流程

![image-20230421152054188](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230421152054188.png)

![image-20230421154012482](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230421154012482.png)

### JSP的生命周期

类似于Servlet：

![image-20230421154127582](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230421154127582.png)

## JSP基本语法

- **声明标签**

  声明标签中的Java代码将被翻译到`_jspService()`**外**，即成员的定义位置

  格式：`<%! // java代码 %>`

  ![image-20230422085837242](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230422085837242.png)

- **脚本标签**

  用于在JSP页面中编写业务逻辑（Java代码）

  格式：`<% // 多行java代码 %>`

  同样的，JSP引擎在翻译脚本标签时，Java代码被**原封不动**地放到Servlet的`_jspService()`**中**，必须遵守Java语法
  
  那么，一个JSP页可以出现多个脚本标签，标签之中可嵌入文本、HTML标记或其他JSP元素，这种特性需要创造出多对脚本标签来维持，要求：单个脚本中的Java语句允许不完整，但多个脚本组合后的结果必须是完整的！
  
  ![image-20230422085154613](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230422085154613.png)
  
- **赋值标签**

  将指定数据或计算结果**输出到浏览器**

  格式：`<%=变量或表达式%>`

  **注意**：赋值标签中的变量或表达式后不能有分号！

- **注释元素**

  JSP引擎翻译时会忽略JSP注释的内容，但HTML的注释会被翻译与执行

  格式：`<%-- 注释信息 --%>`

## JSP指令

声明JSP页面的一些**属性和动作**

格式：`<%@指令名称 属性="值"属性="值"%>`

### page指令

作用：通过定义JSP页面的各种属性告知Tomcat服务器该如何翻译此JSP文件

其作用域为整个JSP页面；与指令位置无关，但最好放在JSP页面的**起始位置**

![image-20230422091135095](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230422091135095.png)

> 对于`errorPage`的全局设置：
>
> 在`web.xml`中写出
>
> ```xml
> <error-page>
> 	<error-code> 500 </error-code>
>     <location> /common/500.jsp </location>
> </error-page>
> ```
>
> `<error-page>`一定要作为`<web-app>`的直接子标签
>
> 
>
> 另外，在某个页面添加了`isErrorPage`属性之后，就可以获取`Exception`对象，显示异常信息等

注意事项：

1. `errorPage`的属性值**必须**使用相对路径，若以`/`开头，则表示相对于当前`Web`应用程序的根目录
2. 可在web.xml文件中使用`<error-page>`元素为整个Web应用程序设置错误处理页面
3. 若设置某个JSP页面`errorPage`属性，则在web.xml文件中设置的错误处理将不对该页面起作用

### include指令

作用：在当前JSP页面的指定位置包含其他页面

格式：`<%@include file="relativeURL"%>`

和上面的注意事项1一样，`file`属性用于指定被引入文件的路径，必须相对路径，若以`/`开头，则表示相对于当前`Web`应用程序的根目录

> 说明：
>
> 1. include指令是将所引入页面的代码与自身代码一并翻译成一个Servlet程序，故include指令引入通常也称为静态引入
>
> 2. 因include指令是将被引入文件的内容直接包含，故此文件中**无需**书写全局的HTML标签，且多个页面中的指令及代码逻辑不能冲突
>
> 3. 被引入的文件可为任意扩展名，仅要求其内容必须遵循JSP语法，但静态引入建议使用`.jspf`
>
> ![image-20230422093903623](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230422093903623.png)

## JSP内置（隐式）对象

### 内置对象的类型

![image-20230428145806565](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230428145806565.png)

### pageContext对象

![image-20230428150142598](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230428150142598.png)

![image-20230428150325157](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230428150325157.png)

![image-20230428150347853](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230428150347853.png)

## JSP动作元素

![image-20230428150602515](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230428150602515.png)