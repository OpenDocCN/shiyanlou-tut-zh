# 第 5 节 java 字符流

* * *

## 实验介绍

本实验将学习字符流中的基本类和缓存流，以及字符流的其它类

## 一、基类

　　java.io 包中专门用于字符流处理的类，是以 Reader 和 Writer 为基础派生的一系列类。

　　同类 InputStream 和 OutputStream 一样，Reader 和 Writer 也是抽象类，只提供了一系列用于字符流处理的接口。它们的方法与类 InputStream 和 OutputStream 类似，只不过其中的参数换成字符或字符数组。

　　我们先来看一看基类 Reader 的方法，其用法与作用都与 InputStream 和 OutputStream，就不做过多的说明了。

```java
| 方法 | 返回值 |
|------|--------|
| close() | void |
| mark (int readAheadLimit) | void |
| markSupported() | boolean |
| read() | int |
| read(char[] cbuf, int off,int len) | int |
| ready() | boolean | 
| reset() | void |
| skip(long n) | long |
```

Writer 的方法：

```java
| 方法 | 返回值 |
|------|--------|
| close() | void |
| flush() | void |
| write(char[] cbuf) | void |
| write(char[] cbuf, int off,int len) | void |
| write(int c) | void | 
| write(String str) | void |
| write(String str, int off, int len) | void |
```

　　InputStreamReader 和 OutputStreamWriter 是 java.io 包中用于处理字符流的最基本的类，用来在字符流和字符流之间作为中介：从字节输入流读入字节，并按编码规范转换为字符；往字节输出流写字符时先将字符按编码规范转换为字节。使用这两者进行字符处理时，在构造方法中应指定一定的平台规范，以便把以字节方式表示的流转换为特定平台上的字符表示。

```java
InputStreamReader(InputStream in); //缺省规范说明

//指定规范 enc
InputStreamReader(InputStream in, String enc);

OutputStreamReader(OutputStream out); //缺省规范说明

//指定规范 enc
OutputStreamReader(OutputStream out, String enc); 
```

　　如果读取的字符流不是来自本地时（比如网上某处与本地编码方式不同的机器），那么在构造字符输入流时就不能简单地使用缺省编码规范，而应该指定一种统一的编码规范“ISO 8859_1”，这是一种映射到 ASCCII 码的编码方式，能够在不同平台之间正确转换字符。

```java
InputStreamReader ir = new InputStreamReader(is,"8859_1"); 
```

## 二、缓存流

　　同样的，为了提高字符流处理的效率，java.io 中也提供了缓冲流 BufferedReader 和 BufferedWrite。其构造方法与 BufferedInputStream 和 BufferedOutPutStream 相类似。另外，除了 read() 和 write() 方法外，它还提供了整行字符处理方法：

1.  public String readLine()：BufferedReader 的方法，从输入流中读取一行字符，行结束标志`\n`、`\r`或者两者一起（这是更具系统而定的）
2.  public void newLine()：BufferedWriter 的方法，向输出流中写入一个行结束标志，它不是简单地换行符`\n`或`\r`，而是系统定义的行隔离标志（line separator）。

看一看例子吧：

```java
package com.shiyanlou;

import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStreamReader;

public class FileToUnicode {
    public static void main(String args[]) {
        try {
            FileInputStream fis = new FileInputStream("file1.txt");
            InputStreamReader dis = new InputStreamReader(fis);
            BufferedReader reader = new BufferedReader(dis);
            String s;
            //每次读取一行，当改行为空时结束
            while((s = reader.readLine()) != null){
                System.out.println("read:" + s);
            }
            dis.close();
        }
        catch(IOException e) {
            System.out.println(e);
        }
    }
} 
```

## 三、其它字符流类

　　在这里我们就列举一下有哪些类，具体的木木就不再讲解了。

1.  对字符数组进行处理： CharArrayReader、CharArrayWrite

2.  对文本文件进行处理：FileReader、FileWriter

3.  对字符串进行处理：StringReader、StringWriter

4.  过滤字符流：FilterReader、FileterWriter

5.  管道字符流：PipedReader、PipedWriter

6.  行处理字符流：LineNumberReader

7.  打印字符流：PrintWriter

    　　类有千万，方法更是不计其数，所以没有必要去掌握所有的方法和类，只需要知道常见常用的就行了，而大多数的类和方法，希望大家有一个印象，当我们在实际开发的时间，能够想到，并且借助其他工具去查询我们需要的方法的应用方式就可以了。

## 四、作业

　　结合我们上一个实验所讲的内容，进行一下比较，找一找它们之间的相同和不同之处，然后分类整理一下，将上一节课的代码用这一节课的方法实现一下，感受一下它们的区别。