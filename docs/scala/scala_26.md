# 第 1 节 简单的示例

## 一、实验简介

本项目课侧重介绍 Scala 的`case class`和`pattern matching`（模式匹配），这两个程序结构对于处理树结构的数据非常有帮助。

### 1.1 知识点

*   Case Class 的基本概念
*   模式匹配的基本概念

### 1.2 准备工作

>**如果你对于如何进入 Scala 编程环境有疑问，或者想通过文本编辑器（例如 Sublime Text）来编写代码，建议你先学习课程《[Scala 基础教程](https://www.shiyanlou.com/courses/490)》。**该基础教程中有编译环境的详细介绍。

为了使用交互式 Scala 解释器，你可以在打开的终端中输入命令：

```scala
cd /usr/local/scala-2.11.7/bin/

scala 
```

当出现`scala>`开始的命令行提示符时，就说明你已经成功进入解释器了。如下图所示。

![](img/a225a95f027891acfc0c142e3621f405.jpg)

本实验的所有命令及语句均可在 Shell 中输入。

## 二、从例子说起——算术表达式

Scala 的`case class`使得对对象进行模式匹配变得非常方便，简单的来说，Scala 的`case class`就是在普通的类定义前加 case 这个关键字，然后你可以对这些类来模式匹配。

在我们详细介绍 Scala 的 Case class 和模式匹配之前，我们可以通过一个简单的例子来说明一些基本概念。我们设计一个函数库，这个函数库可以用来计算算术表达式，为简单起见，我们设计的算术表达式只侧重于变量，数字，单操作符，和双操作符。我们可以采用如下的 Scala 类定义：

```scala
abstract class Expr
case class Var(name:String) extends Expr
case class Number(num:Double) extends Expr
case class UnOp(operator:String, arg:Expr) extends Expr
case class BinOp(operator:String,left:Expr,right:Expr) extends Expr 
```

这里我们定义了一个抽象类`Expr`和四个子类（分别代表变量，数值，单操作符，双操作符），Scala 允许我们不定义类的实现，实际我们视`class C` 和 `class C {}`是等价的。

### 2.1 Case Classes

我们可以看到上面的四个子类定义前面我们使用了 case 关键字，使用了 case 关键字的类定义就是 case classes。使用这个关键字，Scala 编译器会自动为你定义的类生成一些成员。

首先，编译器为`case class`生成一个同名的对象构造器（Factory Method），也就是你可以使用 `Var("x")` 来创建一个类的实例，而无需使用`new Var("x")`。

```scala
scala> val x = Var("x")
x: Var = Var(x) 
```

这个构造器在嵌套使用时显得非常简洁明了。比如，我们构建如下的表达式，这种写法避免了很多`new`的使用。

```scala
scala> val op=BinOp("+",Number(1),x)
op: BinOp = BinOp(+,Number(1.0),Var(x)) 
```

其次，Scala 编译器为`case class`的构造函数的参数创建以参数名为名称的属性。比如，`Val`的类的参数`name:String` 可以直接通过 `.name` 访问，如下所示：

```scala
scala> x.name
res1: String = x 
```

第三，编译器为 `case class` 构造了更自然的`toString`、`hashCode`和`equals`实现，它们会递归打印，比较`case class`的参数属性。比如：

```scala
scala> println(op)
BinOp(+,Number(1.0),Var(x))

scala> op.right == Var("x")
res3: Boolean = true 
```

最后一点，Scala 编译器为`case class`添加了一个`copy`方法。这个`copy`方法可以用来构造类对象的一个可以修改的拷贝。这对于使用已有的对象构造一个新实例非常方便，你只要修新创建实例的某些参数即可。 比如我们想创建一个和`op`类似的新的实例，只想修改+为-,可以使用：

```scala
scala> op.copy(operator="-")
res4: BinOp = BinOp(-,Number(1.0),Var(x)) 
```

以上这些惯例为我们带来了很多便利。但这些便利也需要一些小小的代价，就是需要在`class`前面使用`case`关键字，而构造后的类由于自动添加了一些方法而变大了些。`case class`带来的最大的好处是它们支持模式识别。

### 2.2 Pattern matching（模式匹配）

比如说你需要简化表达式的表示方法，这里给出一个简单的规则：

```scala
UnOp(“-“,Unop(“-“,e)) => e//负负得正
BinOp(“+”,e,Number(0)) => e//和 0 加
BinOp（”*”,e,Number(1)) => e //和 1 乘 
```

使用模式匹配，在 Scala 我们几乎和使用和上面规则非常类似的代码来实现表达式的简化：

```scala
scala> def simplifyTop(expr :Expr) :Expr = expr match {
     |   case UnOp("-",UnOp("-",e))=>e
     |   case BinOp("+",e,Number(0))=>e
     |   case BinOp("*",e,Number(1))=>e
     |   case _ => expr
     |    
     | }
simplifyTop: (expr: Expr)Expr 

scala> simplifyTop(UnOp("-",UnOp("-",Var("x"))))
res6: Expr = Var(x)

scala> 
```

`simplifyTop` 定义使用了`match`表达式，它对应 Java 的 `switch`语句，但它的语法和`switch`不同，它的 `selector` 在 `match`前面：

```scala
selector match { alternatives} 
```

一个模式匹配由多个可选项组成，没个选项由`case`开始，每个选项定义一个模式，每个模式对应一个表达式，表达式应用到当模式匹配的时候。模式和表达式之间使用`=>`分隔。

一个`match`表达式的结果取决于第一个匹配的可选项，当该项模式匹配时，该模式`=>`后的表达式被选中然后计算该表达式的值。

一个常量模式，比如本例中的“`+`”和“`0`”, 匹配对应的常数，一个变量模式比如 `e`，可以匹配任意的值。然后 `=>` 右边的表示式可以应用这个变量，“`_`”为通配符，可以匹配任意的值。

构造器模式，比如`UnOp("-",e)`，可以匹配 UnOp 类型的值，这个 UnOp 的第一个参数为“`-`”，第二个参数可以为任意。

和 Java 的 `switch` 语句比较，Scala 的 `match` 有以下几个不同点：

*   `match`为一表达式，有返回结果，其返回结果为匹配项表示式的值。
*   `match`的选项没有`break`，也不会自动匹配下一个选项（no fall through)。
*   如果没有一个选项匹配，那么将抛出`MatchError`异常，这意味着你必须保证考虑到 Match 的所有的选项，因此可能你需要添加一个缺省选项。

## 三、实验总结

本节实验中，我们从计算算术表达式的例子开始，学习了什么是 Case Class，以及怎样进行模式匹配的操作。有了这些基础，我们将在下一节着重介绍模式的种类。

## 版权声明

此课程内容由作者[引路蜂移动软件](http://www.imobilebbs.com/)提供并授权使用，实验楼基于原著进行了内容和章节的优化，修正了一些错误。版权归原作者所有。未经允许，不得以任何形式进行传播和发布。