# 四、jQuery

## 001-jQuery 介绍


>jQuery是一个快速、简洁的JavaScript框架，是继Prototype之后又一个优秀的JavaScript代码库（框架）于2006年1月由John Resig发布。
jQuery设计的宗旨是“write Less，Do More”，即倡导写更少的代码，做更多的事情。
它封装JavaScript常用的功能代码，提供一种简便的JavaScript设计模式，优化HTML文档操作、事件处理、动画设计和Ajax交互。	

>**什么是 jQuery ?**
jQuery，顾名思义，也就是 JavaScript 和查询（Query），它就是辅助 JavaScript 开发的 js 类库。

>**jQuery 核心思想**
它的核心思想是 write less,do more(写得更少,做得更多)，所以它实现了很多浏览器的兼容问题。

>**jQuery 流行程度**
jQuery 现在已经成为最流行的 JavaScript 库，在世界前 10000 个访问最多的网站中，有超过 55%在使用jQuery。

>**jQuery 好处**
jQuery 是免费、开源的，jQuery 的语法设计可以使开发更加便捷，例如操作文档对象、选择 DOM 元素、制作动画效果、事件处理、使用 Ajax 以及其他功能


## 002-jQuery 的初体验

```html
<head>

  <!--需求：使用 jQuery 给一个按钮绑定单击事件-->

  <!--使用jQuery之前要先下载，然后加入到项目下，再引入jQuery-->
  <script type="text/javascript" src="../jquery-3.6.0.js"></script>

  <script type="text/javascript">
      //原先给按钮绑定事件的做法
/*    window.onload = function (){
      var btnObj = document.getElementById("btnId"); //dom对象
      btnObj.onclick = function (){
        alert("js原先的单击事件");
      }
    }*/

      //使用jQuery给一个按钮绑定单击事件
      $(function (){//表示页面加载完成之后，相当于 window.onload = function(){}
        //查询标签对象
        //表示按id查询标签对象(这是一个jQuery对象，jQuery对象默认在命名时以$开头)
        var $btnObj = $("#btnId");
        //绑定单击事件
        $btnObj.click(function (){
          alert("jQuery的单击事件");
        })
      });
  </script>

</head>
<body>

  <button id="btnId">SayHello</button>

</body>
```

```
常见问题？
1、使用 jQuery 一定要引入 jQuery 库吗？ 
答案： 是，必须

2、jQuery 中的$到底是什么？ 
答案： 它是一个函数

3、怎么为按钮添加点击响应函数的？ 
答案：
	1、使用 jQuery 查询到标签对象
	2、使用标签对象.click( function(){} );
```

## 003-jQuery 核心函数

```
$ 是 jQuery 的核心函数，能完成 jQuery 的很多功能。

$(参数) 就是调用$这个函数

1、传入参数为 [ 函数 ] 时：
表示页面加载完成之后。相当于 window.onload = function(){}

2、传入参数为 [ HTML 字符串 ] 时：
会为我们创建这个 html 标签对象

3、传入参数为 [ 选择器字符串 ] 时：
$(“#id 属性值”); id 选择器，根据 id 查询标签对象
$(“标签名”); 标签名选择器，根据指定的标签名查询标签对象
$(“.class属性值”); 类型选择器，可以根据 class 属性查询标签对象

4、传入参数为 [ DOM 对象 ] 时：
会把这个 dom 对象转换为 jQuery 对象
```

```html
<head>

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>

  <script type="text/javascript">

    //核心函数的4个作用
    //传入参数为[函数]时：在页面加载完成后执行这个函数，相当于window.onload = function(){}
    $(function (){
      alert("页面加载完成之后，自动调用");
    });


    //传入参数为[HTML字符串]时：根据这个字符串创建html标签对象
    $(function (){
      $("  <div>\n" +
        "    <span>div-span1</span>\n" +
        "  </div>").appendTo("body");//创建div和span标签再加上文字到body中(直接在body中先写然后复制到这里)
    });


    //传入参数为[选择器字符串]时：根据这个字符串查找元素节点对象(标签对象)
    //$(“#id 属性值”); id 选择器，根据 id 查询标签对象
    //$(“标签名”); 标签名选择器，根据指定的标签名查询标签对象
    //$(“.class 属性值”); 类型选择器，可以根据 class 属性查询标签对象
    $(function (){
      alert($("button").length);//3
    });


    //传入参数为[DOM对象]时：将DOM对象包装为jQuery对象返回
    $(function (){
      var btn01Obj = document.getElementById("btn01");//dom对象
      alert(btn01Obj);//[object HTMLButtonElement]
      alert($(btn01Obj));//jQuery对象 [object Object]
    });


  </script>

</head>
<body>

  <button id="btn01">按钮1</button>
  <button>按钮2</button>
  <button>按钮3</button>

</body>
```

## 004-jQuery 对象和 dom 对象区分

### （1）什么是 jQuery 对象，什么是 dom对象

```
Dom 对象
1.通过 getElementById()查询出来的标签对象是 Dom 对象
2.通过 getElementsByName()查询出来的标签对象是 Dom 对象
3.通过 getElementsByTagName()查询出来的标签对象是 Dom 对象
4.通过 createElement() 方法创建的对象，是 Dom 对象

DOM 对象 Alert 出来的效果是：[object HTML 标签名 Element]

jQuery 对象
5.通过 JQuery 提供的 API 创建的对象，是 JQuery 对象
6.通过 JQuery 包装的 Dom 对象，也是 JQuery 对象
7.通过 JQuery 提供的 API 查询到的对象，是 JQuery 对象

jQuery 对象 Alert 出来的效果是：[object Object]

API应用程序接口，一些预定义的函数
```

```html
<head>

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>

  <script type="text/javascript">

    $(function (){
      //通过 JQuery 提供的 API 创建的对象，是 JQuery 对象
      alert($("<h1></h1>"));//jQuery对象 [object Object]

      //通过 JQuery 包装的 Dom 对象，也是 JQuery 对象
      var btn01Obj = document.getElementById("btn01");//dom对象
      alert(btn01Obj);//dom对象 [object HTMLButtonElement]
      alert($(btn01Obj));//jQuery对象 [object Object]

      //通过 JQuery 提供的 API 查询到的对象，是 JQuery 对象
      alert($("#btn01"));//jQuery对象 [object Object]
    });
    
  </script>
</head>
<body>
  <button id="btn01">按钮1</button>
</body>
```

### （2）问题：jQuery 对象的本质是什么？

```
jQuery 对象是 dom 对象的数组 + jQuery 提供的一系列功能函数。
```

### （3）jQuery 对象和 Dom 对象使用区别

```
jQuery 对象不能使用 DOM 对象的属性和方法
DOM 对象也不能使用 jQuery 对象的属性和方法
```

### （4）Dom 对象和 jQuery 对象互转

```
1、dom 对象转化为 jQuery 对象（*重点）
	1、先有 DOM 对象
	2、$( DOM 对象 ) --->就可以转换成为 jQuery 对象

2、jQuery 对象转为 dom 对象（*重点）
	1、先有 jQuery 对象
	2、jQuery 对象[下标]--->取出相应的 DOM 对象 (因为jQuery对象本质是dom对象数组)
```

```html
<head>

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>
  <script type="text/javascript">

    $(function(){
      //dom对象转换为jQuery对象
      var divObj = document.getElementById("testDiv");
      alert(divObj);//dom对象
      alert($(divObj));//jQuery对象

      //jQuery对象转换为dom对象
      var $btnObjs = $("button");//jQuery对象，该对象是一个数组，数组里面都是dom对象
      for (var i = 0; i < $btnObjs.length; i++) {
        alert($btnObjs[i]);
      }
      
    });

  </script>

</head>
<body>

  <div id="testDiv">A is Very Good!</div>

  <button id="btn01">btn01</button>
  <button id="btn02">btn02</button>
  <button id="btn03">btn03</button>
  <button id="btn04">btn04</button>

</body>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/c2ff999cae4c4c9e9df393c49426b352.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 005-选择器

### 通过选择器来获取相应的jQuery对象(dom对象标签+函数)

```
看文档jQueryAPI_1.7.1_CN.chm
```

### （1）基本选择器

```
1）id选择器：
	#id
	通过id来匹配标签
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/68cc5e3a101b455288a983489da7808b.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_15,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
2）标签选择器：
	通过标签名来匹配标签
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/431c7b6bdfd84bd7a9f0458687aa17df.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_16,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
3）类选择器：
	.class名
	通过类名来匹配标签
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/7c17017115734e56a6d356ebf0926aa7.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
4）* 选择器：
	得到所有标签
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/141b433fd1264eb1b6ee166cd17a749d.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
5）组合选择器：
	selector1,selector2,selectorN
	上面几个选择器的综合使用

	下面示例中的p.myClass --->标签名是p且类名是myClass的标签
	结果与jQuery顺序不同？
		根据页面的顺序(即HTML代码中的顺序)
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/1280de4153f4476ab0528cc0a4fe3891.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
练习选择器的使用
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>Untitled Document</title>

		<style type="text/css">
			div, span, p {
			    width: 140px;
			    height: 140px;
			    margin: 5px;
			    background: #aaa;
			    border: #000 1px solid;
			    float: left;
			    font-size: 17px;
			    font-family: Verdana;
			}

			div.mini {
			    width: 55px;
			    height: 55px;
			    background-color: #aaa;
			    font-size: 12px;
			}

			div.hide {
			    display: none;
			}
		</style>

		<script type="text/javascript" src="../jquery-3.6.0.js"></script>
		<script type="text/javascript">

        $(function (){
          //1.选择 id 为 one 的元素 设置"background-color","#bbffaa" (id选择器)
          //绑定点击事件
          $("#btn1").click(function (){
            
            //jQuery对象的css(name,value)方法可以设置和获取样式
            
            $("#one").css("background-color","#bbffaa");
          });

          //2.选择 class 为 mini 的所有元素,设置"background-color","#bbffaa" (类选择器)
          //绑定点击事件
          $("#btn2").click(function (){
            $(".mini").css("background-color","#bbffaa");
          });

          //3.选择 元素名是 div 的所有元素 (标签名选择器)
          //绑定点击事件
          $("#btn3").click(function (){
            $("div").css("background-color","#bbffaa");
          });

          //4.选择所有的元素 (* 选择器)
          $("#btn4").click(function (){
            $("*").css("background-color","#bbffaa");
          });

          //5.选择所有的 span 元素和id为two的元素 (组合选择器)
          $("#btn5").click(function (){
            $("span,#two").css("background-color","#bbffaa");
          });

        });

		</script>
	</head>
	<body>

		<input type="button" value="选择 id 为 one 的元素" id="btn1" />
		<input type="button" value="选择 class 为 mini 的所有元素" id="btn2" />
		<input type="button" value="选择 元素名是 div 的所有元素" id="btn3" />
		<input type="button" value="选择 所有的元素" id="btn4" />
		<input type="button" value="选择 所有的 span 元素和id为two的元素" id="btn5" />

		<br>
		<div class="one" id="one">
			id 为 one,class 为 one 的div
			<div class="mini">class为mini</div>
		</div>

		<div class="one" id="two" title="test">
			id为two,class为one,title为test的div
			<div class="mini" title="other">class为mini,title为other</div>
			<div class="mini" title="test">class为mini,title为test</div>
		</div>

		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini"></div>
		</div>

		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini" title="tesst">class为mini,title为tesst</div>
		</div>

		<div style="display:none;" class="none">style的display为"none"的div</div>

    <div class="hide">class为"hide"的div</div>

		<div>
			包含input的type为"hidden"的div<input type="hidden" size="8">
		</div>

		<span class="one" id="span">^^span元素^^</span>
	</body>
</html>


```

### （2）层级选择器

```
1）祖先后代选择器：在给定的祖先标签下匹配所有的后代标签
	语法：ancestor descendant
	
	找到祖先ancester标签的所有后代descendant标签
	即ancester标签的儿子标签，孙子标签等等
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/09f03afb344847ed8a5b00cd752ec286.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
2）子元素(标签)选择器：在给定的父元素下匹配所有的子元素
	语法：parent > child
	找parent标签下的所有child子标签
	只找子标签，孙子标签往后的不找
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2c27c118844b4299b7378863e4623d9a.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_13,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
3）相邻元素选择器：匹配所有紧接在 prev 元素后的 next 元素
	语法：prev + next 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2b3628284eae423fbc5528d3d6a1806a.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
4）兄弟元素选择器：匹配 prev 元素之后的所有 siblings 元素
	语法：prev ~ sibings 
	找到所有在pre标签之后的且与pre标签同辈的标签
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/9d644c2421b242c8979cf1094fc00f96.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_13,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
练习
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>Untitled Document</title>
		<style type="text/css">
			div, span, p {
			    width: 140px;
			    height: 140px;
			    margin: 5px;
			    background: #aaa;
			    border: #000 1px solid;
			    float: left;
			    font-size: 17px;
			    font-family: Verdana;
			}

			div.mini {
			    width: 55px;
			    height: 55px;
			    background-color: #aaa;
			    font-size: 12px;
			}

			div.hide {
			    display: none;
			}
		</style>

		<script type="text/javascript" src="../jquery-3.6.0.js"></script>
		<script type="text/javascript">

			$(document).ready(function(){//这个是 $(function(){});的全写形式

				//1.选择 body 内的所有 div 元素
				$("#btn1").click(function(){
					$("body div").css("background", "#bbffaa");
				});

				//2.在 body 内, 选择div子元素
				$("#btn2").click(function(){
					$("body > div").css("background", "#bbffaa");
				});

				//3.选择 id 为 one 的下一个 div 元素
				$("#btn3").click(function(){
					$("#one + div").css("background", "#bbffaa");
				});

				//4.选择 id 为 two 的元素后面的所有 div 兄弟元素
				$("#btn4").click(function(){
					$("#two ~ div").css("background", "#bbffaa");
				});

			});

		</script>
	</head>
	<body>

		<input type="button" value="选择 body 内的所有 div 元素" id="btn1" />
		<input type="button" value="在 body 内, 选择div子元素" id="btn2" />
		<input type="button" value="选择 id 为 one 的下一个 div 元素" id="btn3" />
		<input type="button" value="选择 id 为 two 的元素后面的所有 div 兄弟元素" id="btn4" />
		<br><br>
		<div class="one" id="one">
			id 为 one,class 为 one 的div
			<div class="mini">class为mini</div>
		</div>
		<div class="one" id="two" title="test">
			id为two,class为one,title为test的div
			<div class="mini" title="other">class为mini,title为other</div>
			<div class="mini" title="test">class为mini,title为test</div>
		</div>
		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini"></div>
		</div>
		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini" title="tesst">class为mini,title为tesst</div>
		</div>
		<div style="display:none;" class="none">style的display为"none"的div</div>
		<div class="hide">class为"hide"的div</div>
		<div>
			包含input的type为"hidden"的div<input type="hidden" size="8">
		</div>
		<span id="span">^^span元素^^</span>
	</body>
</html>


```

### （3）过滤选择器

```
具体方法看文档
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/5e3ac9967c1c403fa918b2a55f86d5a2.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
元素就是标签

1）基本过滤选择器
	:first 获取第一个元素
	:last 获取最后个元素
	:not(selector) 去除所有与给定选择器匹配的元素
	:even 匹配所有索引值为偶数的元素，从 0 开始计数
	:odd 匹配所有索引值为奇数的元素，从 0 开始计数
	:eq(index) 匹配一个给定索引值的元素
	:gt(index) 匹配所有大于给定索引值的元素
	:lt(index) 匹配所有小于给定索引值的元素
	:header 匹配如 h1, h2, h3 之类的标题元素
	:animated 匹配所有正在执行动画效果的元素

练习：
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>Untitled Document</title>
		<style type="text/css">
			div, span, p {
			    width: 140px;
			    height: 140px;
			    margin: 5px;
			    background: #aaa;
			    border: #000 1px solid;
			    float: left;
			    font-size: 17px;
			    font-family: Verdana;
			}

			div.mini {
			    width: 55px;
			    height: 55px;
			    background-color: #aaa;
			    font-size: 12px;
			}

			div.hide {
			    display: none;
			}
		</style>
		<script type="text/javascript" src="../jquery-3.6.0.js"></script>
		<script type="text/javascript">

      //动画
			$(document).ready(function(){
				function anmateIt(){
					$("#mover").slideToggle("slow", anmateIt);
				}
				anmateIt();
			});

			$(document).ready(function(){
				//1.选择第一个 div 元素
				$("#btn1").click(function(){
					$("div:first").css("background", "#bbffaa");
				});

				//2.选择最后一个 div 元素
				$("#btn2").click(function(){
					$("div:last").css("background", "#bbffaa");
				});

				//3.选择class不为 one 的所有 div 元素
				$("#btn3").click(function(){
					$("div:not(.one)").css("background", "#bbffaa");
				});

				//4.选择索引值为偶数的 div 元素
				$("#btn4").click(function(){
					$("div:even").css("background", "#bbffaa");
				});

				//5.选择索引值为奇数的 div 元素
				$("#btn5").click(function(){
					$("div:odd").css("background", "#bbffaa");
				});

				//6.选择索引值为大于 3 的 div 元素
				$("#btn6").click(function(){
					$("div:gt(3)").css("background", "#bbffaa");
				});

				//7.选择索引值为等于 3 的 div 元素
				$("#btn7").click(function(){
					$("div:eq(3)").css("background", "#bbffaa");
				});

				//8.选择索引值为小于 3 的 div 元素
				$("#btn8").click(function(){
					$("div:lt(3)").css("background", "#bbffaa");
				});

				//9.选择所有的标题元素,给页面内所有标题加上背景色
				$("#btn9").click(function(){
					$(":header").css("background", "#bbffaa");
				});

				//10.选择当前正在执行动画的所有元素, 加上背景色
				$("#btn10").click(function(){
					$(":animated").css("background", "#bbffaa");
				});

			    //11.选择没有执行动画的最后一个div, 加上背景色 (多个功能共同使用)
        		$("#btn11").click(function(){
          			$("div:not(:animated):last").css("background", "#bbffaa");
        		});
        		
			});
		</script>
	</head>
	<body>

		<input type="button" value="选择第一个 div 元素" id="btn1" />
		<input type="button" value="选择最后一个 div 元素" id="btn2" />
		<input type="button" value="选择class不为 one 的所有 div 元素" id="btn3" />
		<input type="button" value="选择索引值为偶数的 div 元素" id="btn4" />
		<input type="button" value="选择索引值为奇数的 div 元素" id="btn5" />
		<input type="button" value="选择索引值为大于 3 的 div 元素" id="btn6" />
		<input type="button" value="选择索引值为等于 3 的 div 元素" id="btn7" />
		<input type="button" value="选择索引值为小于 3 的 div 元素" id="btn8" />
		<input type="button" value="选择所有的标题元素" id="btn9" />
		<input type="button" value="选择当前正在执行动画的所有元素" id="btn10" />
		<input type="button" value="选择没有执行动画的最后一个div" id="btn11" />

		<h3>基本选择器.</h3>
		<br><br>
		<div class="one" id="one">
			id 为 one,class 为 one 的div
			<div class="mini">class为mini</div>
		</div>
		<div class="one" id="two" title="test">
			id为two,class为one,title为test的div
			<div class="mini" title="other">class为mini,title为other</div>
			<div class="mini" title="test">class为mini,title为test</div>
		</div>
		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini"></div>
		</div>
		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini" title="tesst">class为mini,title为tesst</div>
		</div>
		<div style="display:none;" class="none">style的display为"none"的div</div>
		<div class="hide">class为"hide"的div</div>
		<div>
			包含input的type为"hidden"的div<input type="hidden" size="8">
		</div>
		<div id="mover">正在执行动画的div元素.</div>
	</body>
</html>


```

```
2）内容过滤选择器
	:contains(text) 匹配包含给定文本的元素
	:empty 匹配所有不包含子元素或者文本的空元素 （空）
	:parent 匹配含有子元素或者文本的元素 （非空）
	:has(selector) 匹配含有选择器所匹配的元素的元素
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20778ac68e454c0fb930fa6aba1a115c.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
-----
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/d9bb7e9ef5ec4f0098df2c54dde37f66.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_17,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
-----
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/060b672f398a4f88a63f2c6cfb3c93e4.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
-----
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/e56b7429f23b4f1f96dfbb7964339a37.bmp?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center)

```
练习：
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>Untitled Document</title>
		<style type="text/css">
			div, span, p {
			    width: 140px;
			    height: 140px;
			    margin: 5px;
			    background: #aaa;
			    border: #000 1px solid;
			    float: left;
			    font-size: 17px;
			    font-family: Verdana;
			}

			div.mini {
			    width: 55px;
			    height: 55px;
			    background-color: #aaa;
			    font-size: 12px;
			}

			div.hide {
			    display: none;
			}
		</style>
		<script type="text/javascript" src="../jquery-3.6.0.js"></script>
		<script type="text/javascript">

			$(document).ready(function(){
				function anmateIt(){
					$("#mover").slideToggle("slow", anmateIt);
				}

				anmateIt();
			});

			/**
			:contains(text)
			:empty
			:has(selector)
			:parent
			*/
			$(document).ready(function(){
				//1.选择 含有文本 'di' 的 div 元素
				$("#btn1").click(function(){
					$("div:contains(di)").css("background", "#bbffaa");
				});

				//2.选择不包含子元素(或者文本元素) 的 div 空元素
				$("#btn2").click(function(){
					$("div:empty").css("background", "#bbffaa");
				});

				//3.选择含有 class 为 mini 元素的 div 元素
				$("#btn3").click(function(){
					$("div:has(.mini)").css("background", "#bbffaa");
				});

				//4.选择含有子元素(或者文本元素)的div元素
				$("#btn4").click(function(){
					$("div:parent").css("background", "#bbffaa");
				});
			});
		</script>
	</head>
	<body>
		<input type="button" value="选择 含有文本 'di' 的 div 元素" id="btn1" />
		<input type="button" value="选择不包含子元素(或者文本元素) 的 div 空元素" id="btn2" />
		<input type="button" value="选择含有 class 为 mini 元素的 div 元素" id="btn3" />
		<input type="button" value="选择含有子元素(或者文本元素)的div元素" id="btn4" />

		<br><br>
		<div class="one" id="one">
			id 为 one,class 为 one 的div
			<div class="mini">class为mini</div>
		</div>
		<div class="one" id="two" title="test">
			id为two,class为one,title为test的div
			<div class="mini" title="other">class为mini,title为other</div>
			<div class="mini" title="test">class为mini,title为test</div>
		</div>
		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini"></div>
		</div>
		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini" title="tesst">class为mini,title为tesst</div>
		</div>
		<div style="display:none;" class="none">style的display为"none"的div</div>
		<div class="hide">class为"hide"的div</div>
		<div>
			包含input的type为"hidden"的div<input type="hidden" size="8">
		</div>
		<div id="mover">正在执行动画的div元素.</div>
	</body>
</html>


```

```
3）可见性过滤选择器	
:hidden  匹配所有不可见元素或者type为hidden的元素
:visible 匹配所有可见元素
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/a75b3a8f4451436e92ee9b75d09fb26f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_20,color_FFFFFF,t_70,g_se,x_16)

```
---
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/d69d68256fac45cb9d8ec5f092710796.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_16,color_FFFFFF,t_70,g_se,x_16)

```
练习：
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>Untitled Document</title>
		<style type="text/css">
			div, span, p {
			    width: 140px;
			    height: 140px;
			    margin: 5px;
			    background: #aaa;
			    border: #000 1px solid;
			    float: left;
			    font-size: 17px;
			    font-family: Verdana;
			}

			div.mini {
			    width: 55px;
			    height: 55px;
			    background-color: #aaa;
			    font-size: 12px;
			}

			div.hide {
			    display: none;
			}
		</style>
		<script type="text/javascript" src="../jquery-3.6.0.js"></script>
		<script type="text/javascript">
			$(document).ready(function(){
				function anmateIt(){
					$("#mover").slideToggle("slow", anmateIt);
				}

				anmateIt();
			});
			/**
				:hidden
				:visible
			*/
			$(document).ready(function(){
				//1.选取所有可见的  div 元素
				$("#btn1").click(function(){
					$("div:visible").css("background", "#bbffaa");
				});

				//2.选择所有不可见的 div 元素
				//不可见：display属性设置为none，或visible设置为hidden
				$("#btn2").click(function(){
					$("div:hidden").show("slow").css("background", "#bbffaa");
				});

				//3.选择所有不可见的 input 元素
				$("#btn3").click(function(){
					alert($("input:hidden").attr("value"));
				});
			});
		</script>
	</head>
	<body>
		<input type="button" value="选取所有可见的  div 元素" id="btn1">
		<input type="button" value="选择所有不可见的 div 元素" id="btn2" />
		<input type="button" value="选择所有不可见的 input 元素" id="btn3" />

		<br>
		<div class="one" id="one">
			id 为 one,class 为 one 的div
			<div class="mini">class为mini</div>
		</div>
		<div class="one" id="two" title="test">
			id为two,class为one,title为test的div
			<div class="mini" title="other">class为mini,title为other</div>
			<div class="mini" title="test">class为mini,title为test</div>
		</div>
		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini"></div>
		</div>
		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini" title="tesst">class为mini,title为tesst</div>
		</div>
		<div style="display:none;" class="none">style的display为"none"的div</div>
		<div class="hide">class为"hide"的div</div>
		<div>
			包含input的type为"hidden"的div<input type="hidden" value="123456789" size="8">
		</div>
		<div id="mover">正在执行动画的div元素.</div>
	</body>
</html>


```

```
4）属性过滤选择器
	[attribute] 匹配包含给定属性的元素。
	[attribute=value] 匹配给定的属性是某个特定值的元素
	[attribute!=value] 匹配所有不含有指定的属性，或者属性不等于特定值的元素。
	[attribute^=value] 匹配给定的属性是以某些值开始的元素
	[attribute$=value] 匹配给定的属性是以某些值结尾的元素
	[attribute*=value] 匹配给定的属性是以包含某些值的元素
	[attrSel1][attrSel2][attrSelN] 复合属性选择器，需要同时满足多个条件时使用。

练习：
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Untitled Document</title>
<style type="text/css">
div,span,p {
	width: 140px;
	height: 140px;
	margin: 5px;
	background: #aaa;
	border: #000 1px solid;
	float: left;
	font-size: 17px;
	font-family: Verdana;
}

div.mini {
	width: 55px;
	height: 55px;
	background-color: #aaa;
	font-size: 12px;
}

div.hide {
	display: none;
}
</style>
<script type="text/javascript" src="../jquery-3.6.0.js"></script>
<script type="text/javascript">
	/**
[attribute]
[attribute=value]
[attribute!=value]
[attribute^=value]
[attribute$=value]
[attribute*=value]
[attrSel1][attrSel2][attrSelN]


	*/
	$(function() {
		//1.选取含有 属性title 的div元素
		$("#btn1").click(function() {
			$("div[title]").css("background", "#bbffaa");
		});
		//2.选取 属性title值等于'test'的div元素
		$("#btn2").click(function() {
			$("div[title='test']").css("background", "#bbffaa");
		});
		//3.选取 属性title值不等于'test'的div元素(*没有属性title的也将被选中)
		$("#btn3").click(function() {
			$("div[title!='test']").css("background", "#bbffaa");
		});
		//4.选取 属性title值 以'te'开始 的div元素
		$("#btn4").click(function() {
			$("div[title^='te']").css("background", "#bbffaa");
		});
		//5.选取 属性title值 以'est'结束 的div元素
		$("#btn5").click(function() {
			$("div[title$='est']").css("background", "#bbffaa");
		});
		//6.选取 属性title值 含有'es'的div元素
		$("#btn6").click(function() {
			$("div[title*='est']").css("background", "#bbffaa");
		});

		//7.首先选取有属性id的div元素，然后在结果中 选取属性title值 含有'es'的 div 元素
		$("#btn7").click(function() {
			$("div[id][title*='es']").css("background", "#bbffaa");
		});
		//8.选取 含有 title 属性值, 且title 属性值不等于 test 的 div 元素
		$("#btn8").click(function() {
			$("div[title][title!='test']").css("background", "#bbffaa");
		});
	});
</script>
</head>
<body>
	<input type="button" value="选取含有 属性title 的div元素." id="btn1" />
	<input type="button" value="选取 属性title值等于'test'的div元素." id="btn2" />
	<input type="button"
		value="选取 属性title值不等于'test'的div元素(没有属性title的也将被选中)." id="btn3" />
	<input type="button" value="选取 属性title值 以'te'开始 的div元素." id="btn4" />
	<input type="button" value="选取 属性title值 以'est'结束 的div元素." id="btn5" />
	<input type="button" value="选取 属性title值 含有'es'的div元素." id="btn6" />
	<input type="button"
		value="组合属性选择器,首先选取有属性id的div元素，然后在结果中 选取属性title值 含有'es'的 div 元素."
		id="btn7" />
	<input type="button"
		value="选取 含有 title 属性值, 且title 属性值不等于 test 的 div 元素." id="btn8" />

	<br>
	<br>
	<div class="one" id="one">
		id 为 one,class 为 one 的div
		<div class="mini">class为mini</div>
	</div>
	<div class="one" id="two" title="test">
		id为two,class为one,title为test的div
		<div class="mini" title="other">class为mini,title为other</div>
		<div class="mini" title="test">class为mini,title为test</div>
	</div>
	<div class="one">
		<div class="mini">class为mini</div>
		<div class="mini">class为mini</div>
		<div class="mini">class为mini</div>
		<div class="mini"></div>
	</div>
	<div class="one">
		<div class="mini">class为mini</div>
		<div class="mini">class为mini</div>
		<div class="mini">class为mini</div>
		<div class="mini" title="tesst">class为mini,title为tesst</div>
	</div>
	<div style="display: none;" class="none">style的display为"none"的div</div>
	<div class="hide">class为"hide"的div</div>
	<div>
		包含input的type为"hidden"的div<input type="hidden" value="123456789"
			size="8">
	</div>
	<div id="mover">正在执行动画的div元素.</div>
</body>
</html>


```

```
5）表单过滤选择器
	:input 匹配所有 input, textarea, select 和 button 元素
	:text 匹配所有 文本输入框
	:password 匹配所有的密码输入框
	:radio 匹配所有的单选框
	:checkbox 匹配所有的复选框
	:submit 匹配所有提交按钮
	:image 匹配所有 img 标签
	:reset 匹配所有重置按钮
	:button 匹配所有 input type=button <button>按钮
	:file 匹配所有 input type=file 文件上传
	:hidden 匹配所有不可见元素 display:none 或 input type=hidden

6）表单对象属性过滤选择器
	:enabled 匹配所有可用元素
	:disabled 匹配所有不可用元素
	:checked 匹配所有选中的被选中元素(复选框、单选框等，不包括select中的option)
	:selected 匹配所有选中的 option

练习：
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>Untitled Document</title>
		<script type="text/javascript" src="../jquery-3.6.0.js"></script>
		<script type="text/javascript">
			$(function(){


		/**
		:input
		:text
		:password
		:radio
		:checkbox
		:submit
		:image
		:reset
		:button
		:file
		:hidden

		表单对象的属性
		:enabled
		:disabled
		:checked
		:selected
		*/


				//1.对表单内 可用的input(text) 进行赋值操作
				$("#btn1").click(function(){
		          //val()可以操作表单项的value属性值
 		          //它可以设置和获取
				  $(":text:enabled").val("New Value");
				});

				//2.对表单内 不可用的input(text) 进行赋值操作
				$("#btn2").click(function(){
					$(":text:disabled").val("New Value Too");
				});

				//3.获取复选框 选中的元素的个数
				$("#btn3").click(function(){
					alert($(":checkbox:checked").length);
				});

				//4.获取多选框，每个选中的元素的value值
				$("#btn4").click(function(){
		          //获取选中的复选框标签对象
		          var $checkboies = $(":checkbox:checked");
		
		          //老式遍历方法
		          for (var i = 0; i < $checkboies.length; i++) {
		            //$checkboies[i]--->dom对象
		            alert($checkboies[i].value);
		          }
		
		          //新的遍历方法
		
		          //each方法是jQuery对象提供用来遍历的方法
		          $checkboies.each(function (){
		            //在遍历的function函数种，有一个this对象，就是当前遍历到的dom对象
		            alert(this.value);
		          });

				});

				//5.获取下拉框中选中的内容
				$("#btn5").click(function(){
		          //获取选中的option标签对象
		          //层次选择器+对象属性过滤选择器
		          var $option = $("select option:selected");
		          //遍历获取option标签中的文本内容
		          $option.each(function (){
		            alert(this.innerHTML);
		          });

				});

			})
		</script>
	</head>
	<body>
		<h3>表单对象属性过滤选择器</h3>
		 <button id="btn1">对表单内 可用input 赋值操作.</button>
  		 <button id="btn2">对表单内 不可用input 赋值操作.</button><br /><br />
		 <button id="btn3">获取多选框选中的个数.</button>
		 <button id="btn4">获取多选框选中的内容.</button><br /><br />
         <button id="btn5">获取下拉框选中的内容.</button><br /><br />

		<form id="form1" action="#">
			可用元素: <input name="add" value="可用文本框1"/><br>
			不可用元素: <input name="email" disabled="disabled" value="不可用文本框"/><br>
			可用元素: <input name="che" value="可用文本框2"/><br>
			不可用元素: <input name="name" disabled="disabled" value="不可用文本框"/><br>
			<br>

			多选框: <br>
			<input type="checkbox" name="newsletter" checked="checked" value="test1" />test1
			<input type="checkbox" name="newsletter" value="test2" />test2
			<input type="checkbox" name="newsletter" value="test3" />test3
			<input type="checkbox" name="newsletter" checked="checked" value="test4" />test4
			<input type="checkbox" name="newsletter" value="test5" />test5

			<br><br>
			下拉列表1: <br>
			<select name="test" multiple="multiple" style="height: 100px" id="sele1">
				<option>浙江</option>
				<option selected="selected">辽宁</option>
				<option>北京</option>
				<option selected="selected">天津</option>
				<option>广州</option>
				<option>湖北</option>
			</select>

			<br><br>
			下拉列表2: <br>
			<select name="test2">
				<option>浙江</option>
				<option>辽宁</option>
				<option selected="selected">北京</option>
				<option>天津</option>
				<option>广州</option>
				<option>湖北</option>
			</select>
		</form>
	</body>
</html>


```

## 006-jQuery 元素筛选

### 通过jQuery对象的方法获得相应的jQuery元素对象

![在这里插入图片描述](https://img-blog.csdnimg.cn/5f57ff5fb03b4abcb89e45ac4bda359a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_15,color_FFFFFF,t_70,g_se,x_16)

```
eq() 			获取给定索引的元素 		功能跟 :eq() 一样
first() 		获取第一个元素 			功能跟 :first 一样
last() 			获取最后一个元素 			功能跟 :last 一样
filter(exp) 	留下匹配的元素
is(exp) 		判断是否匹配给定的选择器，只要有一个匹配就返回，true
has(exp) 		返回包含有匹配选择器的元素的元素 			功能跟 :has 一样

not(exp) 		删除匹配选择器的元素 						功能跟 :not 一样
children(exp) 	返回匹配给定选择器的子元素 					功能跟 parent>child 一样
find(exp) 		返回匹配给定选择器的后代元素 				功能跟 ancestor descendant 一样
next() 			返回当前元素的下一个兄弟元素 				功能跟 prev + next 功能一样
nextAll() 		返回当前元素后面所有的兄弟元素 				功能跟 prev ~ siblings 功能一样
nextUntil() 	查找当前元素之后所有的同辈元素，直到遇到匹配的那个元素为止。
parent() 		返回父元素
prev(exp) 		返回与当前元素相邻的前一个兄弟元素
prevAll() 		返回当前元素前面所有的兄弟元素
prevUntil(exp) 	查找当前元素之前所有的同辈元素，直到遇到匹配的那个元素为止。
siblings(exp) 	返回所有兄弟元素

add() 			把与表达式匹配的元素添加到jQuery对象中。
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>DOM查询</title>
		<style type="text/css">
			div, span, p {
			    width: 140px;
			    height: 140px;
			    margin: 5px;
			    background: #aaa;
			    border: #000 1px solid;
			    float: left;
			    font-size: 17px;
			    font-family: Verdana;
			}

			div.mini {
			    width: 55px;
			    height: 55px;
			    background-color: #aaa;
			    font-size: 12px;
			}

			div.hide {
			    display: none;
			}
		</style>
		<script type="text/javascript" src="../jquery-3.6.0.js"></script>
		<script type="text/javascript">
			$(document).ready(function(){
				function anmateIt(){
					$("#mover").slideToggle("slow", anmateIt);
				}
				anmateIt();

	/**

	过滤
	eq(index|-index)
	first()
	last()
	hasClass(class)
	filter(expr|obj|ele|fn)
	is(expr|obj|ele|fn)1.6*
	has(expr|ele)
	not(expr|ele|fn)
	slice(start,[end])

	查找
	children([expr])
	closest(expr,[con]|obj|ele)1.6*
	find(expr|obj|ele)
	next([expr])
	nextall([expr])
	nextUntil([exp|ele][,fil])1.6*
	parent([expr])
	parents([expr])
	parentsUntil([exp|ele][,fil])1.6*
	prev([expr])
	prevall([expr])
	prevUntil([exp|ele][,fil])1.6*
	siblings([expr])

	串联
	add(expr|ele|html|obj[,con])


	*/

				//(1)eq()  选择索引值为等于 3 的 div 元素
				$("#btn1").click(function(){
					$("div").eq(3).css("background-color","#bfa");
				});

				//(2)first()选择第一个 div 元素
				 $("#btn2").click(function(){
					 //first()   选取第一个元素
					$("div").first().css("background-color","#bfa");
				});

				//(3)last()选择最后一个 div 元素
				$("#btn3").click(function(){
					//last()  选取最后一个元素
					$("div").last().css("background-color","#bfa");
				});

				//(4)filter()在div中选择索引为偶数的
				$("#btn4").click(function(){
					//filter()  过滤   传入的是选择器字符串
					$("div").filter(":even").css("background-color","#bfa");
				});

				 //(5)is()判断#one是否为空
				//is用来检测jq对象是否符合指定的选择器
				$("#btn5").click(function(){
          alert($("#one").is(":empty"));
				});

				//(6)has()选择div中包含.mini的
				$("#btn6").click(function(){
					//has(selector)  选择器字符串    是否包含selector
					$("div").has(".mini").css("background-color","#bfa");
				});

				//(7)not()选择div中class不为one的
				$("#btn7").click(function(){
					//not(selector)  选择不是selector的元素
					$("div").not(".one").css("background-color","#bfa");
				});

				//(8)children()在body中选择所有class为one的div子元素
				$("#btn8").click(function(){
					//children()  选出所有的子元素
					$("body").children("div[class='one']").css("background-color","#bfa");
				});


				//(9)find()在body中选择所有class为mini的div元素
				$("#btn9").click(function(){
					//find()  选出所有的后代元素
					$("body").find("div[class='mini']").css("background-color","#bfa");
				});

				//(10)#one的下一个div
				$("#btn10").click(function(){
					//next()  选择下一个兄弟元素
					$("#one").next("div").css("background-color","#bfa");
				});

				//(11)nextAll() #one后面所有的span元素
				$("#btn11").click(function(){
					//nextAll()   选出后面所有的元素
					$("#one").nextAll("span").css("background-color","#bfa");
				});

				//(12)nextUntil() #one和span之间的元素
				$("#btn12").click(function(){
					//
					$("#one").nextUntil("span").css("background-color","#bfa")
				});

				//(13)parent() .mini的父元素
				$("#btn13").click(function(){
					$(".mini").parent().css("background-color","#bfa");
				});

				//(14)prev() #two的上一个div
				$("#btn14").click(function(){
					//prev()
					$("#two").prev("div").css("background-color","#bfa")
				});

				//(15)prevAll() span前面所有的div
				$("#btn15").click(function(){
					//prevAll()   选出前面所有的元素
					$("span").prevAll("div").css("background-color","#bfa")
				});

				//(16)prevUntil() span向前直到#one的元素
				$("#btn16").click(function(){
					//prevUntil(exp)   找到之前所有的兄弟元素直到找到exp停止
					$("span").prevUntil("#one").css("background-color","#bfa")
				});

				//(17)siblings() #two的所有兄弟元素
				$("#btn17").click(function(){
					//siblings()    找到所有的兄弟元素，包括前面的和后面的
					$("#two").siblings().css("background-color","#bfa")
				});

				//(18)add() 选择所有的 span 元素和id为two的元素
				$("#btn18").click(function(){
					$("span").add("#two").css("background-color","#bfa");
				});

			});

		</script>
	</head>
	<body>
		<input type="button" value="eq()选择索引值为等于 3 的 div 元素" id="btn1" />
		<input type="button" value="first()选择第一个 div 元素" id="btn2" />
		<input type="button" value="last()选择最后一个 div 元素" id="btn3" />
		<input type="button" value="filter()在div中选择索引为偶数的" id="btn4" />
		<input type="button" value="is()判断#one是否为:empty或:parent" id="btn5" />
		<input type="button" value="has()选择div中包含.mini的" id="btn6" />
		<input type="button" value="not()选择div中class不为one的" id="btn7" />
		<input type="button" value="children()在body中选择所有class为one的div子元素" id="btn8" />
		<input type="button" value="find()在body中选择所有class为mini的div后代元素" id="btn9" />
		<input type="button" value="next()#one的下一个div" id="btn10" />
		<input type="button" value="nextAll()#one后面所有的span元素" id="btn11" />
		<input type="button" value="nextUntil()#one和span之间的元素" id="btn12" />
		<input type="button" value="parent().mini的父元素" id="btn13" />
		<input type="button" value="prev()#two的上一个div" id="btn14" />
		<input type="button" value="prevAll()span前面所有的div" id="btn15" />
		<input type="button" value="prevUntil()span向前直到#one的元素" id="btn16" />
		<input type="button" value="siblings()#two的所有兄弟元素" id="btn17" />
		<input type="button" value="add()选择所有的 span 元素和id为two的元素" id="btn18" />


		<br /><br />
		文本框<input type="text" name="account" disabled="disabled" />
		<br><br>
		<div class="one" id="one">
			id 为 one,class 为 one 的div
			<div class="mini">class为mini</div>
		</div>
		<div class="one" id="two" title="test">
			id为two,class为one,title为test的div
			<div class="mini" title="other"><b>class为mini,title为other</b></div>
			<div class="mini" title="test">class为mini,title为test</div>
		</div>

		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini"></div>
		</div>
		<div class="one">
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini">class为mini</div>
			<div class="mini" title="tesst">class为mini,title为tesst</div>
		</div>
		<div style="display:none;" class="none">style的display为"none"的div</div>
		<div class="hide">class为"hide"的div</div>
		<span id="span1">^^span元素 111^^</span>
		<div>
			包含input的type为"hidden"的div<input type="hidden" size="8">
		</div>
		<span id="span2">^^span元素 222^^</span>
		<div id="mover">正在执行动画的div元素.</div>
	</body>
</html>

```

## 007-关于jQuery对象属性的相关方法

```
html() 它可以设置和获取起始标签和结束标签中的内容。 	跟 dom 的属性 innerHTML 一样。
text() 它可以设置和获取起始标签和结束标签之间的文本。 	跟 dom 的属性 innerText 一样。
val()  它可以设置和获取 表单项 的 value 属性值。 	跟 dom 的属性 value 一样
val()  还可以设置单选框、复选框、下拉框的选中状态
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>html()-text()-val()</title>

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>
  <script type="text/javascript">
    $(function (){

      //不传参数是获取，传参数是设置
      //alert($("div").html());//我是div标签 <span>我是div中的span标签
      //$("div").html("<h1>我是div中的标题1</h1>");//我是div中的标题1

      //不传参数是获取，传参数是设置
      //alert($("div").text());//我是div标签 我是div中的span标签
      //$("div").text("<h1>我是div中的标题1</h1>");//<h1>我是div中的标题1</h1>

      //不传参数是获取，传参数是设置
      $("button").click(function (){
        alert($("#username").val());
        $("#username").val("超级程序员");
      });

      

      //val()还可以设置单选框、复选框、下拉框的选中状态

      //设置单选框的radio1选中
      $(":radio").val(["radio1"]);

      //设置复选框全选
      $(":checkbox").val(["checkbox1","checkbox2","checkbox3"]);

      //设置下拉多选框 选中mul2和mul4
      $("#multiple").val(["mul2","mul4"]);

      //设置下拉单选框 选中sin1
      $("#single").val(["sin1"]);


      //也可以一次性设置完
      //$(":radio,:checkbox,#multiple,#single").val(["radio1","checkbox1","checkbox2","checkbox3","mul2","mul4","sin1"]);

    });
  </script>

</head>
<body>
  <div>我是div标签 <span>我是div中的span标签</span></div>

  <input type="text" name="username" id="username">
  <button>操作输入框</button>

  <br>
  <br>

  单选：
  <input name="radio" type="radio" value="radio1" />radio1
  <input name="radio" type="radio" value="radio2" />radio2
  <br>
  多选：
  <input name="checkbox" type="checkbox" value="checkbox1" />checkbox1
  <input name="checkbox" type="checkbox" value="checkbox2" />checkbox2
  <input name="checkbox" type="checkbox" value="checkbox3" />checkbox3
  <br>
  下拉多选：
  <!--设置multiple属性就可以多选，按ctrl键即可-->
  <select id="multiple" multiple="multiple" size="4">
    <option value="mul1">mul1</option>
    <option value="mul2">mul2</option>
    <option value="mul3">mul3</option>
    <option value="mul4">mul4</option>
  </select>
  <br>
  下拉单选：
  <select id="single">
    <option value="sin1">sin1</option>
    <option value="sin2">sin2</option>
    <option value="sin3">sin3</option>
  </select>

</body>
</html>

```

```
attr() 可以设置和获取属性的值，不推荐操作 checked、readOnly、selected、disabled 等等
attr 方法还可以操作非标准的属性。比如自定义属性：abc,bbj

prop() 可以设置和获取属性的值,只推荐操作 checked、readOnly、selected、disabled 等
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>attr()-prop()</title>

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>
  <script type="text/javascript">

    $(function (){
      //attr(属性，属性值)方法
      alert($(":checkbox:first").attr("name"));//获得属性值：获得checkbox1的name属性的值checkbox
      $(":checkbox:first").attr("name","abc")//设置属性值：checkbox1的name属性值变成了abc

      //attr()方法不适用checked、readOnly、selected、disabled等属性操作
      alert($(":checkbox:first").attr("checked"));//返回undefined

      //prop()方法仅适用checked、readOnly、selected、disabled等属性操作
      alert($(":checkbox:first").prop("checked"));//返回false未选中
      $(":checkbox:first").prop("checked",true);//设置checkbox1默认选中


      //attr()方法还可以操作非标准的属性。比如自定义属性：abc,bbj等
      //给checkbox1设置一个属性xxx,属性值为yyy
      $(":checkbox:first").attr("xxx","yyy");
      //设置之后也能获取出来
      alert($(":checkbox:first").attr("xxx"));

    });

  </script>

</head>
<body>

  <input name="checkbox" type="checkbox" value="checkbox1" />checkbox1
  <input name="checkbox" type="checkbox" value="checkbox2" />checkbox2

</body>
</html>

```

## 008-全选、全不选、反选练习

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title></title>

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>
  <script type="text/javascript">

    $(function(){
      //全选按钮
      $("#checkedAllBtn").click(function (){
        $(":checkbox").prop("checked",true);
      });

      //全不选按钮
      $("#checkedNoBtn").click(function (){
        $(":checkbox").prop("checked",false);
      });

      //反选按钮
      $("#checkedRevBtn").click(function (){
        //获取所有球类复选框(不包括你爱好的运动是？那个复选框)
        $(":checkbox[name='items']").each(function (){
          //遍历判断
          //each遍历的function函数中，有一个this对象，这个this对象就是当前正在遍历的dom对象
          this.checked = !this.checked;
        });

        //要检查，是否满选

        //如果全部选中，你爱好的运动是？那个复选框也要选中，否则不选中
        //获取全部的球类个数
        var allCount =  $(":checkbox[name='items']").length;
        //获取选中的球类个数
        var checkedCount =  $(":checkbox[name='items']:checked").length;

        if (allCount == checkedCount){
          $("#checkedAllBox").prop("checked",true);
        }else {
          $("#checkedAllBox").prop("checked",false);
        }

        //一行代码写完
        //$("#checkedAllBox").prop("checked",allCount == checkedCount);
      });


      //提交按钮
      $("#sendBtn").click(function (){
        $(":checkbox[name='items']").each(function (){
            alert(this.value);
        });
      });


      //给你爱好的运动是？那个复选框绑定单击事件
      $("#checkedAllBox").click(function (){
        //在事件的function函数中，有一个this对象，该this对象就是当前正在响应事件的dom对象
        //在这里就是给你爱好的运动是？那个复选框对象
        //这个复选框一点击，下面的复选框应该都选上
        //这个复选框一选中，它的checked属性就变为true，即this.checked=true
        //这个复选框一不选中，它的checked属性就变为false，即this.checked=false
        $(":checkbox[name='items']").prop("checked",this.checked);
      });


      //给全部球类绑定单击事件
      //如果全部选中，你爱好的运动是？那个复选框也要选中，否则不选中
      //同上面的反选中的检查
      $(":checkbox[name='items']").click(function (){
        var allCount =  $(":checkbox[name='items']").length;
        var checkedCount =  $(":checkbox[name='items']:checked").length;

        $("#checkedAllBox").prop("checked",allCount == checkedCount);
      });

    });

  </script>
</head>
<body>

<form method="post" action="">

  你爱好的运动是？<input type="checkbox" id="checkedAllBox" />全选/全不选

  <br>
  <input type="checkbox" name="items" value="足球" />足球
  <input type="checkbox" name="items" value="篮球" />篮球
  <input type="checkbox" name="items" value="羽毛球" />羽毛球
  <input type="checkbox" name="items" value="乒乓球" />乒乓球
  <br>
  <input type="button" id="checkedAllBtn" value="全　选" />
  <input type="button" id="checkedNoBtn" value="全不选" />
  <input type="button" id="checkedRevBtn" value="反　选" />
  <input type="button" id="sendBtn" value="提　交" />

</form>

</body>
</html>

```

## 009-DOM对象(标签)的增删改操作

![在这里插入图片描述](https://img-blog.csdnimg.cn/3bba3602096e46c08846e9d0403f43bc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBATlRVX0FsZXhf,size_10,color_FFFFFF,t_70,g_se,x_16)

```
内部插入：
appendTo() 	a.appendTo(b) 	把 a 插入到 b的 子元素末尾，成为b的最后一个子元素
prependTo() a.prependTo(b) 把 a 插到 b 所有子元素前面，成为第一个子元素

外部插入：
insertAfter() 	a.insertAfter(b) 	得到 ba
insertBefore() 	a.insertBefore(b) 	得到 ab

替换:
replaceWith() 	a.replaceWith(b) 	用 b 替换掉 a
replaceAll() 	a.replaceAll(b) 	用 a 替换掉所有 b

删除：
remove() 	a.remove(); 	删除 a 标签
empty() 	a.empty(); 		清空 a 标签里的内容(清空标签里的子节点包括文本)
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>
  <script type="text/javascript">

    $(function (){
      //创建一个标签对象h1-->$("<h1>标题</h1>")

      //内部插入
      //a.appendTo(b) 	把 a 插入到 b的 子元素末尾，成为b的最后一个子元素
      $("<h1>标题1</h1>").appendTo("div");//也可以.appendTo($("div")) 参数可以是一个选择器

      //a.prependTo(b) 把 a 插到 b 所有子元素前面，成为第一个子元素
      $("<h2>标题2</h2>").prependTo("div");


      //外部插入
      //a.insertAfter(b) 	得到 ba
      $("<h3>标题3</h3>").insertAfter("div");

      //a.insertBefore(b) 	得到 ab
      $("<h4>标题4</h4>").insertBefore("div");


      //替换
      //a.replaceWith(b) 	用 b 替换掉 a
      //$("div").replaceWith("<h5>标题5</h5>");

      //a.replaceAll(b) 	用 a 替换掉 b
      //$("<h6>标题6</h6>").replaceAll("div");


      //删除
      //a.remove() 	删除 a 标签
      //$("div").remove();

      //a.empty() 	清空 a 标签里的内容(清空标签里的子节点包括文本)
      //$("div").empty();

    });

  </script>

</head>
<body>

  <div>1234</div>
  <div>5678</div>

</body>
</html>

```

## 010-从左到右，从右到左练习

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title></title>

  <style type="text/css">
    select {
      width: 100px;
      height: 140px;
    }

    div {
      width: 130px;
      float: left;
      text-align: center;
    }
  </style>

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>
  <script type="text/javascript">

    $(function (){
      //第一个按钮(把选中的添加到右边)
      $("button:eq(0)").click(function (){
        //左边选中的option.appendTo(右边的select里面)
        //select:eq(0) (第一个下拉列表(即左边的下拉列表))
        //option:selected (option中被选中的option)
        //select:eq(0) option:selected (第一个下拉列表中被选中的option)
        $("select:eq(0) option:selected").appendTo($("select:eq(1)"));
      });

      //第二个按钮(把左边的全部添加到右边)
      $("button:eq(1)").click(function (){
        $("select:eq(0) option").appendTo($("select:eq(1)"));
      });

      //第三个按钮(把右边选中的添加到左边)
      $("button:eq(2)").click(function (){
        $("select:eq(1) option:selected").appendTo($("select:eq(0)"));
      });

      //第四个按钮(把右边的全部添加到左边)
      $("button:eq(3)").click(function (){
        $("select:eq(1) option").appendTo($("select:eq(0)"));
      });

    });

  </script>
</head>
<body>

<div id="left">
  <select multiple="multiple" name="sel01">
    <option value="opt01">选项1</option>
    <option value="opt02">选项2</option>
    <option value="opt03">选项3</option>
    <option value="opt04">选项4</option>
    <option value="opt05">选项5</option>
    <option value="opt06">选项6</option>
    <option value="opt07">选项7</option>
    <option value="opt08">选项8</option>
  </select>

  <button>选中添加到右边</button>
  <button>全部添加到右边</button>
</div>
<div id="rigth">
  <select multiple="multiple" name="sel02">
  </select>
  <button>选中删除到左边</button>
  <button>全部删除到左边</button>
</div>

</body>
</html>

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/076ca867a2394568afa87da6767681a0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_12,color_FFFFFF,t_70,g_se,x_16)

## 011-动态添加、删除表格的一行记录练习

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title></title>

  <link rel="stylesheet" type="text/css" href="04.css" />

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>
  <script type="text/javascript">

    $(function (){
      //添加新员工：填写完数据之后，点击submit，会添加一条新员工信息到上面表格中
      //类似于把以下代码写在<table></table>中：
/*      <table>
        <tr>
        ...
        </tr>

        <tr>
        <td>Bob</td>
        <td>bob@tom.com</td>
        <td>10000</td>
        <td><a href="deleteEmp?id=003">Delete</a></td>
        </tr>

        </table>
 */
      //给button按钮绑定单击事件
      $("#addEmpButton").click(function (){
        //创建一个行标签对象tr，添加到表格中

        //获得用户输入的内容
        //姓名
        var name = $("#empName").val();
        //邮箱
        var email = $("#email").val();
        //工资
        var salary = $("#salary").val();


        //创建一个行标签对象，先复制粘贴即可
        var $trObj = $("<tr>" +
          "<td>" + name + "</td>" +
          "<td>" + email + "</td>" +
          "<td>" + salary + "</td>" +
          "<td><a href=\"deleteEmp?id=003\">Delete</a></td>" +
          "</tr>");

        //添加到表格中
        $trObj.appendTo($("#employeeTable"));


        //给新添加的那条数据的a标签绑定单击事件
        $trObj.find("a").click(function (){
          var name = $trObj.find("td:first").text();
          if(confirm("你确定要删除"+ name +"吗？") == true){
            $trObj.remove();
          };

          return false;

        });

      });


      //点击Delete 删除表格中的一条数据
      //给a标签绑定单击事件
      $("a").click(function (){
        //点击Delete之后，弹出窗口询问是否确认删除


        //在事件响应的function函数中，有一个this对象，这个this对象就是当前正在响应事件的dom对象(即a标签对象)
        //而行标签对象是a标签对象的父元素的父元素
        var $trObj = $(this).parent().parent();

        //更详细一点：你确定删除xxxx吗？
        //xxxx怎么得到？
        var name = $trObj.find("td:first").text();


        //confirm()是JavaScript提供的一个确认提示框函数，你给它传什么，它就提示什么
        //当用户点击了确定，返回true;  点击取消，返回false
        //alert是警示框，没有确定取消按钮
        //更详细一点：你确定删除xxxx吗？
        if(confirm("你确定要删除"+ name +"吗？") == true){
          //确定删除
          //行对象.remove()
          //行对象就是tr对象，删除这一行就是把tr标签删除掉
          //如何得到这个行对象？看上面
          $trObj.remove();

        };


        //不想让a标签跳转
        //return false; 可以阻止元素的默认行为
        return false;

      });

    });


  </script>
</head>
<body>

<table id="employeeTable">
  <tr>
    <th>Name</th>
    <th>Email</th>
    <th>Salary</th>
    <th>&nbsp;</th>
  </tr>
  <tr>
    <td>Tom</td>
    <td>tom@tom.com</td>
    <td>5000</td>
    <td><a href="deleteEmp?id=001">Delete</a></td>
  </tr>
  <tr>
    <td>Jerry</td>
    <td>jerry@sohu.com</td>
    <td>8000</td>
    <td><a href="deleteEmp?id=002" >Delete</a></td>
  </tr>
  <tr>
    <td>Bob</td>
    <td>bob@tom.com</td>
    <td>10000</td>
    <td><a href="deleteEmp?id=003">Delete</a></td>
  </tr>
</table>

<div id="formDiv">

  <h4>添加新员工</h4>

  <table>
    <tr>
      <td class="word">name: </td>
      <td class="inp">
        <input type="text" name="empName" id="empName" />
      </td>
    </tr>
    <tr>
      <td class="word">email: </td>
      <td class="inp">
        <input type="text" name="email" id="email" />
      </td>
    </tr>
    <tr>
      <td class="word">salary: </td>
      <td class="inp">
        <input type="text" name="salary" id="salary" />
      </td>
    </tr>
    <tr>
      <td colspan="2" align="center">
        <button id="addEmpButton" value="abc">
          Submit
        </button>
      </td>
    </tr>
  </table>

</div>

</body>
</html>

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/6e7af7e39a1c4a9897d150c6acebf5c3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_16,color_FFFFFF,t_70,g_se,x_16)

```
代码优化：把相同的代码写在函数中
注意：这里的函数调用时不加()
deleteFun() -->错误
deleteFun --->正确函数调用
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title></title>

  <link rel="stylesheet" type="text/css" href="04.css" />

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>
  <script type="text/javascript">
	
	
		
    $(function (){

		var deleteFun = function(){
		//点击Delete之后，弹出窗口询问是否确认删除


        //在事件响应的function函数中，有一个this对象，这个this对象就是当前正在响应事件的dom对象(即a标签对象)
        //而行标签对象是a标签对象的父元素的父元素
        var $trObj = $(this).parent().parent();

        //更详细一点：你确定删除xxxx吗？
        //xxxx怎么得到？
        var name = $trObj.find("td:first").text();


        //confirm()是JavaScript提供的一个确认提示框函数，你给它传什么，它就提示什么
        //当用户点击了确定，返回true;  点击取消，返回false
        //alert是警示框，没有确定取消按钮
        //更详细一点：你确定删除xxxx吗？
        if(confirm("你确定要删除"+ name +"吗？") == true){
          //确定删除
          //行对象.remove()
          //行对象就是tr对象，删除这一行就是把tr标签删除掉
          //如何得到这个行对象？看上面
          $trObj.remove();

        };


        //不想让a标签跳转
        //return false; 可以阻止元素的默认行为
        return false;
	};



    
      //给button按钮绑定单击事件
      $("#addEmpButton").click(function (){
        //创建一个行标签对象tr，添加到表格中

        //获得用户输入的内容
        //姓名
        var name = $("#empName").val();
        //邮箱
        var email = $("#email").val();
        //工资
        var salary = $("#salary").val();


        //创建一个行标签对象，先复制粘贴即可
        var $trObj = $("<tr>" +
          "<td>" + name + "</td>" +
          "<td>" + email + "</td>" +
          "<td>" + salary + "</td>" +
          "<td><a href=\"deleteEmp?id=003\">Delete</a></td>" +
          "</tr>");

        //添加到表格中
        $trObj.appendTo($("#employeeTable"));


        //给新添加的那条数据的a标签绑定单击事件
        $trObj.find("a").click( deleteFun );

      });


      //点击Delete 删除表格中的一条数据
      //给a标签绑定单击事件
      $("a").click( deleteFun );

    });


  </script>
</head>
<body>

<table id="employeeTable">
  <tr>
    <th>Name</th>
    <th>Email</th>
    <th>Salary</th>
    <th>&nbsp;</th>
  </tr>
  <tr>
    <td>Tom</td>
    <td>tom@tom.com</td>
    <td>5000</td>
    <td><a href="deleteEmp?id=001">Delete</a></td>
  </tr>
  <tr>
    <td>Jerry</td>
    <td>jerry@sohu.com</td>
    <td>8000</td>
    <td><a href="deleteEmp?id=002" >Delete</a></td>
  </tr>
  <tr>
    <td>Bob</td>
    <td>bob@tom.com</td>
    <td>10000</td>
    <td><a href="deleteEmp?id=003">Delete</a></td>
  </tr>
</table>

<div id="formDiv">

  <h4>添加新员工</h4>

  <table>
    <tr>
      <td class="word">name: </td>
      <td class="inp">
        <input type="text" name="empName" id="empName" />
      </td>
    </tr>
    <tr>
      <td class="word">email: </td>
      <td class="inp">
        <input type="text" name="email" id="email" />
      </td>
    </tr>
    <tr>
      <td class="word">salary: </td>
      <td class="inp">
        <input type="text" name="salary" id="salary" />
      </td>
    </tr>
    <tr>
      <td colspan="2" align="center">
        <button id="addEmpButton" value="abc">
          Submit
        </button>
      </td>
    </tr>
  </table>

</div>

</body>
</html>

```

## 012-CSS 样式操作

```
addClass() 		给标签添加样式
removeClass() 	给标签删除样式
toggleClass() 	有就删除，没有就添加样式。
offset() 		获取和设置元素的坐标。
```

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title></title>

  <style type="text/css">

    div{
      width:100px;
      height:260px;
    }

    div.whiteborder{
      border: 2px white solid;
    }

    div.redDiv{
      background-color: red;
    }

    /*
    div.blueBorder这个样式写法说明：
    .blueBorder是类型选择器
    前面加上div，表示这个样式只能给div标签使用，别的标签无法使用
     */
    div.blueBorder{
      border: 5px blue solid;
    }

  </style>

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>
  <script type="text/javascript">

    $(function(){

      var $divEle = $('div:first');

      $('#btn01').click(function(){
        //addClass() - 向被选元素添加一个或多个样式(的类选择器)
        $divEle.addClass("redDiv blueBorder");
      });


      $('#btn02').click(function(){
        //removeClass() - 从被选元素删除一个或多个类
        //$divEle.removeClass();删除全部样式
        $divEle.removeClass("redDiv");
      });


      $('#btn03').click(function(){
        //toggleClass() - 对被选元素进行添加/删除类的切换操作
        $divEle.toggleClass("redDiv");
      });


      $('#btn04').click(function(){
        //offset() - 返回匹配元素样式在当前页面的位置。
        var position = $divEle.offset();
        //输出到控制台
        console.log(position);

        //也可以设置位置
        $divEle.offset({
          left:100,
          right:100
        });

      });

    })
  </script>

</head>
<body>

<table align="center">
  <tr>
    <td>

      <div class="border"></div>

    </td>

    <td>

      <div class="btn">
        <input type="button" value="addClass()" id="btn01"/>
        <input type="button" value="removeClass()" id="btn02"/>
        <input type="button" value="toggleClass()" id="btn03"/>
        <input type="button" value="offset()" id="btn04"/>
      </div>

    </td>
  </tr>
</table>

</body>
</html>

```

## 013-[jQuery动画](https://so.csdn.net/so/search?q=jQuery%E5%8A%A8%E7%94%BB&spm=1001.2101.3001.7020)

```
基本动画
	show()	  将隐藏的元素显示
	hide() 	  将可见的元素隐藏。
	toggle()  可见就隐藏，不可见就显示。

淡入淡出动画
	fadeIn() 		淡入（慢慢可见）
	fadeOut() 		淡出（慢慢消失）
	fadeTo() 		在指定时长内慢慢的将透明度修改到指定的值。0 透明，1 完成可见，0.5 半透明
	fadeToggle() 	淡入/淡出 切换

以上动画方法都可以添加参数。
1、第一个参数是动画 执行的时长，以毫秒为单位
2、第二个参数是动画的回调函数 (动画完成后自动调用的函数)
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title></title>

  <link href="style.css" type="text/css" rel="stylesheet" />

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>

  <script type="text/javascript">
    /*
      基本
      show([speed,[easing],[fn]])
      hide([speed,[easing],[fn]])
      toggle([speed],[easing],[fn])

      淡入淡出
      fadeIn([speed],[eas],[fn])
      fadeOut([speed],[eas],[fn])
      fadeTo([[spe],opa,[eas],[fn]])
      fadeToggle([speed,[eas],[fn]])
      */
    $(function(){

      //显示   show()
      $("#btn1").click(function(){
        //$("#div1").show();
        //$("#div1").show(1000);
        $("#div1").show(1000,function (){
          alert("show()动画完成");
        });

      });

      //隐藏  hide()
      $("#btn2").click(function(){
        $("#div1").hide();
      });

      //切换   toggle()
      $("#btn3").click(function(){
        $("#div1").toggle();
      });



      //动画一直动的效果
/*    var abc = function (){
        $("#div1").toggle(1000,abc);
      }
      abc();
      */



      //淡入   fadeIn()
      $("#btn4").click(function(){
        //$("#div1").fadeIn();
        $("#div1").fadeIn(2000,function (){
          alert("fadeIn完成");
        });

      });

      //淡出  fadeOut()
      $("#btn5").click(function(){
        $("#div1").fadeOut();
        //$("#div1").fadeOut(2000);

      });

      //淡化切换  fadeToggle()
      $("#btn7").click(function(){
        $("#div1").fadeToggle();
      });

      //淡化到  fadeTo()
      $("#btn6").click(function(){
        $("#div1").fadeTo(2000,0.5,function (){
          alert("半透明完成");
        });
      });

    })
  </script>

</head>
<body>
<table style="float: left;">
  <tr>
    <td><button id="btn1">显示show()</button></td>
  </tr>
  <tr>
    <td><button id="btn2">隐藏hide()</button></td>
  </tr>
  <tr>
    <td><button id="btn3">显示/隐藏切换 toggle()</button></td>
  </tr>
  <tr>
    <td><button id="btn4">淡入fadeIn()</button></td>
  </tr>
  <tr>
    <td><button id="btn5">淡出fadeOut()</button></td>
  </tr>
  <tr>
    <td><button id="btn6">淡化到fadeTo()</button></td>
  </tr>
  <tr>
    <td><button id="btn7">淡化切换fadeToggle()</button></td>
  </tr>
</table>

<div id="div1" style="float:left;border: 1px solid;background-color: blue;width: 300px;height: 200px;">
  jquery动画定义了很多种动画效果，可以很方便的使用这些动画效果
</div>
</body>

</html>

```

```css
table,td {
	border: 1px solid;
	border-collapse: collapse;
}

span {
	background-color: #fb6;
	text-align: center;
	width: 100px;
	height: 50px;
	display: block;
}
```

## 014-品牌展示练习

```
需求：
1.点击按钮的时候，隐藏和显示卡西欧之后的品牌。
2.当显示全部内容的时候，按钮文本为“显示精简品牌”
然后，小三角形向上。所有品牌产品为默认颜色。
3.当只显示精简品牌的时候，要隐藏卡西欧之后的品牌，按钮文本为“显示全部品牌”
然后小三形向下。并且把 佳能，尼康的品牌颜色改为红色（给 li 标签添加 promoted 样式即可）
```

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
  <title>品牌展示练习</title>

  <style type="text/css">
    * {
      margin: 0;
      padding: 0;
    }

    body {
      font-size: 12px;
      text-align: center;
    }

    a {
      color: #04D;
      text-decoration: none;
    }

    a:hover {
      color: #F50;
      text-decoration: underline;
    }

    .SubCategoryBox {
      width: 600px;
      margin: 0 auto;
      text-align: center;
      margin-top: 40px;
    }

    .SubCategoryBox ul {
      list-style: none;
    }

    .SubCategoryBox ul li {
      display: block;
      float: left;
      width: 200px;
      line-height: 20px;
    }

    .showmore , .showless{
      clear: both;
      text-align: center;
      padding-top: 10px;
    }

    .showmore a , .showless a{
      display: block;
      width: 120px;
      margin: 0 auto;
      line-height: 24px;
      border: 1px solid #AAA;
    }

    /*
    后代选择器
      使用该后代选择器的标签有属性class=showmore
      并且该标签有一个后代a标签，a标签有一个后代span标签
      符合这两个条件的标签才能使用这个样式
      如果想要对某个标签添加这个样式：标签对象.addClass("showmore")
    */
    .showmore a span {
      padding-left: 15px;
      background: url(down.gif) no-repeat 0 0;
    }

    .showless a span {
      padding-left: 15px;
      background: url(up.gif) no-repeat 0 0;
    }

    .promoted a {
      color: #F50;
    }
  </style>

  <script type="text/javascript" src="jquery-3.6.0.js"></script>
  <script type="text/javascript">
    $(function() {
      //基本初始状态(只显示卡西欧之前的品牌和其他品牌相机)
      $("li:gt(5):not(:last)").hide();

      //给 a标签 绑定单击事件
      //div下的div下的a标签
      $("div div a").click(function (){
        //让某些品牌显示，某些品牌隐藏
        $("li:gt(5):not(:last)").toggle();

        //点击时：修改按钮上的文字和图标
        //显示全部品牌 角标向下
        //显示精简品牌 角标向上
        //修改文字：text()
        //修改图标：改变样式addClass()  角标向下样式showmore 角标向上样式showless
        //图标的样式是 div下的div的样式

        //判断品牌 是否 隐藏 来修改内容
        if($("li:gt(5):not(:last)").is(":hidden")){
          //有些品牌被隐藏时
          //修改文字
          //修改div下的div下的a标签下的span标签的内容
          $("div div a span").text("显示全部品牌");

          //修改图标
          //首先把div下的div的样式删除
          $("div div").removeClass();
          //然后添加新的样式
          $("div div").addClass("showless");


          //去除高亮
          $("li:contains('佳能')").removeClass("promoted");
          $("li:contains('尼康')").removeClass("promoted");

        }else {
          //没有品牌被隐藏

          $("div div a span").text("显示精简品牌");
          $("div div").removeClass();
          $("div div").addClass("showmore");


          //品牌全部显示时，对佳能、尼康品牌加高亮
          /*
          上面css代码中已经告诉你怎么显示高亮
          .promoted a {
              color: #F50;
           }
           高亮颜色为#F50
           对属性class=promoted并且有后代标签a标签的标签进行高亮
           */
          $("li:contains('佳能')").addClass("promoted");
          $("li:contains('尼康')").addClass("promoted");

        }

        //不让a标签跳转
        return false;
      });

    });
  </script>

</head>
<body>

<div class="SubCategoryBox">
  <ul>
<!--    #表示虚链接，不需要写链接地址了-->
    <li><a href="#">佳能</a><i>(30440) </i></li>
    <li><a href="#">索尼</a><i>(27220) </i></li>
    <li><a href="#">三星</a><i>(20808) </i></li>
    <li><a href="#">尼康</a><i>(17821) </i></li>
    <li><a href="#">松下</a><i>(12289) </i></li>
    <li><a href="#">卡西欧</a><i>(8242) </i></li>
    <li><a href="#">富士</a><i>(14894) </i></li>
    <li><a href="#">柯达</a><i>(9520) </i></li>
    <li><a href="#">宾得</a><i>(2195) </i></li>
    <li><a href="#">理光</a><i>(4114) </i></li>
    <li><a href="#">奥林巴斯</a><i>(12205) </i></li>
    <li><a href="#">明基</a><i>(1466) </i></li>
    <li><a href="#">爱国者</a><i>(3091) </i></li>
    <li><a href="#">其它品牌相机</a><i>(7275) </i></li>
  </ul>
  <div class="showmore">
    <a href="more.html"><span>显示全部品牌</span></a>
  </div>
</div>
</body>
</html>


```

## 015-jQuery事件操作

```
页面加载完成之后写法：
jQuery：$( function(){} );
javaScript：window.onload = function(){}
二者有什么区别？

（1）执行顺序不同
		jQuery的先执行
		原生的js的后执行
（2）为什么会是这个顺序？
	jQuery的是 浏览器的内核解析完页面的标签，创建好 DOM 对象之后就会马上执行。
	而原生的 js是，除了要等浏览器内核解析完标签，创建好 DOM 对象，还要等标签显示时需要的内容加载完成。

（3）他们执行的次数？
	$( function(){alert("1");} );
	$( function(){alert("2");} );
	$( function(){alert("3");} );
	
	window.onload = function(){alert("1");}
	window.onload = function(){alert("2");}
	window.onload = function(){alert("3");}
	
	原生 js 的页面加载完成之后，只会执行最后一次的赋值函数。//3
	jQuery 的页面加载完成之后是把注册的 function 函数，全部依次执行。//1 2 3
```

## 016-jQuery中的事件相关方法

```
click() 		它可以绑定单击事件，以及触发单击事件
				绑定事件：对象.click(function(){});
				触发事件：对象.click(); 在别的事件里去触发另一个事件

事件方法都可以绑定和触发

mouseover() 	鼠标移入事件
mouseout() 		鼠标移出事件

bind() 			可以给元素一次性绑定一个或多个事件。
one() 			用法跟 bind 一样。但是 one 方法绑定的事件只会响应一次。
unbind() 		跟 bind 方法相反的操作，解除事件的绑定

live() 			也是用来绑定事件。
				它可以用来绑定选择器匹配的所有元素的事件。
				哪怕这个元素是后面动态创建出来的也有效
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title></title>

  <link href="style.css" type="text/css" rel="stylesheet" />

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>
  <script type="text/javascript">

    $(function(){
      $("h5").click(function (){
        alert("click绑定事件");//传function是绑定事件
      });

      $("button").click(function (){
        $("h5").click();//不传function是触发事件
      });

      //鼠标移入事件
      $("h5").mouseover(function (){
        //输出到控制台
        console.log("鼠标移入了");
      });
      //鼠标移出事件
      $("h5").mouseout(function (){
        console.log("鼠标移出了");
      });


      //bind事件
      $("h5").bind("click",function (){
        alert("这是bind绑定的click事件");
      });
      //bind可以一次绑定多个事件
      $("h5").bind("click mouseover mouseout",function (){
        alert("这是bind绑定的click事件");
      });

      //one事件跟bind事件用法一样，但是one事件只会响应一次
      $("h5").one("click mouseover mouseout",function (){
        alert("这是bind绑定的click事件");
      });

      //unbind事件 解除事件的绑定
      //可以解除一个事件，也可以解除多个事件，
      //对象.unbind()什么都不传，会解除所有事件
      $("h5").unbind("click mouseover",function (){
        alert("这是bind绑定的click事件");
      });
      
      //live事件看图 看文档

    });

  </script>
</head>
<body>
<div id="panel">
  <h5 class="head">什么是jQuery?</h5>
  <div class="content">
    jQuery是继Prototype之后又一个优秀的JavaScript库，它是一个由 John Resig 创建于2006年1月的开源项目。jQuery凭借简洁的语法和跨平台的兼容性，极大地简化了JavaScript开发人员遍历HTML文档、操作DOM、处理事件、执行动画和开发Ajax。它独特而又优雅的代码风格改变了JavaScript程序员的设计思路和编写程序的方式。
  </div>
  <button>按钮</button>
</div>
</body>

</html>

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/0a10ef32a93141d195d9fa9cbde260a3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAS0stR3JleXNvbg==,size_20,color_FFFFFF,t_70,g_se,x_16)

## 017-事件的[冒泡](https://so.csdn.net/so/search?q=%E5%86%92%E6%B3%A1&spm=1001.2101.3001.7020)

```
什么是事件的冒泡？
	事件的冒泡是指，父子元素同时监听同一个事件。
	当触发子元素的事件的时候，同一个事件也被传递到了父元素的事件里去响应。

如何阻止事件冒泡呢？
	在子元素事件函数体内，写上 return false; 可以阻止事件的冒泡传递。
```

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>Untitled Document</title>
		<style type="text/css">
			*{
				margin: 0;
				padding: 0;
			}
			body{
				font-size: 13px;
				line-height: 130%;
				padding: 60px;
			}
			#content{
				width: 220px;
				border: 1px solid #0050D0;
				background: #96E555;
			}
			span{
				width: 200px;
				margin: 10px;
				background: #666666;
				cursor: pointer;
				color: white;
				display: block;
			}
			p{
				width: 200px;
				background: #888;
				color: white;
				height: 16px;
			}
		</style>
		<script type="text/javascript" src="jquery-1.7.2.js"></script>
		<script type="text/javascript">
			$(function(){
				//冒泡就是事件的向上传导，子元素的事件被触发，父元素的响应事件也会触发
				
				//span是div的子标签
				//点击div会出现我是div
				//点击span，首先会出现我是span，然后出现我是div
				//解决方法：在子元素事件函数体最后写上：return false;
			
				$("#content").click(function(){
					alert("我是div");
				});
				
				$("span").click(function(){
					alert("我是span");
					//return false;
				});
			})
		</script>
	</head>
	<body>
		<div id="content">
			外层div元素
			<span>内层span元素</span>
			外层div元素
		</div>
		
		<div id="msg"></div>	
		
		<br><br>
		<a href="http://www.hao123.com">WWW.HAO123.COM</a>	
	</body>
</html>


```

## 018-事件对象

```
事件对象，是封装有触发的事件信息的一个 javascript 对象。

我们重点关心的是怎么拿到这个 javascript 的事件对象。以及使用。

如何获取呢 javascript 事件对象呢？
在给元素绑定事件的时候，在事件的 function( event ) 参数列表中添加一个参数，这个参数名，我们习惯取名为 event。
这个 event 就是 javascript 传递参事件处理函数的事件对象。

比如：
```

```html
//1.原生 javascript 获取 事件对象
window.onload = function () {
	document.getElementById("areaDiv").onclick = function (event) {
		console.log(event);
	}
}
```

```html
//2.jQuery 代码获取 事件对象
$(function () {
	$("#areaDiv").click(function (event) {
		console.log(event);
	});
});
```

```html
//3.使用 bind 同时对多个事件绑定同一个函数。怎么获取当前操作是什么事件。
$("#areaDiv").bind("mouseover mouseout",function (event) {
	if (event.type == "mouseover") {
		console.log("鼠标移入");
	} else if (event.type == "mouseout") {
		console.log("鼠标移出");
	}
});
```

## 019-事件练习-图片跟随

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title></title>

  <style type="text/css">
    body {
      text-align: center;
    }
    #small {
      margin-top: 150px;
    }
    #showBig {
      position: absolute;
      display: none;
    }
  </style>

  <script type="text/javascript" src="../jquery-3.6.0.js"></script>
  <script type="text/javascript">
    $(function(){
      //鼠标移到小照片时，照片被放大，鼠标移出，变回原样
      //给小照片绑定鼠标移入移出事件
      //给小照片绑定鼠标移动事件，大照片跟着鼠标移动，使用offset设置大照片的坐标在鼠标上
      $("#small").bind("mouseover mouseout mousemove",function (event){
        //鼠标移入小照片时，照片被放大，即大照片出现
        //鼠标移出小照片时，大照片消失
        if (event.type == "mouseover"){
          $("#showBig").show();
        }else if (event.type == "mouseout"){
          $("#showBig").hide();
        }else if (event.type == "mousemove"){
          //left--->横坐标
          //top--->纵坐标
          //event.pageX event.pageY--->鼠标移动时在页面的位置
          //+10 是因为鼠标位置和大照片位置太近会导致照片闪烁，分开一些距离就好了
          $("#showBig").offset({
            left:event.pageX + 10,
            top:event.pageY + 10
          });
        }

      });
    });
  </script>

</head>
<body>

  <img id="small" src="small.jpg" />

  <div id="showBig">
    <img src="big.jpg">
  </div>

</body>
</html>

```