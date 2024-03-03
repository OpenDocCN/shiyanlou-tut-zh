# 第 5 节 文件 IO（一）

## 一、实验说明

### 1\. 课程说明

本节课程介绍 Linux 系统的文件 IO，除了介绍其基本概念，最主要的是讲解其基本 APIs，包括 open、close、read、write 等等。

### 2\. 如果首次使用 Linux，建议首先学习：

1.  [Linux 基础入门](http://www.shiyanlou.com/courses/1)
2.  [Vim 编辑器](http://www.shiyanlou.com/courses/2)

### 3\. 环境介绍

本实验环境采用带桌面的 Ubuntu Linux 环境，实验中会用到桌面上的程序： 1.命令行终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令

2.Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可

3.gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)

4.gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

## 二、文件 I\O 介绍

### 1\. Linux 系统调用

Linux 系统调用(system call)是指操作系统提供给用户程序的一组“特殊接口”，用户程序可以通过这组“特殊”接口来获得操作系统提供的特殊服务。

为了更好的保护内核空间，将程序的运行空间分为内核空间和用户空间，他们运行在不同的级别上，在逻辑上是相互隔离的。在 Linux 中，用户程序不能直接访问内核提供的服务，必须通过系统调用来使用内核提供的服务。

Linux 中的用户编程接口（API）遵循了 UNIX 中最流行的应用编程界面标准——POSIX。这些系统调用编程接口主要是通过 C 库(libc)实现的。

### 2\. 文件描述符

对内核而言，所有打开文件都由文件描述符引用。文件描述符是一个*非负整数*。当打开一个现存文件或创建一个新文件时，内核向进程返回一个文件描述符。当写一个文件时，用 open 或 create 返回的文件描述符标识该文件，将其作为参数传送给 read 或 write。

在 POSIX 应用程序中，整数 0、1、2 应被代换成符号常数：

1.  STDIN_FILENO(标准输入，默认是键盘)
2.  STDOUT_FILENO(标准输出，默认是屏幕)
3.  STDERR_FILENO(标准错误输出，默认是屏幕)

这些常数都定义在头文件`<unistd.h>`中，文件描述符的范围是 0～OPEN_MAX。早期的 UNIX 版本采用的上限值是 19（允许每个进程打开 20 个文件）， 现在很多系统则将其增加至 256。

可用的文件 I\O 函数很多，包括：打开文件，读文件，写文件等。大多数 Linux 文件 I\O 只需要用到 5 个函数：open，read，write，lseek 以及 close。

## 三、 基本 API

### 1\. open

需要包含的头文件：`<sys/types.h>`, `<sys/stat.h>`, `<fcntl.h>` 函数原型：

```
int open(const str * pathname, int oflag, [..., mode_t mode]) 
```

功能：打开文件 返回值：成功则返回文件描述符，出错返回-1 参数：

**pathname**: 打开或创建的文件的全路径名 **oflag**：可用来说明此函数的多个选择项， 详见后。 **mode**：对于 open 函数而言，仅当创建新闻件时才使用第三个参数，表示新建文件的权限设置。 > 详解 oflag 参数： > oflag 参数由 O*RDONLY(只读打开)、O*WRONLY(只写打开)、O*RDWR(读写打开)中的一个于下列一个或多个常数 > O*APPEND: 追加到文件尾 > O*CREAT: 若文件不存在则创建它。使用此选择项时，需同时说明第三个参数 mode，用其说明新闻件的访问权限 > O*EXCL: 如果同时指定 O*CREAT，而该文件又是存在的，报错；也可以测试一个文件是否存在，不存在则创建。 > O*TRUNC: 如果次文件存在，而且为读写或只写成功打开，则将其长度截短为 0 > O_SYNC: 使每次 write 都等到物理 I\O 操作完成

用 open 创建一个文件： **open.c**

```
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

#define FILE_PATH   "./test.txt"

int main(void)
{
    int fd;
    if ((fd = open(FILE_PATH, O_RDWR | O_CREAT | O_EXCL, 0666)) < 0) {
        printf("open error\n");
        exit(-1);
    } else {
        printf("open success\n");
    }
    return 0;
} 
```

如果当前目录下以存在 test.txt，屏幕上就会打印“open error”；不存在则创建该文件，并打印“open success”

### 2\. read

需要包含的头文件：`<unistd.h>`

函数原型：

```
ssize_t read(int fd, void * buf, size_t count) 
```

功能：从打开的文件中读取数据。 返回值：实际读到的字节数；已读到文件尾返回 0，出错的话返回-1，ssize*t 是系统头文件中用 typedef 定义的数据类型相当于 signed int 参数： **fd**：要读取的文件的描述符 **buf**：得到的数据在内存中的位置的首地址 **count**：期望本次能读取到的最大字节数。size*t 是系统头文件中用 typedef 定义的数据类型，相当于 unsigned int

### 3\. write

需要包含的头文件：`<unistd.h>` 函数原型：

```
ssize_t write(int fd, const void * buf, size_t count) 
```

功能：向打开的文件写数据 返回值：写入成功返回实际写入的字节数，出错返回-1 > 不得不提的是，返回-1 的常见原因是：磁盘空间已满，超过了一个给定进程的文件长度

参数： **fd**：要写入文件的文件描述符 **buf**：要写入文件的数据在内存中存放位置的首地址 **count**：期望写入的数据的最大字节数

**read 和 write 使用范例**：

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void)
{
    char buf[100];
    int num = 0;

    // 获取键盘输入，还记得 POSIX 的文件描述符吗？
    if ((num = read(STDIN_FILENO, buf, 10)) == -1) {
        printf ("read error");
        error(-1);
    } else {
    // 将键盘输入又输出到屏幕上
        write(STDOUT_FILENO, buf, num);
    }

    return 0;
} 
```

### 4\. close

需要包含的头文件：`<unistd.h>` 函数原型：`int close(int filedes)` 功能：关闭一个打开的文件 参数：需要关闭文件的文件描述符

> 当一个进程终止的时候，它所有的打开文件都是由内核自动关闭。很多程序都使用这一功能而不显式地调用 close 关闭一个已打开的文件。 > 但是，作为一名优秀的程序员，应该显式的调用 close 来关闭已不再使用的文件。

### 5\. lseek

每个打开的文件都有一个“当前文件偏移量”，是一个非负整数，用以度量从文件开始处计算的字节数。通常，读写操作都是从当前文件偏移量处开始，并使偏移量增加所读或写的字节数。默认情况下，你打开一个文件时(open)，除非指定 O_APPEND 参数，不然位移量被设为 0。

需要包含的头文件：`<sys/types.h>`, `<unistd.h>` 函数原型：

```
off_t lseek(int filesdes, off_t offset, int whence) 
```

功能：设置文件内容读写位置 返回值：成功返回新的文件位移，出错返回-1；同样 off_t 是系统头文件定义的数据类型，相当于 signed int 参数：

1.  whence 是 SEEK_SET, 那么该文件的位移量设置为据文件开始处 offset 个字节
2.  whence 是 SEEK_CUR, 那么该文件的位移量设置为当前值加 offset。offset 可为正或负
3.  whence 是 SEEK_END, 那么盖文佳的位移量设置为文件长度加 offset。offset 可为正或负

```
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
#include <fcntl.h>

int main(int argc, char * argv[])
{
    int fd;
    char buf[100];
    if ((fd = open(argv[1], O_RDONLY)) < 0) {
        perror("open");
        exit(-1);
    }
    read(fd, buf, 1);
    write(STDOUT_FILENO, buf, 1);
    lseek(fd, 2, SEEK_CUR);

    read(fd, buf, 1);
    write(STDOUT_FILENO, buf, 1);
    lseek(fd, -1, SEEK_END);

    read(fd, buf, 1);
    write(STDOUT_FILENO, buf, 1);
    lseek(fd, 0, SEEK_SET);

    read(fd, buf, 1);
    write(STDOUT_FILENO, buf, 1);
    close(fd);
    printf("\n");

    return 0;
} 
```

### 6\. select

之前的 read 函数可以监控一个文件描述符(eg：键盘)是否有输入，当键盘没有输入，read 将会阻塞，直到用户从键盘输入为止。用相同的方法可以监控鼠标是否有输入。但想同时监控鼠标和键盘是否有输入，这个方法就不行的了。

```
// /dev/input/mice 是鼠标的设备文件
fd = open("/dev/input/mice", O_RDONLY);
read(0, buf, 100);
read(fd, buf, 100); 
```

在上面的程序中，当 read 键盘的时候，若无键盘输入则程序阻塞在第 2 行，此时即使鼠标有输入，程序也没有机会执行第 3 行获得鼠标的输入。这种情况就需要 select 同时监控多个文件描述符。

需要包含的头文件：`<sys/select.h>` 函数原型：

```
int select(int maxfd, fd_set \* readset, fd_set \* writeset, fd_set \* exceptset, const struct timeval \* timeout) 
```

返回值：失败返回-1，成功返回 readset，writeset，exceptset 中所有，有指定变化的文件描述符的数目(若超时返回 0)

参数： **maxfd**：要检测的描述符个数， 因此值应为最大描述符+1 **readset**：被监控是否有输入的文件描述符集。不监控时，设为 NULL **writeset**：被监控是否可以输入的文件描述符集。不监控时，设为 NULL **exceptset**：被监控是否有错误产生的文件描述符集。不监控时，设为 NULL **timeval**：监控超时时间。设置为 NULL 表示一直阻塞到有文件描述符被监控到有指定变化。

> Tips: > readset，writeset，exceptset 这三个描述符集指针均是**值—结果**参数，调用的时候，被监控描述符相应位需要置 1；返回时，未就绪的描数字相应位会被清 0，而就绪的会被置 1。 > 下面的系统定义的宏，和 select 配套使用 > **FD*ZERO(&rset)***：将文件描述符集 rset 的所有位清 0 > **FDSET(4, &reset)**：设置文件描述符集 rset 的 bit 4 > **FD*CLR(fileno(stdin), &rset)***：将文件描述符集 rset 的 bit 0 清 0 > **FDISSET(socketfd, &rset)**：若文件描述符集 rset 中的 socketfd 位置 1

```
#include <stdio.h>
#include <sys/select.h>
#include <fcntl.h>
#include <unistd.h>

#define MAXNUM      100
#define OPEN_DEV    "/dev/input/mice"

int main(void)
{
    fd_set rfds;
    struct timeval tv;
    int retval, fd;
    char buf[MAXNUM];

    fd = open(OPEN_DEV, O_RDONLY);
    while (1) {
        FD_ZERO(&amp;rfds);
        FD_SET(0, &amp;rfds);
        FD_SET(fd, &amp;rfds);
        tv.tv_sec = 5;
        tv.tv_usec = 0;

        retval = select(fd+1, &amp;rfds, NULL, NULL, &amp;tv);
        if (retval < 0)
            printf ("error\n");
        if (retval == 0)
            printf ("No data within 5 seconds\n");
        if (retval > 0) {
            if (FD_ISSET(0, &amp;rfds)) {
                printf ("Data is available from keyboard now\n");
                read(0, buf, MAXNUM);
            }
            if (FD_ISSET(fd, &amp;rfds)) {
                printf ("Data is available from mouse now\n");
                read(fd, buf, MAXNUM);
            }
        }
    }
    return 0;
} 
```