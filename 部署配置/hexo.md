## 前提

- 安装`nodejs`

  教程：https://blog.csdn.net/weixin_52799373/article/details/123840137（过程详细，还覆盖win11，评论下面还有师叔的足迹）

- 使用`nodejs`查找`hexo`官方文档来安装`hexo`

## 开始安装

#### 注意一

全局安装最常用的 express 模块 进行测试

命令如下:

```
npm install express -g
```



**测试ssh是否绑定成功**

```
ssh -T git@github.com
```

如果问你（yes or no）,直接 yes 就可以得到下面这段话

[![img](https://static001.geekbang.org/infoq/82/827db6ee904638d62a4f5249fe110bdf.png)](https://static001.geekbang.org/infoq/82/827db6ee904638d62a4f5249fe110bdf.png)

### 本地访问博客

1、创建一个名为 Blog 的文件，在里面启用 Git Bash Here

[![img](https://static001.geekbang.org/infoq/b0/b06a27bee58606277ea46f413b34ed14.png)](https://static001.geekbang.org/infoq/b0/b06a27bee58606277ea46f413b34ed14.png)

2、初始化hexo

```
hexo init
```



[![img](https://static001.geekbang.org/infoq/dd/ddf2ca2f77a32aaff0c8f17b44bbe99b.png)](https://static001.geekbang.org/infoq/dd/ddf2ca2f77a32aaff0c8f17b44bbe99b.png)

3、生成本地的hexo页面

```
hexo s
```

[![img](https://static001.geekbang.org/infoq/25/259acb6b590338700c8f61ffcc531593.png)](https://static001.geekbang.org/infoq/25/259acb6b590338700c8f61ffcc531593.png)

4、访问

打开本地服务器

```
http://localhost:4000/
```



[![img](https://static001.geekbang.org/infoq/5f/5fde531819308103720a5c098f342092.png)](https://static001.geekbang.org/infoq/5f/5fde531819308103720a5c098f342092.png)

> 长按 Ctrl + c 关闭服务器

[![img](https://static001.geekbang.org/infoq/6f/6f77175cb6028832126b38d0b820be95.png)](https://static001.geekbang.org/infoq/6f/6f77175cb6028832126b38d0b820be95.png)

修改-config.yml文件

![img](https://static001.geekbang.org/infoq/76/76aa4fb226389c10209b17cd97f1b7d8.png)

### 创建GitHub仓库

接下来进入github个人首页，如下图所示。

![](https://dhndzwxj.vercel.app/images/%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E6%97%A5%E5%BF%97/image-20211025-13.jpg)

下面我们就要建立“仓库”用以储存我们的博客网站了！点击如上图中左边的“create repository”（如果你已经有仓库了，那么酒店左上角的“New”，或者点右上角“+”，里面有个“New repository”），进入下面的页面。这个页面里面一些设置要特别注意了！仓库的名字（repository name）一定要和自己的名字(owner)一模一样！而且一定要写成`yourname.github.io`的格式。不然后面没办法生成相应的github页面。

![](https://dhndzwxj.vercel.app/images/%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E6%97%A5%E5%BF%97/image-20211025-14.png)

这样仓库就建好了。每一个仓库都有自己的地址，如下图：

![](https://dhndzwxj.vercel.app/images/%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E6%97%A5%E5%BF%97/image-20211026-15.png)

一般而言，最重要的地址是SSH地址，将其复制下来，粘贴到博客站点配置文件`blog/_config.yml`就可以了。这里解释了上面`deploy`中`repo`的参数的含义！

```yaml
deploy:
  type: git
  repository: git@github.com:xxx/xxx.github.io.git 
  branch: master
```



**安装hexo-deployer-git 自动部署发布工具**

```
npm install hexo-deployer-git --save
```



[![img](https://static001.geekbang.org/infoq/a9/a94aa787c8627d7bc2d95fc3aabe211d.png)](https://static001.geekbang.org/infoq/a9/a94aa787c8627d7bc2d95fc3aabe211d.png)

**生成页面

```
hexo g
```



[![img](https://static001.geekbang.org/infoq/ec/ec4dc5e76906c62036e61f4d083ebaad.png)](https://static001.geekbang.org/infoq/ec/ec4dc5e76906c62036e61f4d083ebaad.png)

**本地文件上传到Github上面**

```
hexo d
```



中间会出现一个登录界面，可以用令牌登录。（令牌及时保存，就看不到了）

结束以后就上传 Github 就成功了！！！

[![img](https://static001.geekbang.org/infoq/1b/1ba83adbf958f9ed0ad0129bd843f785.png)](https://static001.geekbang.org/infoq/1b/1ba83adbf958f9ed0ad0129bd843f785.png)

### 访问GitHub博客

[![img](https://static001.geekbang.org/infoq/0a/0a397f64a129a210ccbebdff832de7af.png)](https://static001.geekbang.org/infoq/0a/0a397f64a129a210ccbebdff832de7af.png)

访问博客，开始的页面是初始化页面，没有做美化和增加内容。

```
https://hhszzzz.github.io/
```



[![img](https://static001.geekbang.org/infoq/aa/aa2f760148fbcbf6c3ce0b43cbe3433c.png)](https://static001.geekbang.org/infoq/aa/aa2f760148fbcbf6c3ce0b43cbe3433c.png)

## 第二部分 文档学习

### 撰写博客

_**电脑要必须有Typora！电脑要必须有Typora！电脑要必须有Typora！**_（重要的事情说三遍）

文本教程：[https://dhndzwxj.vercel.app/3276806131.html](https://dhndzwxj.vercel.app/3276806131.html)

hexo标签教程：[http://haiyong.site/post/cda958f2.html（参考文档看需求加不加）](http://haiyong.site/post/cda958f2.html%EF%BC%88%E5%8F%82%E8%80%83%E6%96%87%E6%A1%A3%E7%9C%8B%E9%9C%80%E6%B1%82%E5%8A%A0%E4%B8%8D%E5%8A%A0%EF%BC%89)

我们打开自己的博客根目录，跟着我一个个了解里面的这些文件（夹）都是干什么的：

[![img](https://static001.geekbang.org/infoq/d9/d9dc3c789aba0ebfab9b2a06a03754bb.png)](https://static001.geekbang.org/infoq/d9/d9dc3c789aba0ebfab9b2a06a03754bb.png)

- `_config.yml`：俗称站点配置文件，很多与博客网站的格式、内容相关的设置都需要在里面改。
- `node_modules`:存储Hexo插件的文件，可以实现各种扩展功能。一般不需要管。
- `package.json`：别问我，我也不知道干嘛的。
- `scaffolds`：模板文件夹，里面的`post.md`文件可以设置每一篇博客的模板。具体用起来就知道能干嘛了。
- `source`：非常重要。所有的个人文件都在里面！
- `themes`：主题文件夹，可以从[Hexo主题官网](https://hexo.io/themes/)或者网上大神的Github主页下载各种各样美观的主题，让自己的网站变得逼格高端的关键！

接下来重点介绍`source`文件夹。新建的博客中，`source`文件夹下默认只有一个子文件夹——`_posts`。我们写的博客都放在这个子文件夹里面。我们还可以在`source`里面新建各种子文件夹满足自己的个性化需求，对初学者而言，我们先把精力放在主线任务上，然后再来搞这些细节。

[![img](https://static001.geekbang.org/infoq/3f/3f7b8b410726691082019f2ab603976b.png)](https://static001.geekbang.org/infoq/3f/3f7b8b410726691082019f2ab603976b.png)

写好Hellworld内容后，在命令行一键三连：

> ‘hexo cl’命令用于清除缓存文件（db.json）和已生成的静态文件（public）。
>
> 例如：在更换主题后，如果发现站点更改不生效，可以运行该命令。

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">hexo cl</span><br></pre></td></tr></tbody></table>

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">hexo g</span><br></pre></td></tr></tbody></table>

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">hexo s</span><br></pre></td></tr></tbody></table>

然后随便打开一个浏览器，在网址栏输入`localhost:4000/`，就能发现自己的网站更新了！不过这只是在本地进行了更新，要想部署到网上（Github上），输入如下代码：

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">hexo d</span><br></pre></td></tr></tbody></table>

然后在浏览器地址栏输入`https://yourname.github.io`，或者`yourname.github.io`就能在网上浏览自己的博客了！

以上，我们的博客网站1.0版本就搭建完成了，如果没有更多的需求，做到这里基本上就可以了。如果有更多的要求，还需要进一步的精耕细作！

### [](https://wushishu.xyz/post/be8880ea.html#%E7%B2%BE%E8%80%95%E7%BB%86%E4%BD%9C "精耕细作")精耕细作

**海拥\\Butterfly 主题美化：**[http://haiyong.site/post/22e1d5da.html](http://haiyong.site/post/22e1d5da.html)

**Butterfly参考文档（小白慎入，但是他也是你走向DIY必须迈出的一歩）**:[https://butterfly.js.org/posts/dc584b87/#Post-Front-matter](https://butterfly.js.org/posts/dc584b87/#Post-Front-matter)

文章中要更改的文件（.yml .bug 等）可以要用viscode打开！！！

**Butterfly 主题安装**

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly</span><br></pre></td></tr></tbody></table>

这里面如果报错，如下图所示（长路漫漫，bug满满）

[![img](https://static001.geekbang.org/infoq/4b/4bd9fe08801a3332cf5552216b631149.png)](https://static001.geekbang.org/infoq/4b/4bd9fe08801a3332cf5552216b631149.png)

只需要在命令行中执行

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br><span class="line">2</span><br></pre></td><td class="code"><pre><span class="line">git config --global --unset http.proxy </span><br><span class="line">git config --global --unset https.proxy</span><br></pre></td></tr></tbody></table>

再次安装主题即可成功

[![img](https://static001.geekbang.org/infoq/a1/a1ee42ae917eb1c19261ec72b82efde4.png)](https://static001.geekbang.org/infoq/a1/a1ee42ae917eb1c19261ec72b82efde4.png)

**应用主题**

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">theme: butterfly</span><br></pre></td></tr></tbody></table>

[![img](https://static001.geekbang.org/infoq/b0/b08a6de31ecdfcc74ccb0037f23094b3.png)](https://static001.geekbang.org/infoq/b0/b08a6de31ecdfcc74ccb0037f23094b3.png)

**安装插件**

如果你没有 pug 以及 stylus 的渲染器，请下载安装：

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">npm install hexo-renderer-pug hexo-renderer-stylus --save</span><br></pre></td></tr></tbody></table>

[![img](https://static001.geekbang.org/infoq/cd/cded5198aec31fec9f8b53bb76676174.png)](https://static001.geekbang.org/infoq/cd/cded5198aec31fec9f8b53bb76676174.png)

### [](https://wushishu.xyz/post/be8880ea.html#Butterfly-%E4%B8%BB%E9%A2%98%E7%BE%8E%E5%8C%96 "Butterfly 主题美化")Butterfly 主题美化

生成文章唯一链接

Hexo的默认文章链接格式是年，月，日，标题这种格式来生成的。如果你的标题是中文的话，那你的URL链接就会包含中文，

复制后的URL路径就是把中文变成了一大堆字符串编码，如果你在其他地方用这边文章的url链接，偶然你又修改了改文章的标题，那这个URL链接就会失效。为了给每一篇文章来上一个属于自己的链接，写下此教程，利用 hexo-abbrlink 插件，A Hexo plugin to generate static post link based on post titles ,来解决这个问题。 参考github官方： hexo-abbrlink 按照此教程配置完之后如下：

1、安装插件，在博客根目录 \[Blogroot\] 下打开终端，运行以下指令：

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">npm install hexo-abbrlink --save</span><br></pre></td></tr></tbody></table>

[![img](https://static001.geekbang.org/infoq/32/32ad907f045425afedb968d20dab4507.png)](https://static001.geekbang.org/infoq/32/32ad907f045425afedb968d20dab4507.png)

2、插件安装成功后，在根目录 \[Blogroot\] 的配置文件 \_config.yml 找到 permalink：

[![img](https://static001.geekbang.org/infoq/7e/7e4272e54cb25564b1e708e85c8539d2.png)](https://static001.geekbang.org/infoq/7e/7e4272e54cb25564b1e708e85c8539d2.png)

### [](https://wushishu.xyz/post/be8880ea.html#%E5%8F%91%E5%B8%83%E5%8D%9A%E5%AE%A2 "发布博客")发布博客

这次了解我上面只有一个HelloWord的时候，为什么不让右键新建，**因为需要命令生成啊，铁汁！**

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">npm i hexo-deployer-git</span><br></pre></td></tr></tbody></table>

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">hexo new post "新建博客文章名"</span><br></pre></td></tr></tbody></table>

plaintext

<table><tbody><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">hexo cl &amp;&amp; hexo g  &amp;&amp; hexo s</span><br></pre></td></tr></tbody></table>

### [](https://wushishu.xyz/post/be8880ea.html#hexo%E6%9B%B4%E6%8D%A2%E8%83%8C%E6%99%AF%E5%9B%BE%E7%89%87 "hexo更换背景图片")hexo更换背景图片

背景图片参考网址：

- [https://wallhaven.cc/](https://wallhaven.cc/)
- [https://wall.alphacoders.com/](https://wall.alphacoders.com/)
- [https://bz.zzzmh.cn/index](https://bz.zzzmh.cn/index)

_本方法解决的是多次同步到GitHub上背景图片未成功的情况_

直接更改原文件

图片所在目录：`hexo/themes/landscape/source/css/images/`

图片名称：`banner.jpg`

## [](https://wushishu.xyz/post/be8880ea.html#%E7%AC%AC%E4%B8%89%E9%83%A8%E5%88%86-%E7%BB%91%E5%AE%9A%E8%87%AA%E5%B7%B1%E7%9A%84%E5%9F%9F%E5%90%8D "第三部分 绑定自己的域名")第三部分 绑定自己的域名

博客地址：[https://www.likecs.com/show-30474.html](https://www.likecs.com/show-30474.html)

**绑定之后你就有有一个自己专属的博客了。**

买一个域名，可以一块钱白嫖，但是续费贵的飞天！！！

_**注意请谨慎绑定，想我就会出现提交一次 (hexo d) ,需要重新绑定域名**_

