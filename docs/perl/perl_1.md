# 第 1 节 Perl 简单介绍

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou

### 2\. 环境介绍

本实验环境采用 Ubuntu Linux 桌面环境，实验中会用到桌面上的程序：

1.  命令行终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令
2.  Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可
3.  gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)
4.  gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

### 3\. 环境使用

使用编辑器输入实验所需的代码及文件，使用命令行终端运行所需命令进行操作。

“实验记录”页面可以在“我的主页”中查看，每次实验的截图及笔记，以及有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您在实验楼学习的真实性证明。

### 4\. 实验介绍

本节介绍 Perl 语言，并讲解了它基本的一些数据类型，包括整型、浮点型和字符串型。

## 二、Perl 概述

### 1\. Perl 特性

Perl 具有高级语言的强大能力和灵活性，它不需要编译器和连接器来运行代码，只需要写出程序并告诉 Perl 来运行而已。具有与 C 语言相当的功能，像 awk, sed 等脚本描述语言一样方便。

### 2\. Perl 使用方法

进入 Perl 环境之后，可以直接输入相应的代码，实例如下：

```pl
$  vim hello.pl 
```

输入代码

```pl
#! /usr/bin/perl
print 'Hello World!';     #打印 hello world(注释) 
```

输入结束，按 ESC 返回到命令模式保存退出

```pl
:wq 
```

改变当前文件的可执行权限

```pl
$ chmod +x hello.pl 
```

测试执行文件

```pl
$ ./hello.pl
Hello World! 
```

## 三、数据类型

### 1\. 整型

perl 最常用的简单变量，由于其与其他语言相同，使用方法也一样，如：

```pl
#! /usr/bin/perl
$x=12345;
if(1217+116 == 1333){
    print $x;
} 
```

**整型的限制：**

perl 实际上把整数存在你的计算机中的浮点寄存器中，所以实际上被当做浮点数看待。在多数计算机中，浮点寄存器可以存贮约 16 位字，超出部分被丢弃。它是浮点数的一个特例。另外，8 进制数以 0 开始，如：\$var = 047,16 进制的数以 0x 开始，如：\$var=0x1f。

### 2\. 浮点数

如：2.13,1e+02 等，浮点寄存器通常不能精确地存储，从而会产生误差，在进行浮点运算的时候需要特别注意。例如：

```pl
$ vim float.pl 
```

输入代码

```pl
#! /usr/bin/perl
$value = 9.01e+21;
print("first value is ",$value,"\n");
$value = 9.01e21;
print("second value is ",$value,"\n"); 
```

输入结束，ESC 退出编辑模式，保存文件

```pl
:wq 
```

运行文件，查看测试结果

```pl
$ chmod +x float.pl
$ ./float.pl
first value is 9.01e+21
second value is 9.01e+21 
```

### 3\. 字符串

perl 中的字符串与 C 语言不同，它的末尾没有 NULL 字符，NULL 字符可以出现在字符串的任何位置，双引号内的字符串中支持简单变量替换，例如：

```pl
$number = 11;
$text = "This text contains the number $number."; 
```

则 $text 的内容为："This text contains the number 11."

双引号内的字符串中支持转义字符。单引号字符串与双引号字符串有两个区别，一是没有变量替换功能，二是反斜线不支持转义字符，而只在包含单引号和反斜线时起作用。单引号另一个特性是可以跨多行，如：

```pl
$text = 'This is two
lines of text
'; 
```

与下句等效：

```pl
$text = "This is two\nlines of text\n"; 
```

## 四、作业练习

请试着让整型、浮点型和字符串两两做加法运算，看看会有什么结果，并考虑为什么。

## 五、参考文档

> * 本实验课程基于 flamephoenix 翻译制作的版本教程版本。感谢原作者[flamephoenix]（http://flamephoenix.126.com）