# 第 3 节 模式限定修饰与重叠模式定义

## 一、实验简介

Java 语言中，在运行时将特定的功能绑定在对象上，是修饰模式的核心。而在 Scala 中，又如何进行模式的限定修饰呢？本节我们将着重介绍如何进行模式限定修饰和重叠模式的定义。

### 1.1 知识点

*   模式限定修饰
*   重叠模式定义

### 1.2 准备工作

>**如果你对于如何进入 Scala 编程环境有疑问，或者想通过文本编辑器（例如 Sublime Text）来编写代码，建议你先学习课程《[Scala 基础教程](https://www.shiyanlou.com/courses/490)》。**该基础教程中有编译环境的详细介绍。

为了使用交互式 Scala 解释器，你可以在打开的终端中输入命令：

```scala
cd /usr/local/scala-2.11.7/bin/

scala 
```

当出现`scala>`开始的命令行提示符时，就说明你已经成功进入解释器了。如下图所示。

![](img/88d5d02e5cd17ab7bd4c636482102e04.jpg)

本实验的所有命令及语句均可在 Shell 中输入。

## 二、模式限定修饰

有些时候，需要对定义的模式做某些限制，比如你需要完成表达式简化，比如两个相同的参数相加 `e+e` ,你想把它简化为`e*2`。也就是：

```scala
BinOp("+",Var("x"),Var("x")) 
```

转化为

```scala
BinOp("*",Var("x"),Number(2)) 
```

你可能试着这么定义规则：

```scala
def simplifyAdd(e:Expr) =e match{
    case BinOp("+",x,x) => BinOp("*",x,Number(2))
    case _ =>
} 
```

但是编译器会报错：

```scala
<console>:13: error: x is already defined as value x
       case BinOp("+",x,x) => BinOp("*",x,Number(2)) 
```

编译器会告诉你变量`x`已经定义过了，也是模式中定义的变量名只能定义一次，那么此时就可以借助于限制修饰，我们重新定义如下：

```scala
def simplifyAdd(e:Expr) = e match{
    case BinOp("+",x,y) if x==y => BinOp("*",x,Number(2))
    case _ =>
} 
```

模式的限定修饰为一个`if`语句，`if`语句可以使用任意的布尔表达式。通常这个布尔表达式对前面定义的模式变量进行了限制。也就是说，只有在条件满足的且满足模式匹配时，才执行`=>`后面的表达式。

尝试在 Scala Shell 中输入如下语句，测试一下：

```scala
scala> simplifyAdd(BinOp("+",Var("x"),Var("x")))
res0: Any = BinOp(*,Var(x),Number(2.0)) 
```

## 三、重叠模式定义

Scala 在匹配模式时，按照模式定义的顺序依次检查，因此越特定的规则越要先定义，而通用的规则后定义。比如我们修改之前的定义的`simplifyTop`：

```scala
def simplifyTop(expr :Expr) :Expr = expr match {
     case UnOp("-",UnOp("-",e))=>e
     case BinOp("+",e,Number(0))=>e
     case BinOp("*",e,Number(1))=>e
     case _ => expr

} 
```

要使它可以简化任意层次的表达式，我们需要添加两个通用的规则，定义如下：

```scala
def simplifyAll(expr :Expr) :Expr = expr match {
     case UnOp("-",UnOp("-",e))=>e
     case BinOp("+",e,Number(0))=>e
     case BinOp("*",e,Number(1))=>e
     case UnOp(op,e) => UnOp(op,simplifyAll(e))
     case BinOp(op,l,r)=>BinOp(op,simplifyAll(l),simplifyAll(r))
     case _ => expr

} 
```

`simplifyAll`规则 1 是规则 4 的特例，而规则 2、3 是规则 5 的特例，因此 1、2、3 需要定义在 4、5 之前，而最后的缺省规则需要放在最后。如果顺序反了，比如把通用的规则放在了特例之前，编译器会给出警告。因为当 Scala 做匹配时，按照规则定义的顺序，首先会匹配通用规则，若规则匹配成功，那么后面的特例就没有机会匹配。比如：

```scala
def simplifyBad(expr:Expr):Expr = expr match{
    case UnOp(op,e) => UnOp(op,simplifyBad(e))
    case UnOp("-",UnOp("-",e))=>e
}

<console>:12: warning: unreachable code
       case UnOp("-",UnOp("-",e))=>e
                                   ^
simplifyBad: (expr: Expr)Expr 
```

## 四、实验总结

就功能而言，相对于生成子类，修饰模式显得更为灵活，于是我们可以给某个对象添加一些功能，而不是为整个类。本节学习的模式限定修饰以及重叠模式定义会经常用到，不妨想想现实生活中有哪些例子可以应用到本节的这两个知识点。