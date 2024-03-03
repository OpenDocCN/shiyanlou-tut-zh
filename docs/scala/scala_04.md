# 第 4 节 算法部分（二）

## 一、实验简介

在本实验中我们将深入讲解 24 点游戏的一些核心算法，如实现全排列、穷举可能的表达式以及如何计算 24。

### 准备工作

为了使用交互式 Scala 解释器，你可以在打开的终端中输入命令：

```scala
cd /usr/local/scala-2.11.7/bin/

scala 
```

当出现`scala>`开始的命令行提示符时，就说明你已经成功进入解释器了。如下图所示。

![](img/813827c218f741ff60cf500d39d25453.jpg)

## 二、实现全排列

对于穷举法计算二十四的算法，其中一个重要的步骤，是把数字进行全排列。比如，对于一个三个数的列表`List(1,2,3)`，其全排列如下：

```scala
List(1, 2, 3)
List(1, 3, 2)
List(2, 1, 3)
List(2, 3, 1)
List(3, 1, 2)
List(3, 2, 1) 
```

解决这种问题的一个策略，是采用“分而治之”的方法。首先把问题分解成小的问题。比如`N`个数的全排列，可以分解成`N-1`的全排列，再加 1 个数的排列。然后，对每个小的问题给出解决方案。

由此，根据前面的描述，可以写出如下的一个递归算法：

```scala
def permutations(l:List[Int]):List[List[Int]] = {
    l match {
      case Nil => List(List())
      case (head::tail) =>
        for(p0 <- permutations(tail);i<-0 to (p0 length);(xs,ys)=p0 splitAt i)  yield xs:::List(head):::ys
    }
  } 
```

空列表的全排列为空。`N`个数的全排列为`N-1`个数的全排列和 1 个数的全排列。对于每个`N-1`的排列，依次插入剩下的一个数，就构成了一个新的全排列。

测试如下：

```scala
scala> permutations(List(1,2,3)).mkString("\n")
res3: String =
List(1, 2, 3)
List(2, 1, 3)
List(2, 3, 1)
List(1, 3, 2)
List(3, 1, 2)
List(3, 2, 1) 
```

再看看`1,1,2`的情况：

```scala
scala> permutations(List(1,1,3)).mkString("\n")
res4: String =
List(1, 1, 3)
List(1, 1, 3)
List(1, 3, 1)
List(1, 3, 1)
List(3, 1, 1)
List(3, 1, 1) 
```

这里出现了重复的排列，我们可以直接借助于`List`的`distinct`方法过滤掉重复的值。

```scala
scala> permutations(List(1,1,3)).distinct.mkString("\n")
res5: String =
List(1, 1, 3)
List(1, 3, 1)
List(3, 1, 1) 
```

这样，全排列的算法就完成了。其实`List`自身已经提供了`permutations`方法，不需要自行实现。具体用法如下：

```scala
scala> List(1,2,3,4).permutations.mkString("\n")
res6: String =
List(1, 2, 3, 4)
List(1, 2, 4, 3)
List(1, 3, 2, 4)
List(1, 3, 4, 2)
List(1, 4, 2, 3)
List(1, 4, 3, 2)
List(2, 1, 3, 4)
List(2, 1, 4, 3)
List(2, 3, 1, 4)
List(2, 3, 4, 1)
List(2, 4, 1, 3)
List(2, 4, 3, 1)
List(3, 1, 2, 4)
List(3, 1, 4, 2)
List(3, 2, 1, 4)
List(3, 2, 4, 1)
List(3, 4, 1, 2)                                                              
List(3, 4, 2, 1)
List(4, 1, 2, 3)
List(4, 1, 3, 2)
List(4, 2, 1, 3)
List(4, 2, 3, 1)
List(4, 3, 1, 2)
List(4, 3, 2, 1) 
```

## 三、穷举可能的表达式

详细的算法说明，可以参考[24 点算法之我见](http://www.cnblogs.com/grenet/archive/2013/03/17/2964455.html)。简单的穷举可以把`+`、`-`、`×`、`/`以及`()`，与四个数进行全排列。但是，这样会出现很多无效的表达式。因此，我们这里参考“24 点算法之我见”的算法，对表达式做些分析：

>换一种思路，介绍我的 24 点的穷举法。 >上面的算法是对数和运算符进行穷举和搜索。

>我的算法是对运算式进行穷举 >无论给什么样的是 4 个数，运算式总是不变的，举例来说：

> - `N+N+N+N=24`，这是一种运算式。 > - `N*N+N*N=24`，这是另一种运算式。 > - `N/（N-N/N）=24`，这又是另一种运算式。

>下面这个例子：

> - `N+N-（N-N）=24` > - `N+N-N+N=24`

>上面虽然是两种不同的运算式，但本质是同一种运算式（肯定同时成立或同时不成立）。穷举的时候只要穷举其中一个就行了。

>再看下面这个例子： > - `N/(N+N+N)=24`

>虽然是一个运算式，但是这个运算式是不可能成立的，也就是无解运算式，穷举的时候是不需要穷举该运算式的。

参考该文章提供的表格，我们可以定义如下两个`List`对象（去掉无解的表达式）。

所有合法的表达式的模板：

```scala
 val templates=List(
    "(N-(N+N))*N",
    "(N-(N-N))*N",
    "(N*N+N)*N",
    "(N*N+N)/N",
    "(N*N-N)*N",
    "(N*N-N)/N",
    "(N/N+N)*N",
    "(N/N-N)*N",
    "(N+N)*(N+N)",
    "(N+N)*(N-N)",
    "(N+N)*N*N",
    "(N+N)*N/N",
    "(N+N)*N+N",
    "(N+N)*N-N",
    "(N+N)/(N/N)",
    "(N+N)/N*N",
    "(N+N)/N+N",
    "(N+N*N)*N",
    "(N+N*N)/N",
    "(N+N/N)*N",
    "(N+N+N)*N",
    "(N+N+N)/N",
    "(N+N-N)*N",
    "(N-N)*(N+N)",
    "(N-N)*(N-N)",
    "(N-N)*N*N",
    "(N-N)*N/N",
    "(N-N)*N+N",
    "(N-N)*N-N",
    "(N-N)/(N/N)",
    "(N-N)/N*N",
    "(N-N*N)*N",
    "(N-N/N)*N",
    "(N-N+N)*N",
    "(N-N-N)*N",
    "N-(N-N)*N",
    "N-(N-N)+N",
    "N-(N-N-N)",
    "N*(N-(N+N))",
    "N*(N-(N-N))",
    "N*(N*N+N)",
    "N*(N*N-N)",
    "N*(N/N+N)",
    "N*(N/N-N)",
    "N*(N+N)*N",
    "N*(N+N)/N",
    "N*(N+N)+N",
    "N*(N+N)-N",
    "N*(N+N*N)",
    "N*(N+N/N)",
    "N*(N+N+N)",
    "N*(N+N-N)",
    "N*(N-N)*N",
    "N*(N-N)/N",
    "N*(N-N)+N",
    "N*(N-N)-N",
    "N*(N-N*N)",
    "N*(N-N/N)",
    "N*(N-N+N)",
    "N*(N-N-N)",
    "N*N-(N+N)",
    "N*N-(N-N)",
    "N*N*(N+N)",
    "N*N*(N-N)",
    "N*N*N*N",
    "N*N*N/N",
    "N*N*N+N",
    "N*N*N-N",
    "N*N/(N*N)",
    "N*N/(N/N)",
    "N*N/(N+N)",
    "N*N/(N-N)",
    "N*N/N*N",
    "N*N/N/N",
    "N*N/N+N",
    "N*N/N-N",
    "N*N+N*N",
    "N*N+N/N",
    "N*N+N+N",
    "N*N+N-N",
    "N*N-N*N",
    "N*N-N/N",
    "N*N-N+N",
    "N*N-N-N",
    "N/((N+N)/N)",
    "N/((N-N)/N)",
    "N/(N*N)*N",
    "N/(N*N/N)",
    "N/(N/(N+N))",
    "N/(N/(N-N))",
    "N/(N/N)*N",
    "N/(N/N)/N",
    "N/(N/N*N)",
    "N/(N/N/N)",
    "N/(N/N-N)",
    "N/(N+N)*N",
    "N/(N-N)*N",
    "N/(N-N/N)",
    "N/N*(N+N)",
    "N/N*(N-N)",
    "N/N*N*N",
    "N/N*N/N",
    "N/N*N+N",
    "N/N*N-N",
    "N/N/(N/N)",
    "N/N/N*N",
    "N/N+N*N",
    "N/N+N+N",
    "N+(N+N)*N",
    "N+(N+N)/N",
    "N+(N-N)*N",
    "N+N-(N-N)",
    "N+N*(N+N)",
    "N+N*(N-N)",
    "N+N*N*N",
    "N+N*N/N",
    "N+N*N+N",
    "N+N*N-N",
    "N+N/(N/N)",
    "N+N/N*N",
    "N+N/N+N",
    "N+N+N*N",
    "N+N+N/N",
    "N+N+N+N",
    "N+N+N-N",
    "N+N-N+N",
    "N+N-N-N",
    "N-N*(N-N)",
    "N-N+N*N",
    "N-N+N+N"

  ) 
```

等价表达式的定义：

```scala
val equivalence  = List(
    "N+N-N+N,N+N+N-N",
    "N+N-(N-N),N+N-N+N,N+N+N-N",
    "N+N*(N+N),(N+N)*N+N",
    "N+N*(N-N),N+(N-N)*N",
    "N+N/N*N,N+N*N/N",
    "(N+N)/N*N,(N+N)*N/N",
    "N+N/(N/N),N+N/N*N,N+N*N/N",
    "(N+N)/(N/N),(N+N)/N*N,(N+N)*N/N",
    "N-N+N+N,N+N+N-N",
    "N-N+N*N,N+N*N-N",
    "(N-N+N)*N,(N+N-N)*N",
    "(N-(N+N))*N,(N-N-N)*N",
    "N-(N-N)+N,N-N+N+N,N+N+N-N",
    "N+N-N-N,N-N+N+N,N+N+N-N",
    "N-(N-N-N),N-N+N+N,N+N+N-N",
    "N-(N-N)*N,N+(N-N)*N",
    "(N-(N-N))*N,(N-N+N)*N,(N+N-N)*N",
    "(N-N)*N+N,N+(N-N)*N",
    "(N-N)*(N+N),(N+N)*(N-N)",
    "N-N*(N-N),N+N*(N-N),N+(N-N)*N",
    "(N-N)/N*N,(N-N)*N/N",
    "(N-N)/(N/N),(N-N)/N*N,(N-N)*N/N",
    "N*N+N+N,N+N+N*N",
    "N*(N+N)+N,N+(N+N)*N",
    "N*(N+N+N),(N+N+N)*N",
    "N*N+N-N,N-N+N*N",
    "N*(N+N)-N,(N+N)*N-N",
    "N*(N+N-N),(N+N-N)*N",
    "N*(N+N)*N,(N+N)*N*N",
    "(N*N+N)*N,(N+N*N)*N",
    "N*(N+N*N),(N+N*N)*N",
    "N*(N+N)/N,(N+N)*N/N",
    "(N*N+N)/N,(N+N*N)/N",
    "N*(N+N/N),(N+N/N)*N",
    "N*N-N+N,N-N+N*N",
    "N*(N-N)+N,N+(N-N)*N",
    "N*(N-N+N),(N+N-N)*N",
    "N*N-(N+N),N*N-N-N",
    "N*(N-(N+N)),N*(N-N-N),(N-N-N)*N",
    "N*(N-N)-N,(N-N)*N-N",
    "N*(N-N-N),(N-N-N)*N",
    "N*N-(N-N),N*N-N+N,N-N+N*N",
    "N*(N-(N-N)),N*(N-N+N),(N+N-N)*N",
    "N*(N-N)*N,(N-N)*N*N",
    "N*(N-N*N),(N-N*N)*N",
    "N*(N-N)/N,(N-M2)*N/N",
    "N*(N-N/N),(N-N/N)*N",
    "N*N*N+N,N+N*N*N",
    "N*N*(N+N),(N+N)*N*N",
    "N*(N*N+N),(N+N*N)*N",
    "N*N*(N-N),(N-N)*N*N",
    "N*(N*N-N),(N*N-N)*N",
    "N*N/N+N,N+N*N/N",
    "N*(N/N+N),(N+N/N)*N",
    "N*N/N*N,N*N*N/N",
    "N*N/(N*N),N*N/N/N",
    "N*N/(N/N),N*N/N*N,N*N*N/N",
    "N/N+N+N,N+N+N/N",
    "N/N+N*N,N*N+N/N",
    "N/(N+N)*N,N*N/(N+N)",
    "(N/N+N)*N,(N+N/N)*N",
    "N/((N+N)/N),N/(N+N)*N,N*N/(N+N)",
    "N/(N-N)*N,N*N/(N-N)",
    "N/((N-N)/N),N/(N-N)*N,N*N/(N-N)",
    "N/N*N+N,N+N*N/N",
    "N/N*(N+N),(N+N)*N/N",
    "N/N*N-N,N*N/N-N",
    "N/N*(N-N),N*(N-N)/N",
    "N/N*N*N,N*N*N/N",
    "N/(N*N)*N,N/N/N*N,N*N/N/N",
    "N/N*N/N,N*N/N/N",
    "N/(N*N/N),N/N/N*N,N*N/N/N",
    "N/(N/(N+N)),N/N*(N+N),(N+N)*N/N",
    "N/(N/(N-N)),N/N*(N-N),(N-N)*N/N",
    "N/N/N*N,N*N/N/N",
    "N/(N/N)*N,N/N*N*N,N*N*N/N",
    "N/(N/N*N),N/N*N/N,N*N/N/N",
    "N/N/(N/N),N/N/N*N,N*N/N/N",
    "N/(N/N)/N,N/N*N/N,N*N/N/N",
    "N/(N/N/N),N/N*N/N,N*N/N/N"
  ) 
```

通过这两个`List`对象，我们去掉等价的表达式，得出最终的合法表达式只有 73 种，大大缩小了需要穷举的表达式的数目：

```scala
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
```

## 四、计算 24 的算法

有了前面的准备工作，我们现在可以给出二十四游戏的算法。首先，我们合并表示式模板和输入的四个数字，计算出结果：

```scala
def calculate(template:String,numbers:List[Int])={
    val values=template.split('N')
    var expression=""
    for(i <- 0 to 3)  expression=expression+values(i) + numbers(i)
    if (values.length==5) expression=expression+values(4)
    (expression,template,eval(expression))
  } 
```

做些简单的测试，如下：

```scala
scala> calculate("N/N*N+N",List(6,9,9,10))
res0: (String, String, Rational) = (6/9*9+10,N/N*N+N,16\1)

scala> calculate("N/N*N+N",List(9,6,10,9))
res1: (String, String, Rational) = (9/6*10+9,N/N*N+N,24\1)

scala> calculate("(N-N/N)*N",List(5,1,5,5))
res2: (String, String, Rational) = ((5-1/5)*5,(N-N/N)*N,24\1) 
```

我们让函数 `calculate` 返回三个值：合成的表达式、使用的模板，以及计算的结果（分数形式）。我们使用一个三元组作为返回结果，这里也可以看到 Scala 函数返回，无需使用 return，函数体的最后一条语句的值作为返回结果。

说到这里，在之前基础教程中的 `Rational` 的实现和 `eval` 函数的实现有一个小错误（表达式出现中的歧义）。之前`Rational` 的字符表现形式为：

```scala
override def toString = numer + "/" +denom 
```

使用到的“`/`”与我们使用的四则运算的除号一样，这样对于这样的表达式`8/1/3`，就有两种解释。

第一种是 `(8/1)/3`。 其中，`8/1` 为计算的中间结果（`Rational`对象中，“`/`”为 `Rational` 字符串形式中的`/`），计算结果为 `８／３`。

另外一种解释为 `8/（1/3）` ，其中 `1/3` 为输入时的除号。

为避免这种歧义，我们将`Rational`的“`/`”改为“`\`”，修改之前的相关定义：

```scala
class Rational (n:Int, d:Int) {
  require(d!=0)
  private val g =gcd (n.abs,d.abs)
  val numer =n/g
  val denom =d/g
  override def toString = numer + "\\" +denom
  ...
 }

object Rational  extends {val op="\\"} with BinaryOp

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
```

我们有了 `calculate` 函数之后，就可以根据数字的全排列和可能的表达式模板，设计出 24 游戏的穷举算法：

```scala
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
```

这个算法可以列出所有可能的算法，比如：

```scala
scala> cal24(List(5,5,5,1))
List(5, 5, 5, 1):(N-N/N)*N:(5-1/5)*5

scala> cal24(List(3,3,8,8))
List(3, 3, 8, 8):N/(N-N/N):8/(3-8/3)

scala> cal24(List(5,6,7,8))
List(5, 6, 7, 8):(N-(N-N))*N:(5-(8-7))*6
List(5, 6, 7, 8):(N-(N-N))*N:(7-(8-5))*6
List(5, 6, 7, 8):N*N/(N-N):6*8/(7-5)
List(5, 6, 7, 8):N*N/(N-N):8*6/(7-5)
List(5, 6, 7, 8):(N+N)*(N-N):(5+7)*(8-6)
List(5, 6, 7, 8):(N+N)*(N-N):(7+5)*(8-6)
List(5, 6, 7, 8):(N-N-N)*N:(5-8-7)*6
List(5, 6, 7, 8):(N-N-N)*N:(7-8-5)*6
List(5, 6, 7, 8):(N+N-N)*N:(5+7-8)*6
List(5, 6, 7, 8):(N+N-N)*N:(7+5-8)*6 
```

对于`5`、`6`、`7`、`8`，由于加法和乘法的交互律，某些算法是等价的。我们可以根据使用的模板是否相同，去掉这些等价的算法。

如果只需要计算一种算法，可以为 `for` 表达式加上条件：

```scala
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
```

测试如下：

```scala
scala> cal24once(List(5,6,7,8))
List(5, 6, 7, 8):(N-(N-N))*N:(5-(8-7))*6

scala> cal24once(List(1,2,3,4))
List(1, 2, 3, 4):N*N*N*N:1*2*3*4

scala> cal24once(List(1,1,1,1))
List(1, 1, 1, 1):no result 
```

## 五、实验总结

至此，我们对于 24 点游戏中需要用到的算法都作了详细介绍。请你现在尝试整理一下之前学过的内容，花一些时间想想整个游戏由哪几个部分组成、各个算法的作用都是什么，再动手把各个部分用 Scala 语言实现出来。

当整个项目涉及多个组件时，你可以用 Sublime Text 2 等文本编辑器来编辑各个部分的代码。之后，再用 Scala 命令行编译和执行它们。

此处我们暂未给出具体的操作步骤，希望你能够自己探索一下（**Done is better than perfect**）。

我们将在下一节为你揭晓该项目的完整代码和计算结果。不过在看到它们之前，仍然建议你先自己实现一遍。