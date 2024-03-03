# 第 8 节 PHP 控制结构（一）

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

本节介绍 PHP 的语句类型。任何 PHP 脚本都是由一系列语句构成的。一条语句可以是一个赋值语句，一个函数调用，一个循环，一个条件语句或者甚至是一个什么也不做的语句（空语句）。语句通常以分号结束。此外，还可以用花括号将一组语句封装成一个语句组。语句组本身可以当作是一行语句。例如本节讲了 if 判断语句，它有 if、if...else、if...elseif...else 三种结构，循环语句有 for、while、do-while、foreach 这三种形式，在循环中可以使用 break 跳出循环，此外它们还有替代写法。

## 二、if 语句

if 结构是很多语言包括 PHP 在内最重要的特性之一，它允许按照条件执行代码片段。PHP 的 if 结构和 C 语言相似：

```php
<?php
if ($a > $b)
  echo "a is bigger than b";
?> 
```

如果 \$a 大于 $b，则以上例子将显示 a is bigger than b

## 三、else 语句

经常需要在满足某个条件时执行一条语句，而在不满足该条件时执行其它语句，这正是 else 的功能。else 延伸了 if 语句，可以在 if 语句中的表达式的值为 FALSE 时执行语句。例如以下代码在 \$a 大于 $b 时显示 a is bigger than b，反之则显示 a is NOT bigger than b：

```php
<?php
if ($a > $b) {
  echo "a is greater than b";
} else {
  echo "a is NOT greater than b";
}
?> 
```

## 四、elseif/else if 语句

elseif，和此名称暗示的一样，是 if 和 else 的组合。和 else 一样，它延伸了 if 语句，可以在原来的 if 表达式值为 FALSE 时执行不同语句。但是和 else 不一样的是，它仅在 elseif 的条件表达式值为 TRUE 时执行语句。例如以下代码将根据条件分别显示 a is bigger than b，a equal to b 或者 a is smaller than b：

```php
<?php
if ($a > $b) {
    echo "a is bigger than b";
} elseif ($a == $b) {
    echo "a is equal to b";
} else {
    echo "a is smaller than b";
}
?> 
```

在同一个 if 语句中可以有多个 elseif 部分，其中第一个表达式值为 TRUE（如果有的话）的 elseif 部分将会执行。在 PHP 中，也可以写成“else if”（两个单词），它和“elseif”（一个单词）的行为完全一样。句法分析的含义有少许区别（如果你熟悉 C 语言的话，与之行为相同），但是底线是两者会产生完全一样的行为。

elseif 的语句仅在之前的 if 和所有之前 elseif 的表达式值为 FALSE，并且当前的 elseif 表达式值为 TRUE 时执行。

*Note: 必须要注意的是 elseif 与 else if 只有在类似上例中使用花括号的情况下才认为是完全相同。如果用冒号来定义 if/elseif 条件，那就不能用两个单词的 else if，否则 PHP 会产生解析错误。*

```php
<?php

/* 不正确的使用方法： */
if($a > $b):
    echo $a." is greater than ".$b;
else if($a == $b): // 将无法编译
    echo "The above line causes a parse error.";
endif;

/* 正确的使用方法： */
if($a > $b):
    echo $a." is greater than ".$b;
elseif($a == $b): // 注意使用了一个单词的 elseif
    echo $a." equals ".$b;
else:
    echo $a." is neither greater than or equal to ".$b;
endif;

?> 
```

## 五、流程控制的替代语法

PHP 提供了一些流程控制的替代语法，包括 if，while，for，foreach 和 switch。替代语法的基本形式是把左花括号（{）换成冒号（:），把右花括号（}）分别换成 endif;，endwhile;，endfor;，endforeach; 以及 endswitch;

```php
<?php if ($a == 5): ?>
A is equal to 5
<?php endif; ?> 
```

在上面的例子中，HTML 内容“A is equal to 5”用替代语法嵌套在 if 语句中。该 HTML 的内容仅在 $a 等于 5 时显示。

替代语法同样可以用在 else 和 elseif 中。下面是一个包括 elseif 和 else 的 if 结构用替代语法格式写的例子：

```php
<?php
if ($a == 5):
    echo "a equals 5";
    echo "...";
elseif ($a == 6):
    echo "a equals 6";
    echo "!!!";
else:
    echo "a is neither 5 nor 6";
endif;
?> 
```

*Note: 不支持在同一个控制块内混合使用两种语法。*

## 六、while 语句

while 循环是 PHP 中最简单的循环类型。它和 C 语言中的 while 表现地一样。while 语句的基本格式是(该代码为语法格式，不是代码案例，无需敲打该代码)：

```php
while (expr)
    {statement} 
```

while 语句的含意很简单，它告诉 PHP 只要 while 表达式的值为 TRUE 就重复执行嵌套中的循环语句。表达式的值在每次开始循环时检查，所以即使这个值在循环语句中改变了，语句也不会停止执行，直到本次循环结束。有时候如果 while 表达式的值一开始就是 FALSE，则循环语句一次都不会执行。

和 if 语句一样，可以在 while 循环中用花括号括起一个语句组，或者用替代语法：

```php
while (expr):
    statement
    ...
endwhile; 
```

下面两个例子完全一样，都显示数字 1 到 10：

```php
<?php
/* example 1 */

$i = 1;
while ($i <= 10) {
    echo $i++;  /* the printed value would be
                    $i before the increment
                    (post-increment) */
}

/* example 2 */

$i = 1;
while ($i <= 10):
    print $i;
    $i++;
endwhile;
?> 
```

## 七、do-while 语句

do-while 循环和 while 循环非常相似，区别在于表达式的值是在每次循环结束时检查而不是开始时。和一般的 while 循环主要的区别是 do-while 的循环语句保证会执行一次（表达式的真值在每次循环结束后检查），然而在一般的 while 循环中就不一定了（表达式真值在循环开始时检查，如果一开始就为 FALSE 则整个循环立即终止）。

do-while 循环只有一种语法：

```php
<?php
$i = 0;
do {
   echo $i;
} while ($i > 0);
?> 
```

以上循环将正好运行一次，因为经过第一次循环后，当检查表达式的真值时，其值为 FALSE（$i 不大于 0）而导致循环终止。 资深的 C 语言用户可能熟悉另一种不同的 do-while 循环用法，把语句放在 do-while(0) 之中，在循环内部用 break 语句来结束执行循环。以下代码片段示范了此方法：

```php
<?php
do {
    if ($i < 5) {
        echo "i is not big enough";
        break;
    }
    $i *= $factor;
    if ($i < $minimum_limit) {
        break;
    }
    echo "i is ok";

    /* process i */

} while(0);
?> 
```

如果还不能立刻理解也不用担心。即使不用此“特性”也照样可以写出强大的代码来。自 PHP 5.3.0 起，还可以使用 goto 来跳出循环。

## 八、for 语句

for 循环是 PHP 中最复杂的循环结构。它的行为和 C 语言的相似。 for 循环的语法是(该代码为语法格式，不是代码案例，无需敲打该代码)：

```php
for (expr1; expr2; expr3)
    {statement} 
```

第一个表达式（expr1）在循环开始前无条件求值（并执行）一次。

expr2 在每次循环开始前求值。如果值为 TRUE，则继续循环，执行嵌套的循环语句。如果值为 FALSE，则终止循环。

expr3 在每次循环之后被求值（并执行）。

每个表达式都可以为空或包括逗号分隔的多个表达式。表达式 expr2 中，所有用逗号分隔的表达式都会计算，但只取最后一个结果。expr2 为空意味着将无限循环下去（和 C 一样，PHP 暗中认为其值为 TRUE）。这可能不像想象中那样没有用，因为经常会希望用有条件的 break 语句来结束循环而不是用 for 的表达式真值判断。

考虑以下的例子，它们都显示数字 1 到 10：

```php
<?php
/* example 1 */

for ($i = 1; $i <= 10; $i++) {
    echo $i;
}

/* example 2 */

for ($i = 1; ; $i++) {
    if ($i > 10) {
        break;
    }
    echo $i;
}

/* example 3 */

$i = 1;
for (;;) {
    if ($i > 10) {
        break;
    }
    echo $i;
    $i++;
}

/* example 4 */

for ($i = 1, $j = 0; $i <= 10; $j += $i, print $i, $i++);
?> 
```

当然，第一个例子看上去最简洁（或者有人认为是第四个），但用户可能会发现在 for 循环中用空的表达式在很多场合下会很方便。 PHP 也支持用冒号的 for 循环的替代语法(该代码为语法格式，不是代码案例，无需敲打该代码)。

```php
for (expr1; expr2; expr3):
    statement;
    ...
endfor; 
```

有时经常需要像下面这样例子一样对数组进行遍历：

```php
<?php
/*
 * 此数组将在遍历的过程中改变其中某些单元的值
 */
$people = Array(
        Array('name' => 'Kalle', 'salt' => 856412), 
        Array('name' => 'Pierre', 'salt' => 215863)
        );

for($i = 0; $i < sizeof($people); ++$i)
{
    $people[$i]['salt'] = rand(000000, 999999);
}
?> 
```

以上代码可能执行很慢，因为每次循环时都要计算一遍数组的长度。由于数组的长度始终不变，可以用一个中间变量来储存数组长度以优化而不是不停调用 count()：

```php
<?php
$people = Array(
        Array('name' => 'Kalle', 'salt' => 856412), 
        Array('name' => 'Pierre', 'salt' => 215863)
        );

for($i = 0, $size = sizeof($people); $i < $size; ++$i)
{
    $people[$i]['salt'] = rand(000000, 999999);
}
?> 
```

## 九、foreach 语句

foreach 语法结构提供了遍历数组的简单方式。foreach 仅能够应用于数组和对象，如果尝试应用于其他数据类型的变量，或者未初始化的变量将发出错误信息。有两种语法(该代码为语法格式，不是代码案例，无需敲打该代码)：

```php
foreach (array_expression as $value)
    statement
foreach (array_expression as $key => $value)
    statement 
```

*   第一种格式遍历给定的 array_expression 数组。每次循环中，当前单元的值被赋给 \$value 并且数组内部的指针向前移一步（因此下一次循环中将会得到下一个单元）。

*   第二种格式做同样的事，只除了当前单元的键名也会在每次循环中被赋给变量 \$key。

还能够自定义遍历对象。

可以很容易地通过在 $value 之前加上 & 来修改数组的元素。此方法将以引用赋值而不是拷贝一个值。

```php
<?php
$arr = array(1, 2, 3, 4);
foreach ($arr as &$value) {
    $value = $value * 2;
}
// $arr is now array(2, 4, 6, 8)
unset($value); // 最后取消掉引用
?> 
```

$value 的引用仅在被遍历的数组可以被引用时才可用（例如是个变量）。以下代码则无法运行：

```php
<?php
foreach (array(1, 2, 3, 4) as &$value) {
    $value = $value * 2;
}

?> 
```

*Warning 数组最后一个元素的 $value 引用在 foreach 循环之后仍会保留。建议使用 unset() 来将其销毁。*

## 十、break 语句

break 结束当前 for，foreach，while，do-while 或者 switch 结构的执行。 break 可以接受一个可选的数字参数来决定跳出几重循环。

```php
<?php
$arr = array('one', 'two', 'three', 'four', 'stop', 'five');
while (list (, $val) = each($arr)) {
    if ($val == 'stop') {
        break;    /* You could also write 'break 1;' here. */
    }
    echo "$val<br />\n";
}

/* 使用可选参数 */

$i = 0;
while (++$i) {
    switch ($i) {
    case 5:
        echo "At 5<br />\n";
        break 1;  /* 只退出 switch. */
    case 10:
        echo "At 10; quitting<br />\n";
        break 2;  /* 退出 switch 和 while 循环 */
    default:
        break;
    }
}
?> 
```

## 十一、作业练习

请使用 for、while 和 do-while 这三种结构各输出九九乘法表。

实验中有任何问题欢迎到[实验楼问答](http://www.shiyanlou.com/questions)提问。