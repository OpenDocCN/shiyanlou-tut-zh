# 第 1 节 认识隐式变换

## 一、实验简介

本项目课主要介绍 Scala 的隐式变换和隐式参数，它们可以让函数库的调用变得更加方便，并避免一些繁琐和显而易见的细节问题。

### 1.1 知识点

*   implicits 的使用规则
*   隐含类型转换
*   转换被方法调用的对象

### 1.2 准备工作

>**如果你对于如何进入 Scala 编程环境有疑问，或者想通过文本编辑器（例如 Sublime Text）来编写代码，建议你先学习课程《[Scala 基础教程](https://www.shiyanlou.com/courses/490)》。**该基础教程中有编译环境的详细介绍。

为了使用交互式 Scala 解释器，你可以在打开的终端中输入命令：

```scala
cd /usr/local/scala-2.11.7/bin/

scala 
```

当出现`scala>`开始的命令行提示符时，就说明你已经成功进入解释器了。如下图所示。

![](img/750554a9738a756c19d855c4bf9239e6.jpg)

本实验的所有命令及语句均可在 Shell 中输入。

## 二、概述

应用中自己写的代码和调用的第三方函数库有着一个基本的区别：也就是你可以任意修改和扩展自己写的代码。而一般来说，在没有源码的情况下，你很难扩展第三方函数库，函数库提供了什么就只能利用什么。

C# 3.0 支持静态扩展方法，可以为已经定义的库、类进行扩展。

在 Scala 中，解决这个问题的途径是使用**隐含类型变换**和**隐式参数**。它们可以让函数库的调用变得更加方便，并避免一些繁琐和显而易见的细节问题。

Scala 的 `implicit` 可以有 `implicit` 类、方法和参数。

本项目课的几个实验将为你介绍 Scala 的隐式变换和隐式参数的用途。

## 三、 使用 implicits 的一些规则

在 Scala 中的 implicit 定义，是指编译器在需要修复类型匹配时，可以用来自动插入的定义。比如说，如果 `x + y` 类型不匹配，那么编译器可能尝试使用 `convert(x) + y`， 其中`convert`就是由某个`implicit`定义的。

这有点类似于一个整数和一个浮点数相加，编译器可以自动把整数转换为浮点数。Scala 的 implicit 定义是对这种情况的一个推广，你可以定义一个类型，使其在需要时自动转换成另外一种类型。

Scala 的 implicit 定义符合下面一些规则：

### 3.1 标记规则

只有那些使用 implicit 关键字的定义才是可以使用的隐式定义。关键字`implicit`用来标记一个隐式定义。编译器随之可以选择它作为隐式变化的候选项。你可以使用`implicit`来标记任意变量，函数或是对象。

例如下面为一个隐式函数定义，尝试在 Shell 中输入这个语句：

```scala
implicit def intToString(x:Int) : x.toString 
```

编译器只有在`convert`被标记成`implicit`后，才会将 `x + y` 改成 `convert(x) + y`。当然这是在 x + y 类型不匹配时。

### 3.2 范围规则

编译器在选择备选`implicit`定义时，只会选取当前作用域的定义。比如说，编译器不会去调用`someVariable.convert`。如果你需要使用`someVariable.convert`，你必须把`someVarible`引入到当前作用域。也就是说，编译器在选择备选`implicit`时，只有当`convert`是当前作用域下的单个标志符时，它才会作为备选`implicit`。举个例子，对于一个函数库而言，在一个`Preamble`对象中定义一些常用的隐式类型转换非常常见，因此需要使用`Preamble`的代码时，可以通过“`import Preamble._`” ，从而把这些 implicit 定义引入到当前作用域。

这个规则有一个例外，编译器也会在类的伙伴对象定义中查找所需的 implicit 定义。例如下面的定义：

```scala
object Dollar {
    implicit def dollarToEuro(x:Dollar):Euro = ...
    ...
}

class Dollar {
   ...
} 
```

你可以尝试补充上述代码中的细节，并在 Shell 中验证一下。

如果在 `class Dollar` 的方法有需要 `Euro` 类型，但输入数据使用的是 `Dollar`，编译器会在其伙伴对象 `object Dollar` 查找所需的隐式类型转换，本例定义了一个从 `Dollar` 到 `Euro` 的 implicit 定义以供使用。

### 3.3 一次规则

编译器在需要使用 implicit 定义时，只会试图转换一次，也就是编译器永远不会把 `x + y` 改写成 `convert1(convert2(x)) + y`。

### 3.4 优先规则

编译器不会在 `x+y` 已经是合法的情况下去调用 implicit 规则。

### 3.5 命名规则

你可以为 implicit 定义任意的名称。通常情况下，你可以任意命名，implicit 的名称只在两种情况下有用：一是你想在一个方法中明确指明；另外一个是想把那一个引入到当前作用域。

比如，我们定义一个对象，包含两个 implicit 定义：

```scala
object MyConversions {
    implicit def stringWrapper(s:String):IndexedSeq[Char] = ...
    implicit def intToString(x:Int):String = ...
} 
```

请尝试补充代码中的细节部分，并在 Shell 中输入后验证一下。

在你的应用中，你想使用 stringWrapper 变换，而不想把整数自动转换成字符串，你可以只引入`stringWrapper`。

用法如下：

```scala
import  MyConversions.stringWrapper 
```

### 3.6 编译器使用 implicit 的几种情况**

有三种情况使用 implicit:

1.  **转换成预期的数据类型**：比如你有一个方法参数类型是`IndexedSeq[Char]`，在你传入`String`时，编译器发现类型不匹配，就会检查当前作用域是否有从 `String` 到 `IndexedSeq` 隐式转换。

2.  **转换`selection`的`receiver`**：这种情况下，允许你适应某些方法调用。比如 `"abc".exist` ，`"abc"`的类型为`String`，它本身没有定义 `exist` 方法，这时编辑器就检查当前作用域内 `String` 的隐式转换后的类型是否有`exist`方法。若发现`stringWrapper`转换后成`IndexedSeq`类型后，就会有`exist`方法，这个和 C# 静态扩展方法功能类似。

3.  **隐含参数**：隐含参数有点类似于缺省参数。如果在调用方法时没有提供某个参数，编译器会查找当前作用域是否有符合条件的`implicit`对象作为参数传入（有点类似 dependency injection)。

## 四、隐含类型转换

使用隐含转换将变量转换成预期的类型，是编译器最先使用 implicit 的地方。这个规则非常简单，当编译器看到类型`X`而却需要类型`Y`，它就在当前作用域查找是否定义了从类型`X`到类型`Y`的`隐式定义`。

比如，通常情况下，双精度实数不能直接当整数使用，因为会损失精度。你可以尝试在 Shell 中输入下面的语句来验证：

```scala
scala> val i:Int = 3.5
<console>:7: error: type mismatch;
 found   : Double(3.5)
 required: Int
       val i:Int = 3.5
                   ^ 
```

当然，你可以直接调用`3.5.toInt`。

这里我们定义一个从`Double`到`Int`的隐含类型转换的定义，然后再把`3.5`赋值给整数，这样就不会报错了。

尝试在 Shell 中输入下面的语句，然后再次验证一下：

```scala
scala> implicit def doubleToInt(x:Double) = x toInt
doubleToInt: (x: Double)Int

scala> val i:Int = 3.5
i: Int = 3 
```

此时编译器看到一个浮点数`3.5`，而当前赋值语句需要一个整数。此时，按照一般情况，编译器会报错。但在报错之前，编译器会搜寻是否定义了从`Double`到`Int`的隐含类型转换。在本例中，它找到了一个`doubleToInt`。因此编译器将把 `val i:Int = 3.5` 转换成 `val i:Int = doubleToInt(3.5)`。

这就是一个隐含转换的例子。但是从浮点数自动转换成整数并不是一个好的例子，因为会损失精度。 Scala 在需要时会自动把整数转换成双精度实数，这是因为在`Scala.Predef`对象中定义了一个隐式转换：

```scala
implicit def int2double(x:Int) :Double = x.toDouble 
```

而`Scala.Predef`是自动引入到当前作用域的，因此编译器在需要时，会自动把整数转换成 Double 类型。

## 五、转换被方法调用的对象

隐式变换也可以转换调用方法的对象。若编译器看到`X.method`，而类型`X`没有定义`method`（包括基类)方法，那么编译器就查找作用域内定义的从`X`到其它对象的类型转换。而对于类型`Y`，若它定义了`method`方法，编译器就首先使用隐含类型转换把`X`转换成`Y`，然后调用`Y`的`method`。

下面我们看看这种用法的两个典型用法：

### 5.1 支持新的类型

这里我们使用课程[《Scala 开发教程》](https://www.shiyanlou.com/courses/490)中讲解`Ordered Trait`时，定义的 Rational 类型为例。先来回顾一下其定义：

```scala
class Rational (n:Int, d:Int) {
    require(d!=0)
    private val g =gcd (n.abs,d.abs)
    val numer =n/g
    val denom =d/g
    override def toString = numer + "/" +denom
    def +(that:Rational)  =
      new Rational(
        numer * that.denom + that.numer* denom,
        denom * that.denom
      )
    def +(i:Int) :Rational =
        new Rational(numer +1*denom,denom)
    def * (that:Rational) =
      new Rational( numer * that.numer, denom * that.denom)
    def this(n:Int) = this(n,1)
    private def gcd(a:Int,b:Int):Int =
      if(b==0) a else gcd(b, a % b)

} 
```

类`Rational`重载了两个 `+` 运算，参数类型分别为 `Rational` 和 `Int`。因此你可以把`Rational`和`Rational`相加，也可以把`Rational`和整数相加。

在 Shell 中输入下列语句测试一下，在这之前别忘了定义好`Rational`类。

```scala
scala> val oneHalf = new Rational(1,2)
oneHalf: Rational = 1/2

scala> oneHalf + oneHalf
res0: Rational = 1/1

scala> oneHalf + 1
res1: Rational = 3/2 
```

但是我们如果使用 `1 + oneHalf`，会出现什么问题呢？测试一下：

```scala
scala> 1 + oneHalf
<console>:10: error: overloaded method value + with alternatives:
  (x: Double)Double <and>
  (x: Float)Float <and>
  (x: Long)Long <and>
  (x: Int)Int <and>
  (x: Char)Int <and>
  (x: Short)Int <and>
  (x: Byte)Int <and>
  (x: String)String
 cannot be applied to (Rational)
              1 + oneHalf
                ^ 
```

整数及其相关类型都没有定义与`Rational`类型相加的操作，因此编译器会报错。此时编译器在`1`能够转换成`Rational`类型才可以编译通过。因此，我们可以定义一个从整数到`Rational`的隐含类型变换。在 Shell 中输入下面这些语句：

```scala
scala> implicit def int2Rational(x:Int) = new Rational(x)
int2Rational: (x: Int)Rational 
```

现在再执行 `1+oneHalf`：

```scala
scala> 1 + oneHalf
res3: Rational = 3/2 
```

在定义了 `int2Rational` 之后，编译器看到 `1 + oneHalf`，发现`1`没有定义与`Rational`相加的操作，通常需要报错。但编译器在报错之前，查找了当前作用域里面从`Int`到其他类型的定义，而这个转换定义了支持与`Rational`相加的操作。例如在本例中，就发现了 `int2Rational`，因此编译器将 `1+ oneHalf`转换为：

```scala
int2Rational(1)+oneHalf 
```

### 5.2 模拟新的语法结构

隐式转换可以用来扩展 Scala 语言，定义新的语法结构。比如，我们在定义一个`Map`对象时，可以使用如下语法：

```scala
Map(1 -> "One", 2->"Two",3->"Three") 
```

你有没有想过，在`->`内部，是如何实现的？ `->`不是 Scala 本身的语法，而是类型`ArrowAssoc`的一个方法。这个类型定义在包`Scala.Predef`对象中。

`Scala.Predef`自动引入到当前作用域。在这个对象中，同时定义了一个从类型`Any`到`ArrowAssoc`的隐含转换。因此当使用 `1 -> "One"`时，编译器自动插入从 `1`到`ArrowAssoc`的转换。具体定义可以参考 Scala 源码。

利用这种特性，你可以定义新的语法结构，比如行业特定语言（DSL）。

## 六、实验总结

若要为现有的类库增加功能，用 java 的话就只能用工具类或者继承的方式来实现，而在 scala 则采用隐式转化的方式来实现。这就是隐式变换带来的好处。当然了，具体问题需要具体分析，在哪些情况下需要用到隐式变换，就需要在今后的开发工作中勤加练习。

## 版权声明

此课程内容由作者[引路蜂移动软件](http://www.imobilebbs.com/)提供并授权使用，实验楼基于原著进行了内容和章节的优化，修正了一些错误。版权归原作者所有。未经允许，不得以任何形式进行传播和发布。