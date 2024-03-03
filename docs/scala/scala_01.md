# 第 1 节 表达式的计算

## 一、实验简介

前面我们基本对 Scala 编程做了完整的介绍，教程可以参见《[Scala 基础教程](https://www.shiyanlou.com/courses/490)》，但是实践才是最有效的学习方法，我们可以通过一些较为实用的例子来练习 Scala 编程。

### 准备工作

>**如果你对于如何进入 Scala 编程环境有疑问，或者想通过文本编辑器（例如 Sublime Text）来编写代码，建议你先学习课程《[Scala 基础教程](https://www.shiyanlou.com/courses/490)》。**该基础教程中有编译环境的详细介绍。

为了使用交互式 Scala 解释器，你可以在打开的终端中输入命令：

```scala
cd /usr/local/scala-2.11.7/bin/

scala 
```

当出现`scala>`开始的命令行提示符时，就说明你已经成功进入解释器了。如下图所示。

![](img/c63d8daa64c67e211e1c25852f6c59e0.jpg)

## 二、二十四点游戏的算法简介

我们小时候经常玩二十四点游戏，现在我们通过 scala 实现算二十四。编程计算二十点的算法大多为穷举法，我们先从最简单的算法开始，计算表达式。还记得以前学习数据结构时，要使用 C 语言实现算二十四的话，需要把表达式首先转成[逆波兰形式](http://baike.baidu.com/view/552648.htm)，然后通过栈来计算表达式的值。我们看看，如果通过 Scala 的函数化编程，如何来实现表达式的计算。

算二十四使用基本的四则运算：加减乘除，外加括号。为简单起见，我们先不考虑带括号的四则运算，后面再逐步扩展。算`24`，比较有名的一个例子是 `5` 、 `5` 、 `5` 、 `1` 。我们最终的结果是需要使用 Scala 实现二十四算法，给出 `5` `5` `5` `1` 的算法，并可以计算任意四个数。如果有解，则给出解；如果穷举后无解，给出无解的结果。

四则运算具有以下二元计算基本表现形式： `（表达式） op (表达式）`

其中，表达式可以是个数字，或是另外一个表达式。 `op` 可以为 `+`、`–`、`*`、`/`。

比如： `3 + 2`、`3 + 4*3`等等。

对于此类二元运算，我们可以设计一个`Extractor`，分解出二元表达式的左右操作数。现在我们尝试写出一个分解加法的`Extractor`，如下：

```scala
object Add {
    def apply(expr1:String,expr2:String) = expr1 + "+" + expr2
    def unapply(str:String) :Option[(String,String)] ={
        val parts = str split "\\+"
        if(parts.length==2) Some(parts(0),parts(1)) else None
    }
} 
```

在交互式 Scala 解释器中测试一下看看：

```scala
scala> Add.unapply("3+2")
res1: Option[(String, String)] = Some((3,2)) 
```

可以看到`Add`对象成功分解了表达式`3+2`的两个操作数`3`和`2`。

设计一个 `eval` 函数，来计算加法的结果。为简单起见，我们先只考虑整数的情况：

```scala
def eval(str:String):Int = str match{
    case Add(expr1,expr2) => eval(expr1) + eval(expr2)
    case _ => str toInt
} 
```

这个`eval`函数计算一个表达式的值（目前只支持加法）。如果输入的是一个加法表达式，分解后计算每个操作时的值；如果不能分解，那么这是个整数，输出该整数：

```scala
scala> eval("3+5")
res0: Int = 8

scala> eval("4+8")
res1: Int = 12 
```

计算结果成功。不过，这个实现有些局限，我们看看`3+5+3`，会发生什么情况：

```scala
scala> eval("3+5+3")
java.lang.NumberFormatException: For input string: "3+5+3"
  at java.lang.NumberFormatException.forInputString(NumberFormatException.java:65)
  at java.lang.Integer.parseInt(Integer.java:492)
  at java.lang.Integer.parseInt(Integer.java:527)
  at scala.collection.immutable.StringLike$class.toInt(StringLike.scala:241)
  at scala.collection.immutable.StringOps.toInt(StringOps.scala:30)
  at .eval(<console>:10)
  ... 32 elided 
```

很明显出错了。这是因为 `Extractor` 对象里 `Add` 的实现，只考虑了表达式只包含一个“`+`”的情况。我们修改下 `Add` 的实现，不使用`split`（使用`split`后，如果有两个以上的`+`，`parts.length`就大于 2)，而是使用 `indexOf` 来查找“`+`”号：

```scala
object Add{
  val op:String="+"
  def apply(expr1:String,expr2:String) = expr1 + op + expr2
  def unapply(str:String) :Option[(String,String)] ={
    val index=str indexOf(op)
    if(index>0)
      Some(str substring(0,index),str substring(index+1))
    else None
  }
} 
```

再来计算“`3+5+3`”等几个表达式的值：

```scala
scala> eval("3+5+3")
res0: Int = 11

scala> eval("1+2+3+4+5+6+7+8+9+10")
res1: Int = 55 
```

同样，我们可以复制 `Add`，修改`op`的值，实现`Subtract`、`Divide`、`Multiply`。为避免重复代码，我们可以设计一个抽象`Trait`：

```scala
trait BinaryOp{
  val op:String
  def apply(expr1:String,expr2:String) = expr1 + op + expr2
  def unapply(str:String) :Option[(String,String)] ={
    val index=str indexOf(op)
    if(index>0)
      Some(str substring(0,index),str substring(index+1))
    else None
  }
}

object Multiply  extends {val op="*"} with BinaryOp
object Divide  extends {val op="/"} with BinaryOp
object Add  extends {val op="+"} with BinaryOp
object Subtract  extends {val op="-"} with BinaryOp 
```

同样修改 `eval` 的实现如下：

```scala
def eval(str:String):Int = str match {

    case Add(expr1,expr2) => eval(expr1)  +  eval(expr2)
    case Subtract(expr1,expr2) => eval(expr1)  -  eval(expr2)
    case Multiply(expr1,expr2) => eval(expr1)  * eval(expr2)
    case Divide(expr1,expr2) => eval(expr1)  /  eval(expr2)
    case _ => str toInt

} 
```

测试如下：

```scala
scala> eval("4*5-2/2")
res3: Int = 19

scala> eval("4*5-5*4")
res4: Int = 0

scala> eval("4*5-5*4-2/2")
res5: Int = 1

scala> eval("4*5-5*4+2/2")
res6: Int = 1 
```

这个简单的计算表达式中，函数 eval 就这么简单地实现了，而且代码也很直观（尽管还有不少局限性）。我们接着再看看带括号的情况。

### 2.1 带括号的情况

在上面的小节中，我们使用 Scala 实现了四则运算，但还不支持带括号的情况。本篇我们接着看看如处理带括号的情况，比如表达式 `1+2+(3*5)+3+3*(3+(3+5))`。

括号的情况稍微有些复杂。一层括号比较简单，对于嵌套括号的情况，需要匹配同一层次的括号。好在我们只需要匹配最外面一层括号，其它的可以通过递归函数的方法依次匹配。这里我们定义一个方法，通过栈结构来匹配最外一层括号：

```scala
import scala.collection.mutable.Stack  

def matchBracket(str:String):Option[(Int,Int)] ={
    val left = str.indexOf('(')
    if(left >=0) {
      val stack = Stack[Char]()
      val remaining = str substring (left+1)
      var index=0
      var right=0
      for(c <-remaining if right==0){
        index=index + 1
        c match{
          case '(' => stack push c
          case ')'  => if (stack isEmpty)  right= left+index else stack pop
          case _ =>
        }

      }

      Some(left,right)
    }else  None
} 
```

这个方法匹配最外面一层括号，并给出他们在字符中的位置。我们做个简单的测试：

```scala
scala> val str="1+2+(3*5)+3+3*(3+(3+5))" 
str: String = 1+2+(3*5)+3+3*(3+(3+5))

scala> val Some((left,right))=matchBracket(str)
left: Int = 4
right: Int = 8

scala> str.charAt(left)
res0: Char = ( 
```

这个函数成功地找到匹配的括号。

对于每个包含括号的表达式，可以有如下形式：

`part1 ( expr ) part2`

因此，我们可以实现如下的 `Bracket` 对象，来匹配括号表达式：

```scala
object Bracket{

  def matchBracket(str:String):Option[(Int,Int)] ={
    val left = str.indexOf('(')
    if(left >=0) {
      val stack = Stack[Char]()
      val remaining = str substring (left+1)
      var index=0
      var right=0
      for(c <-remaining if right==0){
        index=index + 1
        c match{
          case '(' => stack push c
          case ')'  => if (stack isEmpty)  right= left+index else stack pop
          case _ =>
        }

      }

      Some(left,right)
    }else  None
  }

  def apply(part1:String,expr:String,part2:String) =part1+ "(" + expr + ")"+ part2
  def unapply(str:String) :Option[(String,String,String)] ={
     Bracket.matchBracket(str) match{
      case Some((left:Int,right:Int)) =>{
        val part1 = if (left == 0) "" else str substring(0, left )
        val expr = str substring(left + 1, right)
        val part2 = if (right == (str length)-1) "" else str substring (right+1)
        Some(part1, expr, part2)
      }
      case _ => None
    }
  }
} 
```

修改之前的 `eval` 函数。首先，匹配括号表达式：

```scala
def eval(str:String):Int = str match {
    case Bracket(part1,expr,part2) => eval(part1 +  eval(expr) + part2)
    case Add(expr1,expr2) => eval(expr1)  +  eval(expr2)
    case Subtract(expr1,expr2) => eval(expr1)  -  eval(expr2)
    case Multiply(expr1,expr2) => eval(expr1)  * eval(expr2)
    case Divide(expr1,expr2) => eval(expr1)  /  eval(expr2)
    case _ => str toInt

 } 
```

做些简单的测试：

```scala
scala> eval ("1+(3+(4+2)+3+(3+5)+3)+5")
res1: Int = 29

scala> eval ("1+2+(3*5)+3+3*(3+(3+5))")
res2: Int = 54 
```

这样整数的四则运算的算法就基本实现了。当然，它还不是很完善，比如负数、错误处理等。不过，这些对我们解决 24 问题不是很重要，我们暂时忽略这些问题。

### 2.2 精确结果的计算

在上一小节，我们实现了整数的四则运算的算法。这里我们回到之前提到的 `5` `5` `5` `1` 的例子，我们看看`eval( " 5 * ( 5 – 1/5) " )`的结果是多少？

```scala
scala> eval ("5*(5-1/5)")
res15: Int = 25 
```

结果为`25`。我们知道，这个结果应该是`24`。这是因为，前面我们的算法都是针对整数的， `1/5 =0`。当然，我们可以把整数改成浮点数。比如，修改`eval`如下：

```scala
def eval(str:String):Double = str match {
    ...
    case _ => str toDouble
} 
```

重新计算`eval("5*(5-1/5)")`，结果为 `24.0`。但是，浮点数带来了误差，不是特别理想。我们在 Scala 基础教程中介绍类和对象时，使用了一个`Rational`的例子，即任何有理数都可以表示成分数。因此，可以利用这个`Rational`来得到表达式计算的精确结果。

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

  def -(that:Rational)  =
    new Rational(
      numer * that.denom - that.numer* denom,
      denom * that.denom
    )

  def * (that:Rational) =
    new Rational( numer * that.numer, denom * that.denom)

  def / (that:Rational) =
    new Rational( numer * that.denom, denom * that.numer)

  def this(n:Int) = this(n,1)
  private def gcd(a:Int,b:Int):Int =
    if(b==0) a else gcd(b, a % b)
} 
```

利用`Rational`类，我们修改`eval`定义如下：

```scala
def eval(str:String):Rational = str match {
    case Bracket(part1,expr,part2) => eval(part1 +  eval(expr) + part2)
    case Add(expr1,expr2) => eval(expr1)  +  eval(expr2)
    case Subtract(expr1,expr2) => eval(expr1)  -  eval(expr2)
    case Multiply(expr1,expr2) => eval(expr1)  * eval(expr2)
    case Divide(expr1,expr2) => eval(expr1)  /  eval(expr2)
    case _ => new Rational (str.trim toInt,1)

  } 
```

再看看`eval("5*(5-1/5)")`的计算结果：

```scala
scala> eval ("5*(5-1/5)")
res16: Rational = 24/1 
```

我们得出来表达式的精确结果，为分数表示。比如：

```scala
scala> eval ("4*6")
res17: Rational = 24/1

scala> eval ("4*6+3*3+5/7")
res18: Rational = 236/7 
```

## 三、实验总结

到目前为止，我们有了计算四则运算的算法。所以下一个实验中，24 的算法就比较简单了，会用到穷举法。

**注：Scala 中表达式计算的算法还有不少其它方法。比如，对表达式的分析可以利用`scala.util.parsing.combinator`提供的 API。**

## 版权声明

此课程内容由作者[引路蜂移动软件](http://www.imobilebbs.com/)提供并授权使用，实验楼基于原著进行了内容和章节的优化，修正了一些错误。版权归原作者所有。未经允许，不得以任何形式进行传播和发布。