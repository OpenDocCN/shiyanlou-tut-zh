# 第 7 节 Perl 关联数组

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

### 4\. 课程来源

本节讲了 Perl 的关联数组，用 `%` 定义关联数组变量，它类似其他编程语言中的 map。

## 二、关联数组（哈希表）

### 1\. 定义

Perl 中关联数组相当于另一种数组，可以用任意简单变量值来访问其元素，这种数组叫做关联数组，也叫哈希表。为了区分关联数组与普通的数组变量，Perl 使用 % 作为其首字符，而数组变量以 @ 打头。与其它变量名一样，% 后的第一个字符必须为字母，后续字符可以为字母、数字或下划线。

### 2\. 访问关联数组的元素

关联数组的下标可以为任何简单标量值，访问单个元素时以 $ 符号打头，下标用大括号围起来。例如：

```pl
$fruit{"bananas"}
$number{3.14159}
$integer{-7} 
```

简单变量也可作为下标，如：

```pl
$fruit{$my_fruit} 
```

### 3\. 增加删除元素

创建一个关联数组元素最简单的方法是赋值，如语句 $fruit{"bananas"} = 1； 把 1 赋给关联数组 %fruit 下标为 bananas 的元素，如果该元素不存在，则被创建，如果数组 %fruit 从未使用过，也被创建。 这一特性使得关联数组很容易用于计数。删除元素的方法是用内嵌函数 delete，如欲删除上述元素，则：

```pl
delete ($fruit{"bananas"}); 
```

如下例：

```pl
$ echo "good good study, day day up" > file1
$ vim add.pl 
```

输入代码

```pl
#! /usr/bin/perl

open(INFILE, "file1")
while ($inputline = <INFILE>) {
    while ($inputline =~ /\b[a-zA-Z]\S+/g) {
        $word = $&;
        $word =~ s/[;.,:-]$//; # remove punctuation
        $wordlist{$word} += 1;
  }
}
print ("Capitalized words and number of occurrences:\n");
foreach $capword (keys(%wordlist)) {
    print ("$capword: $wordlist{$capword}\n");
} 
```

本程序用关联数组 %wordlist 跟踪首字母大写的单词，下标就用单词本身，元素值为该单词出现的次数。第 11 行使用了内嵌函数 keys()。这个函数返回关联数组的下标列表，foreach 语句就用此列表循环。 注：关联数组总是随机存贮的，因此当你用 keys() 访问其所有元素时，不保证元素以任何顺序出现，特别值得一提的是，它们不会以被创建的顺序出现。

输入结束 ESC 退出输入模式，保存退出当前文件

```pl
:wq 
```

改变文件执行权限

```pl
$ chmod +x add.pl 
```

执行文件测试结果

```pl
$ ./add.pl
Capitalized words and number of occurrences:
day:2
up:1
study:1
good:2 
```

### 4\. 创建关联数组

可以用单个赋值语句创建关联数组，如：

```pl
%fruit = ("apples",17,"bananas",9,"oranges","none"); 
```

此语句创建的关联数组含有下面三个元素：

*   下标为 apples 的元素，值为 17
*   下标为 bananas 的元素，值为 9
*   下标为 oranges 的元素，值为 none

注：用列表给关联数组赋值时，Perl5 允许使用"=>"或","来分隔下标与值，用"=>"可读性更好些，上面语句等效于：

```pl
%fruit = ("apples"=>17,"bananas"=>9,"oranges"=>"none"); 
```

### 5\. 从数组复制到关联数组

与列表一样，也可以通过数组变量创建关联数组，当然，其元素数目应该为偶数，如：

```pl
@fruit = ("apples",17,"bananas",9,"oranges","none");
%fruit = @fruit; 
```

反之，可以把关联数组赋给数组变量，如：

```pl
%fruit = ("grapes",11,"lemons",27);
@fruit = %fruit; 
```

注意，此语句中元素次序未定义，那么数组变量@fruit 可能为("grapes",11,"lemons",27)或("lemons",27,"grapes",11)。 关联数组变量之间可以直接赋值，如：`%fruit2 = %fruit1;`还可以把数组变量同时赋给一些简单变量和一个关联数组变量，如：

```pl
($var1, $var2, %myarray) = @list; 
```

此语句把@list 的第一个元素赋给$var1，第二个赋给$var2，其余的赋给%myarray。 最后，关联数组可以通过返回值为列表的内嵌函数或用户定义的子程序来创建，下例中把 split()函数的返回值--一个列表--赋给一个关联数组变量。

```pl
$ vim array2hash.pl 
```

输入代码

```pl
#! /usr/bin/perl

$inputline = <STDIN>;
$inputline =~ s/^\s+|\s+\n$//g;
%fruit = split(/\s+/, $inputline);
print ("Number of bananas: $fruit{\"bananas\"}\n"); 
```

测试

```pl
$ chmod +x array2hash.pl
$ ./array2hash.pl
bananas 2 apple 3
Number of bananas:2 
```

### 6\. 列出数组的索引和值

keys() 函数可以返回关联数组下表的列表，如：

```pl
%fruit = ("apples", 9,"bananas", 23,"cherries", 11);
@fruitsubs = keys(%fruits); 
```

each() 函数每次返回一个双元素的列表，其第一个元素为下标，第二个元素为相应的值，最后返回一个空列表。 注意：千万不要在 each() 循环中添加或删除元素，否则会产生不可预料的后果。

## 三、实验部分

使用关联数组实现单链表。

```pl
$ vim linklist.pl 
```

输入代码

```pl
#! /usr/bin/perl

# initialize list to empty
$header = "";
while ($line = <STDIN>) {
    # remove leading and trailing spaces
    $line =~ s/^\s+|\s+$//g;
    @words = split(/\s+/, $line);
    foreach $word (@words) {
        # remove closing punctuation, if any
        $word =~ s/[.,;:-]$//;
        # convert all words to lower case
        $word =~ tr/A-Z/a-z/;
        &add_word_to_list($word);
    }
}
&print_list;

sub add_word_to_list {
    local($word) = @_;
    local($pointer);

    # if list is empty, add first item
    if ($header eq "") {
        $header = $word;
        $wordlist{$word} = "";
        return;
    }
    # if word identical to first element in list,
    # do nothing
    return if ($header eq $word);
    # see whether word should be the new
    # first word in the list
    if ($header gt $word) {
        $wordlist{$word} = $header;
        $header = $word;
        return;
    }
    # find place where word belongs
    $pointer = $header;
    while ($wordlist{$pointer} ne "" && $wordlist{$pointer} lt $word){
        $pointer = $wordlist{$pointer};
    }
    # if word already seen, do nothing
    return if ($word eq $wordlist{$pointer});
    $wordlist{$word} = $wordlist{$pointer};
    $wordlist{$pointer} = $word;
}

sub print_list {
    local ($pointer);
    print ("Words in this file:\n");
    $pointer = $header;
    while ($pointer ne "") {
        print ("$pointer\n");
        $pointer = $wordlist{$pointer};
    }
} 
```

输入结束 ESC 退出当前编辑模式，保存退出

```pl
:wq 
```

改变文件可执行权限

```pl
$ chmod +x linklist.pl 
```

执行当前文件测试

```pl
$ ./linklist.pl 
```

## 四、作业练习

请实现一个功能，输入一张成绩表（包含姓名，课程名和成绩），输出每门课的最高成绩。

## 五、参考文档

> * 本实验课程基于 flamephoenix 翻译制作的版本教程版本。感谢原作者[flamephoenix]（http://flamephoenix.126.com）