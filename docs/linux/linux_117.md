# 第 7 节 多进程编程(一)

## 一、实验说明

### 1\. 课程说明

本节课程介绍 Linux 系统多进程编程。会先阐述一些理论知识，重点在于内存布局以及 进程 fork 的知识点。

### 2\. 如果首次使用 Linux，建议首先学习：

1.  [Linux 基础入门](http://www.shiyanlou.com/courses/1)
2.  [Vim 编辑器](http://www.shiyanlou.com/courses/2)

### 3\. 环境介绍

本实验环境采用带桌面的 Ubuntu Linux 环境，实验中会用到桌面上的程序： 1.命令行终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令

2.Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可

3.gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)

4.gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

## 二、 概述

进程的概念这里就不再过多的赘述了，市面上几乎关于计算机操作系统的书都有详细的描述。 在基本的概念里我们学习一下**Linux 进程状态**：

**R (TASK_RUNNING)，可执行状态。**

> 只有在该状态的进程才可能在 CPU 上运行。而同一时刻可能有多个进程处于可执行状态，这些进程的 task_struct 结构（进程控制块）被放入对应 CPU 的可执行队列中（一个进程最多只能出现在一个 CPU 的可执行队列中）。进程调度器的任务就是从各个 CPU 的可执行队列中分别选择一个进程在该 CPU 上运行。

> 很多操作系统教科书将正在 CPU 上执行的进程定义为 RUNNING 状态、而将可执行但是尚未被调度执行的进程定义为 READY 状态，这两种状态在 linux 下统一为 TASK_RUNNING 状态。

**S (TASK_INTERRUPTIBLE)，可中断的睡眠状态。**

> 处于这个状态的进程因为等待某某事件的发生（比如等待 socket 连接、等待信号量），而被挂起。这些进程的 task_struct 结构被放入对应事件的等待队列中。当这些事件发生时（由外部中断触发、或由其他进程触发），对应的等待队列中的一个或多个进程将被唤醒。

> 通过 ps 命令我们会看到，一般情况下，进程列表中的绝大多数进程都处于 TASK_INTERRUPTIBLE 状态（除非机器的负载很高）。毕竟 CPU 就这么一两个，进程动辄几十上百个，如果不是绝大多数进程都在睡眠，CPU 又怎么响应得过来。

**D (TASK_UNINTERRUPTIBLE)，不可中断的睡眠状态。**

> 与 TASK*INTERRUPTIBLE 状态类似，进程处于睡眠状态，但是此刻进程是不可中断的。不可中断，指的并不是 CPU 不响应外部硬件的中断，而是指进程不响应异步信号。 绝大多数情况下，进程处在睡眠状态时，总是应该能够响应异步信号的。否则你将惊奇的发现，kill -9 竟然杀不死一个正在睡眠的进程了！于是我们也很好理解，为什么 ps 命令看到的进程几乎不会出现 TASK*UNINTERRUPTIBLE 状态，而总是 TASK_INTERRUPTIBLE 状态。

> 而 TASK*UNINTERRUPTIBLE 状态存在的意义就在于，内核的某些处理流程是不能被打断的。如果响应异步信号，程序的执行流程中就会被插入一段用于处理异步信号的流程（这个插入的流程可能只存在于内核态，也可能延伸到用户态），于是原有的流程就被中断了。（参见《linux 内核异步中断浅析》） 在进程对某些硬件进行操作时（比如进程调用 read 系统调用对某个设备文件进行读操作，而 read 系统调用最终执行到对应设备驱动的代码，并与对应的物理设备进行交互），可能需要使用 TASK*UNINTERRUPTIBLE 状态对进程进行保护，以避免进程与设备交互的过程被打断，造成设备陷入不可控的状态。这种情况下的 TASK_UNINTERRUPTIBLE 状态总是非常短暂的，通过 ps 命令基本上不可能捕捉到。

> linux 系统中也存在容易捕捉的 TASK*UNINTERRUPTIBLE 状态。执行 vfork 系统调用后，父进程将进入 TASK*UNINTERRUPTIBLE 状态，直到子进程调用 exit 或 exec（参见《神奇的 vfork》）。 通过下面的代码就能得到处于 TASK_UNINTERRUPTIBLE 状态的进程：

```
$ ps -ax | grep a\.out
4371 pts/0    D+     0:00 ./a.out
4372 pts/0    S+     0:00 ./a.out
4374 pts/1    S+     0:00 grep a.out 
```

然后我们可以试验一下 TASK*UNINTERRUPTIBLE 状态的威力。不管 kill 还是 kill -9，这个 TASK*UNINTERRUPTIBLE 状态的父进程依然屹立不倒。

**T (TASK*STOPPED or TASK*TRACED)，暂停状态或跟踪状态。**

> 向进程发送一个 SIGSTOP 信号，它就会因响应该信号而进入 TASK*STOPPED 状态（除非该进程本身处于 TASK*UNINTERRUPTIBLE 状态而不响应信号）。（SIGSTOP 与 SIGKILL 信号一样，是非常强制的。不允许用户进程通过 signal 系列的系统调用重新设置对应的信号处理函数。） 向进程发送一个 SIGCONT 信号，可以让其从 TASK*STOPPED 状态恢复到 TASK*RUNNING 状态。

> 当进程正在被跟踪时，它处于 TASK*TRACED 这个特殊的状态。“正在被跟踪”指的是进程暂停下来，等待跟踪它的进程对它进行操作。比如在 gdb 中对被跟踪的进程下一个断点，进程在断点处停下来的时候就处于 TASK*TRACED 状态。而在其他时候，被跟踪的进程还是处于前面提到的那些状态。

> 对于进程本身来说，TASK*STOPPED 和 TASK*TRACED 状态很类似，都是表示进程暂停下来。 而 TASK*TRACED 状态相当于在 TASK*STOPPED 之上多了一层保护，处于 TASK*TRACED 状态的进程不能响应 SIGCONT 信号而被唤醒。只能等到调试进程通过 ptrace 系统调用执行 PTRACE*CONT、PTRACE*DETACH 等操作（通过 ptrace 系统调用的参数指定操作），或调试进程退出，被调试的进程才能恢复 TASK*RUNNING 状态。

**Z (TASK*DEAD – EXIT*ZOMBIE)，退出状态，进程成为僵尸进程。**

> 进程在退出的过程中，处于 TASK_DEAD 状态。

> 在这个退出过程中，进程占有的所有资源将被回收，除了 task*struct 结构（以及少数资源）以外。于是进程就只剩下 task*struct 这么个空壳，故称为僵尸。 之所以保留 task*struct，是因为 task*struct 里面保存了进程的退出码、以及一些统计信息。而其父进程很可能会关心这些信息。比如在 shell 中，$?变量就保存了最后一个退出的前台进程的退出码，而这个退出码往往被作为 if 语句的判断条件。 当然，内核也可以将这些信息保存在别的地方，而将 task*struct 结构释放掉，以节省一些空间。但是使用 task*struct 结构更为方便，因为在内核中已经建立了从 pid 到 task*struct 查找关系，还有进程间的父子关系。释放掉 task*struct，则需要建立一些新的数据结构，以便让父进程找到它的子进程的退出信息。

> 父进程可以通过 wait 系列的系统调用（如 wait4、waitid）来等待某个或某些子进程的退出，并获取它的退出信息。然后 wait 系列的系统调用会顺便将子进程的尸体（task_struct）也释放掉。 子进程在退出的过程中，内核会给其父进程发送一个信号，通知父进程来“收尸”。这个信号默认是 SIGCHLD，但是在通过 clone 系统调用创建子进程时，可以设置这个信号。

```
$ ps -ax | grep a\.out
10410 pts/0    S+     0:00 ./a.out
10411 pts/0    Z+     0:00 [a.out]
0413 pts/1    S+     0:00 grep a.out 
```

只要父进程不退出，这个僵尸状态的子进程就一直存在。那么如果父进程退出了呢，谁又来给子进程“收尸”？ 当进程退出的时候，会将它的所有子进程都托管给别的进程（使之成为别的进程的子进程）。托管给谁呢？可能是退出进程所在进程组的下一个进程（如果存在的话），或者是 1 号进程。所以每个进程、每时每刻都有父进程存在。除非它是 1 号进程。

> 1 号进程，pid 为 1 的进程，又称 init 进程。 linux 系统启动后，第一个被创建的用户态进程就是 init 进程。它有两项使命： 1、执行系统初始化脚本，创建一系列的进程（它们都是 init 进程的子孙）； 2、在一个死循环中等待其子进程的退出事件，并调用 waitid 系统调用来完成“收尸”工作； init 进程不会被暂停、也不会被杀死（这是由内核来保证的）。它在等待子进程退出的过程中处于 TASK*INTERRUPTIBLE 状态，“收尸”过程中则处于 TASK*RUNNING 状态。

**X (TASK*DEAD – EXIT*DEAD)，退出状态，进程即将被销毁。**

> 而进程在退出过程中也可能不会保留它的 task*struct。比如这个进程是多线程程序中被 detach 过的进程（进程？线程？参见《linux 线程浅析》）。或者父进程通过设置 SIGCHLD 信号的 handler 为 SIG*IGN，显式的忽略了 SIGCHLD 信号。（这是 posix 的规定，尽管子进程的退出信号可以被设置为 SIGCHLD 以外的其他信号。） 此时，进程将被置于 EXIT*DEAD 退出状态，这意味着接下来的代码立即就会将该进程彻底释放。所以 EXIT*DEAD 状态是非常短暂的，几乎不可能通过 ps 命令捕捉到。

以上内容均摘自博文：http://blog.csdn.net/huzia/article/details/18946491

### 1\. 进程标识

获取进程标志号(pid)的 API，主要有两个函数：**getpid**和**getppid**

需要包含的头文件：`<sys/types.h>`, `<unistd.h>` 函数原型：**pid_t getpid(void)** 功能：获取当前进程 ID 返回值：调用进程的进程 ID

函数原型：**pid_t getppid(void)** 功能：获取父进程 ID 返回值：调用进程的父进程 ID

**pid_ppid.c**

```
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>

int main(void)
{
    pid_t pid = getpid();
    pid_t ppid = getppid();

    printf ("pid = %d\n", pid);
    printf ("ppid = %d\n", ppid);

    return 0;
} 
```

## 三、精解 Linux 下 C 进程内存布局

### 1\. C 进程内存布局说明

**text**：代码段。存放的是程序的全部代码（指令），来源于二进制可执行文件中的代码部分

**initialized data**（简称**data**段）和**uninitialized data**（简称**bss**段）组成了数据段。 > 其中 data 段存放的是已初始化全局变量和已初始化 static 局部变量，来源于二进制可执行文件中的数据部分；bss 段存放的是未初始化全局变量和未初始化 static 局部变量，其内容不来源于二进制可执行文件中的数据部分（也就是说：二进制可执行文件中的数据部分没有未初始化全局变量和未初始化 static 局部变量）。根据 C 语言标准规定，他们的初始值必须为 0，因此 bss 段存放的是全 0。将 bss 段清 0 的工作是由系统在加载二进制文件后，开始执行程序前完成的，系统执行这个清 0 操作是由内核的一段代码完成的，这段代码就是即将介绍的 exec 系统调用。至于 exec 从内存什么地方开始清 0 以及要清 0 多少空间，则是由记录在二进制可执行文件中的信息决定的（即：二进制文件中记录了 text、data、bss 段的大小）

**malloc**是从**heap（堆）**中分配空间的

**stack（栈）**存放的是动态局部变量。 > 当子函数被调用时，系统会从栈中分配空间给该子函数的动态局部变量（注意：此时栈向内存低地址延伸）；当子函数返回时，系统的栈会向内存高地址延伸，这相当于释放子函数的动态局部变量的内存空间。我们假设一下，main 函数在调用子函数 A 后立即调用子函数 B，那么子函数 B 的动态局部变量会覆盖原来子函数 A 的动态局部变量的存储空间，这就是子函数不能互相访问对方动态局部变量的根本物理原因。

内存的最高端存放的是命令行参数和环境变量，将命令行参数和环境变量放到指定位置这个操作是由 OS 的一段代码（exec 系统调用）在加载二进制文件到内存后，开始运行程序前完成的

Linux 下 C 进程内存布局可以由下面的程序的运行结果来获得验证：

**memery.c**

```
 1 #include <stdio.h>
 2 #include <stdlib.h>
 3
 4 int global_init_val = 100;
 5 int global_noninit_val;
 6 extern char **environ;
 7
 8 int main(int argc, char *argv[], char *envp[])
 9 {
10         static int localstaticval = 10;
11         char *localval;
12         localval = malloc(10);
13         printf("address of text  is : %p\n", main);
14         printf("address of data  is : %p, %p\n", &amp;global_init_val, &amp;localstaticval);
15         printf("address of bss   is : %p\n", &amp;global_noninit_val);
16         printf("address of heap  is : %p\n", localval);
17         printf("address of stack is : %p\n", &amp;localval);
18         free(localval);
19
20         printf("&amp;environ = %p, environ = %p\n", &amp;envp, envp);
21         printf("&amp;argv = %p, argv = %p\n", &amp;argv, argv);
22         return 0;
23  } 
```

运行结果，如下：

```
1 address of text  is : 0x8048454
2 address of data  is : 0x804a01c, 0x804a020
3 address of bss   is : 0x804a02c
4 address of heap  is : 0x96e1008
5 address of stack is : 0xbffca8bc
6 &amp;environ = 0xbffca8d8, environ = 0xbffca97c
7 &amp;argv = 0xbffca8d4, argv = 0xbffca974 
```

运行结果分析： 运行结果的第 1（2、3、4、5、6、7）行是由程序的第 13（14、15、16、17、20、21）行打印的。 由运行结果的第 1、2、3、4 行可知，存放的是程序代码的 text 段位于进程地址空间的最低端；往上是存放已初始化全局变量和已初始化 static 局部变量的 data 段；往上是存放未初始化全局变量的 bss 段；往上是堆区（heap）。 由运行结果的第 7、6、5 行可知，命令行参数和环境变量存放在进程地址空间的最高端；往下是存放动态局部变量的栈区（stack）。

### 2\. 环境变量的获取与设置

坏境变量在内存中通常是一字符串**环境变量名=环境变量值**的形式存放。对坏境变量含义的急事依赖于具体的应用程序。我们的程序可能会调用 Linux 系统的环境变量，甚至修改环境变量，所以，Linux 向我们提供了这种 API。

需要包含的头文件：`<stdlib.h>`

函数原型： **char * getenc(const char * name)** 返回字符指针，该指针指向变量名为 name 的环境变量的值字符串。

**int putenv(const char * str)** 将“环境变量=环境变量值”形式的字符创增加到环境变量列表中；如果该环境变量已存在，则更新已有的值。

**int setenv(const char * name, const char * value, int rewrite)** 设置名字为 name 的环境变量的值为 value；如果该环境变量已存在，且 rewrite 不为 0，用新值替换旧值；rewrite 为 0，就不做任何事。

**env.c**

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[], char *envp[])
{
        char **ptr;
        for (ptr = envp; *ptr != 0; ptr++)   /* and all env strings */
                printf ("%s\n", *ptr);

        printf ("\n\n--------My environment variable-------\n\n");
        printf ("USERNAME is %s\n", getenv("USERNAME"));
        putenv ("USERNAME=shiyanlou");

        printf ("USERNAME is %s\n", getenv("USERNAME"));
        setenv ("USERNAME", "shiyanlou-2", 0);

        printf ("USERNAME is %s\n", getenv("USERNAME"));
        setenv ("USERNAME", "shiyanlou-2", 1);

        printf ("USERNAME is %s\n", getenv("USERNAME"));

        return 0;
} 
```

## 四、进程控制天字第 1 号系统调用 — fork

### 1\. fork 的机制与特性

```
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>

int main(void)
{
    pid_t pid;
    if ((pid = fork()) == 0) {
        getchar();
        exit(0);
    }
    getchar();
} 
```

父进程调用 fork 将会产生一个子进程。此时会有 2 个问题：

1.  子进程的代码从哪里来？
2.  子进程首次被 OS 调度时，执行的第 1 条代码是哪条代码？

> 子进程的代码是父进程代码的一个完全相同拷贝。事实上不仅仅是 text 段，子进程的全部进程空间（包括：text/data/bss/heap/stack/command line/environment variables）都是父进程空间的一个完全拷贝。 下一个问题是：谁为子进程分配了内存空间？谁拷贝了父进程空间的内容到子进程的内存空间？fork 当仁不让！事实上，查看 fork 实现的源代码，由 4 部分工作组成：首先，为子进程分配内存空间；然后，将父进程空间的全部内容拷贝到分配给子进程的内存空间；然后在内核数据结构中创建并正确初始化子进程的 PCB（包括 2 个重要信息：子进程 pid，PC 的值=善后代码的第 1 条指令地址）；最后是一段善后代码。 由于子进程的 PCB 已经产生，所以子进程已经出生，因此子进程就可以被 OS 调度到来运行。子进程首次被 OS 调度时，执行的第 1 条代码在 fork 内部，不过从应用程序的角度来看，子进程首次被 OS 调度时，执行的第 1 条代码是从 fork 返回。这就导致了 fork 被调用 1 次，却返回 2 次：父、子进程中各返回 1 次。对于应用程序员而言，最重要的是 fork 的 2 次返回值不一样，父进程返回值是子进程的 pid，子进程的返回值是 0。 至于子进程产生后，父、子进程谁先运行，取决于 OS 调度策略，应用程序员无法控制。 以上分析了 fork 的内部实现以及对应用程序的影响。如果应用程序员觉得难以理解的话，可以暂时抛开，只要记住 3 个结论即可：

1.  fork 函数被调用 1 次（在父进程中被调用），但返回 2 次（父、子进程中各返回一次）。两次返回的区别是子进程的返回值是 0，而父进程的返回值则是子进程的进程 ID。
2.  父、子进程完全一样（代码、数据），子进程从 fork 内部开始执行；父、子进程从 fork 返回后，接着执行下一条语句。
3.  一般来说，在 fork 之后是父进程先执行还是子进程先执行是不确定的，应用程序员无法控制。

### 2\. fork 实例分析

**fork.c**

```
 1 #include <stdio.h>
 2 #include <stdlib.h>
 3 #include <unistd.h>
 4 #include <sys/types.h>
 5
 6 #define err_sys(info)         
 7    {                         
 8         printf ("%s\n", info);  
 9         exit(0);                
10    }
11
12 int glob = 6;  /* external variable in initialized data */
13 char buf[ ] = "a write to stdout\n";
14 
15 int main(void)
16 {
17     int var;  /* automatic variable on the stack */
18     pid_t pid;
19     var = 88;
20 
21     if ((write(STDOUT_FILENO, buf, sizeof(buf)-1) != sizeof(buf)-1))
22         err_sys("write error");
23 
24     printf("before fork\n");  /* we don't flush stdout */
25 
26     if ( (pid = fork()) < 0) {
27         err_sys("fork error");
28     } else if (pid == 0) {   /* child */
29        glob++;      /* modify variables */
30        var++;
31     } else {
32         sleep(2);   /* parent */
33     }
34
35     printf("pid = %d, ppid = %d, glob = %d, var = %d\n", getpid(),getppid(), glob, var);
36     exit(0);
37 } 
```

运行结果：

```
1 a write to stdout
2 before fork
3 pid = 9009, ppid = 9008, glob = 7, var = 89
4 pid = 9008, ppid = 8979, glob = 6, var = 88 
```

> 运行结果分析： > 结果的第 1 行是由父进程的 21 行打印； > 结果的第 2 行是由父进程的 24 行打印； > 由于父进程在 24 行睡眠了 2 秒，因此 fork 返回后，子进程先于父进程运行是大概率事件，所以子进程运行到 25 行打印出结果中的第 3 行。由于子进程会拷贝父进程的整个进程空间（这其中包括数据），因此当子进程 26 行从 fork 返回后，子进程中的 glob=6，var=88（拷贝自父进程的数据）。此时子进程中 pid=0，因此子进程会执行 29、30 行，当子进程到达 35 行时，将打印 glob=7，var=89。

虽然，子进程改变了 glob 和 var 的值，但它仅仅是改变了子进程中的 glob 和 var，而影响不了父进程中的 glob 和 var。在子进程出生后，父、子进程的进程空间（代码、数据等）就是独立，互不干扰的。因此当父进程运行到 35 行，将会打印父进程中的 glob 和 var 的值，他们分别是 6 和 88，这就是运行结果的第 4 行。