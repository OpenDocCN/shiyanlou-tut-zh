# 第 3 节 GDB 使用

## 一、实验说明

### 1\. 课程说明

工欲善其事, 必先利其器，因此会从编程工具 gcc，gdb 入手逐步讲解 Linux 系统编程。上次我们讲解了 gcc 编译器的使用，然而没有什么事物是完美无缺的，往往写出来的程序都会有不同程度的缺陷，因此本节课程将讲解 gdb 调试器（Debug）的使用，它可以帮助我们找出程序之中的错误和漏洞等等。

### 2\. 如果首次使用 Linux，建议首先学习：

1.  [Linux 基础入门](http://www.shiyanlou.com/courses/1)
2.  [Vim 编辑器](http://www.shiyanlou.com/courses/2)

### 3\. 环境介绍

本实验环境采用带桌面的 Ubuntu Linux 环境，实验中会用到桌面上的程序： 1.命令行终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令

2.Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可

3.gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)

4.gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

## 二、gdb 概 述

当程序编译完成后，它可能无法正常运行；或许程序会彻底崩溃；或许只是不能正常地运行某些功能；或许它的输出会被挂起；或许不会提示要求正常的输入。无论在何种情况下，跟踪这些问题，特别是在大的工程中，将是开发中最困难的部分，我们将学习 gdb(GNU debugger)调试程序的方法，该程序是一个调试器，是用来帮助程序员寻找程序中的错误的软件。

gdb 是 GNU 开发组织发布的一个强大的 UNIX/Linux 下的程序调试工具。或许，有人比较习惯图形界面方式的，像 VC、BCB 等 IDE 环境，但是在 UNIX/Linux 平台下做软件，gdb 这个调试工具有比 VC、BCB 的图形化调试器更强大的功能。所谓“寸有所长，尺有所短”就是这个道理。 一般来说，gdb 主要帮忙用户完成下面 4 个方面的功能：

1.  启动程序，可以按照用户自定义的要求随心所欲的运行程序。
2.  可让被调试的程序在用户所指定的调试的断点处停住 (断点可以是条件表达式)。
3.  当程序停住时，可以检查此时程序中所发生的事。
4.  动态地改变程序的执行环境。
5.  从上面来看，gdb 和一般的调试工具区别不大，基本上也是完成这些功能，不过在细节上，会发现 gdb 这个调试工具的强大。大家可能习惯了图形化的调试工具，但有时候，命令行的调试工具却有着图形化工具所不能完成的功能。­­­­­­­­­­­­­­­­­­­­­­­­­­­

**gdb.c**

```
#include <stdio.h>
int func(int n)
{
    int sum=0,i;
    for(i=0; i<n; i++) {
        sum+=i;
    }
    return sum;
}

int main(void)
{
    int i;
    long result = 0;
    for(i=1; i<=100; i++) {
        result += i;
    }
    printf("result[1-100] = %ld \n", result );
    printf("result[1-250] = %d \n", func(250) );
 } 
```

编译生成执行文件(Linux 下)：

```
$ gcc –g gdb.c -o testgdb
使用 gdb 调试：
$ gdb testgdb <---------- 启动 gdb
.......此处省略一万行

键入 l 命令相当于 list 命令，从第一行开始列出源码：
$ gdb testgdb
.......此处省略一万行

(gdb) l
7       {
8           sum+=i;
9       }
10      return sum;
11 }
12
13 int main(void)
14 {
15      int i;
16      long result = 0;
(gdb)
17      for(i=1; i<=100; i++)
18      {
19          result += i;
20      }
21      printf("result[1-100] = %ld \n", result );
22      printf("result[1-250] = %d \n", func(250) );
23 }
(gdb) break 16 <-------------------- 设置断点，在源程序第 16 行处。
Breakpoint 1 at 0x804836a: file test.c, line 16.
(gdb) break func <-------------------- 设置断点，在函数 func()入口处。
Breakpoint 2 at 0x804832e: file test.c, line 5.
(gdb) info break <-------------------- 查看断点信息。
Num Type           Disp Enb Address    What
1   breakpoint     keep y   0x0804836a in main at test.c:16
2   breakpoint     keep y   0x0804832e in func at test.c:5
(gdb) r <--------------------- 运行程序，run 命令简写
Starting program: /home/shiyanlou/testgdb

Breakpoint 1, main () at test.c:16 <---------- 在断点处停住。
16                   long result = 0;
(gdb) n <--------------------- 单条语句执行，next 命令简写。
17                   for(i=1; i<=100; i++)
(gdb) n
19                           result += i;
(gdb) n
17                   for(i=1; i<=100; i++)
(gdb) n
19                           result += i;
(gdb) n
17                   for(i=1; i<=100; i++)
(gdb) c     <--------------------- 继续运行程序，continue 命令简写。
Continuing.
result[1-100] = 5050  <----------程序输出。

Breakpoint 2, func (n=250) at test.c:5
5                   int sum=0,i;
(gdb) n
6                    for(i=0; i<n; i++)
(gdb) p I    <--------------------- 打印变量 i 的值，print 命令简写。
$1 = 1107620064
(gdb) n
8                           sum+=i;
(gdb) n
6                    for(i=0; i<n; i++)
(gdb) p sum
$2 = 0
(gdb) bt     <--------------------- 查看函数堆栈。
#0 func (n=250) at test.c:6
#1 0x080483b2 in main () at test.c:22
#2 0x42015574 in __libc_start_main () from /lib/tls/libc.so.6
(gdb) finish <--------------------- 退出函数。
Run till exit from #0 func (n=250) at test.c:6
0x080483b2 in main () at test.c:22
22   printf("result[1-250] = %d /n", func(250) );
Value returned is $3 = 31125
(gdb) c <--------------------- 继续运行。
Continuing.
result[1-250] = 31125

Program exited with code 027\. <--------程序退出，调试结束。
(gdb) q     <--------------------- 退出 gdb。 
```

有了以上的感性认识，下面来系统地学习一下 gdb。

## 三、使 用 gdb

gdb 主要调试的是 C/C++的程序。要调试 C/C++的程序，首先在编译时，必须要把调试信息加到可执行文件中。使用编译器(cc/gcc/g++)的 -g 参数即可。如：

```
$ gcc -g hello.c -o hello
$ g++ -g hello.cpp -o hello 
```

如果没有-g，将看不见程序的函数名和变量名，代替它们的全是运行时的内存地址。当用-g 把调试信息加入，并成功编译目标代码以后，看看如何用 gdb 来调试。 启动 gdb 的方法有以下几种：

1.  gdb <program> program 也就是执行文件，一般在当前目录下。
2.  gdb <program> core 用 gdb 同时调试一个运行程序和 core 文件，core 是程序非法执行后，core dump 后产生的文件。
3.  gdb <program> <PID> 如果程序是一个服务程序，那么可以指定这个服务程序运行时的进程 ID。gdb 会自动 attach 上去，并调试它。program 应该在 PATH 环境变量中搜索得到。 gdb 启动时，可以加上一些 gdb 的启动开关，详细的开关可以用 gdb -help 查看。下面只列举一些比较常用的参数： -symbols <file> -s <file> 从指定文件中读取符号表。 -se file 从指定文件中读取符号表信息，并把它用在可执行文件中。 -core <file> -c <file> 调试时 core dump 的 core 文件。 -directory <directory> -d <directory> 加入一个源文件的搜索路径。默认搜索路径是环境变量中 PATH 所定义的路径。

## 四、作业思考

试着在上节课的例子中，使用 gdb 调试。