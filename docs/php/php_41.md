# 第 4 节 数据类型（二）

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

本节介绍 PHP 的其他数据类型。包括数组、对象、Resource 以及 NULL 等等数据类型，也涵盖了不同数据类型之间的转换等知识点。

## 二、Array 数组

PHP 中的数组实际上是一个有序映射。映射是一种把 values 关联到 keys 的类型。此类型在很多方面做了优化，因此可以把它当成真正的数组，或列表（向量），散列表（是映射的一种实现），字典，集合，栈，队列以及更多可能性。由于数组元素的值也可以是另一个数组，树形结构和多维数组也是允许的。

### 1\. 语法

定义数组 array() 可以用 array() 语言结构来新建一个数组。它接受任意数量用逗号分隔的 键（key） => 值（value）对。

```php
array(  key =>  value
     , ...
     )
// 键（key）可是是一个整数 integer 或字符串 string
// 值（value）可以是任意类型的值 
```

最后一个数组单元之后的逗号可以省略。通常用于单行数组定义中，例如常用 array(1, 2) 而不是 array(1, 2, )。对多行数组定义通常保留最后一个逗号，这样要添加一个新单元时更方便。 5.4 起可以使用短数组定义语法，用 [] 替代 array()。

**Example 1 一个简单数组**

```php
 <?php
$array = array(
    "foo" => "bar",
    "bar" => "foo",
);

// 自 PHP 5.4 起
$array = [
    "foo" => "bar",
    "bar" => "foo",
];
?> 
```

key 可以是 integer 或者 string。value 可以是任意类型。

此外 key 会有如下的强制转换：

*   包含有合法整型值的字符串会被转换为整型。例如键名 "8" 实际会被储存为 8。但是 "08" 则不会强制转换，因为其不是一个合法的十进制数值。
*   浮点数也会被转换为整型，意味着其小数部分会被舍去。例如键名 8.7 实际会被储存为 8。
*   布尔值也会被转换成整型。即键名 true 实际会被储存为 1 而键名 false 会被储存为 0。
*   Null 会被转换为空字符串，即键名 null 实际会被储存为 ""。
*   数组和对象不能被用为键名。坚持这么做会导致警告：Illegal offset type。

如果在数组定义中多个单元都使用了同一个键名，则只使用了最后一个，之前的都被覆盖了。 **Example 2 类型强制与覆盖示例**

```php
<?php
$array = array(
    1    => "a",
    "1"  => "b",
    1.5  => "c",
    true => "d",
);
var_dump($array);
?> 
```

以上例程会输出：

```php
array(1) {
  [1]=>
  string(1) "d"
} 
```

上例中所有的键名都被强制转换为 1，则每一个新单元都会覆盖前一个的值，最后剩下的只有一个 "d"。

PHP 数组可以同时含有 integer 和 string 类型的键名，因为 PHP 实际并不区分索引数组和关联数组。 如果对给出的值没有指定键名，则取当前最大的整数索引值，而新的键名将是该值加一。如果指定的键名已经有了值，则该值会被覆盖。

```php
<?php
$array = array(
    "foo" => "bar",
    "bar" => "foo",
    100   => -100,
    -100  => 100,
);
var_dump($array);
?> 
```

以上例程会输出：

```php
array(4) {
  ["foo"]=>
  string(3) "bar"
  ["bar"]=>
  string(3) "foo"
  [100]=>
  int(-100)
  [-100]=>
  int(100)
} 
```

key 为可选项。如果未指定，PHP 将自动使用之前用过的最大 integer 键名加上 1 作为新的键名。 **Example 4 没有键名的索引数组**

```php
<?php
$array = array("foo", "bar", "hallo", "world");
var_dump($array);
?> 
```

以上例程会输出：

```php
array(4) {
  [0]=>
  string(3) "foo"
  [1]=>
  string(3) "bar"
  [2]=>
  string(5) "hallo"
  [3]=>
  string(5) "world"
} 
```

还可以只对某些单元指定键名而对其它的空置：

```php
<?php
$array = array(
         "a",
         "b",
    6 => "c",
         "d",
);
var_dump($array);
?> 
```

以上例程会输出：

```php
array(4) {
  [0]=>
  string(1) "a"
  [1]=>
  string(1) "b"
  [6]=>
  string(1) "c"
  [7]=>
  string(1) "d"
} 
```

可以看到最后一个值 "d" 被自动赋予了键名 7。这是由于之前最大的整数键名是 6。 **用方括号语法访问数组单元**

```php
<?php
$array = array(
    "foo" => "bar",
    42    => 24,
    "multi" => array(
         "dimensional" => array(
             "array" => "foo"
         )
    )
);

var_dump($array["foo"]);
var_dump($array[42]);
var_dump($array["multi"]["dimensional"]["array"]);
?> 
```

以上例程会输出：

```php
string(3) "bar"
int(24)
string(3) "foo" 
```

**Note: 方括号和花括号可以互换使用来访问数组单元（例如 \$array[42] 和 \$array{42} 在上例中效果相同）。**

## 三、Object 对象

### 1\. 对象初始化

要创建一个新的对象 object，使用 new 语句实例化一个类：

```php
<?php
class foo
{
    function do_foo()
    {
        echo "Doing foo."; 
    }
}

$bar = new foo;
$bar->do_foo();
?> 
```

### 2\. 转换为对象

如果将一个对象转换成对象，它将不会有任何变化。如果其它任何类型的值被转换成对象，将会创建一个内置类 stdClass 的实例。如果该值为 NULL，则新的实例为空。数组转换成对象将使键名成为属性名并具有相对应的值。对于任何其它的值，名为 scalar 的成员变量将包含该值。

```php
<?php
$obj = (object) 'ciao';
echo $obj->scalar;  // outputs 'ciao'
?> 
```

*我们将会在以后的类与对象章节中详细讲述该板块内容*

## 四、Resource 资源类型

资源 resource 是一种特殊变量，保存了外部资源的一个引用。资源是通过专门的函数来建立和使用的。

### 1\. 转换为资源

由于资源类型变量保存有打开文件、数据库连接、图形画布区域等的特殊句柄，因此将其它类型的值转换为资源没有意义。

### 2\. 释放资源

由于 PHP 4 Zend 引擎引进了引用计数系统，可以自动检测到一个资源不再被引用了（和 Java 一样）。这种情况下此资源使用的所有外部资源都会被垃圾回收系统释放。因此，很少需要手工释放内存。

## 五、NULL

特殊的 NULL 值表示一个变量没有值。NULL 类型唯一可能的值就是 NULL。

### 1\. 语法

NULL 类型只有一个值，就是不区分大小写的常量 NULL。

```php
<?php
$var = NULL;       
?> 
```

## 六、类型转换的判别

PHP 在变量定义中不需要（或不支持）明确的类型定义；变量类型是根据使用该变量的上下文所决定的。也就是说，如果把一个字符串值赋给变量 \$var，\$var 就成了一个字符串。如果又把一个整型值赋给 $var，那它就成了一个整数。

PHP 的自动类型转换的一个例子是加法运算符“+”。如果任何一个操作数是浮点数，则所有的操作数都被当成浮点数，结果也是浮点数。否则操作数会被解释为整数，结果也是整数。注意这并没有改变这些操作数本身的类型；改变的仅是这些操作数如何被求值以及表达式本身的类型。

```php
<?php
$foo = "0";  // $foo 是字符串 (ASCII 48)
$foo += 2;   // $foo 现在是一个整数 (2)
$foo = $foo + 1.3;  // $foo 现在是一个浮点数 (3.3)
$foo = 5 + "10 Little Piggies"; // $foo 是整数 (15)
$foo = 5 + "10 Small Pigs";     // $foo 是整数 (15)
?> 
```

### 1\. 类型强制转换

PHP 中的类型强制转换和 C 中的非常像：在要转换的变量之前加上用括号括起来的目标类型。

```php
<?php
$foo = 10;   // $foo is an integer
$bar = (boolean) $foo;   // $bar is a boolean
?> 
```

允许的强制转换有：

*   (int), (integer) - 转换为整形 integer
*   (bool), (boolean) - 转换为布尔类型 boolean
*   (float), (double), (real) - 转换为浮点型 float
*   (string) - 转换为字符串 string
*   (array) - 转换为数组 array
*   (object) - 转换为对象 object
*   (unset) - 转换为 NULL (PHP 5)

注意在括号内允许有空格和制表符，所以下面两个例子功能相同：

```php
<?php
$foo = (int) $bar;
$foo = ( int ) $bar;
?> 
```

将字符串文字和变量转换为二进制字符串：

```php
<?php
$binary = (binary)$string;
$binary = b"binary string";
?> 
```

## 七、作业练习

试试把整型、字符串型、浮点型强转为布尔型会有什么结果。

实验中有任何问题欢迎到[实验楼问答](http://www.shiyanlou.com/questions)提问。