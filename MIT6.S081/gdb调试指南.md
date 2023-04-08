# 前言

在MIT开始lab时，教授演示过gdb的具体操作，但是没有系统的学习过，所以参考文章进行学习。

# 编译

- 安装编译环境

    在调试之前，需要先将文件进行编译，下面先介绍GNU编译器集合中的部分编译器，由于本人用的是Ubuntu22.04，所以可以使用下面指令来对GNU编译器进行安装。

    ```
    sudo apt-get update
    sudo apt-get install build-essential
    ```

	一般来说使用`g++`进行编译，如果是编译C程序，则使用`gcc`即可。
	
- 编译源代码

    > 我们已经安装好了编译所需要的环境，那么接下来就要介绍编译的语法，GNU编译器支持多种命令语法：

    - 一种是将可执行文件的名称`-o program`放在了命令的**最后**。

      ```
      gcc [options] file.c -o program
      ```

    - 另一种是将可执行文件的名称`-o program`放在了命令的**中间**。事实上这两种是等价的。

      ```
      gcc -o program [options] file.c
      ```

    - 实际使用中，如果有多个源代码文件需要编译，通常会将所有源代码文件放在命令的最后，然后在可执行文件名称的位置指定可执行文件的名称，例如：

      ```
      gcc [options] file1.c file2.c file3.c -o program
      ```

    

    >  在编译程序时，我们常常会遇到各种后缀，比如`-o`、`-g`等等。

    - `-o`：指定生成的可执行文件名。例如，`-o example`生成一个名为`example`的可执行文件。

    - `-g`：生成调试信息。

      举个例子，假设我们有一个名为`test.c`的源代码文件，我们可以使用以下命令编译它：

      ```
      gcc -o test -g test.c
      ```

      同样的，对于C++源代码文件，就将`gcc`改为`g++`即可。

    - `-O`：指定优化级别，一般来说有`O1、O2、O3、Os`等优化。

      ```
      gcc -O1 test.c -o
      ```


# 调试

## 启动gdb，载入文件，打印源代码，退出gdb

首先，在编译选项里加上 `-g` ，以生成调试用的符号表。建议**不要**同时开 `-O2` 等优化选项，否则可能会有奇奇怪怪的问题。

打开终端，输入 `gdb [可执行文件名]` ，载入程序（注意，是可执行文件名（比如 1.exe），不是你的源文件名）。正确示范：

```shell
> g++ example.cpp -o example -g
[编译，无提示]
> gdb ./example
```

然后，你可能会见到如下的界面：

![](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230313093659689.png)

这样，你就进入gdb的命令行环境里了。

其中，第一行是**版本信息**，倒数第二行表示**正载入符号表**（在编译和链接过程中生成的符号表，包含了程序中定义的所有符号及其相关信息），最后一行 `(gdb)` 则是 **gdb的提示符** 。

请注意，若你倒数第二行有 `(no debugging symbols found)` 字样，请确保在编译选项里加上 `-g` 选项。

当然，如果你直接输入 `gdb` 启动，不加文件名，也可以。只是，你要使用 `file` 命令手动载入可执行文件。

**以后出现的所有命令，都是在 gdb 的环境，而非系统 shell 的环境执行的。**

> 命令：`file（简写fil）`  
> 格式：`file 可执行文件名`  
> 作用：载入**当前目录下**的对应名称的可执行文件。

例子：

```sql
(gdb) file example
Load new symbol table from "example"? (y or n) y
Reading symbols from example...done.
```

> 命令：`list（简写为l）`  
> 格式：`list [行号]`  
> 作用：打印给定行号周围 101010 行的源代码。若不提供行号，则接续打印上次的源代码。

这里提到了一个**简写**的概念。什么是简写呢？简写是为了简化命令的。比如，打一个 `list` 还是有些麻烦的。这时，我们可以输入它的简写 `l` 。你可以认为一个命令与它的简写是完全等价的。以后若提到简写，不再解释。

例子：

```cpp
(gdb) l 7
	#include <cstdio>
	using namespace std;
	int f(int x){
    	int ans=1;
	    for(int i=1;i<=x;i++) ans*=i;
	    return ans;
	}
	
	int main(){
    	int a;
(gdb) l
	    scanf("%d",&a);
	    printf("%d\n",f(a));
	    return 0;
	}
(gdb) 
```

最后，用 `quit` 命令退出 gdb。

> 命令：`quit（简写为q）`  
> 格式：`quit`（无参数）  
> 作用：退出gdb。

## 分屏

当你需要分割窗口，一边查看代码，一边测试的时候，就需要用到分屏。

> `layout`：用于分割窗口，可以一边查看代码，一边测试。主要有以下几种用法：
> `layout src`：显示源代码窗口
> `layout asm`：显示汇编窗口
> `layout regs`：显示源代码/汇编和寄存器窗口
> `layout split`：显示源代码和汇编窗口
> `layout next`：显示下一个layout
> `layout prev`：显示上一个layout
> `Ctrl + L`：刷新窗口
> `Ctrl + x，再按1`：单窗口模式，显示一个窗口
> `Ctrl + x，再按2`：双窗口模式，显示两个窗口
> `Ctrl + x，再按a`：回到传统模式，即退出layout，回到执行layout之前的调试窗口。

例如：`layout split`

![](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1678692779946.png)

## 设置断点

要调试程序，我们必须让它在某个地方停下来。否则，让它一直执行下去，那和普通的执行程序有什么区别呢？

因此，我们需要用 `break` 来设置断点。此后，程序将会在设定的断点处停下来。

> 命令：`break（简写为b）` 
> 格式：`break 函数名|行号` 
> 作用：在给定函数名或行数处设置断点。

例子：

```kotlin
(gdb) break main //main函数处设置断点
Breakpoint 1 at 0x874: file example.cpp, line 10.
(gdb) break 11 //在第11行处设置断点
Breakpoint 2 at 0x883: file example.cpp, line 11.
```

当然可以用它的简写：

```kotlin
(gdb) b main
Breakpoint 1 at 0x874: file example.cpp, line 10.
(gdb) b 11
Breakpoint 2 at 0x883: file example.cpp, line 11.
```

## 运行

> 命令：`run（简写为r）` 
> 格式：`run`（无参数） 
> 作用：**从头**运行程序。

> 命令：`continue（简写为c）` 
> 格式：`continue`（无参数） 
> 作用：**从当前位置继续**运行程序，直到遇到**下一个断点或程序运行完毕**。

> 命令：`until（简写为u）` 
> 格式：`until 行号` 
> 作用：**从当前位置继续**运行程序，直到**指定行号处**才停下来。

当然，有时候，你可能发现运行上述命令后 gdb 会停住。这有两种情况：

1. 你的程序用了标准输入，gdb 在等待输入。
2. 数据规模太大或程序效率太低，以至于运行到断点的时间较长。

例子：

```scss
(gdb) r
Starting program: /home/acceptedzhs/example 

Breakpoint 1, main () at example.cpp:10
10	int main(){
(gdb) c
Continuing.

Breakpoint 2, main () at example.cpp:12
12	    scanf("%d",&a); 
(gdb) 
```

## 单步执行

很多时候，我们要一步一步地执行程序。无疑，反复地 `break` 与 `continue` 十分麻烦。gdb有两个命令 `next` 与 `step`，可实现单步执行。

> 命令：`next（简写为n）` 
> 格式：`next`（无参数） 
> 作用：单步执行。若当前行有函数调用，则**把这个函数作为一个整体执行**（即不进入函数内部）。

> 命令：`step（简写为s）` 
> 格式：`step`（无参数） 
> 作用：单步执行。若当前行有函数调用，则**进入该函数内部**。

但是，又有人想偷懒了。反复敲 `n` 与 `s` 依然很麻烦。怎么办呢？

gdb有个特性：若什么都不输，直接按回车，则会**执行上一次执行的命令**。

所以，只要开始敲个 `n` 或 `s`，然后一直敲回车就行了。

举个例子好了：

```scss
(gdb) b 13
Breakpoint 1 at 0x89b: file example.cpp, line 13.
(gdb) r
Starting program: /home/acceptedzhs/example 
10

Breakpoint 1, main () at example.cpp:13
13	    printf("%d\n",f(a));
(gdb) n
3628800
14	    return 0;
(gdb) [回车] //看到没，执行了上次的命令，即next
15	}
(gdb) 
```

```scss
Starting program: /home/acceptedzhs/example 
10

Breakpoint 1, main () at example.cpp:13
13	    printf("%d\n",f(a));
(gdb) s
f (x=10) at example.cpp:5 //step命令，进入了f函数内部
5	    int ans=1;
(gdb) 
```

## 输出变量/函数值

有时，我们想要打印某些变量或函数的值，看它是否符合期望。这又怎么办呢？

> 命令：`print（简写为p）`  
> 格式：`print 变量名`  
> 作用：打印**一次**变量名/函数调用对应的值。

> 命令：`display（简写为disp）`  
> 格式：`display 变量名`  
> 作用：设置在每一次停下来时（如到断点时，单步执行等）都打印该变量名/函数调用对应的值。

例子：

```perl
(gdb) b 13
Breakpoint 1 at 0x89b: file example.cpp, line 13.
(gdb) r
Starting program: /home/acceptedzhs/example 
9

Breakpoint 1, main () at example.cpp:13
13	    printf("%d\n",f(a));
(gdb) p a
$1 = 9
(gdb) n
362880 //输出9!
14	    return 0; //这只会显示一次，下一步就不会再打印该变量值了
(gdb) p f(2) //当然，调用函数也可以
$2 = 2
(gdb) 
```

```yaml
(gdb) b f
Breakpoint 1 at 0x841: file example.cpp, line 5.
(gdb) r
Starting program: /home/acceptedzhs/example 
9

Breakpoint 1, f (x=9) at example.cpp:5
5	    int ans=1;
(gdb) disp ans
1: ans = 0
(gdb) n
6	    for(int i=1;i<=x;i++) ans*=i;
1: ans = 1
(gdb) 
7	    return ans;
1: ans = 362880 //每次停下来时，该变量都会显示
(gdb) 
8	}
1: ans = 362880
(gdb)
```

有时，你可能会见到 \`\` 的提示。此时，请检查编译时是否开了优化（如 `-O2` ）。

## 查看某些信息

> 命令：`info（简写为i）` 
> 格式：`info 类型` 
> 作用：打印对应类型的信息。

其中，类型可以是 breakpoints（断点，简写b）、locals（局部变量，简写lo）、display（被设为总是显示的变量，简写 disp）、registers（寄存器，简写reg）等。具体可以通过 `help info` 查看。

比如：

```x86asm
(gdb) b 13
Breakpoint 1 at 0x89b: file example.cpp, line 13.
(gdb) r
Starting program: /home/acceptedzhs/example 
10 //程序的标准输入

Breakpoint 1, main () at example.cpp:13
13	    printf("%d\n",f(a));
(gdb) i lo
a = 10
(gdb) i b
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x000055555555489b in main() at example.cpp:13
	breakpoint already hit 1 time
(gdb) disp a
1: a = 10
(gdb) i display 
Auto-display expressions now in effect:
Num Enb Expression
1:   y  a
(gdb) 
```

我们发现输出了很多信息。其中 Num 是编号。编号有什么用呢？我们待会儿就要见到。

## 删除/禁用/启用某些东西

> 命令：`disable（简写为dis）` 
> 格式：`disable 类型 [编号]` 
> 作用：**临时禁用**某些类型的对应编号的东西，待会儿讲。

> 命令：`delete（简写为d）` 
> 格式：`delete 类型 [编号]` 
> 作用：**删除**某些类型的对应编号的东西。

> 命令：`enable（简写为en）` 
> 格式：`enable 类型 [编号]` 
> 作用：**启用**某些类型的对应编号的东西。

其中，类型就是讲述 `info` 命令时中的类型，编号就是 `info` 命令输出的一堆东西中的 Num 那一栏。

注意：delete **可能**用不了类型的简写。

举个例子：

```javascript
(gdb) b 12
Breakpoint 1 at 0x883: file example.cpp, line 12.
(gdb) info b
Num     Type           Disp Enb Address            What
1       breakpoint     keep n   0x0000555555554883 in main() at example.cpp:12
(gdb) dis b 1 //禁用1号断点
(gdb) r
Starting program: /home/acceptedzhs/example 
10
3628800 //不经过该断点了
[Inferior 1 (process 2695) exited normally]
(gdb) en b 1 //启用该断点
(gdb) r
Starting program: /home/acceptedzhs/example 

Breakpoint 1, main () at example.cpp:12
12	    scanf("%d",&a); //又经过该断点了
(gdb) d breakpoints 1 //删除
(gdb) r
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /home/acceptedzhs/example 
10
3628800 //又不经过断点了
[Inferior 1 (process 3516) exited normally] 
(gdb) 
```

## 获取帮助

有时，我们可能忘记某个命令的用法。这该怎么办呢？

> 命令：`help（简写为h）`  
> 格式：`help 待查询的命令（待查询的命令可以用简写）`  
> 作用：显示待查询的命令的帮助。

例子：

```vbnet
(gdb) h b
Set breakpoint at specified location.
break [PROBE_MODIFIER] [LOCATION] [thread THREADNUM] [if CONDITION]
PROBE_MODIFIER shall be present if the command is to be placed in a
probe point.  Accepted values are `-probe' (for a generic, automatically
guessed probe type), `-probe-stap' (for a SystemTap probe) or 
`-probe-dtrace' (for a DTrace probe).
LOCATION may be a linespec, address, or explicit location as described
below.

With no LOCATION, uses current execution address of the selected
stack frame.  This is useful for breaking on return to a stack frame.
...（省略若干行）...
```

# 命令一览表

| 命令       | 简写   | 作用                             |
| ---------- | ------ | -------------------------------- |
| `file`     | `fil`  | 载入可执行文件                   |
| `list`     | `l`    | 打印源代码                       |
| `quit`     | `q`    | 退出gdb                          |
| `break`    | `b`    | 设置断点                         |
| `run`      | `r`    | 从头运行程序                     |
| `continue` | `c`    | 从当前位置继续运行程序           |
| `until`    | `u`    | 从当前位置继续运行，直到指定行号 |
| `next`     | `n`    | 单步执行                         |
| `step`     | `s`    | 单步执行                         |
| `print`    | `p`    | 打印一次值                       |
| `display`  | `disp` | 设置某个变量/函数总是显示        |
| `info`     | `i`    | 打印相关类型的信息               |
| `disable`  | `dis`  | 临时禁用某些东西                 |
| `delete`   | `d`    | 删除某些东西                     |
| `enable`   | `en`   | 启用某些东西                     |
| `help`     | `h`    | 获取帮助                         |

# 图形界面

gdb 作为一个命令行调试器，对于某些人来说可能望而生畏。

所以，很多人为其开发了图形前端，以方便大家使用。

这里，我推荐 `nemiver`、`ddd`、`gdbgui`。（貌似不支持 windows）

如果你是个 Vim 爱好者，`vim-vebugger `也不错。

对上面不满意？可以试试 gdb 自带的伪图形界面，只要启动gdb时加上 `-tui` 选项即可。

# 如何开始调试？

首先要确保已经安装了gdb工具

随后在`xv6-labs-2021/`目录下输入`make qemu-gdb`命令，就会看到如下界面

![1680881053479](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1680881053479.png)

接下来重要的来了！我们要打开另一个终端窗口，修改自己`home`目录下的`.gdbinit`文件，允许`gdb`在`xv6-labs-2021`这个目录启动的时候，加载该文件下的`.gdbinit`文件。

只需要在`home`目录下输入，注意这里的`YOUR_PATH`是你的`xv6-labs-2021`具体在哪个地方，拿我举例，我的lab放在了`/home/hhs`下。

```bash
echo "add-auto-load-safe-path YOUR_PATH/xv6-labs-2021/.gdbinit " >> ~/.gdbinit
```

就像这样

![image-20230407233133459](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/image-20230407233133459.png)

随后输入`gdb-multiarch`命令启动gdb调试，出现如下界面即代表启动成功（gdb）

![1680881584564](https://picgo-picture-storage.oss-cn-guangzhou.aliyuncs.com/img/1680881584564.png)

这样我们就可以进行调试啦~
