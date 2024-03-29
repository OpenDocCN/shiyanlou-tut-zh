# 第 1 节 格式化字符串漏洞实验

## 一、 实验描述

格式化字符串漏洞是由像 printf(user*input) 这样的代码引起的，其中 user*input 是用户输入的数据，具有 Set-UID root 权限的这类程序在运行的时候，printf 语句将会变得非常危险，因为它可能会导致下面的结果：

*   使得程序崩溃
*   任意一块内存读取数据
*   修改任意一块内存里的数据

最后一种结果是非常危险的，因为它允许用户修改 set-UID root 程序内部变量的值，从而改变这些程序的行为。

本实验将会提供一个具有格式化漏洞的程序，我们将制定一个计划来探索这些漏洞。

## 二、实验预备知识讲解

### 2.1 什么是格式化字符串？

```
printf ("The magic number is: %d", 1911); 
```

试观察运行以上语句，会发现字符串"The magic number is: %d"中的格式符％d 被参数（1911）替换，因此输出变成了“The magic number is: 1911”。 格式化字符串大致就是这么一回事啦。

除了表示十进制数的％d，还有不少其他形式的格式符，一起来认识一下吧~

```
格式符|        含义 |    含义（英）|传
---- | ------------- | ----- | -------
%d   | 十进制数（int）  |decimal |  值
%u   | 无符号十进制数 (unsigned int)  | unsigned decimal | 值
%x   | 十六进制数 (unsigned int)  |hexadecimal |  值
%s   | 字符串 ((const) (unsigned) char *)  | string | 引用（指针）
%n   | %n 符号以前输入的字符数量 (* int) |number of bytes written so far |  引用（指针）
```

（ * **%n**的使用将在 1.5 节中做出说明）

### 2.2 栈与格式化字符串

格式化函数的行为由格式化字符串控制，printf 函数从栈上取得参数。

```
printf ("a has value %d, b has value %d, c is at address: %08x\n",a, b, &c); 
```

![](img/md0417734gdtpVzX.jpg)

### 2.3 如果参数数量不匹配会发生什么？

如果只有一个不匹配会发生什么？

```
printf ("a has value %d, b has value %d, c is at address: %08x\n",a, b); 
```

*   在上面的例子中格式字符串需要 3 个参数，但程序只提供了 2 个。
*   该程序能够通过编译么？
    *   printf()是一个参数长度可变函数。因此，仅仅看参数数量是看不出问题的。
    *   为了查出不匹配，编译器需要了解 printf()的运行机制，然而编译器通常不做这类分析。
    *   有些时候，格式字符串并不是一个常量字符串，它在程序运行期间生成(比如用户输入)，因此，编译器无法发现不匹配。
*   那么 printf()函数自身能检测到不匹配么？
    *   printf()从栈上取得参数，如果格式字符串需要 3 个参数，它会从栈上取 3 个，除非栈被标记了边界，printf()并不知道自己是否会用完提供的所有参数。
    *   既然没有那样的边界标记。printf()会持续从栈上抓取数据，在一个参数数量不匹配的例子中，它会抓取到一些不属于该函数调用到的数据。
*   如果有人特意准备数据让 printf 抓取会发生什么呢？

### 2.4 访问任意位置内存

*   我们需要得到一段数据的内存地址，但我们无法修改代码，供我们使用的只有格式字符串。
*   如果我们调用 printf(%s) 时没有指明内存地址, 那么目标地址就可以通过 printf 函数，在栈上的任意位置获取。printf 函数维护一个初始栈指针,所以能够得到所有参数在栈中的位置
*   观察: 格式字符串位于栈上. 如果我们可以把目标地址编码进格式字符串，那样目标地址也会存在于栈上，在接下来的例子里，格式字符串将保存在栈上的缓冲区中。

```
int main(int argc, char *argv[])
{
    char user_input[100];
    ... ... /* other variable definitions and statements */
    scanf("%s", user_input); /* getting a string from user */
    printf(user_input); /* Vulnerable place */
    return 0;
} 
```

*   如果我们让 printf 函数得到格式字符串中的目标内存地址 (该地址也存在于栈上), 我们就可以访问该地址。（注：代码中引号内容为 user_input 数组内容的展开）

    ```
    printf ("\x10\x01\x48\x08 %x %x %x %x %s"); 
    ```

*   \x10\x01\x48\x08 是目标地址的四个字节， 在 C 语言中, \x10 告诉编译器将一个 16 进制数 0x10 放于当前位置（占 1 字节）。如果去掉前缀\x10 就相当于两个 ascii 字符 1 和 0 了，这就不是我们所期望的结果了。

*   %x 导致栈指针向格式字符串的方向移动（参考 1.2 节）

*   下图解释了攻击方式，如果用户输入中包含了以下格式字符串 ![](img/md0417734gHmSCX6.jpg)

*   如图所示，我们使用四个%x 来移动 printf 函数的栈指针到我们存储格式字符串的位置，一旦到了目标位置，我们使用％s 来打印，它会打印位于地址 0x10014808 的内容，因为是将其作为字符串来处理，所以会一直打印到结束符为止。

*   user_input 数组到传给 printf 函数参数的地址之间的栈空间不是为了 printf 函数准备的。但是，因为程序本身存在格式字符串漏洞，所以 printf 会把这段内存当作传入的参数来匹配％x。

*   最大的挑战就是想方设法找出 printf 函数栈指针(函数取参地址)到 user_input 数组的这一段距离是多少，这段距离决定了你需要在%s 之前输入多少个%x。

### 2.5 在内存中写一个数字

%n: 该符号前输入的字符数量会被存储到对应的参数中去

```
int i;
printf ("12345%n", &i); 
```

*   数字 5（%n 前的字符数量）将会被写入 i 中
*   运用同样的方法在访问任意地址内存的时候，我们可以将一个数字写入指定的内存中。只要将上一小节(1.4)的%s 替换成%n 就能够覆盖 0x10014808 的内容。
*   利用这个方法，攻击者可以做以下事情:
    *   重写程序标识控制访问权限
    *   重写栈或者函数等等的返回地址
*   然而，写入的值是由%n 之前的字符数量决定的。真的有办法能够写入任意数值么？
    *   用最古老的计数方式， 为了写 1000，就填充 1000 个字符吧。
    *   为了防止过长的格式字符串，我们可以使用一个宽度指定的格式指示器。(比如（%0 数字 x）就会左填充预期数量的 0 符号)

## 三、 实验内容

### 实验 1

用户需要输入一段数据，数据保存在 user_input 数组中，程序会使用 printf 函数打印数据内容，并且该程序以 root 权限运行。更加可喜的是，这个程序存在一个格式化漏洞。让我们来看看利用这些漏洞可以搞些什么破坏。

程序说明： >程序内存中存在两个秘密值，我们想要知道这两个值，但发现无法通过读二进制代码的方式来获取它们（实验中为了简单起见，硬编码这些秘密值为 0x44 和 0x55）。尽管我们不知道它们的值，但要得到它们的内存地址倒不是特别困难，因为对大多数系统而言，每次运行程序，这些内存地址基本上是不变的。实验假设我们已经知道了这些内存地址，为了达到这个目的，程序特意为我们打出了这些地址。 > >有了这些前提以后我们需要达到以下目标： >+ 找出 secret[1]的值 >+ 修改 secret[1]的值 >+ 修改 secret[1]为期望值

>**注意**：因为实验环境是 64 位系统，所以需要使用%016llx 才能读取整个字。但为了简便起见，对程序进行了修改了，使用%08x 也能完成实验。

有了之前预备知识的铺垫，先自己尝试一下，祝玩的愉快：）

程序如下：

```
/* vul_prog.c */ 
include <stdlib.h>
include <stdio.h>

define SECRET1 0x44
define SECRET2 0x55

int main(int argc, char *argv[])
{
  char user_input[100];
  int *secret;
  long int_input;
  int a, b, c, d; /* other variables, not used here.*/

  /* The secret value is stored on the heap */
  secret = (int *) malloc(2*sizeof(int));

  /* getting the secret */
  secret[0] = SECRET1; secret[1] = SECRET2;

  printf("The variable secret's address is 0x%8x (on stack)\n", &secret);
  printf("The variable secret's value is 0x%8x (on heap)\n", secret);
  printf("secret[0]'s address is 0x%8x (on heap)\n", &secret[0]);
  printf("secret[1]'s address is 0x%8x (on heap)\n", &secret[1]);

  printf("Please enter a decimal integer\n");
  scanf("%d", &int_input);  /* getting an input from user */
  printf("Please enter a string\n");
  scanf("%s", user_input); /* getting a string from user */

  /* Vulnerable place */
  printf(user_input);  
  printf("\n");

  /* Verify whether your attack is successful */
  printf("The original secrets: 0x%x -- 0x%x\n", SECRET1, SECRET2);
  printf("The new secrets:      0x%x -- 0x%x\n", secret[0], secret[1]);
  return 0;
} 
```

(ps： 编译时可以添加以下参数关掉栈保护。)

```
gcc -z execstack -fno-stack-protector -o vul_prog vul_prog.c 
```

* * *

一点小提示：你会发现 secret[0]和 secret[1]存在于 malloc 出的堆上，我们也知道 secret 的值存在于栈上，如果你想覆盖 secret[0]的值,ok,它的地址就在栈上，你完全可以利用格式化字符串的漏洞来达到目的。然而尽管 secret[1]就在它的兄弟 0 的旁边，你还是没办法从栈上获得它的地址，这对你来说构成了一个挑战，因为没有它的地址你怎么利用格式字符串读写呢。但是真的就没招了么？

* * *

### 3.1.1 找出 secret[1]的值

1.首先定位 int_input 的位置，这样就确认了％s 在格式字符串中的位置。 ![](img/md0417734FwK544g.jpg)

2.输入 secret[1]的地址，记得做进制转换，同时在格式字符串中加入％s。 ![](img/md0417734NLD7k8e.jpg)

大功告成！U 的 ascii 码就是 55。

### 3.1.2 修改 secret[1]的值

1.只要求修改，不要求改什么？简单！不明白%n 用法的可以往前回顾一下。 ![](img/md0417734mEDs0NO.jpg)

大功告成 x2！

### 3.1.3 修改 secret[1]为期望值

1.要改成自己期望的值，咋办？填 1000 岂不累死？！可以用填充嘛！ ![](img/md0417734EAy5uaS.jpg) 哦对了，0x3e8 = 1000。 大功告成 x3！

### 实验 2

现在让我们把第一个 scanf 语句去掉，并去掉与 int_input 变量相关的所有语句。同时设置关闭地址随机化选项。

```
sysctl -w kernel.randomize_va_space=0 
```

关闭地址随机化后，这样每次运行程序得到的 secret 地址就都一样了，让我们再来一次实验 1 中的攻击吧。不过在此之前你需要知道这些：

**如何让 scanf()接受任意数字？**

通常，scanf 将会为你停顿，打印输入。有时，你想要编程得 到一个数 0x05 (不是字符“5”)，不幸的是，当你将“5"作为输入,scanf 实际得到的是 5 的 ASCII 值 0x35,而不是 0x05。

这个问题的一个解决办法是使用文件。我们可以很容易地写一 C 程 序将 0x05 (不是“5")存入一个文件（我们叫它 my string),然后运行输入被重定向到 mystring 的漏洞程序。这样，scanf 将从文件 mystring 中获得输入而不是键盘。 你需要注意一些特殊数字，如 0x0A (新行），0x0C (换页），0x0D (返回），0x20 (空 格)，scanf 将它们视为分隔符，如果在 scanf 里我们仅有一个"％s"的话，它将停止读取这些 特殊符号之后的任何内容。如果这些数字出现在地址屮，你必须想办法避开。为简化任务， 如果你不走运地在 secret 的地址中碰到这些特殊数字，我们允许你在为 secret[2]分配内存地址 之前加上一个 malloc 语句。这个额外的 malloc 语句可以改变 secret 地址的值。

以下程序将一个格式化字符串写入了一个叫 mystring 的文件，前 4 个字节由任意你想放 入格式化字符串的数字构成，接下来的字节由键盘输入。

```
include <sys/types.h>
include <sys/stat.h>
include <fcntl.h>
int main()
{
    char buf[1000];
    int fp, size;
    unsigned int *address;
    /* Putting any number you like at the beginning of the format string */
    address = (unsigned int *) buf;
    *address = 0x113222580;
    /* Getting the rest of the format string */
    scanf("%s", buf+4);
    size = strlen(buf+4) + 4;
    printf("The string length is %d\n", size);
    /* Writing buf to "mystring" */
    fp = open("mystring", O_RDWR | O_CREAT | O_TRUNC, S_IRUSR | S_IWUSR);
    if (fp != -1) {
        write(fp, buf, size);
        close(fp);
    } else {
        printf("Open failed!\n");
    }
} 
```

### 3.2.1 修改 secret[0]的值

让我们先以上面提供的写程序为基础，熟悉一下基础流程。

修改 vul*prog.c 后编译 vul*prog.c 与 write*string.c 然后通过 write*string 程序将内容输入进 mystring 文件中，文件内容包括代码中加入的头四个字节和你之后输入的内容。

写入文件后，输入以下命令：

```
./vul_prog < mystring 
```

![图片描述信息](img/userid51209labid734time1427864804295.jpg)

大功告成！ 0x4c ＝ 76 ＝ 8*8+8 个逗号+开头 4 个字节。

## 四、 练习

在实验楼环境安步骤进行实验，并截图

您已经完成本课程的所有实验，**干的漂亮！**

## 版权声明

本课程所涉及的实验来自[Syracuse SEED labs](http://www.cis.syr.edu/~wedu/seed/)，并在此基础上为适配[实验楼](http://www.shiyanlou.com)网站环境进行修改，修改后的实验文档仍然遵循 GNU Free Documentation License。

本课程文档 github 链接：[`github.com/shiyanlou/seedlab`](https://github.com/shiyanlou/seedlab)

附[Syracuse SEED labs](http://www.cis.syr.edu/~wedu/seed/)版权声明：

> Copyright Statement Copyright 2006 – 2009 Wenliang Du, Syracuse University. The development of this document is funded by the National Science Foundation’s Course, Curriculum, and Laboratory Improvement (CCLI) program under Award No. 0618680 and 0231122\. Permission is granted to copy, distribute and/or modify this document under the terms of the GNU Free Documentation License, Version 1.2 or any later version published by the Free Software Foundation. A copy of the license can befound at http://www.gnu.org/licenses/fdl.html.