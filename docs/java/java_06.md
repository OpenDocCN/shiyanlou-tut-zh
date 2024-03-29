# 第 6 节 输入输出

## 一、实验简介

还记得一次又一次地在程序中输入和输出数据吗？如果程序不能够读写数据，那么程序的功能会受到极大的限制。本实验就将为你介绍 Java 中的输入输出体系，你将有机会自己动手体会一下读写文件的过程。

本章知识点：

*   Java 的输入输出机制
*   字节流和字符流
*   文件的读取和写入

## 二、Java 中的“流”

在大多数程序中，都需要对输入输出进行处理。例如我们在前面各个章节中需要获取用户从键盘上的输入，需要在控制台输出结果等等。除此之外还有从文件中读取数据，向文件中写入数据等等。在 Java 中，我们把这些不同类型的输入输出源抽象地称为`流`，也就是`Stream`；在里面输入输出的数据则称为`数据流`（`Data Stream`），它们通常具有统一的接口。

于是我们得到了数据流的定义： >一个 Java I/O 对象叫做数据流。读取数据的对象叫做输入流，写入数据的对象叫做输出流。

针对其面向的不同角度，我们大致可以将流分为下面几种类型：

*   按照数据流的方向不同分为`输入流`和`输出流`。这种分类不是绝对的，例如在向一个文件写入数据时，它就是输出流；而在读取数据时，它就是输入流。
*   按照处理数据的单位不同分为`字节流`和`字符流`。
*   按照功能的不同分为`节点流`和`处理流`。

需要特别说明，节点流是从特定的数据节点（文件、数据库、内存等）读写数据；处理流是连接在已有的流上，通过对数据的处理为程序提供更多功能。

在 Java 环境中，`java.io`包提供了大多数的类和接口来实现输入输出管理。一些标准的输入输出则来自`java.lang`包中的类，但它们都是继承自`java.io`中的类。我们可以将输入流理解为数据的提供者，而把输出流理解为数据的接收者。在最初的时候，这些派生自抽象类`InputStream`和`OutputStream`的输入输出类是面向 8 位的字节流的。但为了支持国际化，又引入了派生自抽象类`Reader`和`Writer`的类层次，用于读写一些双字节的`Unicode`字符。

**因此，在学习 java 的输入输出上，我们希望你以字节流和字符流作为区分来学习。**

如果需要概括一下，则可以得到下面的定义： >- 字节流：表示以字节为单位从 stream 中读取或往 stream 中写入信息。通常用来读取二进制数据。

*   字符流：以 Unicode 字符为单位从 stream 中读取或往 stream 中写入信息。

按照这样的定义，Java 中流的层级结构可以通过下图来表示：

![流的层级结构](img/document-uid85931labid1097timestamp1436413740400.jpg)

图中蓝色的部分均为抽象类，而绿色的部分则为派生类，是可以直接使用的。

而下图简要说明了字节流和字符流的区别，你也可以[进一步了解字节流与字符流的区别](http://blog.csdn.net/cynhafa/article/details/6882061)。

![字节流与字符流的区别](img/document-uid85931labid1097timestamp1436412496895.jpg)

我们知道 Java 是一门面向对象的语言，所以为了能够永久地保存对象的状态，`java.io`包还以字节流为基础，通过实现`ObjectInput`和`ObjectOutput`接口提供了`对象流`。在此仅作引入，你可以通过查阅 API 手册来详细了解它们。

## 三、读写一个文件

### 1\. 使用 FileInputStream 读取文件

我们在之前的实验中用到了`java.util.Scanner`来读取系统的标准输入流`System.in`。这一次，我们来动手学习如何使用`FileInputStream`来读取文件。

开始编码之前，请在实验环境完成新建文本文件的操作：

>你也可以通过 linux 命令来完成该操作，想一想需要哪些命令？

首先右键单击桌面空白处，创建一个文件。

![新建一个文件](img/document-uid85931labid1097timestamp1436421897689.jpg)

在弹出的对话框中输入文件名`shiyanlou.txt`，并点击`创建`按钮。

![输入文件名](img/document-uid85931labid1097timestamp1436422030558.jpg)

这样你在桌面上就创建好了一个我们需要的文本文件了。

![文本文件](img/document-uid85931labid1097timestamp1436422181468.jpg)

双击这个文件的图标，用 gedit 编辑器打开此文件，在其中输入内容`a`，然后保存并关闭。

![编辑文件](img/document-uid85931labid1097timestamp1436422386213.jpg)

新建文件的过程就已经完成了。

在上一个实验中我们已经知道：在所有的输入输出过程中，都可能发生一些`异常`。所以我们得用`try`和`catch`结构来进行错误处理。

请接着在 Eclipse 中新建项目`FileIO`，创建包`com.shiyanlou.course`和包含`main()`方法的类`ReadFileTest`。

字节流中读取文件的方式主要有两种方法：

>- `int read()`，从此输入流中读取一个数据字节。返回：下一个数据字节。如果已到达文件末尾，则返回 -1。

*   `int read(byte[] b)`，从此输入流中将最多 `b.length` 个字节的数据读入一个 byte 数组中。返回：读入缓冲区的字节总数，如果因为已经到达文件末尾而没有更多的数据，则返回 -1。

这里使用前者来读取，代码片段如下，我们在注释中继续进行说明。

```java
package com.shiyanlou.course;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

public class ReadFileTest {

    public static void main(String[] args) {
        try {

            FileInputStream file = new FileInputStream("/home/shiyanlou/Desktop/shiyanlou.txt");
            //声明一个文件输入流 file，并指明该文件在系统中的路径以方便定位

            int data = 0;
            //声明一个整型变量用于存放读取的数据

            while ((data = file.read()) != -1) {
                //在 while 循环中使用 read()方法持续读取 file，数据赋到 data 中
                //如果读取失败或者结束，则将返回-1，这个特殊的返回值可以作为读取结束的标识

                System.out.println(data);
                //输出读取到数据
            }

            file.close();
            //文件读取结束之后一定要通过 close()方法关闭这个流对象

        } catch (FileNotFoundException e) {
            //如果文件未找到，则捕获该异常
            e.printStackTrace();

        } catch (IOException e) {
            //如果输入输出时发生错误，则捕获该异常
            e.printStackTrace();

        }
    }
} 
```

检查一下代码，点击编译并运行，可以在控制台看到读取的文件数据。

![读取的文件数据](img/document-uid85931labid1097timestamp1436423617896.jpg)

为什么我们在文本文件中输入的是`a`，而输出的结果是`97`和`10`呢？

这是因为文件是 GBK 编码，而文件类型为 ANSI，`a`的 ANSI 编码恰好是`97`，`10`则代表了换行符。

### 2\. 读取文件的其他方式

如果你想使用`BufferedInputStream`来读取文件，只需要在创建对象时使用下面的格式即可：

```java
BufferedInputStream file = new BufferedInputStream(new FileInputStream("文件的路径")); 
```

其他的使用方法与`FileInputStream`一样，此处不再赘述，你不妨动手试试。

上述方法都是对于字节流的，那么对于字符流呢？

查阅 API 文档可以知道，字节流使用的数组是字节数组`byte[] bt`，而字符流使用的数组是字符数组`char[] chs`。

将上个程序的`try`语句块中的程序改为下面这些，就可以使用字符流的方式来读取文件。

```java
FileReader file = new FileReader("/home/shiyanlou/Desktop/shiyanlou.txt");
//声明一个文件输入流 file，并指明该文件在系统中的路径以方便定位

int data = 0;
//声明一个整型变量用于存放读取的数据

while((data=file.read())!=-1){
    //在 while 循环中使用 read()方法持续读取 file，数据赋到 data 中
    //如果读取失败或者结束，则将返回-1，这个特殊的返回值可以作为读取结束的标识

    System.out.print((char)data);
    //输出读取到数据
}

file.close();
//一定要记得读取结束后要关闭文件 
```

这时候你编译并运行，看到的输出结果就不再是 ASCI 编码了，而是`a`：

![读取的文件数据](img/document-uid85931labid1097timestamp1436426311456.jpg)

这是因为我们在输出前进行了强制类型转换。

同样，如果你想使用`BufferedReader`来读取文件，则按照下面的格式来创建对象：

```java
BufferedReader file = new BufferedReader(new FileReader(“文件的路径”)); 
```

`BufferedReader`有一个特有方法`readLine();`，它在读到文件末尾时返回`null`。

### 3\. 使用 FileOutputStream 写入文件

在熟悉了读取文件的过程后，写入文件就显得比较简单了。我们动手来实践一下。

在 Eclipse 中，请继续在同一个项目`FileIO`的同个`com.shiyanlou.course`包下面创建类`WriteFileTest`。

代码片段如下：

```java
package com.shiyanlou.course;

import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

public class WriteFileTest {

    public static void main(String[] args) {
        try {

            String path = "/home/shiyanlou/Desktop/newfile.txt";
            //这一次我们提前声明一个字符串用于存放待写入文件的路径

            String content = "shiyanlou";
            //声明一个字符串存放待写入的内容

            FileOutputStream file = new FileOutputStream(path);
            //声明一个文件输出流对象，并指定路径

            file.write(content.getBytes());    
            //通过 write()方法将数据写入到文件中
            //getBytes()方法是将字符串转化为二进制序列

            file.close();
            //记得关闭文件

            System.out.println("File created successfully.");
            //提示用户创建成功

        } catch (FileNotFoundException e) {

            e.printStackTrace();
        } catch (IOException e) {

            e.printStackTrace();
        }
    }
} 
```

编译并运行，在控制台可以看到写入成功的提示。

![写入成功](img/document-uid85931labid1097timestamp1436427164010.jpg)

在桌面上，也能看到这个文件。

![写入的文件](img/document-uid85931labid1097timestamp1436427210079.jpg)

双击打开这个文件，可以看到我们刚刚写入的内容。

![写入的内容](img/document-uid85931labid1097timestamp1436427229545.jpg)

## 四、实验总结

本章详细介绍了 Java 中输入输出的机制，展示了这个体系下的结构，并结合了两个主要的实验来体会输入输出的用法。但是这是远远不够的，对于一些较为复杂的输入输出方法，希望你在本实验的基础上进行更加深入的学习。

## 五、作业

请查阅 API 文档，使用`BufferedWriter`写一个名为`bfFile.txt`文件，内容为`My First Buffered File.`。

## 参考文档

*   [CSDN 博文 - Java 7 之传统 I/O 第 1 篇- 输入输出流基础框架](http://blog.csdn.net/mazhimazh/article/details/17839091)
*   [Java SE 官方 API 文档 - java.io.FileInputStream](http://docs.oracle.com/javase/7/docs/api/java/io/FileInputStream.html)
*   [Java SE 官方 API 文档 - java.io.FileOutputStream](http://docs.oracle.com/javase/7/docs/api/java/io/FileOutputStream.html)
*   [Java SE 官方 API 文档 - java.io.BufferedInputStream](http://docs.oracle.com/javase/7/docs/api/java/io/BufferedInputStream.html)
*   [Java SE 官方 API 文档 - java.io.BufferedOutputStream](http://docs.oracle.com/javase/7/docs/api/java/io/BufferedOutputStream.html)
*   [Java SE 官方 API 文档 - java.io.FileReader](http://docs.oracle.com/javase/7/docs/api/java/io/FileReader.html)
*   [Java SE 官方 API 文档 - java.io.FileWriter](http://docs.oracle.com/javase/7/docs/api/java/io/Filewriter.html)
*   [Java SE 官方 API 文档 - java.io.BufferedReader](http://docs.oracle.com/javase/7/docs/api/java/io/BufferedReader.html)
*   [Java SE 官方 API 文档 - java.io.BufferedWriter](http://docs.oracle.com/javase/7/docs/api/java/io/BufferedWriter.html)