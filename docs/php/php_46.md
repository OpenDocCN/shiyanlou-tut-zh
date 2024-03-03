# 第 9 节 PHP 控制结构（二）

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou

### 2\. 环境介绍

本实验环境采用 Ubuntu Linux 桌面环境，实验中会用到桌面上的程序：

1.命令行终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令

2.Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可

3.gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)

4.gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

### 3\. 环境使用

使用编辑器输入实验所需的代码及文件，使用命令行终端运行所需命令进行操作。

“实验记录”页面可以在“我的主页”中查看，每次实验的截图及笔记，以及有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您在实验楼学习的真实性证明。

### 4\. 课程介绍

本节介绍继续 PHP 的其他控制结构语句。包括 continue、switch、declare、include 等等知识点。

## 二、continue 语句

continue 在循环结构用来跳过本次循环中剩余的代码并在条件求值为真时开始执行下一次循环。

**Note: 注意在 PHP 中 switch 语句被认为是可以使用 continue 的一种循环结构。**

continue 接受一个可选的数字参数来决定跳过几重循环到循环结尾。默认值是 1，即跳到当前循环末尾。

```php
<?php
while (list ($key, $value) = each($arr)) {
    if (!($key % 2)) { // skip odd members
        continue;
    }
    do_something_odd($value);
}

$i = 0;
while ($i++ < 5) {
    echo "Outer<br />\n";
    while (1) {
        echo "Middle<br />\n";
        while (1) {
            echo "Inner<br />\n";
            continue 3;
        }
        echo "This never gets output.<br />\n";
    }
    echo "Neither does this.<br />\n";
}
?> 
```

省略 continue 后面的分号会导致混淆。以下例子示意了不应该这样做。

```php
<?php
  for ($i = 0; $i < 5; ++$i) {
      if ($i == 2)
          continue
      print "$i\n";
  }
?> 
```

希望得到的结果是：

```php
0
1
3
4 
```

可实际的输出是：

```php
2 
```

因为整个 continue print "\$i\n"; 被当做单一的表达式而求值，所以 print 函数只有在 $i == 2 为真时才被调用（print 的值被当成了上述的可选数字参数而传递给了 continue）。

## 三、switch 语句

switch 语句类似于具有同一个表达式的一系列 if 语句。很多场合下需要把同一个变量（或表达式）与很多不同的值比较，并根据它等于哪个值来执行不同的代码。这正是 switch 语句的用途。 **Note: 注意和其它语言不同，continue 语句作用到 switch 上的作用类似于 break。如果在循环中有一个 switch 并希望 continue 到外层循环中的下一轮循环，用 continue 2。** 下面两个例子使用两种不同方法实现同样的事，一个用一系列的 if 和 elseif 语句，另一个用 switch 语句：

**Example 1 switch 结构**

```php
<?php
if ($i == 0) {
    echo "i equals 0";
} elseif ($i == 1) {
    echo "i equals 1";
} elseif ($i == 2) {
    echo "i equals 2";
}

switch ($i) {
    case 0:
        echo "i equals 0";
        break;
    case 1:
        echo "i equals 1";
        break;
    case 2:
        echo "i equals 2";
        break;
}
?> 
```

**Example2 switch 结构可以用字符串**

```php
<?php
switch ($i) {
case "apple":
    echo "i is apple";
    break;
case "bar":
    echo "i is bar";
    break;
case "cake":
    echo "i is cake";
    break;
}
?> 
```

为避免错误，理解 switch 是怎样执行的非常重要。switch 语句一行接一行地执行（实际上是语句接语句）。开始时没有代码被执行。仅当一个 case 语句中的值和 switch 表达式的值匹配时 PHP 才开始执行语句，直到 switch 的程序段结束或者遇到第一个 break 语句为止。如果不在 case 的语句段最后写上 break 的话，PHP 将继续执行下一个 case 中的语句段。例如：

```php
<?php
switch ($i) {
    case 0:
        echo "i equals 0";
    case 1:
        echo "i equals 1";
    case 2:
        echo "i equals 2";
}
?> 
```

这里如果 \$i 等于 0，PHP 将执行所有的 echo 语句！如果 \$i 等于 1，PHP 将执行后面两条 echo 语句。只有当 $i 等于 2 时，才会得到“预期”的结果——只显示“i equals 2”。所以，别忘了 break 语句就很重要（即使在某些情况下故意想避免提供它们时）。

在 switch 语句中条件只求值一次并用来和每个 case 语句比较。在 elseif 语句中条件会再次求值。如果条件比一个简单的比较要复杂得多或者在一个很多次的循环中，那么用 switch 语句可能会快一些。

在一个 case 中的语句也可以为空，这样只不过将控制转移到了下一个 case 中的语句。

```php
<?php
switch ($i) {
    case 0:
    case 1:
    case 2:
        echo "i is less than 3 but not negative";
        break;
    case 3:
        echo "i is 3";
}
?> 
```

一个 case 的特例是 default。它匹配了任何和其它 case 都不匹配的情况。例如：

```php
<?php
switch ($i) {
    case 0:
        echo "i equals 0";
        break;
    case 1:
        echo "i equals 1";
        break;
    case 2:
        echo "i equals 2";
        break;
    default:
        echo "i is not equal to 0, 1 or 2";
}
?> 
```

case 表达式可以是任何求值为简单类型的表达式，即整型或浮点数以及字符串。不能用数组或对象，除非它们被解除引用成为简单类型。

switch 支持替代语法的流程控制。更多信息见控制结构(一)的替代语法一节。

```php
<?php
switch ($i):
    case 0:
        echo "i equals 0";
        break;
    case 1:
        echo "i equals 1";
        break;
    case 2:
        echo "i equals 2";
        break;
    default:
        echo "i is not equal to 0, 1 or 2";
endswitch;
?> 
```

允许使用分号代替 case 语句后的冒号，例如：

```php
<?php
switch($beer)
{
    case 'tuborg';
    case 'carlsberg';
    case 'heineken';
        echo 'Good choice';
    break;
    default;
        echo 'Please make a new selection...';
    break;
}
?> 
```

## 四、declare 语句

declare 结构用来设定一段代码的执行指令。declare 的语法和其它流程控制结构相似(该代码为语法格式，不是代码案例，无需敲打该代码)：

```php
declare (directive)
    statement 
```

directive 部分允许设定 declare 代码段的行为。目前只认识两个指令：ticks（更多信息见下面 ticks 指令）以及 encoding（更多信息见下面 encoding 指令）。 **Note: encoding 是 PHP 5.3.0 新增指令。** declare 代码段中的 statement 部分将被执行——怎样执行以及执行中有什么副作用出现取决于 directive 中设定的指令。

declare 结构也可用于全局范围，影响到其后的所有代码（但如果有 declare 结构的文件被其它文件包含，则对包含它的父文件不起作用）。

```php
<?php
// these are the same:

// you can use this:
declare(ticks=1) {
    // entire script here
}

// or you can use this:
declare(ticks=1);
// entire script here
?> 
```

### 1\. Ticks

Tick（时钟周期）是一个在 declare 代码段中解释器每执行 N 条可计时的低级语句就会发生的事件。N 的值是在 declare 中的 directive 部分用 ticks=N 来指定的。

不是所有语句都可计时。通常条件表达式和参数表达式都不可计时。

在每个 tick 中出现的事件是由 register*tick*function() 来指定的。更多细节见下面的例子。注意每个 tick 中可以出现多个事件。 **Example 1 Tick 的用法示例**

```php
<?php

declare(ticks=1);

// A function called on each tick event
function tick_handler()
{
    echo "tick_handler() called\n";
}

register_tick_function('tick_handler');

$a = 1;

if ($a > 0) {
    $a += 2;
    print($a);
}

?> 
```

**Example 2 Ticks 的用法示例**

```php
<?php

function tick_handler()
{
  echo "tick_handler() called\n";
}

$a = 1;
tick_handler();

if ($a > 0) {
    $a += 2;
    tick_handler();
    print($a);
    tick_handler();
}
tick_handler();

?> 
```

### 2\. Encoding

可以用 encoding 指令来对每段脚本指定其编码方式。 **Example3 对脚本指定编码方式**

```php
<?php
declare(encoding='ISO-8859-1');
// code here
?> 
```

## 五、return 语句

如果在一个函数中调用 return 语句，将立即结束此函数的执行并将它的参数作为函数的值返回。 **Note: 注意既然 return 是语言结构而不是函数，因此其参数没有必要用括号将其括起来。通常都不用括号，实际上也应该不用，这样可以降低 PHP 的负担。**

```php
a.php
<?php
include 'b.php';

foo();
?>

 b.php
<?php
return;

 function foo() {
      echo 'foo';
 }
?> 
```

执行 a.php 将会输出“foo”

## 六、include 语句

include 语句包含并运行指定文件。

包含文件先按参数给出的路径寻找，如果没有给出目录（只有文件名）时则按照 include*path 指定的目录寻找。如果在 include*path 下没找到该文件则 include 最后才在调用脚本文件所在的目录和当前工作目录下寻找。如果最后仍未找到文件则 include 结构会发出一条警告；这一点和 require 不同，后者会发出一个致命错误。

当一个文件被包含时，其中所包含的代码继承了 include 所在行的变量范围。从该处开始，调用文件在该行处可用的任何变量在被调用的文件中也都可用。不过所有在包含文件中定义的函数和类都具有全局作用域。 **Example 1 基本的 include 例子**

```php
<?php

$color = 'green';
$fruit = 'apple';

?>

test.php
<?php

echo "A $color $fruit"; // A

include 'vars.php';

echo "A $color $fruit"; // A green apple

?> 
```

**控制结构（一）和（二）这两部分包含了流程控制的常用控制结构，其他少数控制结构可在自己学习过程中遇到后查找相关资料**

## 七、练习

请用 switch 语句实现一个成绩评级函数，输入 0-100 的分数，如果在 0-59 输出 D，60-70 输出 C，71-84 输出 B，85-100 输出 A。

实验中有任何问题欢迎到[实验楼问答](http://www.shiyanlou.com/questions)提问。