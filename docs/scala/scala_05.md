# 第 5 节 完整的代码和计算结果

## 一、实验简介

在完成了先前课程的学习，深入理解 24 点游戏的算法之后，我们在本节将为你揭晓项目的完整代码和计算结果。

### 准备工作

为了使用交互式 Scala 解释器，你可以在打开的终端中输入命令：

```scala
cd /usr/local/scala-2.11.7/bin/

scala 
```

当出现`scala>`开始的命令行提示符时，就说明你已经成功进入解释器了。如下图所示。

![](img/95978bbd09d8fdc8dde8ae1aee26c8c9.jpg)

## 二、完整的代码

综合前面的介绍，这里我们给出最终的 24 点游戏的代码：

```scala
import scala.collection.mutable.Stack

trait BinaryOp{
  val op:String
  def apply(expr1:String,expr2:String) = expr1 + op + expr2
  def unapply(str:String) :Option[(String,String)] ={
    val index=str indexOf (op)
    if(index>0)
      Some(str substring(0,index),str substring(index+1))
    else None
  }
}

class Rational (n:Int, d:Int) {
  require(d!=0)
  private val g =gcd (n.abs,d.abs)
  val numer =n/g
  val denom =d/g
  override def toString = numer + "\\" +denom
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

object Multiply  extends {val op="*"} with BinaryOp
object Divide  extends {val op="/"} with BinaryOp
object Add  extends {val op="+"} with BinaryOp
object Subtract  extends {val op="-"} with BinaryOp
object Rational  extends {val op="\\"} with BinaryOp

object Test extends App{

  val templates=List(
    "N*N-N+N",
    "(N-N)*N*N",
    "N*N+N*N",
    "(N+N)*N*N",
    "N*N*N*N",
    "(N+N*N)*N",
    "(N*N-N)*N",
    "N*N+N+N",
    "(N/N-N)*N",
    "(N-(N-N))*N",
    "N-(N-N-N)",
    "N+N-(N-N)",
    "N*(N/N-N)",
    "(N-N*N)*N",
    "N*(N-N)+N",
    "N+N+N/N",
    "(N-N)*(N-N)",
    "N+N*N/N",
    "N*N/(N-N)",
    "(N+N)*(N+N)",
    "(N-N)*N/N",
    "N+(N+N)/N",
    "N*N/(N+N)",
    "(N+N)*N/N",
    "(N*N+N)*N",
    "(N*N-N)/N",
    "(N/N+N)*N",
    "N*N/N/N",
    "N+N+N-N",
    "N-(N-N)+N",
    "N/(N-N/N)",
    "N+(N-N)*N",
    "(N+N+N)*N",
    "N+N*N-N",
    "N*N-N/N",
    "(N+N)*N-N",
    "(N+N)*(N-N)",
    "(N-N/N)*N",
    "N*(N+N)+N",
    "N*N+N/N",
    "N*N/N-N",
    "(N+N/N)*N",
    "N*N*N/N",
    "(N+N*N)/N",
    "N+N*N+N",
    "N-(N-N)*N",
    "(N-(N+N))*N",
    "N*N-N-N",
    "N+N/N+N",
    "(N-N)*N-N",
    "(N+N)/N+N",
    "N*N+N-N",
    "N/N+N+N",
    "N*N*N-N",
    "(N*N+N)/N",
    "N+N+N*N",
    "N*(N-N)/N",
    "N/N*N+N",
    "N+N*N*N",
    "N+N+N+N",
    "N*N/(N*N)",
    "N+(N+N)*N",
    "(N-N)*N+N",
    "(N+N+N)/N",
    "(N+N)*N+N",
    "N*N*N+N",
    "N*N-(N-N)",
    "N*N-(N+N)",
    "(N-N-N)*N",
    "N*N/N+N",
    "(N+N-N)*N",
    "N/(N/N-N)",
    "N*N-N*N"
  )

  def eval(str:String):Rational = {
    str match {
      case Bracket(part1, expr, part2) => eval(part1 + eval(expr) + part2)
      case Add(expr1, expr2) => eval(expr1) + eval(expr2)
      case Subtract(expr1, expr2) => eval(expr1) - eval(expr2)
      case Multiply(expr1, expr2) => eval(expr1) * eval(expr2)
      case Divide(expr1, expr2) =>  eval(expr1) / eval(expr2)
      case "" => new Rational(0, 1)
      case Rational(expr1, expr2) =>   new Rational(expr1.trim toInt, expr2.trim toInt)
      case _ => new Rational(str.trim toInt, 1)

    }
  }

  def calculate(template:String,numbers:List[Int])={
    val values=template.split('N')
    var expression=""
    for(i <- 0 to 3)  expression=expression+values(i) + numbers(i)
    if (values.length==5) expression=expression+values(4)
    (expression,template,eval(expression))
  }

  def cal24(input:List[Int])={
    var found = false
    for (template <- templates; list <- input.permutations ) {
      try {
        val (expression, tp, result) = calculate(template, list)
        if (result.numer == 24 && result.denom == 1) {
          println(input + ":" + tp + ":" + expression)
          found = true
        }
      } catch {
        case e:Throwable=>
      }
    }
    if (!found) {
      println(input+":"+"no result")
    }
  }

  def cal24once(input:List[Int])={
    var found = false
    for (template <- templates; list <- input.permutations if(!found)) {
      try {
        val (expression, tp, result) = calculate(template, list)
        if (result.numer == 24 && result.denom == 1) {
          println(input + ":" + tp + ":" + expression)
          found = true
        }
      } catch {
        case e:Throwable=>
      }
    }
    if (!found) {
      println(input+":"+"no result")
    }
  }
  println(cal24once(List(5,5,5,1)))
} 
```

## 三、计算结果

下面给出所有从`1`到`10`的四个数字有解的结果：

![此处输入图片的描述](img/cbb4ce3fce29ad2c26683e65da527d5a.jpg)

## 四、更简单的表达式算法

前面我们给出了计算 24 的算法，这并非是计算 24 的 Scala 的最短的代码。除了之前“算法部分（一）”章节提到的方法，在 Scala 中，我们还可以使用更简单的方法来计算表达式——Scala 2.10.0 之后的版本新增了字符串插值的功能。

比如：

```scala
scala> val name = "James"
name: String = James

scala> println(s"Hello, $name")
Hello, James 
```

在字符串前使用“`s`”，可以将字符串中包含的字符串变量`$var`计算之后，再插入到最终的字符串中，比如本例中的`$name`。

同样，你可以可以使用表达式，比如：

```scala
scala> println(s" ${(4.0/10+2)*10}")
 24.0 
```

你可以在`${}`使用任意的表示式。如果你有兴趣的话，可以自行实现更简洁的 24 点算法或者对本课程提供的代码进行优化。

## 五、实验总结

至此，使用 Scala 制作 24 点游戏的内容就全部完成了。回顾一下整个项目，我们先是介绍了如何计算表达式，然后用常见的算法计算 24，其中还对`List`类型进行了详细讲解；接着，我们实现了全排列和穷举可能的表达式。综合上述内容后，最终通过 Scala 语言，设计和实现出了 24 点游戏。

下一节，我们将讲解一个拓展的知识点——如何在 Java 中调用 Scala 函数。建议有 Java 编程基础的同学继续学习这部分的内容。