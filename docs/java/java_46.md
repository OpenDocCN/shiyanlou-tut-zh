# 第 1 节 认识 Java

* * *

## 实验介绍

　　本实验将介绍 Java 平台应用和 Java 的核心概念：JVM、JDK、JRE。同时我们会学到如何在 windows 系统中搭建 Java 开发环境，以及如何利用第三方工具进行 Java 程序的开发。并且在这一章里我们会先接触 Java 的第一个程序 Hello World！让我们一起进入 Java 语言的神奇世界吧！

　　由于本平台的环境在 Linux 系统下已经为同学们搭建好了，下面的搭建过程主要是讲解在 Windows 系统下的搭建方法，同学们可以在自己的电脑上自行操作。

* * *

## 一、Java 简介

　　Java 是一种面向对象的语言。于 1995 年，由 Sun 公司出品。虽然 Java 仅仅只产生了短短 20 年，但是它的发展是非常迅速的。在 2009 年 4 月 20 号，ORACLE 收购了 Sun 公司，也就是说 Java 这门语言现在归属于 ORACLE 这家公司门下。

![Java 发展历程](img/document-uid79144labid1041timestamp1433924738604.jpg)

　　在 Java 这门语言体系当中，最基础的部分就是 Java SE 部分，Java 的标准版本。它包括 Java 最基础的一些结构，包括面向对象的一些特性等等，同时它也是 Java 技术基础和核心。在 Java SE 的基础之上，又分为了 Java EE（Java 的企业版），应用于大型企业级应用的开发。Java ME 主要用于嵌入式开发。初学的时候我们都是从 Java SE 开始的。

![Java SE](img/document-uid79144labid1041timestamp1433925724075.jpg)

　　JVM 叫 Java 虚拟机，它也是整个 Java 技术的核心。Java 语言的跨平台就多亏了 JVM。

　　JDK 叫 Java 开发工具包，没有 JDK 就没有办法对 Java 程序的开发。

　　JRE 叫 Java 运行环境，如果我们需要运行一个 Java 程序，就得安装 JRE。

　　JDK、JRE 和 JVM 之间的关系：

![JDK、JRE 和 JVM](img/document-uid79144labid1041timestamp1433925976197.jpg)

* * *

## 二、Java 开发环境搭建

　　搭建 Java 开发环境，第一步我们就需要安装 JDK。大家可以在 ORACLE 官方网站上下载，下载路径：

http://www.oracle.com/technetwork/java/javase/downloads/index.html

　　这里要提醒各位同学，不同平台和系统的安装文件是不一样的，根据自己电脑的情况选着合适的版本进行安装。

　　接下来我们就来讲讲 `windows 系统`下 JDK 的安装。

　　第一步：安装 JDK

　　在 window 系统下，下载 JDK 后，点开安装程序，按照指示按下一步就可以了。

　　第二步：配置环境变量

　　当我们安装好 JDK 后，就要配置环境变量了。一般来说，我们会配置这三个环境变量：

*   JAVA_HOME 配置 JDK 安装路径

*   PATH 配置 JDK 命令文件的位置

*   CLASSPATH 配置类库文件的位置

    　　步骤一：右击`计算机`，选着`属性`

    　　步骤二：打开`高级系统设置`

    　　步骤三：选择`高级`

    　　步骤四：选择`环境变量`

    　　步骤五：在`系统变量`里配置 JAVA_HOME

    　　　　1\. 点击`系统变量`下的`新建`

    　　　　2\. `变量名`填`JAVA_HOME`

    　　　　3\. `变量值`填 JDK 的安装路径（打开计算机->C 盘->Program Files->Java->jdl1.7.0.13(或者其他版本)，拷贝路径就可以了）

    　　　　4\. 点击确定

    　　步骤六：在`在系统变量`里配置 PATH 变量

    　　　　1\. `系统变量`默认里有 Path 变量，找到它，点击`编辑`

    　　　　2\. 在`变量值`最前面添加指定 jdk 命令文件的位置（jdk1.7.0.13->bin 目录，拷贝路径）

    　　　　3\. 将路径添加到 Path`变量值`前面用`;`和后面的路径隔开

    　　步骤七:配置 CLASSPATH 变量

    　　　　1\. 点击`系统变量`下的`新建`

    　　　　2\. `变量名`填`CLASSPATH`

    　　　　3\. `变量值`填 JDK 的安装路径（打开计算机->C 盘->Program Files->Java->jdl1.7.0.13(或者其他版本)->lib 目录，拷贝路径就可以了）

    　　　　4\. 点击确定

    　　第三步：验证环境配置是否正确

    　　1\. 点击`开始`

    　　2\. 在`搜索`中输入 cmd，按`回车`，打开 dos 命令行

    　　3\. 输入命令 `java` ，按`回车`

    　　4\. 下面会出现一系列的命令行，这些提示是 Java 工具的用法和作用

    　　5\. 再执行一个命令，输入`javac`，按`回车`

    　　6\. 如果以上均给出提示，则开发环境配置是没有问题的

    　　7\. 同学们也可以在右边的桌面上打开`Xfce 终端`，输入以上验证操作命令，进行观察。

## 三、利用文本文件编辑 Java 程序

　　我们如何用文本文件去开发一个 Java 程序呢？主要有三步。

　　第一步，我们使用文本文件去编写 Java 的源代码文件，Java 的源代码文件以`.java`结尾，源代码文件默认是不能被机器执行的。

　　第二步，我们要使用编译器（也就是 javac 命令），对源代码文件进行编译的操作。把它编译成字节码文件，字节码文件是以`.class`结尾。它与平台无关，也就是说无论你是 windows 还是 linux，字节码文件都是这样的一个文件，这跟系统没有关系。实际上 Java 的跨平台就是因为字节码文件的存在。

　　第三步，就是我们的解释器。针对不同的平台，根据相应的解释器，去解释运行字节码文件（这里我们使用 java 命令），最终我们可以看到程序运行出的结果。

大家在右侧的桌面试试吧：

　　1\. 在右侧桌面点击右键，选择`从模板创建`，点击`空文件`。

![创建文本](img/document-uid79144labid1041timestamp1433992577485.jpg)

　　2\. 将文件命名为`HelloWorld.java`,点击`创建`。

　　3\. 双击点开桌面上我们刚刚建好的文件，进行编辑

　　4\. 看看这是木木在上面书写的代码，同学们可以参照到下面图片里的代码进行输入。至于这些代码的含义，我们等会儿便会讲到。

![HelloWorld](img/document-uid79144labid1041timestamp1433992642689.jpg)

　　5\. 代码编辑完了保存一下（可以按`ctrl`＋`s`或在菜单栏的`file`里选择`save`）

　　6\. 点开`Xfce 终端`，输入`cd Desktop`,因为我们将文件存放在桌面上的，所以我们要把目录定位到桌面（这时候可以输入`ll`,来查看一下文件是否在桌面上）

![到 Desktop](img/document-uid79144labid1041timestamp1433992836421.jpg)

　　7\. 输入`javac HelloWorld.java`,对源文件进行编译

　　8\. 同学们是不是发现桌面上多出了一个`HelloWorld.class`的文件

　　9\. 接着我们继续输入`java HelloWorld`（java 命令不能跟上文件的后缀名）,对`HelloWorld.class`字节码文件利用解释器去执行它，是不是出现了`Welcome to ShiYanlou!`

![编译解释](img/document-uid79144labid1041timestamp1433992936205.jpg) 　　

## 四、Eclipse

Eclipse（或 MyEclipse）是一个 IDE 工具，IDE 是集成开发环境的意思。它将程序开发环境和程序调试环境集合在一起，提高开发效率。我们在做开始的时候，一般都会借助这种第三方的工具，给大家提供 Eclipse 的官方下载地址：http://www.eclipse.org/downloads/

Eclipse 它是一款开源软件，所以是免费的。

同学们请打开桌面上的 Eclipse，在 Eclipse 上开发 Java 程序一共分为四个步骤：

1.  创建一个 Java 项目
2.  创建程序包
3.  编写 Java 源程序
4.  运行 Java 程序

接下来我们就来创建一个 Java 项目吧！

1.双击 Eclipse 进入我们的开发环境。第一次进入的时候，Eclipse 可能会让你设置工作空间地址(Workspace），直接点`OK`就可以了。在实验楼的环境中，Workspace 的默认地址为 `/home/shiyanlou/workspace`。

2.等待 Eclipse 启动完成，点击菜单里中的 `File` ，选择 `New` ，创建一个 `Project` ,选择 `Java Project` 新建一个 Java 工程（同时同学们也可以右击左侧空白地方，选择 `New` ，创建 `Project` ，选择`Java Project`）。

![此处输入图片的描述](img/document-uid162034labid94timestamp1469599266836.jpg)

![此处输入图片的描述](img/document-uid162034labid94timestamp1469599343577.jpg)

3.接下来我们要为我们的工程命名，比如说指定一个名称 `HelloWorld` 。

![此处输入图片的描述](img/document-uid162034labid94timestamp1469599418778.jpg)

点击 `Finish` 按钮完成创建，如果遇到下面的对话框，点击 `Yes`即可。

![此处输入图片的描述](img/document-uid162034labid94timestamp1469599486626.jpg)

4.创建好了工程，点开工程发现有许多下拉文件，`src`用来放置我们的源代码文件。

![此处输入图片的描述](img/document-uid162034labid94timestamp1469599592050.jpg)

5.接下来我们要创建程序包，创建包的目的是便于我们对源代码的管理。我们可以将不同的源代码文件放到指定的包下，避免重名问题。右击`src`,选择`New`,创建一个 Package，指定包名（一般为域名的反向）。点击`finish`完成。在`src`的下拉目录里就有了我们定义的包。

![创建包](img/document-uid79144labid1041timestamp1434004457355.jpg)

6.接下来我们便在指定的包下面来编写源代码。右击我们刚刚创建的包，选择`New`，新建一个`Class`源代码文件，指定类的名称，我们依然取成`HelloWorld`,点击`finish`,我们就可以看到，在我们的包下有一个`HelloWorld.java`的文件，接下来就在我们的源代码文件里输入我们上面的代码吧。

![创建源代码文件](img/document-uid79144labid1041timestamp1434004483076.jpg)

7.接下来就进入了第四步，运行 Java 程序。右击空白位置，选择`Run As`,运行`Java Application`。

![运行 Java 程序](img/document-uid79144labid1041timestamp1434004530662.jpg)

8.于是我们便会在下面的窗口中看到`Welcome to ShiYanlou!`的输出。

## 五、Hello World

今天我们输入了如下代码两次：

```java
public class HelloWorld
{
    public static void main(String[] args)
    {
        System.out.println("Hello World!");
    }
} 
```

　　那这些代码究竟是什么意思呢？

![代码解释](img/document-uid79144labid1041timestamp1434004577669.jpg)

## 六、作业

　　同学们，今天的课程大家是不是对 java 有了初步的认识呢？接下来大家就熟悉一下 Eclipse 的相关菜单，玩玩代码吧！