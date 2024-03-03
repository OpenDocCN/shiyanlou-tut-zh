# 第 6 节 Perl 子程序

## 一、实验说明介绍

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

本节讲了 Perl 中的函数，用 sub 来定义，它跟其他编程语言中的函数非常类似。

## 二、子程序

### 1\. 定义

子程序即执行一个特殊任务的一段分离的代码，它可以使代码减少重复。在 Perl 中，子程序可以出现在程序的任何地方。定义方法为：

```pl
sub subroutine{
    statements;
} 
```

### 2\. 调用

在 Perl 中调用一个子程序的方法有如下几种：

#### （1）用&调用

```pl
&subname;
...
sub subname{
...
} 
```

#### （2）先定义后调用的方式，可以省略&符号

```pl
sub subname{
    ...
}
...
subname; 
```

#### （3）前向引用，先定义子程序名，后面再定义子程序体

```pl
sub subname;
...
subname;
...
sub subname{
    ...
} 
```

#### （4）用 do 调用

```pl
do sub(arg1,arg2,...);等价于&sub(arg1,arg2,...); 
```

## 三、返回值

默认情况下，perl 中子程序中最后一个语句的值将用作返回值。语句 return(retvalue); 也可以退出子程序并返回值 retvalue，retvalue 也可以为列表。

## 四、局部变量

子程序中局部变量的定义有两种方法： my 和 local。其区别是：my 定义的变量只在该子程序中存在；而 local 定义的变量不存在于主程序中，但存在于该子程序调用的子程序中(在 Perl 中没有 my)。定义时可以给其赋值，如：

```pl
my($scalar)=43;
local(@array)=(1,2,3); 
```

## 五、子程序参数传递

### 1\. 形式

```pl
&sub1($number1,$number2,$number3);
...
sub sub1{
    my($numbeer1,$number2,$number3)=@_;
    ...
} 
```

### 2\. 传送数组

```pl
&addlist(@mylist);
&addlist("14","6","11");
&addlist($value1,@sublist,$value2);
...
sub addlist{
    my(@list) = @_;
    ...
} 
```

参数为数组时，子程序只将它赋值给一个数组变量。如

```pl
sub twolists{
    my(@list1,@list2)=@_;
} 
```

上面的 @list2 必然为空，但简单变量和数组变量可以同时传递：

```pl
&twoargs(4,@mylist);   #4 将赋给$scalar,@mylist 赋给@list
&twoargs(@mylist);   #@mylist 的第一个元素赋给$scalar,其余的元素赋给@list
...
sub twoargs{
    my($scalar,@list) = @_;
    ...
} 
```

## 六、用别名传递数组参数

### 1\. 用上面的 &sub(@array) 方法

把数组 @array 的数据拷贝到子程序中的变量 @_ 中，当数组很大时，将会花费较多的资源和时间，而用别名传递将不做这些工作。如：

```pl
@myarray = (1,2,3,4);
&mysub(*myarray);
sub mysub(){
    my(*subarray) = @_;
} 
```

### 2\. 类 C 语言指针传递

在定义数组的别名之后，如果有同名的简单变量，则对该变量也是起作用的。如：

```pl
$foo = 26;
@foo = ("here's","a","list");
&testsub(*foo);
...
sub testsub{ 
    local(*printarray)=@_;
    ...
    $printarray=61;
} 
```

### 3\. 用别名的方法可以传递多个数组

```pl
@array1=(1,2,3);
@array2=(4,5,6);
&two_array_sub(*array1,*array2);
sub two_array_sub{
    my(*subarray1,*subarray2) = @_;
} 
```

## 七、程序实例

array1 存放了一系列乱序的整数，写一个子程序执行冒泡排序算法将 array1 排序后输出。

```pl
$ vim sortarray.pl 
```

输入代码

```pl
#! /usr/bin/perl
my @array1=(5,7,4,3,9,12,8,2,1);
bubble_sort(\@array1,scalar @array1);
print "@array1\n";
sub bubble_sort{
    my $array_ref = shift||"";
    my $array_length = shift||"";
    for(my $i=0;$i<$array_length;$i++){
       for(my $j=$i+1;$j<$array_length;$j++){
            if($array_ref->[$i]>$array_ref->[$j]){
                my $temp = $array_ref->[$i];
                $array_ref[$i]=$array_ref[$j];
                $array_ref[$j]=$temp;
            }
        }
     }
    return $array_ref;
} 
```

输入结束 ESC 退出编辑模式，保存退出

```pl
:wq 
```

改变文件的执行权限

```pl
$ chmod +x sortarray.pl 
```

执行文件

```pl
$ ./sortarray.pl
1 2 3 4 5 7 8 9 12 
```

## 八、作业练习

请编写一个快速排序的子程序。

## 九、参考文档

> * 本实验课程基于 flamephoenix 翻译制作的版本教程版本。感谢原作者[flamephoenix]（http://flamephoenix.126.com）