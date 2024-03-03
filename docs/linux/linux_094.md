# 高级 Bash 脚本编程指南

基于杨春敏与黄毅的 ABS 译文制作，一本深入学习 shell 脚本艺术的书籍。原版链接：http://www.tldp.org/LDP/abs/html/。

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou 若不小心登出后，直接刷新页面即可

### 2\. 环境使用

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

### 3\. 课程来源

基于杨春敏与黄毅的 ABS 译文制作，一本深入学习 shell 脚本艺术的书籍。原版链接：[点这里](http://www.tldp.org/LDP/abs/html)

### 4.学前须知

本课程将假设你已经完成学习[linux 基础入门](http://www.shiyanlou.com/courses/1)和[vim 编辑器](http://www.shiyanlou.com/courses/2)，如果你对学习这门课程的感到困难，你可以先完成前面两门基础课程的学习。

## 二、什么是 Bash？

### 1.简介

Bash（GNU Bourne-Again Shell）是一个为 GNU 计划编写的 Unix shell，它是许多 Linux 平台默认使用的 shell。

shell 是一个命令解释器，是介于操作系统内核与用户之间的一个绝缘层。准确地说，它也是能力很强的计算机语言，被称为解释性语言或脚本语言。它可以通过将系统调用、公共程序、工具和编译过的二进制程序”粘合“在一起来建立应用，这是大多数脚本语言的共同特征，所以有时候脚本语言又叫做“胶水语言”

事实上，所有的 UNIX 命令和工具再加上公共程序，对于 shell 脚本来说，都是可调用的。Shell 脚本对于管理系统任务和其它的重复工作的例程来说，表现的非常好，根本不需要那些华而不实的成熟紧凑的编译型程序语言。

### 2.为什么学 Bash？

对于任何想适当精通一些系统管理知识的人来说，掌握 shell 脚本知识都是最基本的，即使这些人可能并不打算真正的编写一些脚本。

### 三、初步练习

### 1.Hello World

Bash 之 Hello World

```
$ vim hello.sh 
```

使用 vim 编辑 hello.sh ，输入如下代码并保存：

```
 #!/bin/bash
 # This is a comment
 echo Hello World 
```

运行 Bash 脚本的方式：

```
# 使用 shell 来执行
$ sh hello.sh
# 使用 bash 来执行
$ bash hello.sh 
```

还可以让脚本本身就具有可执行权限，通过`chmod`命令可以修改：

```
# 赋予脚本的所有者该执行权限，允许该用户执行该脚本
$ chmod u+rx hello.sh
# 执行命令，这将使用脚本第一行指定的 shell 来执行，如果指定 shell 不存在，将使用系统默认 shell 来执行
$  ./hello.sh 
```

### 2.使用脚本清除/var/log 下的 log 文件

```
$ vim cleanlogs.sh 
```

输入如下代码，并保存：

```
#!/bin/bash

# 初始化一个变量
LOG_DIR=/var/log

cd $LOG_DIR

cat /dev/null > messages
cat /dev/null > wtmp

echo "Logs cleaned up."

exit 
```

运行脚本前，先看看 /var/log/ 下文件内是否有内容。运行此脚本后，文件的内容将被清除。

## 四、思考练习

了解 linux 下常见的几种 shell；创建一个 bash 脚本文件，使用`read`命令读入一个输入并打印输出到终端显示。

## 一、# 符号

### 1.# 注释

行首以 `#` 开头(除`#!`之外)的是注释。`#!`是用于指定当前脚本的解释器，我们这里为`bash`，且应该指明完整路径，所以为`/bin/bash`

当然，在 echo 中转义的 # 是不能作为注释的：

```
$ vim test.sh 
```

输入如下代码，并保存。（中文为注释，无需要输入）

```
 #!/bin/bash

 echo "The # here does not begin a comment."
 echo 'The # here does not begin a comment.'
 echo The \# here does not begin a comment.
 echo The # 这里开始一个注释

 echo ${PATH#*:}         # 参数替换，不是一个注释
 echo $(( 2#101011 ))   # 数制转换（使用二进制表示），不是一个注释

 # 欢迎来到实验楼参观学习 
```

执行脚本，查看输出

```
$ bash test.sh 
```

解释说明

上面的脚本说明了如何使用`echo`打印出一段字符串和变量内容，这里采用了几种不同的方式，希望你可以理解这几种不同方式的异同

## 二、分号（;）

### 1.命令分隔符

使用分号（;）可以在同一行上写两个或两个以上的命令。

```
$ vim test2.sh 
```

输入如下代码，并保存。

```
 #!/bin/bash
 echo hello; echo there
 filename=ttt.sh
 if [ -r "$filename" ]; then    # 注意: "if"和"then"需要分隔
     echo "File $filename exists."; cp $filename $filename.bak
 else
     echo "File $filename not found."; touch $filename
 fi; echo "File test complete." 
```

执行脚本

```
$ bash test2.sh 
```

查看结果

```
$ ls 
```

解释说明

上面脚本使用了一个`if`件分支判断一个文件是否存在，如果文件存在打印相关信息并将该文件备份；如果不存在打印相关信息并创建一个新的文件。最后将输出"测试完成"。

### 2.终止 case 选项（双分号）

使用双分号（;;）可以终止 case 选项。

```
$ vim test3.sh 
```

输入如下代码，并保存。

```
 #!/bin/bash

 varname=b

 case "$varname" in
     [a-z]) echo "abc";;
     [0-9]) echo "123";;
 esac 
```

执行脚本，查看输出

```
$ bash test3.sh
abc 
```

解释说明

上面脚本使用`case`语句，首先创建了一个变量初始化为 b,然后使用`case`语句判断该变量的范围，并打印相关信息。如果你有其它编程语言的经验，这将很容量理解。

## 三、点号（.）

### 1.等价于 source 命令

bash 中的 source 命令用于在当前 bash 环境下读取并执行 FileName.sh 中的命令。

```
$ source test.sh
Hello World
$ . test.sh
Hello World 
```

## 四、引号

### 1.双引号（")

"STRING" 将会阻止（解释）STRING 中大部分特殊的字符。后面的实验会详细说明。

### 2.单引号（'）

'STRING' 将会阻止 STRING 中所有特殊字符的解释，这是一种比使用"更强烈的形式。后面的实验会详细说明。

## 五、斜线和反斜线

### 1.斜线（/）

文件名路径分隔符。分隔文件名不同的部分（如/home/bozo/projects/Makefile）。也可以用来作为除法算术操作符。

### 2.反斜线（\）

一种对单字符的引用机制。\X 将会“转义”字符 X。这等价于"X"，也等价于'X'。\ 通常用来转义双引号（"）和单引号（'），这样双引号和单引号就不会被解释成特殊含义了。

## 六、反引号（`）

### 1.命令替换

`command` 结构可以将命令的输出赋值到一个变量中去。在后边的后置引用(backquotes)或后置标记(backticks)中也会讲解。

反引号中的命令会优先执行，如：

```
$ cp `mkdir back` test.sh back
$ ls 
```

先创建了 back 目录，然后复制 test.sh 到 back 目录

## 七、冒号（:）

### 1.空命令

等价于“NOP”（no op，一个什么也不干的命令）。也可以被认为与 shell 的内建命令 true 作用相同。“:”命令是一个 bash 的内建命令，它的退出码（exit status）是（0）。

如：

```
#!/bin/bash

while :
do
    echo "endless loop"
done 
```

等价于

```
#!/bin/bash

while true
do
    echo "endless loop"
done 
```

可以在 if/then 中作占位符：

```
#!/bin/bash

condition=5

if [ $condition -gt 0 ]
then :   # 什么都不做，退出分支
else
    echo "$condition"
fi 
```

### 2.变量扩展/子串替换

在与`>`重定向操作符结合使用时，将会把一个文件清空，但是并不会修改这个文件的权限。如果之前这个文件并不存在，那么就创建这个文件。

```
 $ : > test.sh   # 文件“test.sh”现在被清空了
 # 与 cat /dev/null > test.sh 的作用相同
 # 然而,这并不会产生一个新的进程, 因为“:”是一个内建命令 
```

在与`>>`重定向操作符结合使用时，将不会对预先存在的目标文件(: >> target_file)产生任何影响。如果这个文件之前并不存在，那么就创建它。

也可能用来作为注释行，但不推荐这么做。使用 # 来注释的话，将关闭剩余行的错误检查，所以可以在注释行中写任何东西。然而，使用 : 的话将不会这样。如：

```
$ : This is a comment that generates an error, ( if [ $x -eq 3] ) 
```

":"还用来在 `/etc/passwd` 和 `$PATH` 变量中做分隔符，如：

```
$ echo $PATH
/usr/local/bin:/bin:/usr/bin:/usr/X11R6/bin:/sbin:/usr/sbin:/usr/games
$ 
```

## 八、问号（?）

### 1.测试操作符

在一个双括号结构中，`?` 就是 C 语言的三元操作符，如：

```
$ vim test.sh 
```

输入如下代码，并保存：

```
 #!/bin/bash

 a=10
 (( t=a<50?8:9 ))
 echo $t 
```

运行测试

```
$ bash test.sh
8 
```

## 九、美元符号（$）

### 1.变量替换

前面已经用到了

```
$ vim test.sh 
```

```
#!/bin/bash

var1=5
var2=23skidoo

echo $var1     # 5
echo $var2     # 23skidoo 
```

运行测试

```
$ bash test.sh 
```

### 2.命令替换（同反引号）

```
$ cd $(echo Documents)
$ pwd 
```

## 一、小括号（( )）

### 1.命令组

在括号中的命令列表，将会作为一个子 shell 来运行。

在括号中的变量，由于是在子 shell 中，所以对于脚本剩下的部分是不可用的。父进程，也就是脚本本身，将不能够读取在子进程中创建的变量，也就是在子 shell 中创建的变量。如：

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

a=123
( a=321; )

echo "a = $a" 
```

运行代码：

```
$ bash test.sh
a = 123 
```

在圆括号中 a 变量，更像是一个局部变量。

### 2.初始化数组

创建数组

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

arr=(1 4 5 7 9 21)
echo ${arr[3]} # get a value of arr 
```

运行代码：

```
$ bash test.sh
7 
```

## 二、大括号（{ }）

### 1.文件名扩展

复制 t.txt 的内容到 t.back 中

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

if [ ! -w 't.txt' ];
then
    touch t.txt
fi
echo 'test text' >> t.txt
cp t.{txt,back} 
```

运行代码：

```
$ bash test.sh 
```

查看运行结果：

```
$ ls
$ cat t.txt
$ cat t.back 
```

**注意：** 在大括号中，不允许有空白，除非这个空白被引用或转义。

### 2.代码块

代码块，又被称为内部组，这个结构事实上创建了一个匿名函数（一个没有名字的函数）。然而，与“标准”函数不同的是，在其中声明的变量，对于脚本其他部分的代码来说还是可见的。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

a=123
{ a=321; }
echo "a = $a" 
```

运行代码：

```
$ bash test.sh
a = 321 
```

变量 a 的值被更改了。

## 三、中括号（[ ]）

### 1.条件测试

条件测试表达式放在`[ ]`中。值得注意的是`[`是 shell 内建`test`命令的一部分，并不是/usr/bin/test 中的外部命令的一个链接。下列练习中的-lt (less than)表示小于号。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

a=5
if [ $a -lt 10 ]
then
    echo "a: $a"
else
    echo 'a>10'
fi 
```

运行代码：

```
$ bash test.sh
a: 5
$ 
```

双中括号（`[[ ]]`）也用作条件测试（判断），后面的实验会详细讲解。

### 2.数组元素

在一个 array 结构的上下文中，中括号用来引用数组中每个元素的编号。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

arr=(12 22 32)
arr[0]=10
echo ${arr[0]} 
```

运行代码：

```
$ bash test.sh
10 
```

## 四、尖括号（< 和 >）

### 1.重定向

test.sh > filename 重定向 test.sh 的输出到文件 filename 中。如果 filename 存在的话，那么将会被覆盖。

test.sh &> filename 重定向 test.sh 的 stdout（标准输出）和 stderr（标准错误）到 filename 中。

test.sh >&2 重定向 test.sh 的 stdout 到 stderr 中。

test.sh >> filename 把 test.sh 的输出追加到文件 filename 中。如果 filename 不存在的话，将会被创建。

## 五、竖线（|）

### 管道

分析前边命令的输出，并将输出作为后边命令的输入。这是一种产生命令链的好方法。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

tr 'a-z' 'A-Z'
exit 0 
```

现在让我们输送 ls -l 的输出到一个脚本中：

```
$ chmod 755 test.sh
$ ls -l | ./test.sh 
```

输出的内容均变为了大写字母。

## 六、破折号（-）

### 1.选项，前缀

在所有的命令内如果想使用选项参数的话,前边都要加上“-”。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

a=5
b=5
if [ "$a" -eq "$b" ]
then
    echo "a is equal to b."
fi 
```

运行代码：

```
$ bash test.sh
a is equal to b. 
```

### 2.用于重定向 stdin 或 stdout

下面脚本用于备份最后 24 小时当前目录下所有修改的文件.

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

BACKUPFILE=backup-$(date +%m-%d-%Y)
# 在备份文件中嵌入时间.
archive=${1:-$BACKUPFILE}
#  如果在命令行中没有指定备份文件的文件名,
#+ 那么将默认使用"backup-MM-DD-YYYY.tar.gz".

tar cvf - `find . -mtime -1 -type f -print` > $archive.tar
gzip $archive.tar
echo "Directory $PWD backed up in archive file \"$archive.tar.gz\"."

exit 0 
```

运行代码：

```
$ bash test.sh
$ ls 
```

## 七、波浪号（~）

### 1.目录

~ 表示 home 目录。

## 一、变量替换

### 1.概念

变量的名字就是变量保存值的地方。引用变量的值就叫做变量替换。

如果 variable 是一个变量的名字，那么 $variable 就是引用这变量的值，即这变量所包含的数据。

$variable 事实上只是 ${variable} 的简写形式。在某些上下文中 $variable 可能会引起错误，这时候你就需要用 ${variable} 了。

### 2.举例

```
$ vim test.sh 
```

输入代码（中文皆为注释，不用输入）：

```
#!/bin/bash

# 变量赋值和替换

a=375
hello=$a

#-------------------------------------------------------------------------
# 强烈注意, 在赋值的的时候, 等号前后一定不要有空格.
# 如果出现空格会怎么样?

#  "VARIABLE =value"
#                   ^
#% 脚本将尝试运行一个"VARIABLE"的命令, 带着一个"=value"参数.

#  "VARIABLE= value"
#                      ^
#% 脚本将尝试运行一个"value"的命令, 
#+ 并且带着一个被赋值成""的环境变量"VARIABLE". 
#-------------------------------------------------------------------------

echo hello    # 没有变量引用, 只是个 hello 字符串.

echo $hello
echo ${hello}

echo "$hello"
echo "${hello}"

echo

hello="A B  C     D"
echo $hello   # A B C D
echo "$hello" # A B  C     D
# 引用一个变量将保留其中的空白, 当然如果是变量替换就不会保留了.
# 在 bash 中，当变量中有空格、tab 之类的字符时候，
# 如果需要打印这些字符，需要用双引号进行引用 "$hello".

echo

echo '$hello'  # $hello
#     ^           ^
# 全引用（单引号）的作用将会导致"$"被解释为单独的字符, 而不是变量前缀. 
# 使用单引号引用变量时候，变量的值不会被引用，只是简单的保持原始字符串.

# 注意这两种引用所产生的不同的效果.

hello=    # 设置为空值.
echo "\$hello (null value) = $hello"
#  注意设置一个变量为 null, 与 unset 这个变量, 并不是一回事
#+ 虽然最终的结果相同(具体见下边).

# --------------------------------------------------------------

#  可以在同一行上设置多个变量, 
#+ 但是必须以空白进行分隔.
#  慎用, 这么做会降低可读性, 并且不可移植.

var1=21  var2=22  var3=$V3
echo
echo "var1=$var1   var2=$var2   var3=$var3"

# 在老版本的"sh"上可能会引起问题.

# --------------------------------------------------------------

echo; echo

numbers="one two three"
#                      ^    ^
other_numbers="1 2 3"
#                           ^ ^
#  如果在变量中存在空白，那么就必须加上引号
#  other_numbers=1 2 3                  # 将给出一个错误消息. 
echo "numbers = $numbers"
echo "other_numbers = $other_numbers"   # other_numbers = 1 2 3
#  不过也可以采用将空白转义的方法.
mixed_bag=2\ ---\ Whatever
#                     ^    ^ 在转义符后边的空格(\).

echo "$mixed_bag"         # 2 --- Whatever

echo; echo

echo "uninitialized_variable = $uninitialized_variable"
# Uninitialized 变量为 null(就是没有值). 
uninitialized_variable=   #  声明, 但是没有初始化这个变量, 
                         #+ 其实和前边设置为空值的作用是一样的. 
echo "uninitialized_variable = $uninitialized_variable"
                         # 还是一个空值.

uninitialized_variable=23       # 赋值.
unset uninitialized_variable    # Unset 这个变量.
echo "uninitialized_variable = $uninitialized_variable" # 还是一个空值. 
```

运行代码：

```
$ bash test.sh 
```

## 二、变量赋值

### 1.说明

赋值操作前后都不能有空白。

因为 `=` 和 `-eq` 都可以用做条件测试操作，所以不要与这里的赋值操作相混淆。

**注意：** = 既可以用做条件测试操作，也可以用于赋值操作，这需要视具体的上下文而定。bash 中`==`也可作为条件判断。

### 2.举例

```
$ vim test.sh 
```

输入代码（中文皆为注释，不用输入）：

```
#!/bin/bash

a=23              # 简单的赋值
echo $a
b=$a
echo $b

# 现在让我们来点小变化(命令替换).

a=`echo Hello\!`   # 把'echo'命令的结果传给变量'a'
echo $a
#  注意, 如果在一个#+的命令替换结构中包含一个(!)的话, 
#+ 那么在命令行下将无法工作.
#+ 因为这触发了 Bash 的"历史机制."
#  但是, 在脚本中使用的话, 历史功能是被禁用的, 所以就能够正常的运行.

a=`ls -l`         # 把'ls -l'的结果赋值给'a'
echo $a           # 然而, 如果没有引号的话将会删除 ls 结果中多余的 tab 和换行符.
echo
echo "$a"         # 如果加上引号的话, 那么就会保留 ls 结果中的空白符.
                 # (具体请参阅"引用"的相关章节.)

exit 0 
```

运行代码：

```
$ bash test.sh 
```

## 三、变量不区分类型

### 1.说明

与大多数编译型语言不同，Bash 并不区分变量的"类型"。本质上，Bash 变量都是字符串。但是依赖于具体的上下文，Bash 也允许比较操作和整数操作。其中的关键因素就是，为变量赋的值是否只有数字。

### 2.举例

```
$ vim test.sh 
```

输入代码（中文皆为注释，不用输入）：

```
#!/bin/bash

a=2334                   # 整型.
let "a += 1"
echo "a = $a"           # a = 2335
echo                     # 还是整型.

b=${a/23/BB}             # 将"23"替换成"BB".
                        # 这将把变量 b 从整型变为字符串.
echo "b = $b"            # b = BB35
declare -i b             # 即使使用 declare 命令也不会对此有任何帮助，低版本 bash 下可能没有此命令
echo "b = $b"            # b = BB35
# declare 的命令是用于设置变量的属性，个别版本的 bash 可能没有此命令

let "b += 1"             # BB35 + 1 =
echo "b = $b"            # b = 1
echo

c=BB34
echo "c = $c"            # c = BB34
d=${c/BB/23}             # 将"BB"替换成"23".
                        # 这使得变量$d 变为一个整形.
echo "d = $d"            # d = 2334
let "d += 1"             # 2334 + 1 =
echo "d = $d"            # d = 2335
echo

# null 变量会如何呢?
e=""
echo "e = $e"            # e =
let "e += 1"             # 算术操作允许一个 null 变量?
echo "e = $e"            # e = 1
echo                     # null 变量将被转换成一个整型变量.

# 如果没有声明变量会怎样?
echo "f = $f"            # f =
let "f += 1"             # 算术操作能通过么?
echo "f = $f"            # f = 1
echo                     # 未声明的变量将转换成一个整型变量.

# 所以说 Bash 中的变量都是不区分类型的.

exit 0 
```

运行代码：

```
$ bash test.sh 
```

## 四、特殊变量

### 1.局部变量

这种变量只有在代码块或者函数中才可见。后面的实验会详细讲解。

### 2.环境变量

这种变量将影响用户接口和 shell 的行为。

在通常情况下，每个进程都有自己的“环境”，这个环境是由一组变量组成的，这些变量中存有进程可能需要引用的信息。在这种情况下，shell 与一个一般的进程没什么区别。

### 3.位置参数

从命令行传递到脚本的参数：$0，$1，$2，$3...

$0 就是脚本文件自身的名字，$1 是第一个参数，$2 是第二个参数，$3 是第三个参数，然后是第四个。$9 之后的位置参数就必须用大括号括起来了，比如，${10}，${11}，${12}。

两个比较特殊的变量 $* 和 $@ 表示所有的位置参数。

### 4.位置参数实例

```
$ vim test.sh 
```

输入代码（中文皆为注释，不用输入）：

```
#!/bin/bash

# 作为用例, 调用这个脚本至少需要 10 个参数, 比如:
# bash test.sh 1 2 3 4 5 6 7 8 9 10
MINPARAMS=10

echo

echo "The name of this script is \"$0\"."
# 添加./是表示当前目录
echo "The name of this script is \"`basename $0`\"."
# 去掉路径名, 剩下文件名, (参见'basename')

echo

if [ -n "$1" ]              # 测试变量被引用.
then
echo "Parameter #1 is $1"  # 需要引用才能够转义"#"
fi 

if [ -n "$2" ]
then
echo "Parameter #2 is $2"
fi 

if [ -n "$3" ]
then
echo "Parameter #3 is $3"
fi

if [ -n "$4" ]
then
echo "Parameter #4 is $4"
fi

if [ -n "$5" ]
then
echo "Parameter #5 is $5"
fi

if [ -n "$6" ]
then
echo "Parameter #6 is $6"
fi

if [ -n "$7" ]
then
echo "Parameter #7 is $7"
fi

if [ -n "$8" ]
then
echo "Parameter #8 is $8"
fi

if [ -n "$9" ]
then
echo "Parameter #9 is $9"
fi

if [ -n "${10}" ]  # 大于$9 的参数必须用{}括起来.
then
echo "Parameter #10 is ${10}"
fi 

echo "-----------------------------------"
echo "All the command-line parameters are: "$*""

if [ $# -lt "$MINPARAMS" ]
then
 echo
 echo "This script needs at least $MINPARAMS command-line arguments!"
fi  

echo

exit 0 
```

运行代码：

```
$ bash test.sh 1 2 3 4 5 6 7 8 9 10 
```

## 一、引用变量

### 1.介绍

在一个双引号中通过直接使用变量名的方法来引用变量，一般情况下都是没问题的。这么做将阻止所有在引号中的特殊字符被重新解释(即都被当作普通的字符串)，包括变量名，但是 `$`、`（后置引用）和 "\"（转义符）除外。

保留 $ 作为特殊字符的意义是为了能够在双引号中也能够正常的引用变量（"$variable"）。

使用双引号还能够阻止单词分割，即使这个参数包含有空白，单词也不会被分隔开。 如 variable1="a variable containing five words"

### 2.举例

在 echo 语句中，只有在单词分割或者需要保留空白的时候，才需要把参数用双引号括起来。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

var="'(]\\{}\$\""
echo $var        # '(]\{}$"
echo "$var"      # '(]\{}$"     #同上

echo

IFS='\'
echo $var        # '(] {}$"     \ 字符被空白符替换了, 为什么?
echo "$var"      # '(]\{}$"
echo '$var'      # $var

exit 0 
```

>IFS(Internal Field Seperator)在 Linux 的 shell 中预设的分隔符。IFS 是 shell 脚本中的一个重要概念，在处理文本数据时，它是相当有用的。内部字段分隔符是用于特定用途的定界符。IFS 是存储定界符的环境变量，它是当前 shell 环境使用的默认定界字符串。

当我们设置了 bash 内置变量`IFS`后，再使用`echo`输出时，会将所设定的字符用空格去代替。

运行代码：

```
$ bash test.sh 
```

单引号（`' '`）操作与双引号基本一样，但是不允许引用变量，因为 $ 的特殊意义将被关闭。

在单引号中，任何特殊字符都按照字面的意思进行解释，除了单引号本身。所以说单引号（全引用）是一种比双引号（部分引用）更严格的引用方法。

## 二、转义

### 1.概念

转义是一种引用单个字符的方法。一个前面放上转义符（\）的字符就是告诉 shell 这个字符按照字面的意思进行解释，换句话说，就是这个字符失去了它的特殊含义。

### 2.转义符的含义

在 echo 命令中：

```
符号 | 说明
-----|----
\n | 表示新的一行
\r | 表示回车
\t | 表示水平制表符
\v | 表示垂直制表符
\b | 表示后退符
\a | 表示"alert"(蜂鸣或者闪烁)
\0xx | 转换为八进制的 ASCII 码, 等价于 0xx
\" | 表示引号字面的意思，如：
```

```
$ echo "\"Hello\", he said." # "Hello", he said. 
```

`\$` 表示 `$` 本身字面的含义（跟在 `\$` 后边的变量名将不能引用变量的值），如：

```
$ echo "\$variable01"  # 结果是$variable01 
```

`\\` 表示反斜线字面的意思，如：

```
$ echo "\\"  # 结果是 \ 
```

### 3.一个实例

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

echo "\v\v\v\v"      # 逐字的打印\v\v\v\v.
# 使用-e 选项的'echo'命令来打印转义符.
echo "============="
echo "VERTICAL TABS"
echo -e "\v\v\v\v"   # 打印 4 个垂直制表符.
echo "=============="

echo "QUOTATION MARK"
echo -e "\042"       # 打印" (引号, 8 进制的 ASCII 码就是 42).
echo "=============="

# 如果使用$'\X'结构,那-e 选项就不必要了.
echo; echo "NEWLINE AND BEEP"
echo $'\n'           # 新行.
echo $'\a'           # 警告(蜂鸣).

echo "==============="
echo "QUOTATION MARKS"
# 版本 2 以后 Bash 允许使用$'\nnn'结构.
# 注意在这里, '\nnn\'是 8 进制的值.
echo $'\t \042 \t'   # 被水平制表符括起来的引号(").

# 当然,也可以使用 16 进制的值,使用$'\xhhh' 结构.
echo $'\t \x22 \t'  # 被水平制表符括起来的引号(").
# 感谢, Greg Keraunen, 指出了这点.
# 早一点的 Bash 版本允许'\x022'这种形式.
echo "==============="
echo

# 分配 ASCII 字符到变量中.
# ----------------------------------------
quote=$'\042'        # " 被赋值到变量中.
echo "$quote This is a quoted string, $quote and this lies outside the quotes."

echo

# 变量中的连续的 ASCII 字符.
triple_underline=$'\137\137\137'  # 137 是八进制的'_'.
echo "$triple_underline UNDERLINE $triple_underline"

echo

ABC=$'\101\102\103\010'           # 101, 102, 103 是八进制码的 A, B, C.
echo $ABC

echo; echo

escape=$'\033'                    # 033 是八进制码的 esc.
echo "\"escape\" echoes as $escape"
#                                   没有变量被输出.

echo; echo

exit 0 
```

其中`echo`的`-e`参数表示使能反斜线转义

运行代码：

```
$ bash test.sh 
```

### 4.转义符（\）的行为探究

`\` 的行为依赖于它自身是否被转义，被引用（""），或者是否出现在命令替换或 here document 中。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
                   #  简单的转义和引用
echo \z               #  z
echo \\z              # \z
echo '\z'             # \z
echo '\\z'            # \\z
echo "\z"             # \z
echo "\\z"            # \z

                   #  命令替换
echo `echo \z`        #  z
echo `echo \\z`       #  z
echo `echo \\\z`      # \z
echo `echo \\\\z`     # \z
echo `echo \\\\\\z`   # \z
echo `echo \\\\\\\z`  # \\z
echo `echo "\z"`      # \z
echo `echo "\\z"`     # \z

                   # Here document
cat <<EOF              
\z                      
EOF                   # \z

cat <<EOF              
\\z                     
EOF                   # \z 
```

运行代码：

```
$ bash test.sh 
```

### 5.变量中的转义

赋值给变量的字符串的元素也会被转义，但是不能把一个单独的转义符赋值给变量。

```
$ vim test.sh 
```

输入代码：

```
 #!/bin/bash

 variable=\
 echo "$variable"
 # 不能正常运行 - 会报错:
 # test.sh: : command not found
 # 一个"裸体的"转义符是不能够安全的赋值给变量的.
 #
 #  事实上在这里"\"转义了一个换行符(变成了续航符的含义), 
 #+ 效果就是                variable=echo "$variable"
 #+                      不可用的变量赋值

 variable=\
 23skidoo
 echo "$variable"        #  23skidoo
                         #  这句是可以的, 因为
                         #+ 第 2 行是一个可用的变量赋值.

 variable=\ 
 #             \^    转义一个空格
 echo "$variable"        # 显示空格

 variable=\\
 echo "$variable"        # \

 variable=\\\
 echo "$variable"
 # 不能正常运行 - 报错:
 # test.sh: \: command not found
 #
 #  第一个转义符把第 2 个\转义了,但是第 3 个又变成"裸体的"了,
 #+ 与上边的例子的原因相同.

 variable=\\\\
 echo "$variable"        # \\
                         # 第 2 和第 4 个反斜线被转义了.
                         # 这是正确的. 
```

运行代码：

```
$ bash test.sh 
```

### 6.转义空格

转义一个空格会阻止命令行参数列表的“单词分割”问题。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

file_list="/bin/cat /bin/gzip /bin/more /usr/bin/less /usr/bin/emacs-20.7"
# 列出的文件都作为命令的参数.

# 加两个文件到参数列表中, 列出所有的文件信息.
ls -l /usr/lib/gconv /usr $file_list

echo "-------------------------------------------------------------------------"

# 如果我们将上边的两个空个转义了会产生什么效果?
ls -l /usr/lib/gconv\ /usr\ $file_list
# 错误: 因为前 3 个路径被合并成一个参数传递给了'ls -l'
#       而且两个经过转义的空格组织了参数(单词)分割. 
```

运行代码：

```
$ bash test.sh 
```

### 7.续行功能

转义符也提供续行功能，也就是编写多行命令的功能。

每一个单独行都包含一个不同的命令，但是每行结尾的转义符都会转义换行符，这样下一行会与上一行一起形成一个命令序列。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

mkdir ~/source
mkdir ~/dest
touch ~/source/s.tar

(cd ~/source &amp;&amp; tar cf - . ) | \
(cd ~/dest &amp;&amp; tar xpvf -)
# 重复 Alan Cox 的目录数拷贝命令,
# 但是分成两行是为了增加可读性.

# 也可以使用如下方式:
# tar cf - -C ~/source/ . |
# tar xpvf - -C ~/dest/ 
```

运行代码：

```
$ bash test.sh 
```

如果一个脚本以 `|` （管道符）结束，那么就不用非的加上转义符（`\`）了。但是一个好的编程风格，还是应该在行尾加上转义符。

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou 若不小心登出后，直接刷新页面即可

### 2\. 环境使用

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

### 3\. 课程来源

基于杨春敏与黄毅的 ABS 译文制作，一本深入学习 shell 脚本艺术的书籍。原版链接：http://www.tldp.org/LDP/abs/html/。

## 一、退出状态码

### 1.退出

exit 被用来结束一个脚本，它也返回一个值，并且这个值会传递给脚本的父进程，父进程会使用这个值做下一步的处理。

### 2.退出状态码

每个命令都会返回一个退出状态码（有时候也被称为返回状态）。

成功的命令返回 0，不成功的命令返回非零值，非零值通常都被解释成一个错误码。行为良好的 UNIX 命令、程序和工具都会返回 0 作为退出码来表示成功，虽然偶尔也会有例外。

同样的，脚本中的函数和脚本本身也会返回退出状态码。在脚本或者是脚本函数中执行的最后的命令会决定退出状态码。在脚本中，exit nnn 命令将会 nnn 退出码传递给 shell（nnn 必须是十进制数，范围必须是 0-255）。

当脚本以不带参数的 exit 命令来结束时，脚本的退出状态码就由脚本中最后执行的命令来决定（就是 exit 之前的命令）。

### 3.一个例子

```
$ vim test.sh 
```

输入代码：

```
 #!/bin/bash

 echo hello
 echo $?    # 退出状态为 0, 因为命令执行成功.

 lskdf      # 无效命令.
 echo $?    # 非零的退出状态, 因为命令执行失败.

 echo

 exit 113   # 返回 113 退出状态给 shell.
            # 为了验证这个结果, 可以在脚本结束的地方使用"echo $?". 

 #  一般的, 'exit 0' 表示成功,
 #+ 而一个非零的退出码表示一个错误, 或者是反常的条件 
```

$? 指代的是上一条指令的执行结果。

不带参数的 exit 命令与 exit $? 的效果是一样的，甚至脚本的结尾不写 exit 也与前两者的效果相同。

运行代码：

```
$ bash test.sh 
```

## 二、反转一个条件的用法

### 1.例子

! 逻辑“非”操作符，将会反转命令或条件测试的结果，并且这会影响退出状态码。

```
$ vim test.sh 
```

输入代码：

```
 #!/bin/bash

 true  # "true" 是内建命令.
 echo "exit status of \"true\" = $?"     # 0

 ! true
 echo "exit status of \"! true\" = $?"   # 1
 # 注意: "!" 和指令间需要一个空格
 # 如果一个命令以'!'开头, 那么会启用 Bash 的历史机制.

 true
 !true
 # 这次就没有错误了, 也没有反转结果.
 # 它只是重复了之前的命令(true)，这种语法在个别版本的 bash 中并不支持 
```

运行代码：

```
$ bash test.sh 
```

特定的退出状态码具有保留含义，所以用户不应该在脚本中指定它。

## 一、条件测试结构

### 1.if/then 结构

if/then 结构用来判断命令列表的退出状态码是否为 0，因为 0 表示成功，如果成功的话，这里应该那么就执行接下来的一个或多个命令。

**注意：** 这里与 C 语言的等其它语言不同，不能直接使用 0 或者 1 作为判断条件，而应该以`false`，`true`代替。以其它大多数语言相反的`true`返回的是 0,`false`返回的是 1

有一个专有命令 `[` （左中括号，特殊字符）。这个命令与`test`命令等价，由于效率上的考虑，bash 将它作为一个内建命令。

**注意：** 由于 bash 的语法检查机制，如果在条件测试时只使用一个`[`将会出现一个错误提示，为了避免这个问题，我们通常将使用一对方括号包含条件测试`[]`

在版本 2.02 的 Bash 中, 引入了 "[[ ... ]]" 扩展测试命令，`[[` 是一个关键字，并不是一个命令。

if 命令能够测试任何命令，并不仅仅是中括号中的条件。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

# 逐字节比较 a b 两个文件是否相同
if cmp a b &amp;> /dev/null  # 禁止输出.
then echo "Files a and b are identical."
else echo "Files a and b differ."
fi

# 非常有用的"if-grep"结构:
# ------------------------
if grep -q Bash file
then echo "File contains at least one occurrence of Bash."
fi

word=Linux
letter_sequence=inu
if echo "$word" | grep -q "$letter_sequence"
# "-q" 选项是用来禁止输出的.
then
 echo "$letter_sequence found in $word"
else
 echo "$letter_sequence not found in $word"
fi

# 将打印 Command failed
if COMMAND_WHOSE_EXIT_STATUS_IS_0_UNLESS_ERROR_OCCURRED
then echo "Command succeeded."
else echo "Command failed."
fi 
```

运行代码：

```
$ bash test.sh 
```

### 2.多级比较（应注意与嵌套条件分支区分）

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

# 这里应该理解为子 if/then 当做一个整体作为测试条件
if echo "Next *if* is part of the comparison for the first *if*."
   if [[ $comparison = "integer" ]]
     then (( a < b )) # (( 算数表达式 ))， 用作算数运算
   else
     [[ $a < $b ]]
   fi
then
 echo '$a is less than $b'
fi 
```

**注意：** ((...))和 let...如果运算结果为非 0，该语句退出码为 0，否则为 1；[[...]]是作为一个单独的语句并且会返回一个退出码

运行代码：

```
$ bash test.sh 
```

### 3.真假判断

```
$ vim test.sh 
```

输入代码：

```
 #!/bin/bash

 #  小技巧:
 #  如果你不能够确定一个特定的条件该如何进行判断,
 #+ 那么就使用 if-test 结构.

 echo

 echo "Testing \"0\""
 if [ 0 ]      # zero
 then
   echo "0 is true."
 else
   echo "0 is false."
 fi            # 0 为真.

 echo

 echo "Testing \"1\""
 if [ 1 ]      # one
 then
   echo "1 is true."
 else
   echo "1 is false."
 fi            # 1 为真.

 echo

 echo "Testing \"-1\""
 if [ -1 ]     # 负 1
 then
   echo "-1 is true."
 else
   echo "-1 is false."
 fi            # -1 为真.

 echo

 echo "Testing \"NULL\""
 if [ ]        # NULL (空状态)
 then
   echo "NULL is true."
 else
   echo "NULL is false."
 fi            # NULL 为假.

 echo

 echo "Testing \"xyz\""
 if [ xyz ]    # 字符串
 then
   echo "Random string is true."
 else
   echo "Random string is false."
 fi            # 随便的一串字符为真.

 echo

 echo "Testing \"\$xyz\""
 if [ $xyz ]   # 判断$xyz 是否为 null, 但是...
               # 这只是一个未初始化的变量.
 then
   echo "Uninitialized variable is true."
 else
   echo "Uninitialized variable is false."
 fi            # 未定义的初始化为假.

 echo

 echo "Testing \"-n \$xyz\""
 if [ -n "$xyz" ]            # 更加正规的条件检查.
 then
   echo "Uninitialized variable is true."
 else
   echo "Uninitialized variable is false."
 fi            # 未初始化的变量为假.

 echo

 xyz=          # 初始化了, 但是赋 null 值.

 echo "Testing \"-n \$xyz\""
 if [ -n "$xyz" ]
 then
   echo "Null variable is true."
 else
   echo "Null variable is false."
 fi            # null 变量为假.

 echo

 # 什么时候"false"为真?

 echo "Testing \"false\""
 if [ "false" ]              #  看起来"false"只不过是一个字符串而已.
 then
   echo "\"false\" is true." #+ 并且条件判断的结果为真.
 else
   echo "\"false\" is false."
 fi            # "false" 为真.

 echo

 echo "Testing \"\$false\""  # 再来一个, 未初始化的变量.
 if [ "$false" ]
 then
   echo "\"\$false\" is true."
 else
   echo "\"\$false\" is false."
 fi            # "$false" 为假.
               # 现在, 我们得到了预期的结果.

 #  如果我们测试以下为初始化的变量"$true"会发生什么呢?

 echo

 exit 0 
```

运行代码：

```
$ bash test.sh 
```

### 4.(( )) 结构

(( )) 结构扩展并计算一个算术表达式的值。如果表达式的结果为 0，那么返回的退出状态码为 1，或者是"假"。而一个非零值的表达式所返回的退出状态码将为 0，或者是"true"。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
# 算术测试.

# (( ... ))结构可以用来计算并测试算术表达式的结果.
# 退出状态将会与[ ... ]结构完全相反!

(( 0 ))
echo "Exit status of \"(( 0 ))\" is $?."         # 1

(( 1 ))
echo "Exit status of \"(( 1 ))\" is $?."         # 0

(( 5 > 4 ))                                      # 真
echo "Exit status of \"(( 5 > 4 ))\" is $?."     # 0

(( 5 > 9 ))                                      # 假
echo "Exit status of \"(( 5 > 9 ))\" is $?."     # 1

(( 5 - 5 ))                                      # 0
echo "Exit status of \"(( 5 - 5 ))\" is $?."     # 1

(( 5 / 4 ))                                      # 除法也可以.
echo "Exit status of \"(( 5 / 4 ))\" is $?."     # 0

(( 1 / 2 ))                                      # 除法的计算结果 < 1.
echo "Exit status of \"(( 1 / 2 ))\" is $?."     # 截取之后的结果为 0.
                                                # 1

(( 1 / 0 )) 2>/dev/null                          # 除数为 0, 非法计算.
#           ^^^^^^^^^^^
echo "Exit status of \"(( 1 / 0 ))\" is $?."     # 1

# "2>/dev/null"起了什么作用?
# 如果这句被删除会怎样?
# 尝试删除这句, 然后在运行这个脚本.

exit 0 
```

运行代码：

```
$ bash test.sh 
```

## 二、文件测试操作符

### 1.操作符列举

```
操作符 | 说明
------|------
-e |文件存在
-a |文件存在，这个选项的效果与 -e 相同。但是它已经被“弃用”了，并且不鼓励使用。
-f |表示这个文件是一个一般文件（并不是目录或者设备文件）
-s | 文件大小不为零
-d |表示这是一个目录
-b |表示这是一个块设备（软盘，光驱，等等）
-c |表示这是一个字符设备（键盘，modem，声卡，等等）
-p |这个文件是一个管道
-h |这是一个符号链接
-L |这是一个符号链接
-S |表示这是一个 socket
-t |文件（描述符）被关联到一个终端设备上，这个测试选项一般被用来检测脚本中的 stdin([ -t 0 ]) 或者 stdout([ -t 1 ])是否来自于一个终端
-r |文件是否具有可读权限（指的是正在运行这个测试命令的用户是否具有读权限）
-w |文件是否具有可写权限（指的是正在运行这个测试命令的用户是否具有写权限）
-x |文件是否具有可执行权限（指的是正在运行这个测试命令的用户是否具有可执行权限）
-g |set-group-id(sgid)标记被设置到文件或目录上
-k |设置粘贴位
-O |判断你是否是文件的拥有者
-G |文件的 group-id 是否与你的相同
-N |从文件上一次被读取到现在为止, 文件是否被修改过
f1 -nt f2 |文件 f1 比文件 f2 新
f1 -ot f2 |文件 f1 比文件 f2 旧
f1 -ef f2 |文件 f1 和文件 f2 是相同文件的硬链接
! |“非”，反转上边所有测试的结果（如果没给出条件，那么返回真）
```

### 2.举例

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
# broken-link.sh
# 由 Lee bigelow 所编写 <ligelowbee@yahoo.com>
# 已经争得作者的授权引用在本书中.

#一个纯粹的 shell 脚本用来找出那些断掉的符号链接文件并且输出它们所指向的文件.
#以便于它们可以把输出提供给 xargs 来进行处理 :)
#比如. broken-link.sh /somedir /someotherdir|xargs rm
#
#下边的方法, 不管怎么说, 都是一种更好的办法:
#
#find "somedir" -type l -print0|\
#xargs -r0 file|\
#grep "broken symbolic"|
#sed -e 's/^\|: *broken symbolic.*$/"/g'
#
#但这不是一个纯粹的 bash 脚本, 最起码现在不是.
#注意: 谨防在/proc 文件系统和任何死循环链接中使用!
##############################################################

#如果没有参数被传递到脚本中, 那么就使用
#当前目录. 否则就是用传递进来的参数作为目录
#来搜索.
####################
[ $# -eq 0 ] &amp;&amp; directorys=`pwd` || directorys=$@

#编写函数 linkchk 用来检查传递进来的目录或文件是否是链接,
#并判断这些文件或目录是否存在. 然后打印它们所指向的文件.
#如果传递进来的元素包含子目录,
#那么把子目录也放到 linkcheck 函数中处理, 这样就达到了递归的目的.
##########
linkchk () {
   for element in $1/*; do
   [ -h "$element" -a ! -e "$element" ] &amp;&amp; echo \"$element\"
   [ -d "$element" ] &amp;&amp; linkchk $element
   # 当然, '-h'用来测试符号链接, '-d'用来测试目录.
   done
}

#把每个传递到脚本的参数都送到 linkchk 函数中进行处理,
#检查是否有可用目录. 如果没有, 那么就打印错误消息和
#使用信息.
################
for directory in $directorys; do
   if [ -d $directory ]
  then linkchk $directory
  else
      echo "$directory is not a directory"
      echo "Usage: $0 dir1 dir2 ..."
   fi
done

exit 0 
```

运行代码：

```
$ bash test.sh 
```

## 三、二元比较操作符

### 1.整数比较

-eq 等于

```
if [ "$a" -eq "$b" ] 
```

-ne 不等于

```
if [ "$a" -ne "$b" ] 
```

-gt 大于

```
if [ "$a" -gt "$b" ] 
```

-ge 大于等于

```
if [ "$a" -ge "$b" ] 
```

-lt 小于

```
if [ "$a" -lt "$b" ] 
```

-le 小于等于

```
if [ "$a" -le "$b" ] 
```

< 小于(在双括号中使用)

```
(("$a" < "$b")) 
```

<= 小于等于(在双括号中使用)

```
(("$a" <= "$b")) 
```

> 大于(在双括号中使用)

```
(("$a" > "$b")) 
```

>= 大于等于(在双括号中使用)

```
(("$a" >= "$b")) 
```

### 2.字符串比较

= 等于

```
if [ "$a" = "$b" ] 
```

== 等于，与=等价

```
if [ "$a" == "$b" ] 
```

!= 不等号

```
if [ "$a" != "$b" ] 
```

< 小于，按照 ASCII 字符进行排序

```
if [[ "$a" < "$b" ]]
if [ "$a" \< "$b" ]
注意"<"使用在[ ]结构中的时候需要被转义 
```

> 大于，按照 ASCII 字符进行排序

```
if [[ "$a" > "$b" ]]
if [ "$a" \> "$b" ]
注意“>”使用在[ ]结构中的时候需要被转义 
```

-z 字符串为“null”，意思就是字符串长度为零 -n 字符串不为“null”

### 3.算术比较与字符串比较

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

a=4
b=5

#  这里的"a"和"b"既可以被认为是整型也可被认为是字符串.
#  这里在算术比较与字符串比较之间是容易让人产生混淆,
#+ 因为 Bash 变量并不是强类型的.

#  Bash 允许对于变量进行整形操作与比较操作.
#+ 但前提是变量中只能包含数字字符.
#  不管怎么样, 还是要小心.

echo

if [ "$a" -ne "$b" ]
then
 echo "$a is not equal to $b"
 echo "(arithmetic comparison)"
fi

echo

if [ "$a" != "$b" ]
then
 echo "$a is not equal to $b."
 echo "(string comparison)"
 #     "4"  != "5"
 # ASCII 52 != ASCII 53
fi

# 在这个特定的例子中, "-ne"和"!="都可以.

echo

exit 0 
```

运行代码：

```
$ bash test.sh 
```

### 4.检查字符串是否为 null

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
#  str-test.sh: 检查 null 字符串和未引用的字符串,
#+ but not strings and sealing wax, not to mention cabbages and kings . . .
#+ 但不是字符串和封蜡, 也并没有提到卷心菜和国王. . . ??? (没看懂, rojy bug)

# 使用   if [ ... ]

# 如果字符串并没有被初始化, 那么它里面的值未定义.
# 这种状态被称为"null" (注意这与零值不同).

if [ -n $string1 ]    # $string1 没有被声明和初始化.
then
 echo "String \"string1\" is not null."
else  
 echo "String \"string1\" is null."
fi  
# 错误的结果.
# 显示$string1 为非 null, 虽然这个变量并没有被初始化.

echo

# 让我们再试一下.

if [ -n "$string1" ]  # 这次$string1 被引号扩起来了.
then
 echo "String \"string1\" is not null."
else  
 echo "String \"string1\" is null."
fi                    # 注意一定要将引用的字符放到中括号结构中!

echo

if [ $string1 ]       # 这次, 就一个$string1, 什么都不加.
then
 echo "String \"string1\" is not null."
else  
 echo "String \"string1\" is null."
fi  
# 这种情况运行的非常好.
# [ ] 测试操作符能够独立检查 string 是否为 null.
# 然而, 使用("$string1")是一种非常好的习惯.
#
# 就像 Stephane Chazelas 所指出的,
#    if [ $string1 ]    只有一个参数, "]"
#    if [ "$string1" ]  有两个参数, 一个是空的"$string1", 另一个是"]"

echo

string1=initialized

if [ $string1 ]       # 再来, 还是只有$string1, 什么都不加.
then
 echo "String \"string1\" is not null."
else  
 echo "String \"string1\" is null."
fi  
# 再来试一下, 给出了正确的结果.
# 再强调一下, 使用引用的("$string1")还是更好一些, 原因我们上边已经说过了.

string1="a = b"

if [ $string1 ]       # 再来, 还是只有$string1, 什么都不加.
then
 echo "String \"string1\" is not null."
else  
 echo "String \"string1\" is null."
fi  
# 未引用的"$string1", 这回给出了错误的结果!

exit 0 
```

运行代码：

```
$ bash test.sh 
```

### 5.zmore

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
# zmore

#使用'more'来查看 gzip 文件

NOARGS=65
NOTFOUND=66
NOTGZIP=67

if [ $# -eq 0 ] # 与 if [ -z "$1" ]效果相同
# (译者注: 上边这句注释有问题), $1 是可以存在的, 可以为空, 如:  zmore "" arg2 arg3
then
 echo "Usage: `basename $0` filename" >&amp;2
 # 错误消息输出到 stderr.
 exit $NOARGS
 # 返回 65 作为脚本的退出状态的值(错误码).
fi  

filename=$1

if [ ! -f "$filename" ]   # 将$filename 引用起来, 这样允许其中包含空白字符.
then
 echo "File $filename not found!" >&amp;2
 # 错误消息输出到 stderr.
 exit $NOTFOUND
fi  

if [ ${filename##*.} != "gz" ]
# 在变量替换中使用中括号结构.
then
 echo "File $1 is not a gzipped file!"
 exit $NOTGZIP
fi  

zcat $1 | more

# 使用过滤命令'more.'
# 当然, 如果你愿意, 也可以使用'less'.

exit $?   # 脚本将把管道的退出状态作为返回值.
# 事实上, 也不一定非要加上"exit $?", 因为在任何情况下,
# 脚本都会将最后一条命令的退出状态作为返回值. 
```

运行代码：

```
$ bash test.sh 
```

### 6.compound 和 comparison

`-a` 逻辑与 exp1 -a exp2 如果表达式 exp1 和 exp2 都为真的话，那么结果为真。

`-o` 逻辑或 exp1 -o exp2 如果表达式 exp1 和 exp2 中至少有一个为真的话，那么结果为真。

**注意：**这与 Bash 中的比较操作符 && 和 || 非常相像，但是这个两个操作符是用在双中括号结构中的。

```
[[ condition1 &amp;&amp; condition2 ]] 
```

`-o`和`-a`操作符一般都是和 test 命令或者是单中括号结构一起使用的

```
if [ "$exp1" -a "$exp2" ] 
```

## 一、赋值

### 1.变量赋值

`=` 通用赋值操作符，可用于算术和字符串赋值。

```
var=27
category=minerals  # 在"="之后是不允许出现空白字符的. 
```

不要混淆`=`赋值操作符与 `=` 测试操作符。也正是因为这一点，在 bash 里面测试操作符也常写作`==`，但这可能在其它 shell 中是不允许的

```
#    = 在这里是测试操作符

if [ "$string1" = "$string2" ]
# if [ "X$string1" = "X$string2" ] 是一种更安全的做法,
# 这样可以防止两个变量中的一个为空所产生的错误.
# (字符"X"作为前缀在等式两边是可以相互抵消的.)
then
  command
fi 
```

## 二、算术操作符

### 1.简单算术操作符

```
操作符 | 说明
------|-----
+ | 加法计算
- | 减法计算
* | 乘法计算
/ | 除法计算
** | 幂运算
% | 模运算，或者是求余运算（返回一次除法运算的余数）
```

### 2.求最大公约数

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
# gcd.sh: 最大公约数
#         使用 Euclid 的算法

#  两个整数的"最大公约数" (gcd),
#+ 就是两个整数所能够同时整除的最大的数.

#  Euclid 算法采用连续除法.
#  在每一次循环中,
#+ 被除数 <---  除数
#+ 除数 <---  余数
#+ 直到 余数 = 0.
#+ 在最后一次循环中, gcd = 被除数.
#
#  关于 Euclid 算法的更精彩的讨论, 可以到
#+ Jim Loy 的站点, http://www.jimloy.com/number/euclids.htm.

# ------------------------------------------------------
# 参数检查
ARGS=2
E_BADARGS=65

if [ $# -ne "$ARGS" ]
then
 echo "Usage: `basename $0` first-number second-number"
 exit $E_BADARGS
fi
# ------------------------------------------------------

gcd ()
{

 dividend=$1                    #  随意赋值.
 divisor=$2                     #+ 在这里, 哪个值给的大都没关系.
                                #  为什么没关系?

 remainder=1                    #  如果在循环中使用了未初始化的变量,
                                #+ 那么在第一次循环中,
                                #+ 它将会产生一个错误消息.

 until [ "$remainder" -eq 0 ]
 do
   let "remainder = $dividend % $divisor"
   dividend=$divisor            # 现在使用两个最小的数来重复.
   divisor=$remainder
 done                           # Euclid 的算法

}                                # Last $dividend is the gcd.

gcd $1 $2

echo; echo "GCD of $1 and $2 = $dividend"; echo
# 或者 echo -e "\nGCD of $1 and $2 = $dividend\n"

# Exercise :
# --------
#  检查传递进来的命令行参数来确保它们都是整数.
#+ 如果不是整数, 那就给出一个适当的错误消息并退出脚本.

exit 0 
```

运行代码：

```
$ bash test.sh 
```

### 3.思考练习

*   上面练习脚本中，为何 dividend 与 divisor 的赋值的顺序没有关系
*   完成上面的 Excercise

### 4.其他算术操作符

`+=` “加-等于”（把变量的值增加一个常量然后再把结果赋给变量）

```
let "var += 5" # var 变量的值会在原来的基础上加 5 
```

`-=` “减-等于”（把变量的值减去一个常量然后再把结果赋给变量） `*=` “乘-等于”（先把变量的值乘以一个常量的值，然后再把结果赋给变量）

```
let "var *= 4" # var 变量的结果将会在原来的基础上乘以 4 
```

`/=` “除-等于”（先把变量的值除以一个常量的值，然后再把结果赋给变量） `%=` “取模-等于”（先对变量进行模运算，即除以一个常量取模，然后把结果赋给变量）

### 5.使用算术操作符

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
# 使用 10 种不同的方法计数到 11.

n=1; echo -n "$n "

let "n = $n + 1"   # let "n = n + 1"  也可以.
echo -n "$n "

: $((n = $n + 1))
#  ":" 是必需的, 因为如果没有":"的话,
#+ Bash 将会尝试把"$((n = $n + 1))"解释为一个命令.
echo -n "$n "

(( n = n + 1 ))
#  上边这句是一种更简单方法.
#  感谢, David Lombard, 指出这点.
echo -n "$n "

n=$(($n + 1))
echo -n "$n "

: $[ n = $n + 1 ]
#  ":" 是必需的, 因为如果没有":"的话,
#+ Bash 将会尝试把"$[ n = $n + 1 ]"解释为一个命令.
#  即使"n"被初始化为字符串, 这句也能够正常运行.
echo -n "$n "

n=$[ $n + 1 ]
#  即使"n"被初始化为字符串, 这句也能够正常运行.
#* 应该尽量避免使用这种类型的结构, 因为它已经被废弃了, 而且不具可移植性.
#  感谢, Stephane Chazelas.
echo -n "$n "

# 现在来一个 C 风格的增量操作.
# 感谢, Frank Wang, 指出这点.

let "n++"          # let "++n"  也可以.
echo -n "$n "

(( n++ ))          # (( ++n )  也可以.
echo -n "$n "

: $(( n++ ))       # : $(( ++n )) 也可以.
echo -n "$n "

: $[ n++ ]         # : $[[ ++n ]] 也可以.
echo -n "$n "

echo

exit 0 
```

运行代码：

```
$ bash test.sh 
```

## 三、位操作符

### 1.简介

`<<` 左移一位（每次左移都相当于乘以 2）

`<<=` “左移-赋值”

```
let "var <<= 2" #这句的结果就是变量 var 左移 2 位(就是乘以 4) 
```

`\>>` 右移一位（每次右移都将除以 2）

`\>>=` “右移-赋值”（与<<=正好相反）

`&amp;` 按位与

`&amp;=` “按位与-赋值”

`|` 按位或

`|=` “按位或-赋值”

`～` 按位反

`!` 按位非

`^` 按位异或 XOR

`^=` “按位异或-赋值”

## 四、逻辑操作符

### 1.简介

`&amp;&amp;` 与(逻辑) `||` 或(逻辑)

### 2.使用&&和||进行混合条件测试

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

a=24
b=47

if [ "$a" -eq 24 ] &amp;&amp; [ "$b" -eq 47 ]
then
 echo "Test #1 succeeds."
else
 echo "Test #1 fails."
fi

# ERROR:   if [ "$a" -eq 24 &amp;&amp; "$b" -eq 47 ]
#+         尝试运行' [ "$a" -eq 24 '
#+         因为没找到匹配的']'所以失败了.
#
#  注意:  if [[ $a -eq 24 &amp;&amp; $b -eq 24 ]]  能正常运行.
#  双中括号的 if-test 结构要比
#+ 单中括号的 if-test 结构更加灵活.
#    (在第 17 行"&amp;&amp;"与第 6 行的"&amp;&amp;"具有不同的含义.)
#    感谢, Stephane Chazelas, 指出这点.

if [ "$a" -eq 98 ] || [ "$b" -eq 47 ]
then
 echo "Test #2 succeeds."
else
 echo "Test #2 fails."
fi

#  -a 和-o 选项提供了
#+ 一种可选的混合条件测试的方法.
#  感谢 Patrick Callahan 指出这点.

if [ "$a" -eq 24 -a "$b" -eq 47 ]
then
 echo "Test #3 succeeds."
else
 echo "Test #3 fails."
fi

if [ "$a" -eq 98 -o "$b" -eq 47 ]
then
 echo "Test #4 succeeds."
else
 echo "Test #4 fails."
fi

a=rhino
b=crocodile
if [ "$a" = rhino ] &amp;&amp; [ "$b" = crocodile ]
then
 echo "Test #5 succeeds."
else
 echo "Test #5 fails."
fi

exit 0 
```

运行代码：

```
$ bash test.sh 
```

## 五、混杂的操作符

### 1.简介

`,` 逗号操作符

逗号操作符可以连接两个或多个算术运算。所有的操作都会被运行（可能会有负作用），但是只会返回最后操作的结果。

```
let "t1 = ((5 + 3, 7 - 1, 15 - 4))"
echo "t1 = $t1"               # t1 = 11

let "t2 = ((a = 9, 15 / 3))"  # 设置"a"并且计算"t2".
echo "t2 = $t2    a = $a"     # t2 = 5    a = 9 
```

## 一、内置变量

### 1.$BASH

Bash 的二进制程序文件的路径（脚本解释器的路径）

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
echo $BASH 
```

运行代码：

```
$ bash test.sh 
```

### 2.$FUNCNAME

当前函数的名字

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
xyz23 ()
{
  echo "$FUNCNAME now executing."
  # 正常情况下打印: xyz23 now executing.
  # 在个别版本的 bash 中，并不支持$FUNCNAME 内置变量
}

xyz23

echo "FUNCNAME = $FUNCNAME"        # FUNCNAME =
# 超出函数的作用域就变为 null 值了. 
```

运行代码：

```
$ bash test.sh 
```

### 3.$IFS

内部域分隔符，这个变量用来决定 Bash 在解释字符串时如何识别域，或者单词边界。

`$IFS`默认为空白（空格，制表符，和换行符），但这是可以修改的，比如，在分析逗号分 隔的数据文件时，就可以设置为逗号，注意 `$*` 使用的是保存在`$IFS`中的第一个字符。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
# $IFS 处理空白与处理其他字符不同.

output_args_one_per_line()
{
  for arg
  do echo "[$arg]"
  done
}

echo; echo "IFS=\" \""
echo "-------"

IFS=" "
var=" a  b c   "
output_args_one_per_line $var  # output_args_one_per_line `echo " a  b c   "`
#
# [a]
# [b]
# [c]

echo; echo "IFS=:"
echo "-----"

IFS=:
var=":a::b:c:::"               # 与上边一样, 但是用" "替换了":".
output_args_one_per_line $var
#
# []
# [a]
# []
# [b]
# [c]
# []
# []
# []

# 同样的事情也会发生在 awk 的"FS"域中.

# 感谢, Stephane Chazelas.

echo

exit 0 
```

**注意:** 你可能对上面的 for 循环和变量 arg 有疑问，这将在下一节进行讨论。

运行代码：

```
$ bash test.sh 
```

### 4、$REPLY

当没有参数变量提供给 read 命令的时候，这个变量会作为默认变量提供给 read 命令。也 可以用于 select 菜单，但是只提供所选择变量的编号，而不是变量本身的值。在个别版本的 bash 中，并不支持内置变量$REPLY。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
# reply.sh

# REPLY 是提供给'read'命令的默认变量.

echo
echo -n "What is your favorite vegetable? "
read

echo "Your favorite vegetable is $REPLY."
#  当且仅当没有变量提供给"read"命令时,
#+ REPLY 才保存最后一个"read"命令读入的值.

echo
echo -n "What is your favorite fruit? "
read fruit
echo "Your favorite fruit is $fruit."
echo "but..."
echo "Value of \$REPLY is still $REPLY."
#  $REPLY 还是保存着上一个 read 命令的值,
#+ 因为变量$fruit 被传入到了这个新的"read"命令中.

echo

exit 0 
```

运行代码：

```
$ bash test.sh 
```

### 5.通过`$*`和`$@`列出所有的参数

`"$*"` 所有的位置参数都被看作为一个单词。 `$@`与 `$*` 相同，但是每个参数都是一个独立的引用字符串，这就意味着，参数是被完整 传递的，并没有被解释或扩展。这也意味着，参数列表中每个参数都被看作为单独的单词。

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash
# 多使用几个参数来调用这个脚本, 比如"one two three".

E_BADARGS=65

if [ ! -n "$1" ]
then
echo "Usage: `basename $0` argument1 argument2 etc."
exit $E_BADARGS
fi  

echo

index=1          # 起始计数.

echo "Listing args with \"\$*\":"
for arg in "$*"  # 如果"$*"不被""（双引号）引用,那么将不能正常地工作.
do
echo "Arg #$index = $arg"
let "index+=1"
done             # $* 将所有的参数看成一个单词.
echo "Entire arg list seen as single word."

echo

index=1          # 重置计数(译者注: 从 1 开始).

echo "Listing args with \"\$@\":"
for arg in "$@"
do
echo "Arg #$index = $arg"
let "index+=1"
done             # $@ 把每个参数都看成是单独的单词.
echo "Arg list seen as separate words."

echo

index=1          # 重置计数(译者注: 从 1 开始).

echo "Listing args with \$* (unquoted):"
for arg in $*
do
echo "Arg #$index = $arg"
let "index+=1"
done             # 未引用的$*将会把参数看成单独的单词.
echo "Arg list seen as separate words."

exit 0 
```

运行代码：

```
$ bash test.sh 
```

### 二、操作字符串

### 1.字符串长度

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

stringZ=abcABC123ABCabc

echo ${#stringZ}                 # 15
expr length $stringZ             # 15 
```

运行代码：

```
$ bash test.sh 
```

### 2.使用 awk 来处理字符串

Bash 脚本也可以调用 awk 的字符串操作功能来代替它自己内建的字符串操作。

例子：提取字符串

```
$ vim test.sh 
```

输入代码：

```
#!/bin/bash

String=23skidoo1
#      012345678    Bash
#      123456789    awk
# 注意不同的字符串索引系统:
# Bash 的第一个字符是从'0'开始记录的.
# Awk 的第一个字符是从'1'开始记录的.

echo ${String:2:4} # 位置 3 (0-1-2), 4 个字符长
# skid

# awk 中等价于${string:pos:length}的命令是 substr(string,pos,length).
echo | awk '
{
    print substr("'"${String}"'",3,4)      # skid
}
'
#  使用一个空的"echo"通过管道传递给 awk 一个假的输入,
#+ 这样就不必提供一个文件名给 awk.

exit 0 
```

运行代码：

```
$ bash test.sh 
```

### 三、参数替换

### 1.处理和（或）扩展变量

${parameter} 与 $parameter 相同，也就是变量 parameter 的值。但在某些情况下，最好使用${parameter} 以避免产生混淆。

```
your_id=${USER}-on-${HOSTNAME}
echo "$your_id"
echo "Old \$PATH = $PATH"
PATH=${PATH}:/opt/bin  #在脚本的生命周期中, /opt/bin 会被添加到$PATH 变量中.
echo "New \$PATH = $PATH" 
```

${parameter-default} 如果变量 parameter 没被声明，那么就使用默认值。 ${parameter:-default} 如果变量 parameter 没被设置，那么就使用默认值。

```
#!/bin/bash
# param-sub.sh

#  一个变量是否被声明或设置,
#+ 将会影响这个变量是否使用默认值,
#+ 即使这个变量值为空(null).

username0=
echo "username0 has been declared, but is set to null."
echo "username0 = ${username0-`whoami`}"
# 不会有输出.

echo

echo username1 has not been declared.
echo "username1 = ${username1-`whoami`}"
# 将会输出默认值.

username2=
echo "username2 has been declared, but is set to null."
echo "username2 = ${username2:-`whoami`}"
#                            ^
# 会输出, 因为:-会比-多一个条件测试.
# 可以与上边的例子比较一下. 
```

```
指定方式 | 说明
---------|--------
${parameter-default} | 如果变量 parameter 没被声明，那么就使用默认值。
${parameter:-default} | 如果变量 parameter 没被设置，那么就使用默认值。
${parameter=default} | 如果变量 parameter 没声明，那么就把它的值设为 default。
${parameter:=default} | 如果变量 parameter 没设置，那么就把它的值设为 default。
${parameter+alt*value} | 如果变量 parameter 被声明了，那么就使用 alt*value，否则就使用 null 字符串。
${parameter:+alt*value} | 如果变量 parameter 被设置了，那么就使用 alt*value，否则就使用 null 字符串。
${parameter?err*msg} | 如果 parameter 已经被声明，那么就使用设置的值，否则打印 err*msg 错误消息。
${parameter:?err*msg} | 如果 parameter 已经被设置，那么就使用设置的值，否则打印 err*msg 错误消息。
```

```
#!/bin/bash

#  检查一些系统环境变量.
#  这是一种可以做一些预防性保护措施的好习惯.
#  比如, 如果$USER(用户在控制台上中的名字)没有被设置的话,
#+ 那么系统就会不认你.

: ${HOSTNAME?} ${USER?} ${HOME?} ${MAIL?}
echo
echo "Name of the machine is $HOSTNAME."
echo "You are $USER."
echo "Your home directory is $HOME."
echo "Your mail INBOX is located in $MAIL."
echo
echo "If you are reading this message,"
echo "critical environmental variables have been set."
echo
echo

# ------------------------------------------------------

#  ${variablename?}结构
#+ 也能够检查脚本中变量的设置情况.

ThisVariable=Value-of-ThisVariable
#  注意, 顺便提一下,
#+ 这个字符串变量可能会被设置一些非法字符.
: ${ThisVariable?}
echo "Value of ThisVariable is $ThisVariable".
echo
echo

: ${ZZXy23AB?"ZZXy23AB has not been set."}
#  如果变量 ZZXy23AB 没有被设置的话,
#+ 那么这个脚本会打印一个错误信息, 然后结束.

# 你可以自己指定错误消息.
# : ${variablename?"ERROR MESSAGE"}

# 等价于:    dummy_variable=${ZZXy23AB?}
#            dummy_variable=${ZZXy23AB?"ZXy23AB has not been set."}
#
#            echo ${ZZXy23AB?} >/dev/null

#  使用命令"set -u"来比较这些检查变量是否被设置的方法.
#

echo "You will not see this message, because script already terminated."

HERE=0
exit $HERE   # 不会在这里退出.

# 事实上, 这个脚本将会以返回值 1 作为退出状态(echo $?). 
```

### 2.变量长度/子串删除

${#var} 字符串长度（变量$var 得字符个数）。对于 array 来说，${#array}表示的是数组中第一个元素的长度.。

变量长度：

```
#!/bin/bash

E_NO_ARGS=65

if [ $# -eq 0 ]  # 这个演示脚本必须有命令行参数.
then
echo "Please invoke this script with one or more command-line arguments."
exit $E_NO_ARGS
fi  

var01=abcdEFGH28ij
echo "var01 = ${var01}"
echo "Length of var01 = ${#var01}"
# 现在, 让我们试试在变量中嵌入一个空格.
var02="abcd EFGH28ij"
echo "var02 = ${var02}"
echo "Length of var02 = ${#var02}"

echo "Number of command-line arguments passed to script = ${#@}"
echo "Number of command-line arguments passed to script = ${#*}"

exit 0 
```

${var#Pattern}, ${var##Pattern} 从变量$var 的开头删除最短或最长匹配$Pattern 的子串。 “#”表示匹配最短，“##”表示匹配最长。

```
strip_leading_zero2 () # 去掉开头可能存在的 0(也可能有多个 0), 因为如果不取掉的话,
{                      # Bash 就会把这个值当作 8 进制的值来解释.
  shopt -s extglob     # 打开扩展的通配(globbing).
  local val=${1##+(0)} # 使用局部变量, 匹配最长连续的一个或多个 0.
  shopt -u extglob     # 关闭扩展的通配(globbing).
  _strip_leading_zero2=${val:-0}
  # 如果输入为 0, 那么返回 0 来代替"".
} 
```

${var%Pattern}, ${var%%Pattern} 从变量$var 的结尾删除最短或最长匹配$Pattern 的子串。 “%”表示匹配最短，“%%”表示匹配最长。

### 3.变量扩展/子串替换

${var:pos} 变量 var 从位置 pos 开始扩展， 也就是 pos 之前的字符都丢弃。 ${var:pos:len} 变量 var 从位置 pos 开始，并扩展 len 个字符。 ${var/Pattern/Replacement} 使用 Replacement 来替换变量 var 中第一个匹配 Pattern 的字符串。 ${var//Pattern/Replacement} 全局替换。所有在变量 var 匹配 Pattern 的字符串，都会被替换为 Replacement。

```
#!/bin/bash

var1=abcd-1234-defg
echo "var1 = $var1"

t=${var1#*-*}
echo "var1 (with everything, up to and including first - stripped out) = $t"
#  t=${var1#*-}  也一样,
#+ 因为#匹配最短的字符串,
#+ 同时*匹配任意前缀, 包括空字符串.
# (感谢, Stephane Chazelas, 指出这点.)

t=${var1##*-*}
echo "If var1 contains a \"-\", returns empty string...   var1 = $t"

t=${var1%*-*}
echo "var1 (with everything from the last - on stripped out) = $t" 
```

${var/#Pattern/Replacement} 如果变量 var 的前缀匹配 Pattern，那么就使用 Replacement 来替换匹配到 Pattern 的字符串。 ${var/%Pattern/Replacement} 如果变量 var 的后缀匹配 Pattern，那么就使用 Replacement 来替换匹配到 Pattern 的字符串。

```
#!/bin/bash
# var-match.sh:
# 对字符串的前缀和后缀进行模式替换的一个演示.

v0=abc1234zip1234abc    # 变量原始值.
echo "v0 = $v0"         # abc1234zip1234abc
echo

# 匹配字符串的前缀(开头).
v1=${v0/#abc/ABCDEF}    # abc1234zip1234abc
# |-|
echo "v1 = $v1"         # ABCDEF1234zip1234abc
# |----|

# 匹配字符串的后缀(结尾).
v2=${v0/%abc/ABCDEF}    # abc1234zip123abc
#              |-|
echo "v2 = $v2"         # abc1234zip1234ABCDEF
#               |----|

echo

#  ----------------------------------------------------
#  必须匹配字符串的开头或结尾,
#+ 否则是不会产生替换结果的.
#  ----------------------------------------------------
v3=${v0/#123/000}       # 匹配, 但不是在开头.
echo "v3 = $v3"         # abc1234zip1234abc
# 不会发生替换.
v4=${v0/%123/000}       # 匹配, 但不是在结尾.
echo "v4 = $v4"         # abc1234zip1234abc
# 不会发生替换.

exit 0
${!varprefix*}, ${!varprefix@} 匹配所有之前声明过的，并且以 varprefix 开头的变量。
xyz23=whatever
xyz24=

a=${!xyz*}      # 展开所有以"xyz"开头的, 并且之前声明过的变量名.
echo "a = $a"   # a = xyz23 xyz24
a=${!xyz@}      # 同上.
echo "a = $a"   # a = xyz23 xyz24

# Bash, 版本 2.04, 添加了这个功能. 
```

## 四、指定变量的类型: 使用 declare 或者 typeset

### 1.declare/typeset 选项

```
选项 | 说明
-----|----
-r | 只读
-i | 整型
-a | 数组
-f | 函数
-x | export
```

### 2.使用 declare 来指定变量的类型

```
#!/bin/bash

func1 ()
{
  echo This is a function.
}

declare -f        # 列出前面定义的所有函数.

echo

declare -i var1   # var1 是个整型变量.
var1=2367
echo "var1 declared as $var1"
var1=var1+1       # 整型变量的声明并不需要使用'let'命令.
echo "var1 incremented by 1 is $var1."
# 尝试修改一个已经声明为整型变量的值.
echo "Attempting to change var1 to floating point value, 2367.1."
var1=2367.1       # 产生错误信息, 并且变量并没有被修改.
echo "var1 is still $var1"

echo

declare -r var2=13.36         # 'declare'允许设置变量的属性,
#+ 同时给变量赋值.
echo "var2 declared as $var2" # 试图修改只读变量的值.
var2=13.37                    # 产生错误消息, 并且从脚本退出.

echo "var2 is still $var2"    # 将不会执行到这行.

exit 0                        # 脚本也不会从此处退出. 
```

## 五、变量的间接引用

### 1.间接引用

假设一个变量的值是第二个变量的名字。如果 a=letter*of*alphabet 并且 letter*of*alphabet=z， 它被称为间接引用。我们能够通过引用变量 a 来获得 z，它使用 eval var1=\$$var2 这种不平常的形式。

```
#!/bin/bash
# 访问一个以另一个变量内容作为名字的变量的值.(译者注: 怎么译都不顺)

a=letter_of_alphabet   # 变量"a"的值是另一个变量的名字.
letter_of_alphabet=z

echo

# 直接引用.
echo "a = $a"          # a = letter_of_alphabet

# 间接引用.
eval a=\$$a
echo "Now a = $a"      # 现在 a = z

echo

# 现在, 让我们试试修改第二个引用的值.

t=table_cell_3
table_cell_3=24
echo "\"table_cell_3\" = $table_cell_3"            # "table_cell_3" = 24
echo -n "dereferenced \"t\" = "; eval echo \$$t    # 解引用 "t" = 24
# 在这个简单的例子中, 下面的表达式也能正常工作么(为什么?).
#         eval t=\$$t; echo "\"t\" = $t"

echo

t=table_cell_3
NEW_VAL=387
table_cell_3=$NEW_VAL
echo "Changing value of \"table_cell_3\" to $NEW_VAL."
echo "\"table_cell_3\" now $table_cell_3"
echo -n "dereferenced \"t\" now "; eval echo \$$t
# "eval" 带有两个参数 "echo" 和 "\$$t" (与$table_cell_3 等价)

echo

exit 0 
```

### 2.传递一个间接引用给 awk

```
#!/bin/bash

#  这是"求文件中指定列的总和"脚本的另一个版本,
#+ 这个脚本可以计算目标文件中指定列(此列的内容必须都是数字)的所有数字的和.
#  这个脚本使用了间接引用.

ARGS=2
E_WRONGARGS=65

if [ $# -ne "$ARGS" ] # 检查命令行参数的个数是否合适.
then
echo "Usage: `basename $0` filename column-number"
exit $E_WRONGARGS
fi

filename=$1
column_number=$2

#===== 在这一行上边的部分, 与原始脚本是相同的 =====#

# 多行的 awk 脚本的调用方法为: awk ' ..... '

# awk 脚本开始.
# ------------------------------------------------
awk "

{ total += \$${column_number} # 间接引用
}
END {
  print total
}

" "$filename"
# ------------------------------------------------
# awk 脚本结束.

exit 0 
```

## 六、双圆括号结构

### 1.简介

与 let 命令很相似，((...)) 结构允许算术扩展和赋值。 如，a=$(( 5 + 3 ))，将把变量“a”设为“5 + 3”，或者 8。

### 2.C 语言风格的变量操作

```
#!/bin/bash
# 使用((...))结构操作一个变量, C 语言风格的变量操作.

echo

(( a = 23 ))  # C 语言风格的变量赋值, "="两边允许有空格.
echo "a (initial value) = $a"

(( a++ ))     # C 语言风格的后置自加.
echo "a (after a++) = $a"

(( a-- ))     # C 语言风格的后置自减.
echo "a (after a--) = $a"

(( ++a ))     # C 语言风格的前置自加.
echo "a (after ++a) = $a"

(( --a ))     # C 语言风格的前置自减.
echo "a (after --a) = $a"

echo

########################################################
#  注意: 就像在 C 语言中一样, 前置或后置自减操作
#+ 会产生一些不同的副作用.

n=1; let --n &amp;&amp; echo "True" || echo "False"  # False
n=1; let n-- &amp;&amp; echo "True" || echo "False"  # True

echo

(( t = a<45?7:11 ))   # C 语言风格的三元操作.
echo "If a < 45, then t = 7, else t = 11."
echo "t = $t "        # Yes!

echo

# 你也可以参考一些 "for" 和 "while" 循环中使用((...))结构的例子.

exit 0 
```

## 一、循环

### 1.for 循环

```
#!/bin/bash
# 列出所有的行星名称. (译者注: 现在的太阳系行星已经有了变化^_^)

for planet in Mercury Venus Earth Mars Jupiter Saturn Uranus Neptune Pluto
do
echo $planet  # 每个行星都被单独打印在一行上.
done

echo

for planet in "Mercury Venus Earth Mars Jupiter Saturn Uranus Neptune Pluto"
# 所有的行星名称都打印在同一行上.
# 整个'list'都被双引号封成了一个变量.
do
echo $planet
done

exit 0 
```

### 2.while 循环

```
#!/bin/bash

var0=0
LIMIT=10

while [ "$var0" -lt "$LIMIT" ]
do
  echo -n "$var0 "        # -n 将会阻止产生新行.
  #                     ^           空格, 数字之间的分隔.

  var0=`expr $var0 + 1`   # var0=$(($var0+1))  也可以.
  # var0=$((var0 + 1)) 也可以.
  # let "var0 += 1"    也可以.
done                      # 使用其他的方法也行.

echo

exit 0 
```

### 3.until 循环

```
#!/bin/bash

END_CONDITION=end

until [ "$var1" = "$END_CONDITION" ]
# 在循环的顶部进行条件判断.
do
  echo "Input variable #1 "
  echo "($END_CONDITION to exit)"
  read var1
  echo "variable #1 = $var1"
  echo
done  

exit 0 
```

## 二、嵌套循环

### 1.嵌套循环

```
#!/bin/bash
# nested-loop.sh: 嵌套的"for"循环.

outer=1             # 设置外部循环计数.

# 开始外部循环.
for a in 1 2 3 4 5
do
  echo "Pass $outer in outer loop."
  echo "---------------------"
  inner=1           # 重置内部循环计数.

  # ===============================================
  # 开始内部循环.
  for b in 1 2 3 4 5
  do
    echo "Pass $inner in inner loop."
    let "inner+=1"  # 增加内部循环计数.
  done
# 内部循环结束.
# ===============================================

  let "outer+=1"    # 增加外部循环的计数.
  echo              # 每次外部循环之间的间隔.
done
# 外部循环结束.

exit 0 
```

**注意：** 为了能够更友好的让自己或者别人阅读和维护你的代码，应在代码中使用合理的缩进

## 三、循环控制

### 1.break

```
#!/bin/bash

LIMIT=19  # 上限

echo
echo "Printing Numbers 1 through 20 (but not 3 and 11)."

a=0

while [ "$a" -le "$LIMIT" ]
do
  a=$(($a+1))

  if [ "$a" -gt 2 ]
  then
    break  # 将会跳出整个循环.
  fi

  echo -n "$a "
done

echo; echo; echo

exit 0 
```

### 2.continue

```
#!/bin/bash

LIMIT=19  # 上限

echo
echo "Printing Numbers 1 through 20 (but not 3 and 11)."

a=0

while [ $a -le "$LIMIT" ]
do
  a=$(($a+1))

  if [ "$a" -eq 3 ] || [ "$a" -eq 11 ]  # 除了 3 和 11.
  then
    continue      # 跳过本次循环剩余的语句.
  fi

  echo -n "$a "   # 在$a 等于 3 和 11 的时候，这句将不会执行.
done

echo; echo

echo Printing Numbers 1 through 20, but something happens after 2.
exit 0 
```

## 四、测试与分支

### 1.case (in) / esac

在 shell 中的 case 结构与 C/C++中的 switch 结构是相同的。它允许通过判断来选择代码块中多 条路径中的一条。它的作用和多个 if/then/else 语句的作用相同，是它们的简化结构，特别 适用于创建菜单。case 块以 esac（case 的反向拼写）结尾。

```
#!/bin/bash
# 测试字符串范围.

echo; echo "Hit a key, then hit return."
read Keypress

case "$Keypress" in
[[:lower:]]   ) echo "Lowercase letter";;
[[:upper:]]   ) echo "Uppercase letter";;
[0-9]         ) echo "Digit";;
*             ) echo "Punctuation, whitespace, or other";;
esac      #  允许字符串的范围出现在[中括号]中,
#+ 或者出现在 POSIX 风格的[[双中括号中.

#  在这个例子的第一个版本中,
#+ 测试大写和小写字符串的工作使用的是
#+ [a-z] 和 [A-Z].
#  这种用法在某些特定场合的或某些 Linux 发行版中不能够正常工作.
#  POSIX 的风格更具可移植性.

exit 0 
```

### 2.select

select 结构是建立菜单的另一种工具，这种结构是从 ksh 中引入的。

```
#!/bin/bash

PS3='Choose your favorite vegetable: ' # 设置提示符字串.

echo

select vegetable in "beans" "carrots" "potatoes" "onions" "rutabagas"
do
  echo
  echo "Your favorite veggie is $vegetable."
  echo "Yuck!"
  echo
  break  # 如果这里没有 'break' 会发生什么?
done

exit 0 
```