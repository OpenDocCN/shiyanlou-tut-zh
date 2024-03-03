# 第 4 节 Sealed Classes 与 Option 类型

## 一、实验简介

前面说过，在写模式匹配时，你必须保证你所写的可选项覆盖了全部的可能性。因此，你通常得加上一个缺省通配符选项。但这种情况只适应于缺省通配符有意义的情况。如果对于一些没有缺省项的情况，你怎么才能保证你写的可选项是完全的呢？

本节我们将探讨这个问题，并学习 Scala 语言中的一种标准类型——`Option`类型。

### 1.1 知识点

*   Sealed Classes
*   Option 类型

### 1.2 准备工作

>**如果你对于如何进入 Scala 编程环境有疑问，或者想通过文本编辑器（例如 Sublime Text）来编写代码，建议你先学习课程《[Scala 基础教程](https://www.shiyanlou.com/courses/490)》。**该基础教程中有编译环境的详细介绍。

为了使用交互式 Scala 解释器，你可以在打开的终端中输入命令：

```scala
cd /usr/local/scala-2.11.7/bin/

scala 
```

当出现`scala>`开始的命令行提示符时，就说明你已经成功进入解释器了。如下图所示。

![](img/a05ec7641f65fd87f2df25544bd5bdec.jpg)

本实验的所有命令及语句均可在 Shell 中输入。

## 二、Sealed Classes

实际上，对于本节实验简介中提到的问题，你可以借助于 Scala 编译器来帮忙，要做到这一点，编译器需要预先知道所有可能的匹配项，这种通常情况下是不可能的。比如你总可以派生出新的子类，然后再可选项中添加这个新创建的子类的模式。

一种可能的实现是为基类添加上`sealed`关键字，一个`sealed`的类只能在定义它的同一个文件中定义它的子类。这样你就只需要关心已经定义的子类。如果你使用这些子类作为模式定义，如果可选项不全的话，编译器会自动警告。

我们还是使用之前定义的表达式的例子：

```scala
sealed abstract class Expr
case class Var(name:String) extends Expr
case class Number(num:Double) extends Expr
case class UnOp(operator:String, arg:Expr) extends Expr
case class BinOp(operator:String,left:Expr,right:Expr) extends Expr 
```

下面我们定义一个不完全的模式匹配：

```scala
def describe(e:Expr) :String =e match{
    case Number(_) => "a number"
    case Var(_) => "a variable"
}

<console>:12: warning: match may not be exhaustive.
It would fail on the following inputs: BinOp(_, _, _), UnOp(_, _)
       def describe(e:Expr) :String =e match{
                                     ^
describe: (e: Expr)String 
```

编译器给出警告，表示你的定义可能会抛出`MatchError`异常。因为`BinOp`和`UnOp`没有定义在模式定义中。

而有的时候，你可能只需要匹配部分模式，于是添加一个缺省匹配，比如通配符模式。例如下面的例子：

```scala
def describe(e:Expr) :String =e match{
    case Number(_) => "a number"
    case Var(_) => "a variable"
    case _ => throw new RuntimeException
} 
```

为简洁起见，Scala 支持使用标注(annotation)的方法暂时取消编译器检查模式定义是否完备，为变量添加`@unchecked`标注后，编译器不再给出警告：

```scala
def describe(e:Expr) :String =(e: @unchecked) match{
    case Number(_) => "a number"
    case Var(_) => "a variable"
} 
```

`@unchecked`在模式匹配中具有特殊意义，如果模式匹配的变量使用该标准，Scala 编译器不对该模式进行完备性检查。

## 三、Option 类型

Scala 语言中包含一个标准类型——`Option`类型。它代表可选值，`Option`类型的值可以有两个可能的值：一个为`some(x)` 其中`x`为有效值，另外一个为`None`对象，代表空值。

`Option`类型的值通常作为 Scala 集合类型（List、Map 等）操作的返回类型。 比如 Map 的`get`方法，尝试在 Scala Shell 中输入如下语句：

```scala
scala> val capitals = Map("France"->"Paris", "Japan"->"Tokyo","China"->"Beijing")
capitals: scala.collection.immutable.Map[String,String] = Map(France -> Paris, Japan -> Tokyo, China -> Beijing)

scala> capitals get "France"
res0: Option[String] = Some(Paris)

scala> capitals get "North Pole"
res1: Option[String] = None 
```

将`Option`类型的值放开的一种常见方法，是使用模式匹配。尝试在 Shell 中输入下面的语句：

```scala
scala> def show(x:Option[String]) = x match{
    case Some(s) => s
    case None => "?"
}

show: (x: Option[String])String

scala> show (capitals get "China")
res3: String = Beijing

scala> show (capitals get "France")
res4: String = Paris

scala> show (capitals get "North Pole")
res5: String = ? 
```

在 Scala 程序中，使用`Option`的情况非常频繁。在 Java 中使用 `null` 来表示空值，代码中很多地方都要添加`null` 检测，不然很容易出现`NullPointException`。

因此，Java 程序需要关心那些变量可能是 `Null`，而这些变量出现`Null`的可能性很低，但一但出现，很难查出为什么出现`NullPointerException`。

Scala 的`Option`类型可以避免这种情况。因此，Scala 应用推荐使用`Option`类型来代表一些可选值。使用`Option`类型，读者一眼就可以看出这种类型的值可能为`None`。

**注：**`Option`类型是`Monoid`，通俗的理解可以认为是一种设计模式。

## 四、模式无处不在

Scala 程序很多地方都可以使用模式，而不仅仅用在模式匹配(`match`表达式)，本篇给出几种使用模式的情况。

### 4.1 变量定义

任何时候你使用`val`或`var`定义变量时，你都可以使用模式定义多个变量。此时你定义的元组，分别赋值到不同的变量。

请在 Shell 中尝试输入下面的语句：

```scala
scala> val myTuple=(134,"abc")
myTuple: (Int, String) = (134,abc)

scala> var(number,string)= myTuple
number: Int = 134
string: String = abc 
```

这个情况也适应`case classes`。 如果你知道某个值的具体形式，你可以利用模式来分解单个项：

```scala
scala> val exp=new BinOp("*",Number(5),Number(1))
exp: BinOp = BinOp(*,Number(5.0),Number(1.0))

scala> val BinOp(op,left,right)=exp
op: String = *
left: Expr = Number(5.0)
right: Expr = Number(1.0) 
```

### 4.2 Case 序列定义部分方程

一个`Case`序列（case squence，也成为可选项）为包含在`{ }`的代码，它可以用在可以使用任何方程字面量的地方。从根本上来说，`case`序列也是一个函数字面量，只是更一般化的函数。通常的函数只有一个入口点和一组参数。一个`Case`序列可以有多个入口点和多组参数。每个`Case`可选项都是函数的入口点，而它对于模式为参数定义。其函数体为`Case`序列的右边部分。

这里给出一个简单的例子：

```scala
val withDefault: Option[Int] => Int ={
    case Some(x) =>x
    case None => 0
} 
```

这个函数定义了两个可选项：第一个选项匹配`Some`对象，第二个选项匹配`None`。

尝试在 Shell 中输入下方语句来测试一下：

```scala
scala> withDefault(Some(5))
res0: Int = 5

scala> withDefault(None)
res1: Int = 0 
```

此外需要注意的是，一个`Case`序列定义了一个部分函数(partial function)，如果你传入一个该函数不支持的参数，代码会给出`Runtime`异常。比如，我们定义下面一个部分函数：

```scala
val second: List[Int] => Int = {
 case x::y::_ => y
} 
```

返回列表的第二个元素，系统会给出如下警告：

```scala
<console>:7: warning: match may not be exhaustive.
It would fail on the following input: List(_)
       val second: List[Int] => Int = {
                                      ^
second: List[Int] => Int = <function1> 
```

系统警告匹配不完全，比如函数无法匹配`List(1)`、`List()`等，测试如下：

```scala
scala> second(List(1,2))
res7: Int = 2

scala> second(List(1))
scala.MatchError: List(1) (of class scala.collection.immutable.$colon$colon)
        at $anonfun$1.apply(<console>:7)
        at $anonfun$1.apply(<console>:7) 
```

如果你需要测试某个部分函数是否定义，你就需要告诉编译器你在使用部分函数。而类型`List[Int] => Int`代表了所有由列表到整数的变换。而如果我们需要定义由`List[int]`到`int`的部分函数，则需要使用`PartialFunction`来定义，例如：

```scala
val second:PartialFunction[List[Int],Int] = {
    case x::y::_ => y
} 
```

`PartialFunction` 定义了一个`isDefinedAt`方法可以用来测试某种类型的部分函数是否确定了。例如：

```scala
scala> second.isDefinedAt(List(5,6,7))
res0: Boolean = true

scala> second.isDefinedAt(List(1))
res1: Boolean = false 
```

### 4.3 for 表达式中使用模式

你也可以在`for`表达式中使用模式，比如我们之前定义的：

```scala
val capitals = Map("France"->"Paris", "Japan"->"Tokyo","China"->"Beijing") 
```

我们可以使用`for`表达式来枚举国家和首都：

```scala
for((country,city) <- capitals) 
    println("The captical of " + country + " is " + city)

The captical of France is Paris
The captical of Japan is Tokyo
The captical of China is Beijing 
```

这个例子的`(county,city)`匹配都会成功，因为`capitals` 的每个元素都是一个二元组。如果某些匹配不成功，则这些元素自动跳过。比如：

```scala
val results=List(Some("apple"),None,Some("Orange"))

scala> for(Some(fruit) <- results) println (fruit)
apple
Orange 
```

至此，Scala 的模式学习专题教程就结束了。

## 四、实验总结

综上所述，`sealed`的作用在于保证被修饰的`trait`、`class`等只能在当前文件里面被继承，并且可以通过该关键字来告诉 Scala 编译器在检查模式匹配的时候，使其知道这些`case`的所有情况。这样子的好处在于，Scala 可以在编译的时候进行检查，从而看代码是否有漏掉哪个`case`，最终减少编程上的错误。而使用 `Option` 类型可以使一些问题显得更加清晰，避免未知的错误。

希望此专题教程对你今后的 Scala 学习和开发工作有所帮助。若有任何问题，也欢迎到实验楼的【[问答](https://www.shiyanlou.com/questions/)】版块与我们交流。