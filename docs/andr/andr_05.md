# 第 1 节 Hello World

## 一、实验简介

欢迎打开 Android 开发的大门！从本实验开始，你将学习到 Android 开发的一些基础知识，了解关于 Android 系统的发展历程和架构，并动手建立自己的第一个 Hello World 程序。

### 1\. 知识点

*   Android 系统的发展历程
*   Android 系统的架构
*   Android 开发环境的配置
*   实现 Hello World 应用

### 2\. 所需知识

在学习 Android 开发知识之前，建议你先掌握 Java 相关的开发基础。

若你对 Java 还不是非常熟悉，你可以根据自己的实际情况，学习实验楼开设的以下 Java 系列课程：

*   [Java 编程语言（新版）](https://www.shiyanlou.com/courses/18)
*   [Java 进阶之设计模式](https://www.shiyanlou.com/courses/100)
*   [J2SE 核心开发实战](https://www.shiyanlou.com/courses/111)

此外，对于未来 Android 学习过程中可能会涉及到的知识点，以下课程也推荐你进行学习：

*   [J2SE 网络通信实践](https://www.shiyanlou.com/courses/362)
*   [SQL 基础课程](https://www.shiyanlou.com/courses/9)

## 二、认识 Android 系统及生态圈

### 1\. Android 发展历程

Android 是一种基于[Linux](http://baike.baidu.com/view/1634.htm)的自由及开放源代码的操作系统，主要使用于移动设备，如智能手机和平板电脑，由 Google 公司和开放手机联盟领导及开发。尚未有统一中文名称，在我国大陆地区的开发圈内多被称为“安卓”。

![此处输入图片的描述](img/1eb25a16eb1d8a4b169f7c5a7353cbf5.jpg)

它大致经历了下面的发展历程：

> - Android 操作系统最初由 Andy Rubin 开发，主要支持的设备为手机。 > - 2005 年 8 月由 Google 收购注资。 > - 2007 年 11 月，Google 与 84 家硬件制造商、软件开发商及电信营运商组建开放手机联盟共同研发改良 Android 系统。随后 Google 以 Apache 开源许可证的授权方式，发布了 Android 的源代码。 > - 第一部 Android 智能手机发布于 2008 年 10 月。Android 逐渐扩展到平板电脑及其他领域上，如电视、数码相机、游戏机等。 > - 2011 年第一季度，Android 在全球的市场份额首次超过塞班系统，跃居全球第一。 > - 2013 年第四季度，Android 平台手机的全球市场份额已经达到 78.1%。 > - 2013 年 9 月 24 日谷歌开发的操作系统 Android 迎来了 5 岁生日，全世界采用这款系统的设备数量已经达到 10 亿台。

截止到本课程发布之日，Android 已经发展到了自己的 6.0 版本（`Android M`）。

### 2\. Android 生态圈

Android 平台具有高度的开放性，开发的平台允许任何移动终端厂商加入到 Android 联盟中来。显著的开放性可以使其拥有更多的开发者，随着用户和应用的日益丰富，一个崭新的平台也将很快走向成熟。开发性对于 Android 的发展而言，有利于积累人气，这里的人气包括消费者和厂商，而对于消费者来讲，最大的受益正是丰富的软件资源。开放的平台也会带来更大竞争，如此一来，消费者将可以用更低的价位购得心仪的手机。但这也导致了 Android 生态圈碎片化严重的问题。

下图展示了近年来 Android 在智能手机操作系统市场占有率及对比（图据 statista）： ![此处输入图片的描述](img/ade48beaee70cbbb33ce3782dddf9704.jpg)

推荐一篇发表于 2014 年的文章《[移动 5 年，Android 生态系统的演进](http://www.csdn.net/article/2014-10-22/2822247)》，在这里你能了解到更多关于 Android 生态圈的信息。

### 3\. Android 的系统架构

Android 的系统架构是分层设计的，从底层到上层共有四层，分别是 Linux 内核、系统库、应用框架层和应用程序程序层，如下图所示。

![img](img/59f5abf60c2f9c144e15fe7c3222fd24.jpg)

*   **linux 内核层**

    1.  硬件和其他软件堆层之间的一个抽象隔离层；
    2.  提供安全机制、内存管理、进程管理、网络协议堆栈和驱动程序等。

    ![img](img/bd16668469ffe78bdaa0ab7e410d9acb.jpg)

*   **系统库和 Android 运行时的环境层**

    1.  本地库：主要提供一组基于 C/C++的函数库；
    2.  核心库：提供 Android 特有的函数和 Java 语言函数；
    3.  虚拟机：实现 Linux 内核的线程管理和底层内存管理；

    ![img](img/33085ad0dac429206fccaf0bb7fb3ed7.jpg)

*   **框架层**

    提供 Android 平台基本的管理功能和组件重用机制。

![img](img/0bb4b352ab881266ec4363024a248bf9.jpg)

*   **应用层**

    提供一系列的核心应用程序，包括电子邮件客户端、浏览器、通讯录和日历等。

![img](img/da2b2da709e47777d3d2405b6de661ff.jpg)

## 三、开发环境配置

俗话说：工欲善其事，必先利其器。要想进行 Android 开发，首先需要配置好相应的开发环境。

**注：实验楼已经为你配置好开发环境，此过程仅供学习者参考。**

### 1\. 下载并配置 Java JDK

首先你需要确定自己的 Linux 版本是 32 位还是 64 位的，如果不是很清楚，可以通过在终端输入下列命令来查看：

```java
uname -a 
```

如果显示的系统版本中出现`x86_64`字样，则说明系统是 64 位的。若出现`i386`或`i686`字样，则说明系统是 32 位的。

确定了系统版本后，在[Oracle 官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)可以下载到最新的 JDK（Java Development Kit）。你也可以根据自己的实际需要下载历史版本。推荐下载 1.7 系列。

下载好后的压缩包通常名称如下：

```java
jdk-7u15-linux-x64.tar.gz（64 位）
jdk-7u15-linux-i586.tar.gz（32 位） 
```

你需要通过`cd`命令切换到下载的目录，输入命令解压：

```java
sudo tar -xzf jdk-8u65-linux-x64.tar.gz 
```

然后将解压的文件复制到`/usr/lib/jvm`目录下，如果此目录不存在就使用如下命令建立该目录。

新建目录的命令如下：

```java
sudo mkdir /usr/lib/jvm 
```

在`jdk1.8.0\_65`文件目录下输入下面的命令（`jdk1.8.0\_65`是解压后文件夹的名称，请根据不同版本的 JDK 更改,下面 JAVA_HOME 和配置默认版本时也进行相应更改）。

```java
sudo mv jdk1.8.0_25 /usr/lib/jvm 
```

文件下载完成后，接下来便是配置环境变量，在终端输入以下命令：

```java
gedit ~/.bashrc 
```

然后在文件末尾追加以下内容：

```java
export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_65    
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH 
```

保存后退出 gedit，上述内容设置了 java 环境的环境变量。

### 2\. 下载并安装 Android Studio

鉴于 2015 年 9 月发生的“[XcodeGhost](http://baike.baidu.com/item/XcodeGhost)”事件，我们强烈建议每位开发者（特别是初学者）一定要从正规途径下载和使用各类开发工具。

Android Studio 的下载可以在 Android 开发者官网找到（由于众所周知的原因，中国大陆暂时无法直接访问），如需访问，请点击[这里](http://developer.android.com/sdk/index.html)。

选择`Develop Tools`，下载对应平台的 linux 版本的 Android Studio，下载的文件名类似于`android-studio-ide-135.1641136-linux.zip`。

下载完成后，你可以通过[MD5](http://baike.baidu.com/view/274197.htm)或[SHA1](http://baike.baidu.com/view/1228622.htm)校验来确保下载文件的完整性，具体步骤你可以通过搜索相关文档来学习，此处不再赘述。

接下来，可以直接在图形界面来解压 zip 文件，解压后文件夹名为`android-studio`。

将解压好后的文件夹移动到`/opt`目录下：

```java
sudo mv android-studio /opt/ 
```

进入`/opt/android-studio/bin/`文件夹：

```java
cd /opt/android-studio/bin/ 
```

运行`Android Studio`：

```java
sudo ./studio.sh 
```

如果是第一次安装，第一次进入选择`I do not have a previous version of Android Studio ...`

![img](img/0686e5d999b46e2411846b6bef79c538.jpg)

此时会出现启动画面：

![img](img/f1dbdea8d038ead41898deb7c30dbe53.jpg)

接下来进入组件安装部分`Setup Wizard - Welcome`， 点击`next`按钮开始安装。

![img](img/63ec985cb441ee20b1e51988b81da500.jpg)

选择`Custom`，点击`next`按钮： ![img](img/149e4c19cfc3099bc80a0ec33844b33a.jpg)

这儿的`Android SDK Location`建议修改为`/home/shiyanlou/Android/Sdk`（shiyanlou 为用户名）： ![img](img/eeb33a5ad5eab8c53d414b4b2aa18478.jpg)

进入`License Agreement`，选中`accept`复选框，然后点击`next`按钮。

![img](img/d769f1124d4f57e783f53a665a9b0f8b.jpg)

之后就等待安装过程，直到成功（中途需要连接 google 服务器拉取信息，可能需要“科学上网”）。

**你现在所使用的实验楼的环境已经为你安装好 Android Studio，可以直接点击桌面上的图标打开。**

## 四、Hello World

下面，我们通过一个快速的例子创建 Android 世界中的第一个示例程序——“Hello Word”。

双击桌面左上角的`AndroidStudio`图标打开它。

![此处输入图片的描述](img/2840cefb1c4e6371bb415f5c7ee9a76b.jpg)

在下图所示的 loading 画面中等候开发环境启动完成。

![此处输入图片的描述](img/f8a80d19105bdb6b34fe66807fad2dd8.jpg)

当 Android Studio 的欢迎页（Welcome to Android Studio）出来时，点击第一项`Start a new Android Studio project`来创建一个新的 Android 项目：

![此处输入图片的描述](img/d302ea53f90268b9f4fdb6c93bd28cd0.jpg)

在新弹出的`Create New Project`对话框中，填入项目相关的信息：

*   `Application name`（应用名称）中填写`HelloWorld`。
*   `Company Domain`（公司域名）中填写`shiyanlou.com`。

然后点击右下角蓝色的`Next`按钮进入下一步。

![此处输入图片的描述](img/a532528b2ef5356c6ca755d7ec294aa8.jpg)

在选择目标设备的最小 SDK 版本时，请选择`API 22: Android 5.1(Lollipop)`一项，以后的项目也都请选择这一版本。

![此处输入图片的描述](img/5334b60e9146869a84e25da91f07bac8.jpg)

选择完成后点击下一步`Next`。

为应用添加一个 Activity，此处请选择`Blank Activity`，然后进入下一步。

![此处输入图片的描述](img/39e3600dd3dce1194e32052262185663.jpg)

最后是为刚刚选择的这个 Activity 设置名称和布局文件名称，此处可以不做修改，直接使用默认值即可。点击`Finish`按钮完成创建。

![此处输入图片的描述](img/a87862655a98dea5d1cd4d0f2292ecfc.jpg)

对于这些步骤中每个设置的含义，请在本文末尾的作业中找到对应的项目课来学习。

等待开发环境设置完成，就会显示一个含有丰富的开发工具的编辑器。

遇到的`Tips of the Day`窗口可以直接关闭。

![此处输入图片的描述](img/bccfb56b5e6149f530490aa8ae20d167.jpg)

等待 1~2 分钟，项目相关文件配置好后，便会出现如下图所示的 UI 设计窗口。暂时不用关心这些复杂的选项都是什么，在后续的课程会学到它们。

![此处输入图片的描述](img/a796978cbfb210414addf4a773e1243c.jpg)

接下来，在 Android Studio 的主界面上方的工具栏中，你可以看到一个名为 AVD Manager 的按钮，点击它你就能打开 Android 虚拟设备管理器（AVD: Android Virtual Device）。

![此处输入图片的描述](img/19efbcede29b05b05f46be9213793fb4.jpg)

此时没有任何虚拟设备，点击`Create a virtual device`按钮来创建一台模拟器。

![此处输入图片的描述](img/e8356150edaf99e958b11c24dfe4a4a0.jpg)

创建模拟器的第一步是选择硬件，选择`Nexus S`这台虚拟设备。

![此处输入图片的描述](img/d586dbbaa28dc9a0ef777f8210a2ce01.jpg)

点击右下角的`Next`按钮，进入到系统镜像的选择，请选择第一个`Android 5.1.1`的镜像。

![此处输入图片的描述](img/4f3327cec927ca090ee3436955597165.jpg)

接着，点击右下角的`Next`按钮，进入到确认配置的一步。

![此处输入图片的描述](img/81aeba5d49c646a9084281ed427e1b24.jpg)

在这里，你可以设置模拟器的名称，此处使用默认值即可。

**需要特别注意的是**：在实验楼的环境中，我们建议你关闭`Use Host GPU`这一选项。而在你自己的电脑上则建议将其打开。

点击`Finish`按钮完成模拟器的创建，如果窗口没有响应，则需要等待约 1 分钟。

创建成功后，点击右侧的绿色箭头按钮来启动这台模拟器。

![此处输入图片的描述](img/d76b415ecbbf8e5410f9acd180d8c46b.jpg)

经过测算，在实验楼提供的实验环境中，Android 模拟器的启动时间在**6-8 分钟**左右。在此期间，你不必专门等候，耐心地阅读左侧的课程文档，并在 Android Studio 的编辑环境中跟着编写代码。在你创建的项目快要结束编写的时候，模拟器自然就启动好了。此时，你再编译并运行对应的项目即可。

遇到如下窗口可以直接关闭。

![此处输入图片的描述](img/ecf123b09ef8c47abae1a5425f1cb8a8.jpg)

下面就是 Android 模拟器的启动画面，这个画面要持续**6-8 分钟**左右。

![此处输入图片的描述](img/4deeace1ad6ff28999729584a712b3e0.jpg)

我们先回到 Android Studio 的编辑环境，看一下项目的目录树。一个完整的项目通常由下面这些部分组成。

![此处输入图片的描述](img/acdf98175466682ab0c9fa626d80a5e3.jpg)

1.  `build`：该目录包含了自动生成的文件，这些文件包括了编译设置项、R 类等。
2.  `libs`：该目录包含了开发 Android 应用所需要的库文件。
3.  `src`：该目录存放了应用的源代码`.java`文件。默认情况下，它包含了`MainActivity.java`文件，这个源代码 j 有一部分是执行了你点击应用图标时启动应用所需要功能。
4.  `res`：该目录存放了所有的资源文件。
5.  `drawable`：该目录存放了项目的`drawable`对象和一些图片资源。
6.  `layout`：该目录存放了各个界面的布局文件。
7.  `menu`：该目录存放了应用中设计的菜单对象。
8.  `mipmap`：该目录存放了应用的主要图片资源。
9.  `values`：该目录存放了字符串、颜色等定义的资源集合。
10.  `AndroidManifest.xml`：该文件是描述应用基础特性的文件，定义了每个组件。

模拟器启动完成后，画面如下：

![此处输入图片的描述](img/b7c23b4bc067f63082402246ce667c71.jpg)

回到 Android Studio 中，点击 Android Studio 上方的运行按钮。

![此处输入图片的描述](img/3695906825025404c7e199d852c4b4c5.jpg)

此时项目进入编译状态，编译完成后会弹出`Choose Device`选择设备对话框，点击下方`OK`按钮。

![此处输入图片的描述](img/379fe17421589a044df4d4e008f7a46d.jpg)

切换到模拟器，你就会看到你的第一个 Hello World 应用。

![此处输入图片的描述](img/01ed33b99f607489a777e11135f8e598.jpg)

恭喜你完成了上述步骤。要关闭项目时，请记得保存。你可以将代码提交至你的代码库。

## 四、实验总结

在本节课程我们主要完成了 Android 的一个 Hello World 项目的创建，并且了解了一个项目的构成。这将是你学习 Android 应用开发的第一步，请保持专注，投入更多精力学习后续的课程。

除了本节实验，实验楼也为你准备了丰富的 Android 课程，你可以[点击此链接](https://www.shiyanlou.com/courses/?course_type=all&tag=Android)来查看。

## 五、作业

请在本节课程学习结束后，继续学习项目课《[Android Studio 项目创建和模拟器配置](https://www.shiyanlou.com/courses/417)》。

## 参考文档

*   http://www.cnblogs.com/royenhome/archive/2010/04/24/1719293.html