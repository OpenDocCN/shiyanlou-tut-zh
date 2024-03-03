# Scala 专题教程 - 抽象成员

本项目课主要介绍 Scala 的抽象成员的用法。在本实验中，你可以学到如何使用 Type 成员，以及抽象 vals 变量的初始化方法等要点。

## 抽象成员

## 一、实验简介

本项目课主要介绍 Scala 的抽象成员的用法。在先前的基础课程中，我们提到了 Scala 中的类型成员也可以是抽象的。抽象字段和抽象方法都是只有字段或者方法的定义，而没有字段或者方法的具体实现。在本节，你就将会学到如何使用 Type 成员，以及抽象 vals 变量的初始化方法等要点。

### 1.1 知识点

*   Type 成员
*   抽象 vals 的初始化
*   成员值的预先初始化
*   延迟初始化
*   抽象类型

### 1.2 准备工作

>**如果你对于如何进入 Scala 编程环境有疑问，或者想通过文本编辑器（例如 Sublime Text）来编写代码，建议你先学习课程《[Scala 基础教程](https://www.shiyanlou.com/courses/490)》。**该基础教程中有编译环境的详细介绍。

为了使用交互式 Scala 解释器，你可以在打开的终端中输入命令：

```scala
cd /usr/local/scala-2.11.7/bin/

scala 
```

当出现`scala>`开始的命令行提示符时，就说明你已经成功进入解释器了。如下图所示。

![](img/9ced3ab605371d46ad23077dc5fe65d7.jpg)

本实验的所有命令及语句均可在 Shell 中输入。

## 二、概述

当一个类或者`Trait`的成员没有定义完整的实现时，我们称其为`抽象成员`。抽象成员需要在其子类中完成实现。在 Scala 中，抽象成员除了成员函数外，也可以是成员变量或是抽象类型。

本实验将介绍四种抽象成员：`vals`、 `vars`、`methods`和`types`。

下面的例子定义了几种抽象成员：

```scala
trait Abstract {
    type T
    def transform(x:T) :T
    val initial :T
    var current :T
} 
```

这个例子定义了一个抽象类型`T`、一个抽象方法`transform`、一个抽象成员变量`var current`和一个抽象成员变量`val initial`。

派生的实类需要实现这些抽象成员，比如：

```scala
class Concrete extends Abstract {
    type T = String
    def transform(x:String) = x + x
    val initial = "hi"
    var current = initial
} 
```

这个类实现，给抽象类型`T`赋予了真实的类型`String`。然后为其它抽象成员给出了具体的定义。

## 三、Type 成员

Scala 中的抽象类型，指的是在类或 Trait 中使用`type`定义的类型。 类或 Trait 本身也可以使用`abstract`来定义，但这种使用`abstract`定义的类和 Trait 在 Scala 中不称为抽象类型。

你可以把派生于抽象类型的`非抽象类型`看作给一个类型，并且起一个别名。例如上面例子`Concrete`中的类型`T`， 可将`Concrete` 类定义中`String`类型的别名取为`T`，于是在 `Concrete` 中所有出现`T`的地方都代表`String`，因此前面的`Concrete`其实也可以写成：

```scala
class Concrete extends Abstract {
    type T = String
    def transform(x:T) = x + x
    val initial = "hi"
    var current = initial
} 
```

我们将在后面再详细介绍抽象类型的用法。

## 四、初始化抽象 vals

抽象定义的 vals 在某些时候，可以起到父类参数的角色。它们允许你在子类中提供在父类中省略的定义，这对于 Trait 来说尤其重要。因为 Trait 没有提供可供传入参数的构造函数。因此为 Trait 提供参数支持，通常是通过抽象 vals 来实现的。

例如我们之前例子中使用过的有理数类型（Rational），使用 Trait 方式定义如下：

```scala
trait RationalTrait{
    val numerArg:Int
    val denomArg:Int
} 
```

我们之前使用的`Rational`类定义定义了两个参数 `n`、`d`，分别代表分子和分母。这里我们使用`RationalTrait` 定义了两个抽象的 vals 值。为了构造这个 Trait 的一个实例，你需要提供这些抽象成员的实现，这里可以使用匿名类实例的方法构造一个实例：

```scala
scala> val r= new RationalTrait {
     | val numerArg = 1
     | val denomArg = 2
     | }
r: RationalTrait = $anon$1@341f55dd 
```

这种构造 `RationalTrait` 实例的方法，在形式上和之前的 `new Rational(1,2)` 有点相像，但还是有些细节上的差别——在表达式初始化的顺序上的差异。

当你使用如下代码：

```scala
new Rational(expr1,expr2) 
```

其中的两个表达式 `expr1,expr2` 在初始化类`Rational`之前就计算好了，因此在初始化`Rational`时，这些表达式是可以用的。而对于 Trait 来说，情况却相反：

```scala
new RationalTrait{
    val numerArg = expr1
    val denomArg = expr2
} 
```

计算表达式 `expr1,expr2` 是在处理匿名类实例的过程中进行的，而匿名类的处理是在 `RationalTrait` 之后进行的。因此，在初始化 RationalTrait 时，这两个值是不可用的（或者说这两个值是缺省值`0`）。这对于 `RationalTrait` 定义来说，不是个什么问题，因为 `RationalTrait` 的初始化没有使用到 `numerArg` 和 `denomArg` ，但对于下面的 `RationalTrait` 定义就存在问题了：

```scala
trait RationalTrait{
    val numerArg :Int
    val denomArg :Int

    require(denomArg !=0)
    private val g = gcd(numerArg,denomArg)

    val numer = numerArg/g
    val denom = denomArg/g

    private def gcd(a:Int,b:Int):Int =
        if(b==0) a else gcd(b, a % b)

    override def toString = numer + "/" + denom
} 
```

如果此时，你使用某些表达式来构造这个 Trait 的实例，就会出问题了：

```scala
scala> new RationalTrait {
     | val numerArg = x 
     | val denomArg  = 2 * x
     | }
java.lang.IllegalArgumentException: requirement failed
  at scala.Predef$.require(Predef.scala:207)
  at RationalTrait$class.$init$(<console>:11)
  ... 39 elided

scala> new RationalTrait {
     | val numerArg = 1
     | val denomArg  = 2
     | }
java.lang.IllegalArgumentException: requirement failed
  at scala.Predef$.require(Predef.scala:207)
  at RationalTrait$class.$init$(<console>:11)
  ... 39 elided 
```

这是因为在执行 `RationalTrait` 的初始化代码时， `denomArg` 的值还是 `0`，所以抛出了异常。

由此你可以知道，抽象 val 值和类参数之间存在不同。对于使用 Trait 的这个问题，Scala 提供了两个解决方案：域的预先初始化，还有 lazy vals。我们在下个小节介绍它们。

## 五、预先初始化成员的值

为了解决上面的 RationalTrait 中的问题，有两个解决方案：第一种方案是使用预先初始化成员的值的方法，这种方法可以让你在调用父类构造函数之前首先初始化子类的成员。

这种方法，是把初始化成员变量的定义放在调用父构造函数之前。

下面是一个匿名实例化 Trait 的例子，尝试在 Shell 中输入这些语句：

```scala
val x = 1

new {
    val numerArg =1 * x
    val denomArg = 2 *x
} with RationalTrait

res1: RationalTrait = 1/2 
```

可以看到在这个例子中，我们把初始化成员的代码放在其父 Trait 之前。再看一个例子：

```scala
object twoThirds extends {
    val numerArg =1 * x
    val denomArg = 2 *x
} with RationalTrait

defined object twoThirds 
```

初始化成员部分也是防止其父 Trait 之前，两个方法都使用`with`。

这种方法除了可以应用中匿名实例和对象外，也可以应用中类的定义说，比如我们可以定义如下的 RationalClass：

```scala
class RationalClass(n:Int,d:Int) extends {
    val numerArg =n
    val denomArg =d
} with RationalTrait {
    def + (that: RationalClass) = new RationalClass(
        numer * that.denom + that.numer * denom,
        denom * that.denom
    )
} 
```

因为这些预先初始化的值发生在调用父类的构造函数之前，因此这些初始化这些值时，不可以引用正在构造的对象。正因为如此，如果在初始化的表达式中使用`this`，这个 `this` 不是指正在构造的对象，而是指包含这个定义的对象。比如：

```scala
new {
    val numberArg =1
    val denomArg = this.numerArg *2
} with RationalTrait

<console>:11: error: value numerArg is not a member of object $iw
              val denomArg = this.numerArg *2 
```

这个例子无法编译，这是因为编译器无法找到 对象$iw 的 numerArg 成员，$iw 为 Scala 命令行输入的当前对象。

## 六、延迟初始化(Lazy vals)

除了前面介绍的预先初始化成员值外，你还可以让系统自行决定何时初始化成员的初始值。这是通过在 val 定义前面添加 lazy(懒惰），也是说直到你第一次需要引用该成员时，系统才会去初始化，否则该成员就不初始化（这也是 lazy 的由来，可理解为懒加载)。

首先我们定义一个正常定义 val 的例子：

```scala
object Demo {
    val x = { println("initializing x"); "done"}
} 
```

我们首先引用 `Demo`，然后 `Demo.x`：

```scala
scala> Demo
initializing x
res0: Demo.type = Demo$@78178c35

scala> Demo.x
res1: String = done 
```

正如你所看到的，当引用 `Demo` 对象时，它的成员`x`也会初始化，初始化 `x` 伴随着初始化 `Demo` 的过程。然后，如果我们在 `val x` 前添加 `lazy` ，情况就有所不同了:

```scala
object Demo {
    lazy val x = { println("initializing x"); "done"}
}

defined object Demo

scala> Demo
res0: Demo.type = Demo$@7de1c412

scala> Demo.x
initializing x
res1: String = done 
```

在使用 `lazy` 之后，初始化`Demo` 时，不会初始化 `x` ，只有在引用到 `Demo.x` 时，该初始化代码才会执行。

这有点类似定义了一个无参数的方法，但和 `def` 不同的是，`lazy` 变量初始化代码只会执行一次。

通过这个例子，我们可以看到诸如 `Demo` 的对象本身也像一个 `lazy` 变量，也是在第一次引用时才会初始化，这是正确的。实际上，一个 `object` 定义可以看成是使用了 `lazy val` 定义一个匿名类实例的简化方式。

使用 `lazy val` ，我们可以修改之前的 `RationalTrait`。在这个新的 Trait 定义中，所有的类成员变量的实现（非抽象成员）都使用 `lazy` 来修饰。

```scala
trait LazyRationalTrait{
    val numerArg :Int
    val denomArg :Int

    lazy val numer = numerArg/g
    lazy val denom = denomArg/g

    private lazy val g = {
        require(denomArg !=0)
        gcd(numerArg,denomArg)
    }
    private def gcd(a:Int,b:Int):Int =
        if(b==0) a else gcd(b, a % b)

    override def toString = numer + "/" + denom
} 
```

同时我们把 `require` 移动到 `g` 里面，这样所有的 `lazy val` 初始化代码都移动到 `val` 定义的右边。我们不再需要预先初始化成员变量。测试如下：

```scala
scala> val x = 2
x: Int = 2

scala> new LazyRationalTrait{
    val numerArg = x
    val denomArg = 2 * x
}

res2: LazyRationalTrait = 1/2 
```

我们来分析一下这段代码中命令行的执行顺序：

首先，创建了一个新的 `LazyRationalTrait` 的实例，执行 `LazyRationalTrait` 的初始化代码，这部分代码为空，`LazyRationalTrait` 所有成员变量都没有初始化。

其次，该 Trait 的匿名子类的主构造函数被执行，这部分初始化 `numberArg` 和 `denomArg` 为`2` 和`4`。

接下来，命令行需要调用该实例的 `toString` 方法来显示该实例的值。

接下来， `toString` 需要访问成员 `number`。这是第一次访问该成员，因此 `lazy val` 初始化代码被执行。初始化代码调用私有成员`g` ，因此需要计算 `g` 的值，用到之前定义过的 `numberArg` 和 `denomArg`。

接下来 `toString` 需要访问成员 `denom`，这是第一次访问该成员，因此 `lazy val` 初始化代码被执行。初始化代码调用私有成员`g`，因此需要计算 `g` 的值，因为 `g` 已经计算过，无需再计算。

最后，`toString`的结果 `1/2` 构造出来并显示。

在这个例子中，我们在写代码时，`g` 定义在 `number` 和 `denom` 的后面。然而，由于这三个变量都是使用 `lazy` 来定义的，因此它们在代码中出现的顺序并不重要。

## 七、抽象类型

在本实验的一开始，我们看到“Type T”的用法，这是声明一个抽象类型。本篇介绍这种声明的意义和用途。和其它类型的抽象类型一样，抽象类型定义也是定义了一个“占位符”类型，其具体定义由其子类声明。不同的子类可以定义不同 T 的具体类型。

下面我们通过一个例子来说明抽象类型的用法。假定你需要为动物的饮食习性建模， 你可能会定义如下的数据结构：

```scala
class Food
abstract class Animal {
    def eat(food: Food)
} 
```

然后对于与牛和草，我们可能需要实现两个不同的实类 `Cows` 和 `Grass`：

```scala
class Grass extends Food
class Cow extends Animal {
    override def eat(food: Grass) {}
} 
```

但是这段代码编译可能不通过：

```scala
<console>:13: error: class Cow needs to be abstract, since method eat in class Animal of type (food: Food)Unit is not defined
(Note that Food does not match Grass: class Grass is a subclass of class Food, but method parameter types must match exactly.)
       class Cow extends Animal {
             ^
<console>:14: error: method eat overrides nothing.
Note: the super classes of class Cow contain the following, non final members named eat:
def eat(food: Food): Unit
       override def eat(food: Grass) {} 
```

怎么会出错呢？这是因为类 `Cow` 中的 `eat` 不能重载其父类。不能重载的原因是参数类型不一致，`Animal`中 `food` 类型为 `Food` ，而 `Cow` 中类型为 `Grass`。 仅管我们可能会说 `Grass` 是 `Food` 的子类，编译器没有必要这么严格。但是如果编译器允许这种情况存在，那么很快就会出现新问题。

假定前面的编译没有问题，我们再定义一个 `Fish` 类：

```scala
class Fish extends Food
val bessy: Animal = new Cow
bessy eat (new Fish) 
```

问题来了，我们给牛喂了鱼。如果前面的 `Cow` 可以编译通过的话，这段代码也是合法的，但结果却显然不对，因此编译器编译不通过 `Cow` 是有道理的。

对于这个问题，我们可以通过抽象类型来解决，哪种动物吃哪种食物由动物决定：

```scala
class Food
abstract class Animal {
    type SuitableFood <: Food
    def eat(food:SuitableFood)
} 
```

当定义新的 `Animal` 子类时，动物只能吃合适的食物，而 `Animal` 类本身不能决定那种食物合适，因此我们使用抽象类型定义。 但这个抽象类型有类型上界限制，表示 `Animal` 子类中使用的 `SuitableFood` 必须是 `Food` 的子类。

我们重新定义 `Cow` 如下：

```scala
class Grass extends Food
class Cow extends Animal {
    type SuitableFood = Grass
    override def eat(food:Grass) {}
} 
```

你现在再试试喂鱼给牛看看：

```scala
scala> class Fish extends Food
defined class Fish

scala> val bessy :Animal = new Cow
bessy: Animal = Cow@a0992a

scala> bessy eat (new Fish)
<console>:14: error: type mismatch;
 found   : Fish
 required: bessy.SuitableFood
              bessy eat (new Fish)
                         ^ 
```

发现了什么呢？

## 八、实验总结

学到这里，你，注意不要与 Trait 的用法搞混淆了。我们来总结一下关于抽象类和抽象成员中主要被注意的几个要点：

*   抽象类是不能被实例的类。
*   抽象类的某个或某几个成员没有被完整定义，这些没有被完整定义的成员称为抽象方法或抽象字段（抽象成员）。
*   抽象类通过`abstract`保留字标记。
*   重写抽象方法、抽象字段时，不需要使用 override 保留字

至此，本实验即结束了。如果你对 Scala 课程有更多的疑问或者建议，欢迎到【[问答](https://www.shiyanlou.com/questions/)】版块与我们交流。

## 版权声明

此课程内容由作者[引路蜂移动软件](http://www.imobilebbs.com/)提供并授权使用，实验楼基于原著进行了内容和章节的优化，修正了一些错误。版权归原作者所有。未经允许，不得以任何形式进行传播和发布。