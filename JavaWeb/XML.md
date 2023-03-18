# 六、XML

## 001-xml 简介

```
什么是 xml？
	xml 是可扩展(可以自定义)的标记性语言。
```

XML的标签和属性都是自定义的

```
xml 的作用？
	xml 的主要作用有：
	1、用来保存数据，而且这些数据具有自我描述性
	2、它还可以做为项目或者模块的配置文件
	3、还可以做为网络传输数据的格式（现在以 JSON 为主）
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/b29374e5425442369eb2c3a143437682.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_15,color_FFFFFF,t_70,g_se,x_16)

## 002-xml 语法

### （1）xml文件的声明

```
<?xml version="1.0" encoding="UTF-8" ?>
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
这个是xml文件的声明
version="1.0" 表示xml的版本
encoding="UTF-8" 表示xml文件本身的编码方式
-->

<books> <!--books表示多个图书信息-->
  <book sn="SN1234"><!--book表示一个图书信息 sn属性表示图书序列号-->
    <name>时间简史</name><!--name标签表示书名-->
    <author>霍金</author><!--author表示作者-->
    <price>75</price><!--price表示价格-->
  </book>
  <book sn="SN4567"><!--book表示一个图书信息 sn属性表示图书序列号-->
    <name>红楼梦</name><!--name标签表示书名-->
    <author>曹雪芹</author><!--author表示作者-->
    <price>66</price><!--price表示价格-->
  </book>
</books>

```

### （2）注释

```
html 和 XML 注释 一样 : <!-- html 注释 -->
```

### （3）元素（标签）

```xml
咱们先回忆一下:
html 标签：
格式：<标签名>封装的数据</标签名>
单标签: <标签名 /> <br /> 换行 <hr />水平线
双标签 <标签名>封装的数据</标签名>
标签名大小写不敏感
标签有属性，有基本属性和事件属性
标签要闭合（不闭合 ，html 中不报错。但我们要养成良好的书写习惯。闭合）
```

#### 1）什么是 xml 元素

```xml
元素是指从开始标签到结束标签的内容。
例如：<title>java 编程思想</title>

元素 我们可以简单的理解为是 标签
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/d0ac6d7f0afd4c5896606066322b07e9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 2）xml 元素命名规则

```xml
2.1）名称可以含字母、数字以及其他的字符
例如： 
<book id="SN213412341"> <!-- 描述一本书 -->
<author>班导</author> <!-- 描述书的作者信息 -->
<name>java 编程思想</name> <!-- 书名 -->
<price>9.9</price> <!-- 价格 -->
</book>

2.2）名称不能以数字或者标点符号开始
2.3）名称不能包含空格
```

#### 3）xml 中的元素（标签）也 分成 单标签和双标签

```xml
单标签
	格式： <标签名 属性=”值” 属性=”值” ...... />
双标签
	格式： <标签名 属性=”值” 属性=”值” ......>文本数据或子标签</标签名>
```

### （4）属性

```
xml 的标签属性和 html 的标签属性是非常类似的，属性可以提供元素的额外信息

在标签上可以书写属性：
	一个标签上可以书写多个属性。每个属性的值必须使用 引号 引起来。
	的规则和标签的书写规则一致
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2f3d6638e40a492fb3bf88bd8e9a4448.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

### （5）语法规则

```
1）所有 XML 元素都须有关闭标签（也就是闭合）
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/3f59f2dd0751480c95235354c644d2a4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

```
2）XML 标签对大小写敏感
3）XML 标签 必须正确地嵌套
4）XML 文档必须有根元素
	根元素就是顶级元素，
	没有父标签的元素，叫顶级元素。
	根元素是没有父标签的顶级元素，而且一个XML文档只允许有一个根元素
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/c6ffb7344579415181ac1aaea50134fa.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

```
5）XML 的属性值须加引号
6）XML 中的特殊字符
	&gt;  >
	&lt;  <
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/9f17e3f636df49668e63250e0efdf2ae.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

```
7）文本区域（CDATA 区)
CDATA 语法可以告诉 xml 解析器，我 CDATA 里的文本内容，只是纯文本，不需要 xml 语法解析

CDATA 格式：
	<![CDATA[ 这里可以把你输入的字符原样显示，不会解析 xml ]]
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/989b518ff9714beeb4ef9389767e90cc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

## 003-xml 解析技术介绍

```
xml 可扩展的标记语言。
不管是 html 文件还是 xml 文件它们都是标记型文档，都可以使用 w3c 组织制定的 dom 技术来解析
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/168adf6f3c3c4162977450b7fcc6b046.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)


>1. document 对象表示的是整个文档（可以是 html 文档，也可以是 xml 文档
早期 JDK 为我们提供了两种 xml 解析技术 DOM 和 Sax 简介（这两种技术已经过时，但我们需要知道这两种技术）
>2. dom 解析技术是 W3C 组织制定的，而所有的编程语言都对这个解析技术使用了自己语言的特点进行实现。
>3. Java 对 dom 技术解析标记也做了实现。
>4. sun 公司在 JDK5 版本对 dom 解析技术进行升级：SAX（ Simple API for XML ）
SAX 解析，它跟 W3C 制定的解析不太一样。它是以类似事件机制通过回调告诉用户当前正在解析的内容。
>5. 它是一行一行的读取 xml 文件进行解析的。不会创建大量的 dom 对象，所以它在解析 xml 的时候，在内存的使用上。和性能上。都优于 Dom 解析。
6. 第三方提供的解析技术：
>jdom 在 dom 基础上进行了封装 、dom4j 又对 jdom 进行了封装。
pull 主要用在 Android 手机开发，是在跟 sax 非常类似都是事件机制解析 xml 文件。
这个 Dom4j 它是第三方的解析技术。我们需要使用第三方给我们提供好的类库才可以解析 xml


## 004-dom4j 解析技术

>由于 dom4j 它不是 sun 公司的技术，而属于第三方公司的技术，我们需要使用 dom4j 就需要到 dom4j 官网下载 dom4j的 jar 包。

### （1）Dom4j 类库的使用

![在这里插入图片描述](https://img-blog.csdnimg.cn/7b03cb55aeef4855899dd9568ac9031d.png)

### （2）dom4j 目录的介绍

>1）docs 是 文 档 目 录

![在这里插入图片描述](https://img-blog.csdnimg.cn/c7c39ab3c29f41dfa7da2fd98916cc15.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

>如何查 Dom4j 的文档

![在这里插入图片描述](https://img-blog.csdnimg.cn/52660841ff684f87b108c9439470718d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

>Dom4j 快速入门

![在这里插入图片描述](https://img-blog.csdnimg.cn/4c8d086a59fa4760a2f225b0aa7aa1e0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

>2）lib 目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/1a58851e776e405192fc1c481484dec4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

>3）src 目录是第三方类库的源码目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/78917dbc4190442ca0c75bab4288e3eb.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

### （3）使用dom4j解析一个xml文件

```
解析xml文件，即将xml文件中的标签变为dom对象

待解析的xml文件：books.xml：
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<books>
  <book sn="SN12341232">
    <name>辟邪剑谱</name>
    <price>9.9</price>
    <author>班主任</author>
  </book>
  <book sn="SN12341231">
    <name>葵花宝典</name>
    <price>99.99</price>
    <author>班长</author>
  </book>
</books>
```

```
(java)生成一个Book类:
//每一个book标签要解析成为一个Book类
//写一个Book类对应book标签的内容
```

```java
import java.math.BigDecimal;

//每一个book标签要解析成为一个Book类
//写一个Book类对应book标签的内容
public class Book {
  private String sn;
  private String name;
  private BigDecimal price;
  private String author;

  public Book() {
  }

  public Book(String sn, String name, BigDecimal price, String author) {
    this.sn = sn;
    this.name = name;
    this.price = price;
    this.author = author;
  }

  public String getSn() {
    return sn;
  }

  public void setSn(String sn) {
    this.sn = sn;
  }

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public BigDecimal getPrice() {
    return price;
  }

  public void setPrice(BigDecimal price) {
    this.price = price;
  }

  public String getAuthor() {
    return author;
  }

  public void setAuthor(String author) {
    this.author = author;
  }

  @Override
  public String toString() {
    return "Book{" +
      "sn='" + sn + '\'' +
      ", name='" + name + '\'' +
      ", price=" + price +
      ", author='" + author + '\'' +
      '}';
  }
}

```

```
在解析前在模块下创建一个lib目录
存放jar包：
dom4j的jar包和
@Test 注解是TestNG的核心注解，被打上该注解的方法，表示为一个测试方法。所需要的hamcrest和junit jar包
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/49dd7c5bbd4b4f94acc842648a835a06.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/fbc9c137d69345ac91f6d1d2048197e2.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/d205ac8653d84db6a9acec0d6a70d0db.png)

```
然后创建一个java类Dom4jTest 进行解析
```

```java
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;
import org.junit.Test;

import java.math.BigDecimal;
import java.util.List;

public class Dom4jTest {
  @Test
  public void test01() throws Exception {
    //创建一个SaxReader输入流，去读取xml配置文件，生成Document对象
    SAXReader saxReader = new SAXReader();
    //读取xml文件，获得Document对象
    Document document = saxReader.read("src/books.xml");
  }

  //读取books.xml文件生成Book类
  @Test
  public void test02() throws Exception {
    //1 读取books.xml文件
    SAXReader saxReader = new SAXReader();
    //在Junit测试中，相对路径从模块名开始算
    Document document = saxReader.read("src/books.xml");

    //2 通过Document对象获取根元素
    Element rootElement = document.getRootElement();

    //3 通过根元素获取book标签对象
    //element()和elements()都是通过标签名获得子元素
    //element()获得一个，elements()获得多个
    List<Element> books = rootElement.elements();

    //4 遍历，处理每个book标签转换为Book类
    for (Element book:books) {
      //asXML() 把标签对象转换为标签字符串
      //System.out.println(book.asXML());
      Element nameElement = book.element("name");
      //getText() 可以获得标签里的文本内容
      String nameText = nameElement.getText();

      //elementText()可以直接获取指定标签里的文本内容
      String priceText = book.elementText("price");

      String authorText = book.elementText("author");

      //获得属性值
      String snValue = book.attributeValue("sn");

      //生成一个Book对象
      System.out.println(new Book(snValue,nameText,new BigDecimal(priceText),authorText));

    }
  }
}

输出结果：
Book{sn='SN12341232', name='辟邪剑谱', price=9.9, author='班主任'}
Book{sn='SN12341231', name='葵花宝典', price=99.99, author='班长'}
```

>总结：
需要分四步操作:
第一步，通过创建 SAXReader 对象。来读取 xml 文件，获取 Document 对象
第二步，通过 Document 对象。拿到 XML 的根元素对象
第三步，通过根元素对象。获取所有的 book 标签对象
第四步，遍历每个 book 标签对象。然后获取到 book 标签对象内的每一个元素，再通过 getText() 方法拿到起始标签和结束标签之间的文本内容