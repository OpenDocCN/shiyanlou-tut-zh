# 第 1 节 开启神奇的 Scala 编程之旅

## 一、实验简介

Scala 是一门多范式的编程语言，类似于 Java。设计初衷是实现可伸缩的语言、并集成面向对象编程和函数式编程的各种特性。从本实验开始，你就将打开一扇新的大门，将会学习到如何通过 Scala 来编写程序。

### 知识点

*   Scala 的简介、特性
*   实现 Hello World

## 二、Scala 简介及实验环境介绍

学习 Scala 的最好的方法，是取决于你已有的编程知识。很多人觉得同时使用教程和编程实践相结合的方法最有效。本教程将带你走入 Scala 编程的旅程，同时希望你在看本教程的同时，通过尝试输入本课程的例子，使用 Scala 编译器查看结果，以便能很快的掌握 Scala 编程。

学习本教程，首先需要安装好 Scala 运行环境，你可以使用命令行，Notepad，或者使用 Scala IDE 或是 IntelliJ，如果你不想安装任何软件，你可以通过在线 Scala 编译器 Scalakata.com ，通过浏览器来练习 Scala 编程。

在实验楼中，我们提供了命令行的方式来让你学习 Scala。

首先需要打开终端，你可以通过多种方式完成此步，如下图所示：

![此处输入图片的描述](img/8dac8f0955844e98b5e6fee21eaa5825.jpg)

接着，只需要在终端中输入如下命令，便可进入 Scala Shell：

```scala
/usr/local/scala-2.11.7/bin/scala 
```

![此处输入图片的描述](img/69487d9ec484c2ae3e3d45e789d42861.jpg)

如上图所示，当看到`scala>`字样开始的命令行提示符时，则说明已经进入 Scala 的“交互式命令行环境”。

>实验楼为你提供了两种实验环境的模式：图形界面和字符界面。其中，字符界面更加适合 Scala 解释器的工作。如果需要在两种界面间切换，你可以在顶部工具栏上进行选择，如下图所示。

![此处输入图片的描述](img/5db3589f7b18d2595feee39a2de63f0b.jpg)

### 三、Hello World

最后，我们以每个编程语言开发教程都会有的应用“Hello，World”来结束本篇文章。

```scala
object HelloWorld {
  def main(args: Array[String]) {
    println("Hello, world!")
  }
} 
```

如果一行中的代码太长，可以在该行最后以`Shift 键`+`Enter 键`进行换行即可。输入上述代码后，显示如下图所示：

![此处输入图片的描述](img/b9e29e232d8651fe826c0b1d315b3147.jpg)

这段代码对于 Java 程序员来说似曾相识，它包含一个`main`方法，用来打印“Hello, world”这个问候语。

这段代码，你可以使用命令行交互式执行：

```scala
HelloWorld.main(null) 
```

执行的结果如下图所示：

![此处输入图片的描述](img/2ae96b68fbb10b411495e5736271c28f.jpg)

如果你不想使用这种交换式运行方式，你可以使用任何文本编辑器或是使用 IDE 来运行 Scala 代码。如果使用普通的编辑器，在把代码保存为 `HelloWorld.scala` 之后，可以在命令行使用`scalac` (和 javac 使用方法类似）来编译 Scala 代码。

在实验楼的实验环境中，你可以通过任务栏的`应用程序菜单`中的`开发->Sublime`来打开`Sublime Text 2`（一款流行的文本编辑器软件）。如下图所示：

![此处输入图片的描述](img/d848a12a55d5b2c18b7c88a1fa813c25.jpg)

然后在窗体中输入前文提到的 HelloWorld 代码：

![此处输入图片的描述](img/18666c7c94a4c0775853654a665e950b.jpg)

然后将其保存到桌面上，文件名为`HelloWorld.scala`：

![此处输入图片的描述](img/624835e6571f7303a7f34c383d3536b0.jpg)

接着，在桌面上打开终端，然后输入编译的命令：

```scala
 /usr/local/scala-2.11.7/bin/scalac HelloWorld.scala 
```

运行 Scala 应用：

```scala
 /usr/local/scala-2.11.7/bin/scala HelloWorld 
```

注意`scalac`编译也是生产`.class`文件（和 Java 一样），因此你也可以通过`-d`、`-cp`等选项指定生产的 class 的目录和运行应用时 class 的访问目录。

而使用 scala 来运行应用时，无需再调用`HelloWorld.main(null)`。Scala 会自动执行指定类的`main`方法，运行效果如下图所示：

![此处输入图片的描述](img/7bff453bed756fa1d7749bb79558e605.jpg)

你也可以把 Scala 代码作为脚本语言来运行，比如使用下面代码在 Ubuntu 些运行。

```scala
#!/bin/sh
exec scala "$0" "$@"
!#
object HelloWorld extends App {
  println("Hello, world!")
}
HelloWorld.main(null) 
```

运行效果如图（图片由原作者提供）： ![此处输入图片的描述](img/5c247877446f589a9ac197e133638506.jpg)

## 四、实验总结

在本节实验里，我们了解到了什么是 Scala 语言，以及如何在实验楼的环境中进行 Scala 语言的编程工作。

在后续的学习过程中，对于实验环境的操作有任何问题，你都你可以返回到本实验查看 Scala 语言的几种运行方式。同时，对于课程的任何问题，欢迎到实验楼的[问答](https://www.shiyanlou.com/questions/)版块与大家交流。

## 参考文档

[百度百科 - Scala](http://baike.baidu.com/view/1588150.htm)

## 版权声明

此课程内容由作者[引路蜂移动软件](http://www.imobilebbs.com)提供并授权使用，实验楼基于原著进行了内容和章节的优化，修正了一些错误。版权归原作者所有。未经允许，不得以任何形式进行传播和发布。