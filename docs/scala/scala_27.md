# 第 2 节 模式的种类

## 一、实验简介

在上一节里，我们看到了几种不同的模式：常量模式，通配模式，变量模式等。在本实验中，我们将逐个介绍模式的种类。

### 1.1 知识点

*   通配模式
*   常量模式
*   变量模式
*   常量和变量模式的选择
*   构造器模式
*   序列模式
*   多元组模式
*   类型模式
*   变量的绑定

### 1.2 准备工作

>**如果你对于如何进入 Scala 编程环境有疑问，或者想通过文本编辑器（例如 Sublime Text）来编写代码，建议你先学习课程《[Scala 基础教程](https://www.shiyanlou.com/courses/490)》。**该基础教程中有编译环境的详细介绍。

为了使用交互式 Scala 解释器，你可以在打开的终端中输入命令：

```scala
cd /usr/local/scala-2.11.7/bin/

scala 
```

当出现`scala>`开始的命令行提示符时，就说明你已经成功进入解释器了。如下图所示。

![](img/fce25ceacd742cca2e3cab14c0ca6e5c.jpg)

本实验的所有命令及语句均可在 Shell 中输入。

## 二、模式的种类

### 2.1 通配模式

通配符 “`_`” 可以用来匹配任意对象。通常在模式匹配中作为最后一个匹配项，匹配其它所有的输入对象。比如：

```scala
expr match {
    case BinOp(op,left,right) => println( expr + " is a binary operation")
    case _ =>
} 
```

通配模式也可以用来忽略一些你不打算处理的对象，比如在本例中，只关心输入是否为一个二元操作，其它的直接忽略。如果你不关心具体的操作符，如左右操作符，可以直接使用通配符忽略这些部分，如：

```scala
expr match {
    case BinOp(_,_,_) => println( expr + " is a binary operation")
    case _ =>
} 
```

### 2.2 常量模式

常量模式可以匹配和常量值本身相同的输入。任意的字面量都可以作为常量模式。此外，所有的单例(singleton)也可以作为常量模式。比如`Nil`可以匹配空列表。

```scala
def describe(x:Any) =x match {
    case 5 => "five"
    case true => "truth"
    case "hello" => "hi!"
    case Nil => "the empty list"
    case _ => "something else"
} 
```

本例定义了多个常量模式，我们来看看一些测试结果：

```scala
scala> describe (5)
res0: String = five

scala> describe(true)
res1: String = truth

scala> describe ("hello")
res2: String = hi!

scala> describe(Nil)
res3: String = the empty list

scala> describe(List(1,3,4))
res4: String = something else 
```

### 2.3 变量模式

一个变量模式可以匹配任意对象，在这一点上和通配符“`_`”一样。但和通配符不同的是，Scala 将这个变量绑定到输入的对象上，然后你在后面定义的表示中可以引用这个变量。比如下面代码匹配`0`，对于其它的对象使用了变量模式，这其后的表示中可以引用这个变量：

```scala
def isZero(x:Any) = x match{
    case 0 => "zero"
    case somethingElse => "not zero:" + somethingElse
}

scala> isZero(0)
res5: String = zero

scala> isZero(1)
res6: String = not zero:1 
```

### 2.4 常量模式还是变量模式

常量模式可以采用符号名称，比如前面定义的`Nil`（它也是作为常量模式），这里给出另外一个相关的例子：

```scala
E match {
    case Pi => "strange math? Pi =" + Pi
     case _ =>"OK"
} 
```

在 Scala Shell 中输入上述语句：

```scala
scala> E match {
     |     case Pi => "strange math? Pi =" + Pi
     |      case _ =>"OK"
     | }
res7: String = OK 
```

`E` 显然不是`Pi`，那么 Scala 编译器如何知道`Pi`是个常量，而不是一个变量呢？Scala 编译器使用一个简单的规则来判断：如果一个变量使用小写字母开始，那么它作为一个变量模式，其它则作为常量模式。因此变量首字符的大小写显得非常重要。

如果我们重新定义`pi` (小写`p`)：

```scala
scala> val pi=math.Pi
pi: Double = 3.141592653589793

scala> E match {
     |     case pi => "strange math? Pi =" + Pi
     | }
res9: String = strange math? Pi =3.141592653589793 
```

次数`pi`作为变量模式，它可以匹配任意输入，因此可以匹配`E`。由于变量模式可以匹配任意的输入，如果此时你再使用通配符，那么通配符不会被执行到，因此系统会报错。

```scala
scala> E match {
          case pi => "strange math? Pi =" + Pi
          case _ => "OK"

      }
<console>:10: warning: patterns after a variable pattern cannot match (SLS 8.1.1)
                        case pi => "strange math? Pi =" + Pi
                             ^
<console>:11: warning: unreachable code due to variable pattern 'pi' on line 10
                case _ => "OK" 
```

如果你还是想使用小写字符开始的符号作为常量模式，有两个方法：如果这个变量是某个对象的属性，可以在这个变量前使用前缀，或者使用反单引号```scala，比如修改代码如下：

```
scala> E match {
          case `pi` => "strange math? Pi =" + Pi
          case _ => "OK"

      }

res11: String = OK 
```scala

使用了`"`的`pi`又作为常量模式来匹配输入值。

### 2.5 构造器模式

构造器模式功能非常强大，比如一个构造器模式可以定义为 `BinOp(“+”,e,Number(0))`。 它由一个名称`BinOp`（某个 case class 的名称）和一系列由括号分开的模式构成（“`+`”，`e`和`Number(0)`)。这个模式首先检查输入对象是否是`BinOp`类型的对象，然后检查构造器参数是否匹配输入的对象。

这些额外的模式表示 Scala 支持深度匹配，这些模式不仅仅检查最高层次的匹配，并且检查其内部内容的匹配，同时这些额外的模式自身还可以说构造器模式，因此你可以构造嵌套任意层次的构造器模式。比如：

```
expr match{
    case BinOp("+",e,Number(0)) => println(" a deep match")
    case _ =>
} 
```scala

它的第三个参数 `Number(0)` 自身也是一个构造器模式，其参数又匹配`0`，因此有三个层次的匹配。

### 2.6 序列模式

你也可以匹配如`List`和数组等序列类型的数据。和匹配`case class`语法类似，但你可以指明序列中任意数量的元素。比如下面这个例子，匹配了含有三个元素且其中首元素为`0`的列表：

```
scala> List(0,2,4) match{
     | case List(0,_,_) => print ("found it " )
     | case _ =>
     | }
found it 
```scala

如果你需要匹配一个未指明长度的序列，可以使用“`_*`” 作为模式的后一元素，这个“`_*`” 可以匹配任意数目的元素（包括 0 个元素）

```
expr match{
      case List(0,_*) => print ("found it")
      case _ =>
      } 
```scala

### 2.7 多元组模式

除序列外，你可以匹配多元组，比如：

```
scala> def tupleDemo(expr:Any) =
     |     expr match{
     |       case (a,b,c) => print("matched " + a +":" +b +":"+c )
     |       case _ =>
     |     }
tupleDemo: (expr: Any)Unit

scala> tupleDemo(2,3,4)
matched 2:3:4 
```scala

### 2.8 类型模式

你可以使用类型模式匹配来代替类型检查和类型转换。比如：

```
def generalSize(x:Any) = x match{
    case s:String => s.length
    case m:Map[_,_] =m.size
    case _ => -1
} 
```scala

函数`generalSize`返回某些对象长度或是大小。它的参数类型为`Any`，因此可以传入任意类型的数据。 模式`s:String` 是一个类型模式，用于匹配任意类型为`String`的非空对象。变量`s`为匹配的字符串。

第二个匹配 `m:Map[_,_]` 用于匹配任意类型的`Map`对象。这里我们不关心具体的`key`和`value`，因此使用通配符`_`。如果你需要在后面表达式中使用`key`、`value`，可以使用`key,value`替换掉`_`。

对于 Map 类型的数据，我们能否匹配指定`key`或`value`类型的数据呢，比如：

```
def  isIntIntMap(x:Any) = x match {
    case m:Map[Int,Int]=>true
    case _ => false
} 
```scala

此时编译器会给出警告：

```
<console>:9: warning: non-variable type argument Int in type pattern Map[Int,Int] is unchecked since it is eliminated by erasure
       case m:Map[Int,Int]=>true
              ^
isIntIntMap: (x: Any)Boolean 
```scala

Scala 和 Java 类似对于`generic`类采用了“type erasure”，也就是说，在运行时不保存 Map 的`Key`和`value`的类型，因此我们无法匹配指定类型`key`或`value`的 Map 对象。

尝试输入下面的语句：

```
scala> isIntIntMap(Map(1->1))
res14: Boolean = true

scala> isIntIntMap(Map("a"->"b"))
res15: Boolean = true 
```scala

可以看到，这两个都返回`true`，这和预期不同。因此，对于这种情况，编译器会给出警告，`pattern Map[Int,Int]`中的类型不起作用。

但有一个特例，数组和一般的`generic`处理不同，它支持匹配元素类型。比如：

```
def isStringArray(x:Any) = x match{
    case a:Array[String]=>"yes"
    case _ => "no"
} 
```scala

在 Shell 中输入下方的语句测试一下：

```
scala> val as =Array("abc")
as: Array[String] = Array(abc)

scala> isStringArray(as)
res16: String = yes

scala> val ai = Array(1,2,3)
ai: Array[Int] = Array(1, 2, 3)

scala> isStringArray(ai)
res17: String = no 
```scala

### 2.9 变量绑定

除了独立的变量模式外，你还可以把一个变量添加到另外的模式中。可以简单地定义一个变量，然后再添加一个`@`符号，然后再写其它的模式。这就定义了一个变量绑定过的模式。这意味着还是按照标准的模式匹配来匹配输入对象，如果匹配成功，匹配成功的对象会赋值到定义的变量中。

例如：

```
expr match {
    case UnOp("abs",e @ UnOp("abs",_)) => e
    case _ =>
} 
```scala

这里定义了一个绑定到变量的模式，`e @ UnOp(“abs”,_)`，而整个模式是匹配了运用了两次“`abs`”操作的对象。如果匹配成功，`e`将被赋值为匹配`UnOp(“abs”,_)`的部分。比如：

```
scala>  val expr = UnOp("abs",UnOp("abs",Number(5)))
expr: UnOp = UnOp(abs,UnOp(abs,Number(5.0)))

scala> expr match {
     |     case UnOp("abs",e @ UnOp("abs",_)) => e
     |     case _ =>
     |   }
res0: Any = UnOp(abs,Number(5.0)) 
```

可以看到，匹配 `UnOp(“abs”,UnOp(“abs”,Number(5)))`成功后，`e`赋值为 `UnOp(abs,Number(5.0))`。

## 三、实验总结

本节我们详细地展开了各种模式并对其进行了学习。至此，你应该思考如何将不同的模式应用在不同的问题上。而这些模式的更多详细说明，可以参考 Scala 的[官方手册](http://www.scala-lang.org/api/current/index.html)。