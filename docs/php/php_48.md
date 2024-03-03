# 第 11 节 PHP 类与对象（一）

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

本节介绍 PHP 的类和对象。PHP 中用 class 来定义类，用 new 实例化对象，用 extends 继承类，不过只能单继承，属性和方法有 public、private 和 protected 做访问控制，默认为 public，在类里定义常量不需要\$，用::范围解析符可以调用父类的方法，访问类的静态变量、静态方法和常量。

## 二、基本概念

### 1\. class

每个类的定义都以关键字 class 开头，后面跟着类名，可以是任何非 PHP 保留字的名字。后面跟着一对花括号，里面包含有类成员和方法的定义。伪变量 \$this 可以在当一个方法在对象内部调用时使用。\$this 是一个到调用对象（通常是方法所属于的对象，但也可以是另一个对象，如果该方法是从第二个对象内静态调用的话）的引用。看下面例子： **Example 1 面向对象语言中的 $this 变量:**

```php
<?php
class A
{
    function foo()
    {
        if (isset($this)) {
            echo '$this is defined (';
            echo get_class($this);
            echo ")\n";
        } else {
            echo "\$this is not defined.\n";
        }
    }
}

class B
{
    function bar()
    {
        A::foo();
    }
}

$a = new A();
$a->foo();
A::foo();
$b = new B();
$b->bar();
B::bar();
?> 
```

以上例程会输出：

```php
$this is defined (a)
$this is not defined.
$this is defined (b)
$this is not defined. 
```

**Example 2 简单的类定义:**

```php
<?php
class SimpleClass
{
    // 成员声明
    public $var = 'a default value';

    // 方法声明
    public function displayVar() {
        echo $this->var;
    }
}
?> 
```

**Example 3 类成员的默认值:**

```php
<?php
class SimpleClass
{
    // 无效的类成员定义：
    public $var1 = 'hello '.'world';
    public $var2 = <<<EOD
hello world
EOD;
    public $var3 = 1+2;
    public $var4 = self::myStaticMethod();
    public $var5 = $myVar;

    // 正确的类成员定义：
    public $var6 = myConstant;
    public $var7 = self::classConstant;
    public $var8 = array(true, false);

}
?> 
```

### 2\. new

要创建一个对象的实例，必须创建一个新对象并将其赋给一个变量。当创建新对象时该对象总是被赋值，除非该对象定义了构造函数并且在出错时抛出了一个异常。 **Example 4 创建一个实例:**

```php
<?php
$instance = new SimpleClass();
?> 
```

当把一个对象已经创建的实例赋给一个新变量时，新变量会访问同一个实例，就和用该对象赋值一样。此行为和给函数传递入实例时一样。可以用克隆给一个已创建的对象建立一个新实例。

**Example 5 对象赋值**

```php
<?php
$assigned   =  $instance;
$reference  =& $instance;

$instance->var = '$assigned will have this value';

$instance = null; // $instance and $reference become null

var_dump($instance);
var_dump($reference);
var_dump($assigned);
?> 
```

以上例程会输出：

```php
NULL
NULL
object(SimpleClass)#1 (1) {
   ["var"]=>
     string(30) "$assigned will have this value"
} 
```

### 3\. extends

一个类可以在声明中用 extends 关键字继承另一个类的方法和成员。PHP 不支持多重继承，一个类只能继承一个基类。

被继承的方法和成员可以通过用同样的名字重新声明被覆盖，除非父类定义方法时使用了 final 关键字。可以通过 parent:: 来访问被覆盖的方法或成员。 **简单的类继承：**

```php
<?php
class ExtendClass extends SimpleClass
{
    // Redefine the parent method
    function displayVar()
    {
        echo "Extending class\n";
        parent::displayVar();
    }
}

$extended = new ExtendClass();
$extended->displayVar();
?> 
```

以上例程会输出：

```php
Extending class
a default value 
```

## 三、属性

类的变量成员叫做“属性”，或者叫“字段”、“特征”，在本文档统一称为“属性”。 属性声明是由关键字 public 或者 protected 或者 private 开头，然后跟一个变量来组成。 属性中的变量可以初始化，但是初始化的值必须是常数，这里的常数是指 php 脚本在编译阶段时就为常数，而不是在编译阶段之后在运行阶段运算出的常数。

在类的成员方法里面，可以通过\$this->property(property 是属性名字)这种方式来访问类的属性、 方法，但是要访问类的静态属性或者在静态方法里面却不能使用，而是使用 self::$property。 **Example 1 属性声明**

```php
<?php
class SimpleClass
{
   // 错误的属性声明
   public $var1 = 'hello ' . 'world';
   public $var2 = <<<EOD
hello world
EOD;
   public $var3 = 1+2;
   public $var4 = self::myStaticMethod();
   public $var5 = $myVar;

   // 正确的属性声明
   public $var6 = myConstant;
   public $var7 = array(true, false);

   //在 php 5.3.0 及之后，下面的声明也正确
   public $var8 = <<<'EOD'
hello world
EOD;
}
?> 
```

跟 heredocs 不同, nowdocs 能够使用在静态变量，也能使用在静态声明。 **Example 2 示例：使用 nowdoc 初始化属性**

```php
<?php
class foo {
   // As of PHP 5.3.0
   public $bar = <<<'EOT'
bar
EOT;
}
?> 
```

## 四、类常量

我们可以在类中定义常量。常量的值将始终保持不变。在定义和使用常量的时候不需要使用$符号。

常量的值必须是一个定值，不能是变量，类属性或其它操作（如函数调用）的结果。 **Example 1 定义和使用一个类常量**

```php
<?php
class MyClass
{
    const constant = 'constant value';

    function showConstant() {
        echo  self::constant . "\n";
    }
}

echo MyClass::constant . "\n";

$classname = "MyClass";
echo $classname::constant . "\n"; // PHP 5.3.0 之后

$class = new MyClass();
$class->showConstant();

echo $class::constant."\n"; // PHP 5.3.0 之后
?> 
```

**Example 2 静态数据示例**

```php
<?php
class foo {
    // PHP 5.3.0 之后
    const bar = <<<'EOT'
bar
EOT;
}
?> 
```

和 heredocs（字符串边界符）不同，nowdocs 可以用在任何静态数据中。

## 五、自动加载对象

在 5.3.0 版之前，_*autoload 函数抛出的异常不能被 catch 语句块捕获并会导致一个致命错误。从 5.3.0+ 之后，_*autoload 函数抛出的异常可以被 catch 语句块捕获，但需要遵循一个条件。如果抛出的是一个自定义异常，那么必须存在相应的自定义异常类。__autoload 函数可以递归的自动加载自定义异常类。 **Note: 如果使用 PHP 的 CLI 交互模式 时，Autoloading 是无效的。**

**Example 1 Autoload 例子**

```php
<?php
function __autoload($class_name) {
    require_once $class_name . '.php';
}

$obj  = new MyClass1();
$obj2 = new MyClass2();
?> 
```

## 六、构造函数和析构函数

### 1\. 构造函数

void __construct ([ mixed 、\$args [, \$... ]] )

PHP 5 允行开发者在一个类中定义一个方法作为构造函数。具有构造函数的类会在每次创建新对象时先调用此方法，所以非常适合在使用对象之前做一些初始化工作。

**Note: 如果子类中定义了构造函数则不会隐式调用其父类的构造函数。要执行父类的构造函数，需要在子类的构造函数中调用 parent::__construct()。**

**Example 1 使用新标准的构造函数**

```php
<?php
class BaseClass {
   function __construct() {
       print "In BaseClass constructor\n";
   }
}

class SubClass extends BaseClass {
   function __construct() {
       parent::__construct();
       print "In SubClass constructor\n";
   }
}

$obj = new BaseClass();
$obj = new SubClass();
?> 
```

为了实现向后兼容性，如果 PHP 5 在类中找不到 _*construct() 函数，它就会尝试寻找旧式的构造函数，也就是和类同名的函数。因此唯一会产生兼容性问题的情况是：类中已有一个名为 _*construct() 的方法，但它却又不是构造函数。

### 2\. 析构函数

void __destruct ( void ) PHP 5 引入了析构函数的概念，这类似于其它面向对象的语言，如 C++。析构函数会在到某个对象的所有引用都被删除或者当对象被显式销毁时执行。 **Example 3 析构函数示例**

```php
<?php
class MyDestructableClass {
   function __construct() {
       print "In constructor\n";
       $this->name = "MyDestructableClass";
   }

   function __destruct() {
       print "Destroying " . $this->name . "\n";
   }
}

$obj = new MyDestructableClass();
?> 
```

和构造函数一样，父类的析构函数不会被引擎暗中调用。要执行父类的析构函数，必须在子类的析构函数体中显式调用 parent::__destruct()。

析构函数即使在使用 exit()终止脚本运行时也会被调用。在析构函数中 调用 exit()将会中止其余关闭操作的运行。

## 七、访问控制

对属性或方法的访问控制，是通过在前面添加关键字 public、protected 或 private 来实现的。由 public 所定义的类成员可以在任何地方被访问；由 protected 所定义的类成员则可以被其所在类的子类和父类访问（当然，该成员所在的类也可以访问）；而由 private 定义的类成员则只能被其所在类访问。

### 1\. 对类成员的访问控制

类成员都必须使用关键字 public、protected 或 private 进行定义，默认为 public

**Example 1 声明类成员**

```php
<?php
/**
 * Define MyClass
 */
class MyClass
{
    public $public = 'Public';
    protected $protected = 'Protected';
    private $private = 'Private';

    function printHello()
    {
        echo $this->public;
        echo $this->protected;
        echo $this->private;
    }
}

$obj = new MyClass();
echo $obj->public; // 这行能被正常执行
echo $obj->protected; // 这行会产生一个致命错误
echo $obj->private; // 这行也会产生一个致命错误
$obj->printHello(); // 输出 Public、Protected 和 Private

/**
 * Define MyClass2
 */
class MyClass2 extends MyClass
{
    // 可以对 public 和 protected 进行重定义，但 private 而不能
    protected $protected = 'Protected2';

    function printHello()
    {
        echo $this->public;
        echo $this->protected;
        echo $this->private;
    }
}

$obj2 = new MyClass2();
echo $obj->public; // 这行能被正常执行
echo $obj2->private; // 未定义 private
echo $obj2->protected; // 这行会产生一个致命错误
$obj2->printHello(); // 输出 Public、Protected2，但不会输出 Private

class Bar 
{
    public function test() {
        $this->testPrivate();
        $this->testPublic();
    }

    public function testPublic() {
        echo "Bar::testPublic\n";
    }

    private function testPrivate() {
        echo "Bar::testPrivate\n";
    }
}

class Foo extends Bar 
{
    public function testPublic() {
        echo "Foo::testPublic\n";
    }

    private function testPrivate() {
        echo "Foo::testPrivate\n";
    }
}

$myFoo = new foo();
$myFoo->test(); // Bar::testPrivate 
                // Foo::testPublic
?> 
```

**Note: 为了兼容性考虑，在 PHP 4 中使用 var 关键字对变量进行定义的方法在 PHP 5 中仍然有效（只是作为 public 关键字的一个别名）。在 PHP 5.1.3 之前的版本，该语法会产生一个 E_STRICT 警告。**

## 2\. 对方法的访问控制

类中的方法都必须使用关键字 public、protected 或 private 进行定义。如果没有设置这些关键字，则该方法会被设置成默认的 public。

**Example 2 声明类中的方法**

```php
<?php
/**
 * Define MyClass
 */
class MyClass
{
    // 构造函数必须是 public
    public function __construct() { }

    // 声明一个 public 的方法
    public function MyPublic() { }

    // 声明一个 protected 的方法
    protected function MyProtected() { }

    // 声明一个 private 的方法
    private function MyPrivate() { }

    // 这个方法也是 public 的
    function Foo()
    {
        $this->MyPublic();
        $this->MyProtected();
        $this->MyPrivate();
    }
}

$myclass = new MyClass;
$myclass->MyPublic(); // 这行能被正常执行
$myclass->MyProtected(); // 这行会产生一个致命错误
$myclass->MyPrivate(); // 这行会产生一个致命错误
$myclass->Foo(); // Public、Protected 和 Private 都被调用了

/**
 * Define MyClass2
 */
class MyClass2 extends MyClass
{
    // This is public
    function Foo2()
    {
        $this->MyPublic();
        $this->MyProtected();
        $this->MyPrivate(); // 这行会产生一个致命错误
    }
}

$myclass2 = new MyClass2;
$myclass2->MyPublic(); // 这行能被正常执行
$myclass2->Foo2(); // Public 和 Protected 都被调用了，但 Private 不会被调用
?> 
```

## 八、对象继承

继承已为大家所熟知的一个程序设计特性，PHP 的对象模型也使用了继承。继承将会影响到类与类，对象与对象之间的关系。

比如，当扩展一个类，子类就会继承父类的所有公有和保护方法。但是子类的方法会覆盖父类的方法。 继承对于功能的设计和抽象是非常有用的，而且对于类似的对象增加新功能就无须重新再写这些公用的功能。

**Example 1 继承代码示例**

```php
<?php

class foo
{
    public function printItem($string) 
    {
        echo 'Foo: ' . $string . PHP_EOL;
    }

    public function printPHP()
    {
        echo 'PHP is great.' . PHP_EOL;
    }
}

class bar extends foo
{
    public function printItem($string)
    {
        echo 'Bar: ' . $string . PHP_EOL;
    }
}

$foo = new foo();
$bar = new bar();
$foo->printItem('baz'); // Output: 'Foo: baz'
$foo->printPHP();       // Output: 'PHP is great' 
$bar->printItem('baz'); // Output: 'Bar: baz'
$bar->printPHP();       // Output: 'PHP is great'

?> 
```

## 九、范围解析操作符（::）

范围解析操作符（也可称作 Paamayim Nekudotayim）或者更简单地说是一对冒号，可以用于访问静态成员、方法和常量，还可以用于覆盖类中的成员和方法。 当在类的外部访问这些静态成员、方法和常量时，必须使用类的名字。 把 Paamayim Nekudotayim 选作该操作符的名字似乎有些奇怪。然而，这是 Zend 开发小组在写 Zend Engine 0.5 （被用于 PHP 3 中）时所作出的决定。事实上这个词在希伯莱文就是双冒号的意思。

**Example 1 在类的外部使用 :: 操作符**

```php
<?php
class MyClass {
    const CONST_VALUE = 'A constant value';
}

echo MyClass::CONST_VALUE;
?>  

self 和 parent 这两个特殊的关键字是用于在类的内部对成员或方法进行访问的。 

Example #2 :: from inside the class definition

<?php
class OtherClass extends MyClass
{
    public static $my_static = 'static var';

    public static function doubleColon() {
        echo parent::CONST_VALUE . "\n";
        echo self::$my_static . "\n";
    }
}

OtherClass::doubleColon();
?> 
```

当一个子类覆盖其父类中的方法时，PHP 不会再执行父类中已被覆盖的方法，直到子类中调用这些方法为止。这种机制也作用于构造函数和析构函数、重载及魔术函数。

**Example 3 调用父类的方法**

```php
<?php
class MyClass
{
    protected function myFunc() {
        echo "MyClass::myFunc()\n";
    }
}

class OtherClass extends MyClass
{
    // 覆盖父类中的方法
    public function myFunc()
    {
        // 但仍然可以调用已被覆盖的方法
        parent::myFunc();
        echo "OtherClass::myFunc()\n";
    }
}

$class = new OtherClass();
$class->myFunc();
?> 
```

## 十、作业练习

请定义一个动物类，然后定义一个小狗类继承动物类，里面定义一个吠叫的方法，最后实例化一条小狗，调用它吠叫的方法。

实验中有任何问题欢迎到[实验楼问答](http://www.shiyanlou.com/questions)提问。