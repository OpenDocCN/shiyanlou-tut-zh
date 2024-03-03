# 第 1 节 C 语言实现 Linux touch 命令

## 一、实验简介

linux 的 touch 命令用来修改文件时间戳，或者新建一个不存在的文件，使用方式如下：

```
touch [-acm][-r ref_file(参照文件)|-t time(时间值)] file(文件名) 
```

本实验基于 C 语言实现 Linux `touch` 命令的 `[-acm]` 选项。

注:

`touch file1.txt` 更新 file1.txt 的存取和修改时间
`touch -a file1.txt` 改变 file1.txt 的读取时间记录
`touch -c file1.txt` 如果 file1.txt 不存在，不创建文件
`touch -m file1.txt` 改变 file1.txt 的修改时间记录

### 1.1 知识点

*   知识点 1: 函数 `int getopt(int argc,char * const argv[ ],const char * optstring);` 包含在头文件 `#include <unistd.h>`，作用： 分析命令行参数。 返回值：参数字符。
*   知识点 2: 函数 open 是 UNIX 系统的系统调用函数，区别于 C 语言库函数 fopen。包含在头文件 `#include <fcntl.h>`，作用：打开和创建文件。

```
int open(constchar*pathname, int flags);    
int open(constchar*pathname, int flags, mode_t mode); 
```

返回值：成功则返回文件描述符，否则返回-1
参数说明：对于 open 函数来说，第三个参数仅当创建新文件时（即使用了 O_CREAT 时）才使用，用于指定文件的访问权限位（access permission bits）。pathname 是待打开/创建文件的 POSIX 路径名（如/home/user/a.cpp）；flags 用于指定文件的打开/创建模式，这个参数可由以下常量（定义于 fcntl.h）通过逻辑位或逻辑构成。

```
O_RDONLY 只读模式
O_WRONLY 只写模式
O_RDWR 读写模式 
```

参数 `mode` 则有下列数种组合, 只有在建立新文件时才会生效, 此外真正建文件时的权限会受到`umask` 值所影响, 因此该文件权限应该为 (`mode-umaks`).
`S_IRWXU` 00700 权限, 代表该文件所有者具有可读、可写及可执行的权限.
`S_IRUSR` 或`S_IREAD`, 00400 权限, 代表该文件所有者具有可读取的权限.
`S_IWUSR` 或`S_IWRITE`, 00200 权限, 代表该文件所有者具有可写入的权限.
`S_IXUSR` 或`S_IEXEC`, 00100 权限, 代表该文件所有者具有可执行的权限.
`S_IRWXG` 00070 权限, 代表该文件用户组具有可读、可写及可执行的权限.
`S_IRGRP` 00040 权限, 代表该文件用户组具有可读的权限.
`S_IWGRP` 00020 权限, 代表该文件用户组具有可写入的权限.
`S_IXGRP` 00010 权限, 代表该文件用户组具有可执行的权限.
`S_IRWXO` 00007 权限, 代表其他用户具有可读、可写及可执行的权限.
`S_IROTH` 00004 权限, 代表其他用户具有可读的权限
`S_IWOTH` 00002 权限, 代表其他用户具有可写入的权限.
`S_IXOTH` 00001 权限, 代表其他用户具有可执行的权限.

*   知识点 3： 函数
    `int utimensat(int dirfd, const char *pathname,const struct timespec times[2], intflags);`,包含在`#include <sys/stat.h>`，作用：utimensat 是以纳秒级的精度改变文件的时间戳。`utimensat()`通过文件的路径（`pathname`）获得文件。
    这个系统调用函数都是通过一个时间数组`times`来改变时间戳的，`times[0]`修改最后一次访问的时间(access time)，`times[1]`修改最后修改的时间(modify time)。该时间数组是由秒和纳秒两个部分组成，数据结构如下：

```
struct timespec {
time_t tv_sec; /* 秒 */  
long tv_nsec; /* 纳秒 */
}; 
```

特别注意：当 `times[x].tv_sec = UTIME_OMIT;` 相应的时间戳不变，`times[x].tv_sec = UTIME_NOW;` 相应的时间戳编程当前时间

## 二、实验步骤 1-设计 main()函数

`main()` 完成的工作是从命令行接收参数，并解析选项，设置相应的标志变量，然后调用 mytouch 函数。
main 函数中两个参数是用来接收命令行传进来的参数`int argc, char **argv`。

初始化 `ok`、`change_times`、`no_create`：

```
bool ok = true;
change_times = 0;
no_create = false; 
```

从命令行中得到命令的选项，即以'-'开头的参数。目前只支持三种选型-a, -c, -m，则需给 getopt 传参"acm"即可。代码如下:

```
while ((c = getopt(argc, argv, "acm")) != -1) 
```

然后根据不同的情况设置不同的标志变量：

```
switch (c)
        {
        case 'a':
            change_times |= CH_ATIME;
            break;

        case 'c':
            no_create = true;
            break;

        case 'm':
            change_times |= CH_MTIME;
            break;

        default:
            printf("fault option!"); 
```

main 函数完整代码如下：

```
int
main(int argc, char **argv)
{
    int c;
    bool ok = true;
    change_times = 0;
    no_create = false;
    //从命令行中得到命令的选项，即以'-'开头的参数。目前只支持三种选型-a, -c, -m。
    while ((c = getopt(argc, argv, "acm")) != -1)
    {
        switch (c)
        {
        case 'a':
            change_times |= CH_ATIME;
            break;

        case 'c':
            no_create = true;
            break;

        case 'm':
            change_times |= CH_MTIME;
            break;

        default:
            printf("fault option!");
        }
    }

    if (change_times == 0)
        change_times = CH_ATIME | CH_MTIME;
    //当 newtime[0].
    newtime[0].tv_nsec = UTIME_NOW;
    newtime[1].tv_nsec = UTIME_NOW;
    //如果 optind == argc，代表少输入文件名字。
    if (optind == argc)
    {
        printf("missing file operand\n");
    } 
    //针对多个文件名字调用 mytouch 函数
    for (; optind < argc; ++optind)
        ok &amp;= mytouch(argv[optind]);

    exit(ok ? EXIT_SUCCESS : EXIT_FAILURE);
} 
```

## 三、实验步骤 2-设计 mytouch 函数

mytouch 主要实现创建文件和更改文件的两种之间的功能。创建文件代码如下：

对于 `open` 函数来说，`file` 是用户输入的文件名字。`O_CREAT | O_WRONLY` 代表该函数的选项是文件不存在则创建和只写打开。最后一个参数指定创建的文件是权限是所有用户可读可写。

```
fd = open(file, O_CREAT | O_WRONLY, MODE_RW_UGO); 
```

修改文件的时间戳使用如下代码：

对于 `utimensat` 函数来说，第一个参数 `AT_FDCWD` 指定创建文件的路径是当前工作目录。第二个参数 `file` 指定要修改的时间戳文件名。第三个参数 `newtime` 指定修改的具体时间值。最后一个参数默认为 0。

```
utimensat(AT_FDCWD, file, newtime, 0) 
```

mytouch 函数具体的函数逻辑如下：

该函数首先判断 `no_create` 是否为假，若为假，则创建文件。然后检测 `change_times` 的值，并根据其值做相关的处理。

完整的实现代码：

```
static bool
mytouch(const char *file)
{
    bool ok;
    int fd = -1;
    if (!no_create)
    {
        fd = open(file, O_CREAT | O_WRONLY, MODE_RW_UGO);
    }
    //在主函数中，如果没有检测到（-a）（-m），则 change_times == (CH_ATIME | CH_MTIME)，\
    否则按照选项只用改变其中一个时间戳。
    if (change_times != (CH_ATIME | CH_MTIME))
    {
        //只设定其中一个时间值。
        if (change_times == CH_MTIME)
            //如果 change_times == CH_MTIME，即（-m）,将对应的修改 access time\
            的 timespec 结构的 tv_nsec 设置为 UTIME_OMIT;参考 utimensat 函数的用法
            newtime[0].tv_nsec = UTIME_OMIT;
        else
            //如果 change_times == CH_MTIME，即（-a）,将对应的修改 modify time\
            的 timespec 结构的 tv_nsec 设置为 UTIME_OMIT;参考 utimensat 函数的用法
            newtime[1].tv_nsec = UTIME_OMIT;
    }
        //AT_FDCWD 表示当前工作目录。
        ok = (utimensat(AT_FDCWD, file, newtime, 0) == 0);
    return true;
} 
```

## 四、实验步骤 3-编译并测试

### 4.1 编译程序

首先要编译源程序。在实验楼环境中，采用 gcc 编译器，生成名字为 mytouch 的可执行文件，具体命令如下：

![此处输入图片的描述](img/wm)

### 4.2 测试 mytouch

创建名字为 shiyanlou 的文件。并且查看该文件的状态，注意到最近访问时间、最近更改时间和最近改动时间均是执行 mytouch 的时间。具体命令如下：

![此处输入图片的描述](img/wm)

#### 测试-a 选项

![此处输入图片的描述](img/wm)

#### 测试-m 选项

![此处输入图片的描述](img/wm)

#### 测试-c 选项

![此处输入图片的描述](img/wm)

## 五、实验总结

本实验涉及的知识点包括文件 IO 相关的系统调用函数：`open`，`utimensat`，`getopt`等。完成本实验可以对 UNIX 文件系统及文件系统 IO 操作有深入的了解。

本项目完整代码：

```
#include <stdio.h>
#include <getopt.h>
#include <sys/types.h>
#include <time.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdbool.h>
#include <sys/time.h>
#include <sys/stat.h>

#define CH_ATIME 1
#define CH_MTIME 2

//定义创建文件时的模式，此处对用户，组，其他设置的权限都是可读可写。
#define MODE_RW_UGO (S_IRUSR | S_IWUSR | S_IRGRP | S_IWGRP | S_IROTH | S_IWOTH)

//标志文件 access time 和 modify time 的改变情况
static int change_times;

// 如果有(-c)选项，并且不存在命令行中输入的文件名，则不创建 
static bool no_create;

//当设置新的 access time 和 modify time 的时候使用
static struct timespec newtime[2];

//mytouch 命令核心的核心模块，用于创建或者更新文件的时间戳。
static bool
mytouch(const char *file)
{
    bool ok;
    int fd = -1;
    if (!no_create)
    {
        fd = open(file, O_CREAT | O_WRONLY, MODE_RW_UGO);
    }
    //在主函数中，如果没有检测到（-a）（-m），则 change_times == (CH_ATIME | CH_MTIME)，\
    否则按照选项只用改变其中一个时间戳。
    if (change_times != (CH_ATIME | CH_MTIME))
    {
        //只设定其中一个时间值。
        if (change_times == CH_MTIME)
            //如果 change_times == CH_MTIME，即（-m）,将对应的修改 access time\
            的 timespec 结构的 tv_nsec 设置为 UTIME_OMIT;参考 utimensat 函数的用法
            newtime[0].tv_nsec = UTIME_OMIT;
        else
            //如果 change_times == CH_MTIME，即（-a）,将对应的修改 modify time\
            的 timespec 结构的 tv_nsec 设置为 UTIME_OMIT;参考 utimensat 函数的用法
            newtime[1].tv_nsec = UTIME_OMIT;
    }
        //AT_FDCWD 表示当前工作目录。
        ok = (utimensat(AT_FDCWD, file, newtime, 0) == 0);
    return true;
}

int
main(int argc, char **argv)
{
    int c;
    bool ok = true;
    change_times = 0;
    no_create = false;
    //从命令行中得到命令的选项，即以'-'开头的参数。目前只支持三种选型-a, -c, -m。
    while ((c = getopt(argc, argv, "acm")) != -1)
    {
        switch (c)
        {
        case 'a':
            change_times |= CH_ATIME;
            break;

        case 'c':
            no_create = true;
            break;

        case 'm':
            change_times |= CH_MTIME;
            break;

        default:
            printf("fault option!");
        }
    }

    if (change_times == 0)
        change_times = CH_ATIME | CH_MTIME;
    //当 newtime[0].
    newtime[0].tv_nsec = UTIME_NOW;
    newtime[1].tv_nsec = UTIME_NOW;
    //如果 optind == argc，代表少输入文件名字。
    if (optind == argc)
    {
        printf("missing file operand\n");
    } 
    //针对多个文件名字调用 mytouch 函数
    for (; optind < argc; ++optind)
        ok &amp;= mytouch(argv[optind]);

    exit(ok ? EXIT_SUCCESS : EXIT_FAILURE);
} 
```

## 五、作业

在该代码的基础上实现 `touch` 命令的 `-r`，`-d`等选项。

## 参考文档

*   [《UNIX 环境高级编程》](https://book.douban.com/subject/1788421/)
*   [《UNIX 程序员手册》](http://vdisk.weibo.com/s/uxBxovsWmacxr)