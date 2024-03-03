# 第 5 节 PHP 变量

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

本节介绍 PHP 的变量。PHP 中的变量用一个美元符号后面跟变量名来表示，一个有效的变量名由字母或者下划线开头，后面跟上任意数量的字母，数字，或者下划线，而且区分大小写，想实现如 C 中的全局变量，可以使用 global 关键字或者$GLOBALS，static 关键字可以声明静态变量，PHP 也支持可变变量和引用变量。

## 二、基础

PHP 中的变量用一个美元符号后面跟变量名来表示。变量名是区分大小写的。 变量名与 PHP 中其它的标签一样遵循相同的规则。一个有效的变量名由字母或者下划线开头，后面跟上任意数量的字母，数字，或者下划线。

```php
<?php
$var = 'Bob';
$Var = 'Joe';
echo "$var, $Var";      // 输出 "Bob, Joe"

$4site = 'not yet';     // 非法变量名；以数字开头
$_4site = 'not yet';    // 合法变量名；以下划线开头
$i 站点 is = 'mansikka';  // 合法变量名；可以用中文
?> 
```

变量默认总是传值赋值。那也就是说，当将一个表达式的值赋予一个变量时，整个原始表达式的值被赋值到目标变量。这意味着，例如，当一个变量的值赋予另外一个变量时，改变其中一个变量的值，将不会影响到另外一个变量。

PHP 也提供了另外一种方式给变量赋值：引用赋值。这意味着新的变量简单的引用（换言之，“成为其别名” 或者 “指向”）了原始变量。改动新的变量将影响到原始变量，反之亦然。

使用引用赋值，简单地将一个 & 符号加到将要赋值的变量前（源变量）。例如，下列代码片断将输出“My name is Bob”两次：

```php
<?php
$foo = 'Bob';              // 将 'Bob' 赋给 $foo
$bar = &$foo;              // 通过 $bar 引用 $foo
$bar = "My name is $bar";  // 修改 $bar 变量
echo $bar;
echo $foo;                 // $foo 的值也被修改
?> 
```

有一点重要事项必须指出，那就是只有有名字的变量才可以引用赋值。

```php
<?php
$foo = 25;
$bar = &$foo;      // 合法的赋值
$bar = &(24 * 7);  // 非法; 引用没有名字的表达式

function test()
{
   return 25;
}

$bar = &test();    // 非法
?> 
```

虽然在 PHP 中并不需要初始化变量，但对变量进行初始化是个好习惯。未初始化的变量具有其类型的默认值 - 布尔类型的变量默认值是 FALSE，整形和浮点型变量默认值是零，字符串型变量（例如用于 echo 中）默认值是空字符串以及数组变量的默认值是空数组。

## 三、预定义变量

PHP 提供了大量的预定义变量。由于许多变量依赖于运行的服务器的版本和设置，及其它因素，所以并没有详细的说明文档。一些预定义变量在 PHP 以命令行形式运行时并不生效。 从 PHP 4.1.0 开始，PHP 提供了一套附加的预定数组，这些数组变量包含了来自 web 服务器（如果可用），运行环境，和用户输入的数据。这些数组非常特别，它们在全局范围内自动生效，例如，在任何范围内自动生效。因此通常被称为自动全局变量（autoglobals）或者超全局变量（superglobals）。（PHP 中没有用户自定义超全局变量的机制。）超全局变量罗列于下文中。 **Note: 可变变量 超级全局变量不能被用作函数或类方法中的可变变量。**

## 四、变量范围

变量的范围即它定义的上下文背景（也就是它的生效范围）。大部分的 PHP 变量只有一个单独的范围。这个单独的范围跨度同样包含了 include 和 require 引入的文件。例如：

```php
<?php
$a = 1;
include 'b.inc';
?> 
```

这里变量 $a 将会在包含文件 b.inc 中生效。但是，在用户自定义函数中，一个局部函数范围将被引入。任何用于函数内部的变量按缺省情况将被限制在局部函数范围内。例如：

```php
<?php
$a = 1; /* global scope */

function Test()
{
    echo $a; /* reference to local scope variable */
}

Test();
?> 
```

这个脚本不会有任何输出，因为 echo 语句引用了一个局部版本的变量 $a，而且在这个范围内，它并没有被赋值。你可能注意到 PHP 的全局变量和 C 语言有一点点不同，在 C 语言中，全局变量在函数中自动生效，除非被局部变量覆盖。这可能引起一些问题，有些人可能不小心就改变了一个全局变量。PHP 中全局变量在函数中使用时必须声明为 global。

### 1\. global 关键字

使用 global

```php
<?php
$a = 1;
$b = 2;

function Sum()
{
    global $a, $b;

    $b = $a + $b;
}

Sum();
echo $b;
?> 
```

以上脚本的输出将是“3”。在函数中声明了全局变量 $a 和 $b 之后，对任一变量的所有引用都会指向其全局版本。对于一个函数能够声明的全局变量的最大个数，PHP 没有限制。

在全局范围内访问变量的第二个办法，是用特殊的 PHP 自定义 $GLOBALS 数组。前面的例子可以写成：

```php
<?php
$a = 1;
$b = 2;

function Sum()
{
    $GLOBALS['b'] = $GLOBALS['a'] + $GLOBALS['b'];
}

Sum();
echo $b;
?> 
```

\$GLOBALS 是一个关联数组，每一个变量为一个元素，键名对应变量名，值对应变量的内容。\$GLOBALS 之所以在全局范围内存在，是因为 $GLOBALS 是一个超全局变量。以下范例显示了超全局变量的用处：

```php
<?php
function test_global()
{
    // 大多数的预定义变量并不 "super"，它们需要用 'global' 关键字来使它们在函数的本地区域中有效。
    global $HTTP_POST_VARS;

    echo $HTTP_POST_VARS['name'];

    // Superglobals 在任何范围内都有效，它们并不需要 'global' 声明。Superglobals 是在 PHP 4.1.0 引入的。
    echo $_POST['name'];
}
?> 
```

### 2\. 使用静态变量

变量范围的另一个重要特性是静态变量（static variable）。静态变量仅在局部函数域中存在，但当程序执行离开此作用域时，其值并不丢失。看看下面的例子：

```php
<?php
function Test()
{
    $a = 0;
    echo $a;
    $a++;
}
?> 
```

本函数没什么用处，因为每次调用时都会将 \$a 的值设为 0 并输出 0。将变量加一的 \$a++ 没有作用，因为一旦退出本函数则变量 \$a 就不存在了。要写一个不会丢失本次计数值的计数函数，要将变量 \$a 定义为静态的：

```php
<?php
function test()
{
    static $a = 0;
    echo $a;
    $a++;
}
?> 
```

现在，变量 \$a 仅在第一次调用 test() 函数时被初始化，之后每次调用 test() 函数都会输出 $a 的值并加一。

## 五、可变变量

有时候使用可变变量名是很方便的。就是说，一个变量的变量名可以动态的设置和使用。一个普通的变量通过声明来设置，例如：

```php
<?php
$a = 'hello';
?> 
```

一个可变变量获取了一个普通变量的值作为这个可变变量的变量名。在上面的例子中 hello 使用了两个美元符号（$）以后，就可以作为一个可变变量的变量了。例如：

```php
<?php
$$a = 'world';
?> 
```

这时，两个变量都被定义了：\$a 的内容是“hello”并且 \$hello 的内容是“world”。因此，以下语句：

```php
<?php
echo "$a ${$a}";
?> 
```

与以下语句输出完全相同的结果：

```php
<?php
echo "$a $hello";
?> 
```

它们都会输出：

```php
hello world 
```

## 六、作业练习

请通过查阅资料掌握几个常用的预定义变量。

实验中有任何问题欢迎到[实验楼问答](http://www.shiyanlou.com/questions)提问。