# 第 6 节 文件 IO（二）

## 一、实验说明

### 1\. 课程说明

本节课程继续介绍 Linux 系统的文件 IO。主要介绍 stat 的使用（查看文件相关信息，例如文件类型、文件权限等等），以及目录相关（打开、读取、关闭目录）的操作。

### 2\. 如果首次使用 Linux，建议首先学习：

1.  [Linux 基础入门](http://www.shiyanlou.com/courses/1)
2.  [Vim 编辑器](http://www.shiyanlou.com/courses/2)

### 3\. 环境介绍

本实验环境采用带桌面的 Ubuntu Linux 环境，实验中会用到桌面上的程序： 1.命令行终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令

2.Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可

3.gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)

4.gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

## 二、stat 的使用

Linux 有个命令，`ls -l`,效果如下：

```
$ ls -l
-rw-rw-r-- 1 shiyanlou shiyanlou 978 Sep 19 02:13 hello.c 
```

这个命令能显示文件的类型、操作权限、硬链接数量、属主、所属组、大小、修改时间、文件名。它是怎么获得这些信息的能，这一节我们将拨开迷雾。

### 1\. stat 的基本使用

系统调用 stat 的作用是获取文件的各个属性。

需要包含的头文件：`<sys/types.h>`，`<sys/stat.h>`，`<unistd.h>` 函数原型：

```
int stat(const char \* path, struct stat \* buf) 
```

功能：查看文件或目录属性。将参数 path 所指的文件的属性，复制到参数 buf 所指的结构中。 参数： **path**：要查看属性的文件或目录的全路径名称。 **buf**：指向用于存放属性的结构体。stat 成功调用后，buf 的各个字段将存放各个属性。struct stat 是系统头文件中定义的结构体，定义如下：

```
struct stat {
    dev_t       st_dev;
    ino_t       st_ino;
    mode_t      st_mode;
    nlink_t     st_nlink;
    uid_t       st_uid;
    gid_t       st_gid;
    dev_t       st_rdev;
    off_t       st_size;
    blksize_t   st_blksize;
    blkcnt_t    st_blocks;
    time_t      st_atime;
    time_t      st_mtime;
    time_t      st_ctime;
}; 
```

**st*ino***：节点号 **stmode**：文件类型和文件访问权限被编码在该字段中 **st*nlink***：硬连接数 **stuid**：属主的用户 ID **st*gid***：所属组的组 ID **strdev**：设备文件的主、次设备号编码在该字段中 **st*size***：文件的大小 **stmtime**：文件最后被修改时间

返回值：成功返回 0；失败返回-1

```
#include <stdio.h>
#include <stdlib.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>

int main(int argc, char **argv)
{
    struct stat     buf;
    if(argc != 2) { 
        printf("Usage: stat <pathname>"); 
        exit(-1); 
    }
    if(stat(argv[1], &amp;buf) != 0) { 
        printf("stat error."); 
        exit(-1); 
    }
    printf("#i-node:    %ld\n", buf.st_ino);
    printf("#link:      %d\n", buf.st_nlink);
    printf("UID:        %d\n", buf.st_uid);
    printf("GID:        %d\n", buf.st_gid);
    printf("Size        %ld\n", buf.st_size);
    exit(0);
} 
```

### 2\. 文件类型的判定

上一小节中 struct stat 中有个字段为`st_mode`，可用来获取文件类型和文件访问权限，我们将陆续学到从该字段解码我们需要的文件信息。 **st_mode 中文件类型宏定义**：

```
| 宏定义     |    文件类型 |
| :--------  | --------:|
| S*ISREG()  | 普通文件 |
| S*ISDIR()  | 目录文件 |
| S*ISCHR()  | 字符设备文件 |
| S*ISBLK()  | 块设备文件 |
| S*ISFIFO() | 有名管道文件 |
| S*ISLNK()  | 软连接(符号链接)文件 |
| S_ISSOCK() | 套接字文件 |
```

我们修改上面的例子：

```
#include <stdio.h>
#include <stdlib.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>

int main(int argc, char **argv)
{
    struct stat buf;
    char * file_mode;
    if(argc != 2) {
        printf("Usage: stat <pathname>\n"); 
        exit(-1); 
    }
    if(stat(argv[1], &amp;buf) != 0) {
        printf("stat error.\n"); 
        exit(-1); 
    }
    if (S_ISREG(buf.st_mode))
        file_mode = "-";
    else if (S_ISDIR(buf.st_mode))
        file_mode = "d";
    else if (S_ISCHR(buf.st_mode))
        file_mode = "c";
    else if(S_ISBLK(buf.st_mode))
        file_mode = "b";
    printf("#i-node:    %ld\n", buf.st_ino);
    printf("#link:      %d\n", buf.st_nlink);
    printf("UID:        %d\n", buf.st_uid);
    printf("GID:        %d\n", buf.st_gid);
    printf("Size        %ld\n", buf.st_size);
    printf("mode: %s\n", file_mode);
    exit(0);
} 
```

### 3\. 文件权限的判定

文件类型与许可设定被一起编码在 st_mode 字段中，同上面一样，我们也需要一组由系统提供的宏来完成解码。

```
| 宏定义     |    文件类型 |
| :--------  | --------:|
| S*ISUID | 执行时，设置用户 ID |
| S*ISGID | 执行时，设置组 ID |
| S*ISVTX | 保存正文 |
| S*IRWXU | 拥有者的读、写和执行权限 |
| S*IRUSR | 拥有者的读权限 |
| S*IWUSR | 拥有者的写权限 |
| S*IXUSR | 拥有者的执行权限 |
| S*IRWXG | 用户组的读、写和执行权限 |
| S*IRGRP | 用户组的读权限 |
| S*IWGRP | 用户组的写权限 |
| S*IXGRP | 用户组的执行权限 |
| S*IRWXO | 其它读、写、执行权限 |
| S*IROTH | 其它读权限 |
| S*IWOTH | 其它写权限 |
| S_IXOTH | 其它执行权限 |
```

## 三、 目录操作

当目标是目录而不是文件的时候，ls -l 的结果会显示目录下所有子条目的信息，怎么去遍历整个目录呢？答案马上揭晓！

### 1\. 打开目录

需要包含的头文件：`<sys/types.h>`，`<dirent.h>` 函数原型：`DIR * opendir(const char * name)` 功能：opendir()用来打开参数 name 指定的目录，并返回 DIR *形态的目录流 返回值：成功返回目录流；失败返回 NULL

### 2\. 读取目录

函数原型：`struct dirent * readdir(DIR * dir)` 功能：readdir()返回参数 dir 目录流的下一个子条目(子目录或子文件) 返回值： 成功返回结构体指向的指针，错误或以读完目录，返回 NULL > 函数执行成功返回的结构体原型如下： > `> struct dirent { > ino_t d_ino; > off_t d_off; > unsigned short d_reclen; > unsigned char d_type; > char d_name[256]; > }; >` > 其中 d_name 字段，是存放子条目的名称

### 3\. 关闭目录

函数原型：`int closedir(DIR * dir)` 功能：closedir()关闭 dir 所指的目录流 返回值：成功返回 0；失败返回-1，错误原因在 errno 中

我们来学习一个综合的例子吧：

```
#include <stdio.h>
#include <stdlib.h>
#include <dirent.h>
int main(int argc, char *argv[])
{
    DIR *dp;
    struct dirent *entp;
    if (argc != 2) {
        printf("usage: showdir dirname\n");
        exit(0);
    }
    if ((dp = opendir(argv[1])) == NULL) {
        perror("opendir");
        exit(-1);
    }
    while ((entp = readdir(dp)) != NULL)
        printf("%s\n", entp->d_name);

    closedir(dp);
    return 0;
} 
```