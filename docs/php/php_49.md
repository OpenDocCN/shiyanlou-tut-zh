# 第 12 节 PHP 类与对象（二）

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

本节继续介绍 PHP 的类与对象，会更深入到类与对象的其他方面。包括 static 关键字、抽象类和接口等等知识点。

## 二、Static 关键字

声明类成员或方法为 static，就可以不实例化类而直接访问。不能通过一个对象来访问其中的静态成员（静态方法除外）。

为了兼容 PHP4，如果没有指定“可见性”，属性和方法默认为 public。

由于静态方法不需要通过对象即可调用，所以伪变量$this 在静态方法中不可用。

静态属性不可以由对象通过->操作符来访问。

用::方式调用一个非静态方法会导致一个 E_STRICT 级别的错误。

就像其它所有的 PHP 静态变量一样，静态属性只能被初始化为一个字符值或一个常量，不能使用表达式。 所以你可以把静态属性初始化为整型或数组，但不能指向另一个变量或函数返回值，也不能指向一个对象。

**Example 1 静态成员代码示例**

```php
<?php
class Foo
{
    public static $my_static = 'foo';

    public function staticValue() {
        return self::$my_static;
    }
}

class Bar extends Foo
{
    public function fooStatic() {
        return parent::$my_static;
    }
}

print Foo::$my_static . "\n";

$foo = new Foo();
print $foo->staticValue() . "\n";
print $foo->my_static . "\n";      // Undefined "Property" my_static 

print $foo::$my_static . "\n";
$classname = 'Foo';
print $classname::$my_static . "\n"; // PHP 5.3.0 之后可以动态调用

print Bar::$my_static . "\n";
$bar = new Bar();
print $bar->fooStatic() . "\n";
?> 
```

**Example 2 静态方法代码示例**

```php
<?php
class Foo {
    public static function aStaticMethod() {
        // ...
    }
}

Foo::aStaticMethod();
$classname = 'Foo';
$classname::aStaticMethod(); // As of PHP 5.3.0
?> 
```

## 三、抽象类

PHP5 支持抽象类和抽象方法。定义为抽象的类可能无法直接被实例化，任何一个类， 如果它里面至少有一个方法是被声明为抽象的，那么这个类就必须被声明为抽象的。如果类方法被声明为抽象的， 那么其中就不能包括具体的功能实现。

**Example 1 抽象类代码示例**

```php
<?php
abstract class AbstractClass
{
 // 强制要求子类定义这些方法
    abstract protected function getValue();
    abstract protected function prefixValue($prefix);

    // 普通方法（非抽象方法）
    public function printOut() {
        print $this->getValue() . "\n";
    }
}

class ConcreteClass1 extends AbstractClass
{
    protected function getValue() {
        return "ConcreteClass1";
    }

    public function prefixValue($prefix) {
        return "{$prefix}ConcreteClass1";
    }
}

class ConcreteClass2 extends AbstractClass
{
    public function getValue() {
        return "ConcreteClass2";
    }

    public function prefixValue($prefix) {
        return "{$prefix}ConcreteClass2";
    }
}

$class1 = new ConcreteClass1;
$class1->printOut();
echo $class1->prefixValue('FOO_') ."\n";

$class2 = new ConcreteClass2;
$class2->printOut();
echo $class2->prefixValue('FOO_') ."\n";
?> 
```

以上例程会输出：

```php
ConcreteClass1
FOO_ConcreteClass1
ConcreteClass2
FOO_ConcreteClass2 
```

**Example 2 抽象类的例子**

```php
<?php
abstract class AbstractClass
{
    // 我们的抽象方法仅需要定义需要的参数
    abstract protected function prefixName($name);

}

class ConcreteClass extends AbstractClass
{

    // 我们的子类可以定义父类签名中不存在的可选参数
    public function prefixName($name, $separator = ".") {
        if ($name == "Pacman") {
            $prefix = "Mr";
        } elseif ($name == "Pacwoman") {
            $prefix = "Mrs";
        } else {
            $prefix = "";
        }
        return "{$prefix}{$separator} {$name}";
    }
}

$class = new ConcreteClass;
echo $class->prefixName("Pacman"), "\n";
echo $class->prefixName("Pacwoman"), "\n";
?> 
```

以上例程会输出：

```php
Mr. Pacman
Mrs. Pacwoman 
```

## 四、接口

使用接口（interface），你可以指定某个类必须实现哪些方法，但不需要定义这些方法的具体内容。我们可以通过 interface 来定义一个接口，就像定义一个标准的类一样，但其中定义所有的方法都是空的。 接口中定义的所有方法都必须是 public，这是接口的特性。

### 1\. 实现

要实现一个接口，可以使用 implements 操作符。类中必须实现接口中定义的所有方法，否则会报一个 fatal 错误。如果要实现多个接口，可以用逗号来分隔多个接口的名称。 **Note: 实现多个接口时，接口中的方法不能有重名。** **Note: 接口也可以继承，通过使用 extends 操作符。**

## 2\. 常量

接口中也可以定义常量。接口常量和类常量的使用完全相同。 它们都是定值，不能被子类或子接口修改。

**Example 1 接口代码示例**

```php
<?php

// 声明一个'iTemplate'接口
interface iTemplate
{
    public function setVariable($name, $var);
    public function getHtml($template);
}

// 实现接口
// 下面的写法是正确的
class Template implements iTemplate
{
    private $vars = array();

    public function setVariable($name, $var)
    {
        $this->vars[$name] = $var;
    }

    public function getHtml($template)
    {
        foreach($this->vars as $name => $value) {
            $template = str_replace('{' . $name . '}', $value, $template);
        }

        return $template;
    }
}

// 下面的写法是错误的，会报错：
// Fatal error: Class BadTemplate contains 1 abstract methods
// and must therefore be declared abstract (iTemplate::getHtml)
class BadTemplate implements iTemplate
{
    private $vars = array();

    public function setVariable($name, $var)
    {
        $this->vars[$name] = $var;
    }
}
?> 
```

**Example 2 Extendable Interfaces**

```php
<?php
interface a
{
    public function foo();
}

interface b extends a
{
    public function baz(Baz $baz);
}

// 正确写法
class c implements b
{
    public function foo()
    {
    }

    public function baz(Baz $baz)
    {
    }
}

// 错误写法会导致一个 fatal error
class d implements b
{
    public function foo()
    {
    }

    public function baz(Foo $foo)
    {
    }
}
?> 
```

**Example 3 多个接口间的继承**

```php
<?php
interface a
{
    public function foo();
}

interface b
{
    public function bar();
}

interface c extends a, b
{
    public function baz();
}

class d implements c
{
    public function foo()
    {
    }

    public function bar()
    {
    }

    public function baz()
    {
    }
}
?> 
```

**Example 4 使用接口常量**

```php
<?php
interface a
{
    const b = 'Interface constant';
}

// 输出接口常量
echo a::b;

// 错误写法，因为常量的值不能被修改。接口常量的概念和类常量是一样的。
class b implements a
{
    const b = 'Class constant';
}
?> 
```

## 五、作业练习

请掌握抽象类和接口并理解它们的区别。

实验中有任何问题欢迎到[实验楼问答](http://www.shiyanlou.com/questions)提问。