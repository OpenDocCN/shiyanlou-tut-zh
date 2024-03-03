# 第 3 节 数据类型（一）

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

本节介绍 PHP 的数据类型。php 有 8 种原始数据类型，分别是 boolean（布尔型）、integer（整型）、float（浮点型，也称作 double)、string（字符串）、array（数组）、object（对象）、resource 和 NULL。boolean 型只有 true 和 false 两种值，不区分大小写，整型的最大值根据机器而定，PHP*INT*MAX 保存的就是最大值，php 中不分有无符号，float 能通常表示 14 位有效数字，string 可以用单引号、双引号、heredoc 和 nowdoc 这四种方式来赋值。

## 二、简介

PHP 支持 8 种原始数据类型。

四种标量类型：

*   boolean（布尔型）
*   integer（整型）
*   float（浮点型，也称作 double)
*   string（字符串）

两种复合类型：

*   array（数组）
*   object（对象）

最后是两种特殊类型：

*   resource（资源）
*   NULL（无类型）

**Note: 如果想查看某个表达式的值和类型，用 var_dump() 函数。**

如果只是想得到一个易读懂的类型的表达方式用于调试，用 gettype() 函数。要查看某个类型，不要用 gettype()，而用 is_type 函数。以下是一些范例：

```php
<?php
$a_bool = TRUE;   // a boolean
$a_str  = "foo";  // a string
$a_str2 = 'foo';  // a string
$an_int = 12;     // an integer

echo gettype($a_bool); // prints out:  boolean
echo gettype($a_str);  // prints out:  string

// If this is an integer, increment it by four
if (is_int($an_int)) {
    $an_int += 4;
}

// If $bool is a string, print it out
// (does not print out anything)
if (is_string($a_bool)) {
    echo "String: $a_bool";
}
?> 
```

如果要将一个变量强制转换为某类型，可以对其使用强制转换或者 settype() 函数。

注意变量根据其当时的类型在特定场合下会表现出不同的值。更多信息见类型转换的判别。此外，还可以参考 PHP 类型比较表看不同类型相互比较的例子。

## 三、Boolean 布尔类型

这是最简单的类型。boolean 表达了真值，可以为 TRUE 或 FALSE。

### 1\. 语法

要指定一个布尔值，使用关键字 TRUE 或 FALSE。两个都不区分大小写。

```php
<?php
$foo = True; // assign the value TRUE to $foo
?> 
```

通常运算符所返回的 boolean 值结果会被传递给控制流程。

```php
<?php
// == 是一个操作符，它检测两个变量是否相等，并返回一个布尔值
if ($action == "show_version") {
    echo "The version is 1.23";
}

// 这样做是不必要的...
if ($show_separators == TRUE) {
    echo "<hr>\n";
}

// ...因为可以使用下面这种简单的方式：
if ($show_separators) {
    echo "<hr>\n";
}
?> 
```

**Warning -1 和其它非零值（不论正负）一样，被认为是 TRUE！**

## 四、Integer 整型

一个 integer 是集合 ℤ = {..., -2, -1, 0, 1, 2, ...} 中的一个数。

### 1\. 语法

整型值可以使用十进制，十六进制，八进制或二进制表示，前面可以加上可选的符号（- 或者 +）。

二进制表达的 integer 自 PHP 5.4.0 起可用。

要使用八进制表达，数字前必须加上 0（零）。要使用十六进制表达，数字前必须加上 0x。要使用二进制表达，数字前必须加上 0b。 **Example 1 整数文字表达**

```php
<?php
$a = 1234; // 十进制数
$a = -123; // 负数
$a = 0123; // 八进制数 (等于十进制 83)
$a = 0x1A; // 十六进制数 (等于十进制 26)
?> 
```

整型数的字长和平台有关，尽管通常最大值是大约二十亿（32 位有符号）。64 位平台下的最大值通常是大约 9E18。PHP 不支持无符号整数。Integer 值的字长可以用常量 PHP*INT*SIZE 来表示，自 PHP 4.4.0 和 PHP 5.0.5 后，最大值可以用常量 PHP*INT*MAX 来表示。 **Warning：如果向八进制数传递了一个非法数字（即 8 或 9），则后面其余数字会被忽略。**

**Example 2 八进制数的怪事**

```php
<?php
var_dump(01090); // 八进制 010 = 十进制 8
?> 
```

### 2\. 整数溢出

如果给定的一个数超出了 integer 的范围，将会被解释为 float。同样如果执行的运算结果超出了 integer 范围，也会返回 float。 **Example 3 32 位系统下的整数溢出**

```php
<?php
$large_number = 2147483647;
var_dump($large_number);                     // int(2147483647)

$large_number = 2147483648;
var_dump($large_number);                     // float(2147483648)

$million = 1000000;
$large_number =  50000 * $million;
var_dump($large_number);                     // float(50000000000)
?> 
```

**Example 4 64 位系统下的整数溢出**

```php
<?php
$large_number = 9223372036854775807;
var_dump($large_number);                     // int(9223372036854775807)

$large_number = 9223372036854775808;
var_dump($large_number);                     // float(9.2233720368548E+18)

$million = 1000000;
$large_number =  50000000000000 * $million;
var_dump($large_number);                     // float(5.0E+19)
?> 
```

PHP 中没有整除的运算符。1/2 产生出 float 0.5。值可以舍弃小数部分强制转换为 integer，或者使用 round() 函数可以更好地进行四舍五入。

```php
<?php
var_dump(25/7);         // float(3.5714285714286) 
var_dump((int) (25/7)); // int(3)
var_dump(round(25/7));  // float(4) 
?> 
```

## 五、Float 浮点型

浮点型（也叫浮点数 float，双精度数 double 或实数 real）可以用以下任一语法定义：

```php
<?php
$a = 1.234; 
$b = 1.2e3; 
$c = 7E-10;
?> 
```

浮点数的字长和平台相关，尽管通常最大值是 1.8e308 并具有 14 位十进制数字的精度（64 位 IEEE 格式）。 **Warning 浮点数的精度有限。尽管取决于系统，PHP 通常使用 IEEE 754 双精度格式，则由于取整而导致的最大相对误差为 1.11e-16。非基本数学运算可能会给出更大误差，并且要考虑到进行复合运算时的误差传递。 此外，以十进制能够精确表示的有理数如 0.1 或 0.7，无论有多少尾数都不能被内部所使用的二进制精确表示，因此不能在不丢失一点点精度的情况下转换为二进制的格式。这就会造成混乱的结果：例如，floor((0.1+0.7)*10) 通常会返回 7 而不是预期中的 8，因为该结果内部的表示其实是类似 7.9999999999999991118...。**

## 六、String 字符串

一个字符串 string 就是由一系列的字符组成，其中每个字符等同于一个字节。这意味着 PHP 只能支持 256 的字符集，因此不支持 Unicode 。

### 1\. 语法

一个字符串可以用 4 种方式表达： 单引号 、双引号、heredoc 语法结构、nowdoc 语法结构（自 PHP 5.3.0 起）

#### （1）单引号

定义一个字符串的最简单的方法是用单引号把它包围起来（字符 '）。

要表达一个单引号自身，需在它的前面加个反斜线（\）来转义。要表达一个反斜线自身，则用两个反斜线（\\）。其它任何方式的反斜线都会被当成反斜线本身：也就是说如果想使用其它转义序列例如 \r 或者 \n，并不代表任何特殊含义，就单纯是这两个字符本身。

```php
<?php
echo 'this is a simple string';

// 可以录入多行
echo 'You can also have embedded newlines in 
strings this way as it is
okay to do';

// 输出： Arnold once said: "I'll be back"
echo 'Arnold once said: "I\'ll be back"';

// 输出： You deleted C:\*.*?
echo 'You deleted C:\\*.*?';

// 输出： You deleted C:\*.*?
echo 'You deleted C:\*.*?';

// 输出： This will not expand: \n a newline
echo 'This will not expand: \n a newline';

// 输出： Variables do not $expand $either
echo 'Variables do not $expand $either';
?> 
```

#### （2）双引号

如果字符串是包围在双引号（"）中， PHP 将对一些特殊的字符进行解析： ![Alt text](img/dd37f1fc84d5cd6e8572ca949c22dc7c.jpg) 和单引号字符串一样，转义任何其它字符都会导致反斜线被显示出来。

#### （3）Heredoc 结构

第三种表达字符串的方法是用 heredoc 句法结构：<<<。在该运算符之后要提供一个标识符，然后换行。接下来是字符串 string 本身，最后要用前面定义的标识符作为结束标志。

结束时所引用的标识符必须在该行的第一列，而且，标识符的命名也要像其它标签一样遵守 PHP 的规则：只能包含字母、数字和下划线，并且必须以字母和下划线作为开头。

** Heredoc 结构的字符串示例**

```php
<?php
$str = <<<EOD
Example of string
spanning multiple lines
using heredoc syntax.
EOD;

/* 含有变量的更复杂示例 */
class foo
{
    var $foo;
    var $bar;

    function foo()
    {
        $this->foo = 'Foo';
        $this->bar = array('Bar1', 'Bar2', 'Bar3');
    }
}

$foo = new foo();
$name = 'MyName';

echo <<<EOT
My name is "$name". I am printing some $foo->foo.
Now, I am printing some {$foo->bar[1]}.
This should print a capital 'A': \x41
EOT;
?> 
```

以上例程会输出：

```php
My name is "MyName". I am printing some Foo.
Now, I am printing some Bar2.
This should print a capital 'A': A 
```

也可以把 Heredoc 结构用在函数参数中来传递数据：

```php
<?php
var_dump(array(<<<EOD
foobar!
EOD
));
?> 
```

使用 Heredoc 结构来初始化静态值：

```php
<?php
// 静态变量
function foo()
{
    static $bar = <<<LABEL
Nothing in here...
LABEL;
}

// 类的常量、属性
class foo
{
    const BAR = <<<FOOBAR
Constant example
FOOBAR;

    public $baz = <<<FOOBAR
Property example
FOOBAR;
}
?> 
```

**在 heredoc 结构中使用双引号**

```php
<?php
echo <<<"FOOBAR"
Hello World!
FOOBAR;
?> 
```

#### （4）Nowdoc 结构

就象 heredoc 结构类似于双引号字符串，Nowdoc 结构是类似于单引号字符串的。Nowdoc 结构很象 heredoc 结构，但是 nowdoc 中不进行解析操作。这种结构很适合用于嵌入 PHP 代码或其它大段文本而无需对其中的特殊字符进行转义。 一个 nowdoc 结构也用和 heredocs 结构一样的标记 <<<， 但是跟在后面的标识符要用单引号括起来，即 <<<'EOT'。Heredoc 结构的所有规则也同样适用于 nowdoc 结构，尤其是结束标识符的规则。

**Nowdoc 结构字符串示例**

```php
<?php
$str = <<<'EOD'
Example of string
spanning multiple lines
using nowdoc syntax.
EOD;

/* 含有变量的更复杂的示例 */
class foo
{
    public $foo;
    public $bar;

    function foo()
    {
        $this->foo = 'Foo';
        $this->bar = array('Bar1', 'Bar2', 'Bar3');
    }
}

$foo = new foo();
$name = 'MyName';

echo <<<'EOT'
My name is "$name". I am printing some $foo->foo.
Now, I am printing some {$foo->bar[1]}.
This should not print a capital 'A': \x41
EOT;
?> 
```

以上例程会输出：

```php
My name is "$name". I am printing some $foo->foo.
Now, I am printing some {$foo->bar[1]}.
This should not print a capital 'A': \x41 
```

不象 heredoc 结构，nowdoc 结构可以用在任意的静态数据环境中，最典型的示例是用来初始化类的属性或常量：

```php
<?php
class foo {
    public $bar = <<<'EOT'
bar
EOT;
}
?> 
```

#### （5）变量解析

当字符串用双引号或 heredoc 结构定义时，其中的变量将会被解析。

这里共有两种语法规则：一种简单规则，一种复杂规则。简单的语法规则是最常用和最方便的，它可以用最少的代码在一个 string 中嵌入一个变量，一个 array 的值，或一个 object 的属性。

复杂规则语法的显著标记是用花括号包围的表达式。

当 PHP 解析器遇到一个美元符号（$）时，它会和其它很多解析器一样，去组合尽量多的标识以形成一个合法的变量名。可以用花括号来明确变量名的界线。

```php
<?php
$juice = "apple";

echo "He drank some $juice juice.".PHP_EOL;
// Invalid. "s" is a valid character for a variable name, but the variable is $juice.
echo "He drank some juice made of $juices.";
?> 
```

以上例程会输出：

```php
He drank some apple juice.
He drank some juice made of . 
```

类似的，一个 array 索引或一个 object 属性也可被解析。数组索引要用方括号（]）来表示索引结束的边际，对象属性则是和上述的变量规则相同。

```php
<?php
$juices = array("apple", "orange", "koolaid1" => "purple");

echo "He drank some $juices[0] juice.".PHP_EOL;
echo "He drank some $juices[1] juice.".PHP_EOL;
echo "He drank some juice made of $juice[0]s.".PHP_EOL; // Won't work
echo "He drank some $juices[koolaid1] juice.".PHP_EOL;

class people {
    public $john = "John Smith";
    public $jane = "Jane Smith";
    public $robert = "Robert Paulsen";

    public $smith = "Smith";
}

$people = new people();

echo "$people->john drank some $juices[0] juice.".PHP_EOL;
echo "$people->john then said hello to $people->jane.".PHP_EOL;
echo "$people->john's wife greeted $people->robert.".PHP_EOL;
echo "$people->robert greeted the two $people->smiths."; // Won't work
?> 
```

以上例程会输出：

```php
He drank some apple juice.
He drank some orange juice.
He drank some juice made of s.
He drank some purple juice.
John Smith drank some apple juice.
John Smith then said hello to Jane Smith.
John Smith's wife greeted Robert Paulsen.
Robert Paulsen greeted the two . 
```

复杂语法不是因为其语法复杂而得名，而是因为它可以使用复杂的表达式。

任何具有 string 表达的标量变量，数组单元或对象属性都可使用此语法。只需简单地像在 string 以外的地方那样写出表达式，然后用花括号 { 和 } 把它括起来即可。由于 { 无法被转义，只有 \$ 紧挨着 { 时才会被识别。可以用 {\$ 来表达 {$。下面的示例可以更好的解释：

```php
<?php
// 显示所有错误
error_reporting(E_ALL);

$great = 'fantastic';

// 无效，输出: This is { fantastic}
echo "This is { $great}";

// 有效，输出： This is fantastic
echo "This is {$great}";
echo "This is ${great}";

// 有效
echo "This square is {$square->width}00 centimeters broad."; 

// 有效，只有通过花括号语法才能正确解析带引号的键名
echo "This works: {$arr['key']}";

// 有效
echo "This works: {$arr[4][3]}";

// 这是错误的表达式，因为就象 $foo[bar] 的格式在字符串以外也是错的一样。
// 换句话说，只有在 PHP 能找到常量 foo 的前提下才会正常工作；这里会产生一个
// E_NOTICE (undefined constant) 级别的错误。
echo "This is wrong: {$arr[foo][3]}"; 

// 有效，当在字符串中使用多重数组时，一定要用括号将它括起来
echo "This works: {$arr['foo'][3]}";

// 有效
echo "This works: " . $arr['foo'][3];

echo "This works too: {$obj->values[3]->name}";

echo "This is the value of the var named $name: {${$name}}";

echo "This is the value of the var named by the return value of getName(): {${getName()}}";

echo "This is the value of the var named by the return value of \$object->getName(): {${$object->getName()}}";

// 无效，输出： This is the return value of getName(): {getName()}
echo "This is the return value of getName(): {getName()}";
?> 
```

也可以在字符串中用此语法通过变量来调用类的属性。

```php
<?php
class foo {
    var $bar = 'I am bar.';
}

$foo = new foo();
$bar = 'bar';
$baz = array('foo', 'bar', 'baz', 'quux');
echo "{$foo->$bar}\n";
echo "{$foo->$baz[1]}\n";
?> 
```

以上例程会输出:

```php
I am bar.
I am bar. 
```

## 七、作业练习

请试试整型跟字符串、布尔型、浮点型做加法运算，看看有什么结果，并想想为什么。

实验中有任何问题欢迎到[实验楼问答](http://www.shiyanlou.com/questions)提问。