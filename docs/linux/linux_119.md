# 第 9 节 多进程编程(三)

## 一、实验说明

### 1\. 课程说明

本节是介绍 Linux 系统多进程编程的最后一节课程。会涉及一些 gdb 在调试多进程程序方面的技巧，以及经进程消亡相关的知识点。

### 2\. 如果首次使用 Linux，建议首先学习：

1.  [Linux 基础入门](http://www.shiyanlou.com/courses/1)
2.  [Vim 编辑器](http://www.shiyanlou.com/courses/2)

### 3\. 环境介绍

本实验环境采用带桌面的 Ubuntu Linux 环境，实验中会用到桌面上的程序： 1.命令行终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令

2.Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可

3.gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)

4.gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

## 二、gdb 调试多进程程序的技巧

对多进程程序进行调试，存在一个较大的难题，那就是当程序调用 fork 产生子进程后，gdb 跟踪的是父进程，无法进入到子进程里去单步调试子进程。这样一来，如果子进程中的代码运行出错的话，将无法进行调试。

因此想调试子进程的话，需要一点技巧：

1.  在子进程的入口处加入**sleep(20)**函数，以使子进程在被创建后能暂时停止。
2.  用 ps 查看子进程的 pid，假定 pid 为 222，则输入命令：gdb 程序名称 222。从而再运行一个调试程序，使得 gdb attach 到子进程。
3.  用 gdb 的 break 命令在子进程中设定断点。
4.  用 gdb 的 continue，恢复子进程的运行。
5.  等待 sleep 的睡眠时间到达，从而子进程将在断点处停下来。

## 三、进程的消亡

### 1\. 启动例程与 main 函数

从程序员的角度看，C 应用程序从 main 函数开始运行。但事实上，当 C 应用程序被内核通过 exec 启动时，一个启动例程会先于 main 函数运行，它会为 main 函数的运行准备好环境后，调用 main 函数。而 main 函数正常结束后 return 语句将使得 main 函数返回到启动例程，启动例程在完成必要的善后处理后将最终调用 _exit 结束进程。

有 5 冲方式结束进程： **正常结束：** 1.从 main 函数返回 2.调用 exit 3.调用 _exit

**非正常结束：** 4.调用 abort 5.被信号中止

### 2\. exit 函数与 _exit 函数

需要包含的头文件：`<stdlib.h>`、`<unistd.h>` 函数原型： `void exit(int status)`、 `void _exit(int status)`

这两个函数的功能都是使进程正常结束。 `_exit`：立即返回内核，它是一个系统调用 `exit`：在返回内核钱会执行一些清理操作，这些清理操作包括调用 exit handler，以及彻底关闭标准 I/O 流(这回使得 I/O 流的 buffer 中的数据被刷新，即被提交给内核)，它是标准 C 库中的一个函数。

### 3\. I/O 流和 I/O 库缓存

上一节提到 I/O 流以及 I/O 流的 buffer，我们现在来了解一下。

**iocache.c**

```
1 #include <stdio.h>
2 #include <unistd.h>
3 
4 int main(void)
5 {
6     printf("hello");
7     //printf("hello\n");
8     //write(1, "hello", 5);
9     sleep(100);
10    return 0;
11 } 
```

**你将会看到的是，没有任何输出！为什么呢？**

当应用程序调用 printf 时，将字符串"hello"提交给了标准 I/O 库的 I/O 库缓存。I/O 库缓存大致可以认为是 printf 实现中定义的全局字符数组，因此它位于用户空间，可见"hello"并没有被提交给内核(所以也不可能出现内核将"hello"打印到屏幕的操作)，所以没有打印出任何东西。只有当某些条件满足时，标准 I/O 库才会刷新 I/O 库缓存，这些条件包括：

1.  用户空间的 I/O 库缓存已被填满
2.  I/O 库缓存遇到了换行符(‘\n’)，并且输出目标是行缓冲设备(屏幕就是这种设备)。因此将上面的代码第 6 行注释掉，并取消第 7 行的注释，就可以看到打印出了 hello
3.  I/O 流被关闭，上节中的 exit 函数就会关闭 I/O 流

> Tips: > 当标准 I/O 库缓存时，会调用以前的我们学过的系统调用，例如：write，将 I/O 库缓存中的内容提交给内核。 > so，上述代码也可以这样：第 6 行注释，第 7 行注释，第 8 行取消注释。也可以在屏幕上看见"hello"

### 4\. Exit handler

Exit handler 是程序员编写的函数，进程正常结束时，它们会被系统调回。这使程序员具备了在进程正常结束时，控制进程执行某些善后操作的能力。 使用 Exit handler，需要程序员完成两件事情：编写 Exit handler 函数；调用 atexit 或 on_exit 向系统注册 Exit handler(即告知系统需要回调的 Exit handler 函数是谁)

需要包含头的文件：`<stdlib.h>` 函数原型：

```
int atexit(void (* func)(void)) 
```

```
int on_exit(void (* func)(int, void *),) 
```

功能： **atexit**注册的函数 func 没有参数；**on_exit**注册的函数 func 有一个 int 型参数，系统调用回调 func 时将向该参数传入进程的退出值，func 的另一个 void *类型参数将会是 arg。

ANSI C 中，进程最多可以注册 32 个 Exit handler 函数，这些函数按照注册时的顺序被逆序调用。

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
static void my_exit0(int, void *);
static void my_exit1(void);
static void my_exit2(void);
char str[9]="for test";
int main(void) {
        //char str[9]="for test";
    if (atexit(my_exit2) != 0) {
        perror("can't register my_exit2"); 
        exit(-1); 
    }
    if (atexit(my_exit1) != 0) { 
        perror("can't register my_exit1"); 
        exit(-1); 
    }
    if (on_exit(my_exit0,(void *)str) !=0) {
        perror("can't register my_exit0"); 
        exit(-1); 
    }
    printf("main is done\n");
    printf("abc");
    //_exit(1234);
    exit(1234);
}

static void my_exit0(int status, void *arg) {
    printf("zero exit handler\n");
    printf("exit %d\n", status);
    printf("arg=%s\n",(char *)arg);
}

static void my_exit1(void) {
    printf("first exit handler\n");
}
static void my_exit2(void)  {
    printf("second exit handler\n");
} 
```