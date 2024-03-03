# 第 10 节 PHP 函数

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

本节介绍 PHP 函数的相关知识。可以使用 function 关键字定义，PHP 中还支持可变函数、引用函数和匿名函数等。

## 二、用户自定义函数

一个函数可由以下的语法来定义： **Example 1 展示函数用途的伪代码:**

```php
<?php
function foo($arg_1, $arg_2, ..., $arg_n)
{
    echo "Example function.\n";
    return $retval;
}
?> 
```

任何有效的 PHP 代码都有可能出现在函数内部，甚至包括其它函数和类定义。 函数名和 PHP 中的其它标识符命名规则相同。有效的函数名以字母或下划线打头，后面跟字母，数字或下划线。

除非是下面两个例子中的情况，函数必须在其调用之前定义。 **Example 2 条件函数:**

```php
<?php

$makefoo = true;

/* 我们不能在处调用 foo()函数，
   因为它还不存在，但可以调用 bar()函数。*/

bar();

if ($makefoo) {
  function foo()
  {
    echo "I don't exist until program execution reaches me.\n";
  }
}

/* 现在我们可以安全调用函数 foo()了，
   因为 $makefoo 值为真 */

if ($makefoo) foo();

function bar()
{
  echo "I exist immediately upon program start.\n";
}

?> 
```

**Example 3 函数中的函数:**

```php
<?php
function foo()
{
  function bar()
  {
    echo "I don't exist until foo() is called.\n";
  }
}

/* 现在还不能调用 bar()函数，因为它还不存在 */

foo();

/* 现在可以调用 bar()函数了，因为 foo()函数
   的执行使得 bar()函数变为已定义的函数 */

bar();

?> 
```

PHP 中的所有函数和类都具有全局作用域，可以在内部定义外部调用，反之亦然。

PHP 不支持函数重载，也不可能取消定义或者重定义已声明的函数。

**Note: 函数名是大小写无关的，不过在调用函数的时候，通常使用其在定义时相同的形式。**

**Example 4 递归函数:**

```php
<?php
function recursion($a)
{
    if ($a < 20) {
        echo "$a\n";
        recursion($a + 1);
    }
}
?> 
```

## 三、函数的参数

通过参数列表可以传递信息到函数，即以逗号作为分隔符的表达式列表。 PHP 支持按值传递参数（默认），通过引用传递参数以及默认参数。也支持可变数量的参数； **Example 1 向函数传递数组：**

```php
<?php
function takes_array($input)
{
    echo "$input[0] + $input[1] = ", $input[0]+$input[1];
}
?> 
```

### 1\. 通过引用传递参数

缺省情况下，函数参数通过值传递（因而即使在函数内部改变参数的值，它并不会改变函数外部的值）。如果希望允许函数修改它的参数值，必须通过引用传递参数。 如果想要函数的一个参数总是通过引用传递，可以在函数定义中该参数的前面预先加上符号 &： **Example 2 用引用传递函数参数:**

```php
<?php
function add_some_extra(&$string)
{
    $string .= 'and something extra.';
}
$str = 'This is a string, ';
add_some_extra($str);
echo $str;    // outputs 'This is a string, and something extra.'
?> 
```

### 2\. 默认参数的值

函数可以定义 C++ 风格的标量参数默认值，如下： **Example 3 在函数中使用默认参数:**

```php
<?php
function makecoffee($type = "cappuccino")
{
    return "Making a cup of $type.\n";
}
echo makecoffee();
echo makecoffee(null);
echo makecoffee("espresso");
?> 
```

以上例程会输出：

```php
Making a cup of cappuccino.
Making a cup of .
Making a cup of espresso. 
```

## 四、返回值

值通过使用可选的返回语句返回。可以返回包括数组和对象的任意类型。返回语句会立即中止函数的运行，并且将控制权交回调用该函数的代码行。 **Example 1 return 的使用:**

```php
<?php
function square($num)
{
    return $num * $num;
}
echo square(4);   // outputs '16'.
?> 
```

函数不能返回多个值，但可以通过返回一个数组来得到类似的效果。

**Example 2 返回一个数组以得到多个返回值:**

```php
<?php
function small_numbers()
{
    return array (0, 1, 2);
}
list ($zero, $one, $two) = small_numbers();
?> 
```

从函数返回一个引用，必须在函数声明和指派返回值给一个变量时都使用引用操作符 & ： **Example 3 从函数返回一个引用**

```php
<?php
function &returns_reference()
{
    return $someref;
}

$newref =& returns_reference();
?> 
```

## 五、可变函数

PHP 支持可变函数的概念。这意味着如果一个变量名后有圆括号，PHP 将寻找与变量的值同名的函数，并且尝试执行它。可变函数可以用来实现包括回调函数，函数表在内的一些用途。

变量函数不能用于语言结构，例如 echo， print， unset()， isset()， empty()， include， require 以及类似的语句。需要使用自己的包装函数来将这些结构用作变量函数。 **Example 1 可变函数示例：**

```php
<?php
function foo() {
    echo "In foo()<br />\n";
}

function bar($arg = '') {
    echo "In bar(); argument was '$arg'.<br />\n";
}

// 使用 echo 的包装函数
function echoit($string)
{
    echo $string;
}

$func = 'foo';
$func();        // This calls foo()

$func = 'bar';
$func('test');  // This calls bar()

$func = 'echoit';
$func('test');  // This calls echoit()
?> 
```

还可以利用可变函数的特性来调用一个对象的方法。

**Example 2 可变方法范例:**

```php
<?php
class Foo
{
    function Variable()
    {
        $name = 'Bar';
        $this->$name(); // This calls the Bar() method
    }

    function Bar()
    {
        echo "This is Bar";
    }
}

$foo = new Foo();
$funcname = "Variable";
$foo->$funcname();   // This calls $foo->Variable()

?> 
```

## 六、匿名函数

**Example 1 Anonymous function 示例**

```php
<?php
echo preg_replace_callback('~-([a-z])~', function ($match) {
    return strtoupper($match[1]);
}, 'hello-world');
// 输出 helloWorld
?> 
```

闭包函数也可以作为变量的值来使用。PHP 会自动把表达式转换成内置类 Closure 的 对象实例。把一个 closure 对象赋值给一个变量的方式与普通变量赋值的语法是一样的，最后也要加上分号。 **Example 2 匿名函数变量赋值示例:**

```php
<?php
$greet = function($name)
{
    printf("Hello %s\r\n", $name);
};

$greet('World');
$greet('PHP');
?> 
```

## 七、作业练习

掌握返回引用的函数和返回值的函数的区别。

实验中有任何问题欢迎到[实验楼问答](http://www.shiyanlou.com/questions)提问。