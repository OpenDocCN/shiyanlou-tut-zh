# 第 2 节 字符串与包装类

## 一、实验简介

在本章，我们将学习一些用于处理字符串的 API 以及包装类的相关知识。

本章知识点

*   字符串 API
*   包装类及其应用

## 二、认识字符串类

### 1\. 什么是字符串类

Java 字符串类（java.lang.String）是 Java 中使用最多的类，也是最为特殊的一个类，很多时候，我们对它既熟悉又陌生。

首先你要知道什么是字符串： >字符串（String）是由数字、字母、下划线组成的一串字符，是编程语言中表示文本的数据类型。

你可以[查看更多关于字符串的知识](http://baike.baidu.com/link?url=yqDEZns6qOj21Eh-JTJ4Wk0txwUKJYhApEHD-CCAK85LTdhGmC4vxt-RtXr9dZw0so-PvCOIQeW7KIVIXvS1sK)。

操作字符串的常用方法有复制、计算其长度、查找字符串中的某些字符等。

然而，Java 标准库中的 String 类包含很多的方法，要想记住所有的类和方法是一件不太可能的事情。最好的办法就是养成随时查阅 API 文档的习惯。在最后的参考文献中我们为你提供了官方 API 文档的链接，你可以在其中详细查看每一个操作字符串的方法。

**查阅 API 文档对于未来章节的学习也十分重要，请保持这个习惯。**

### 2\. 用 String 类中的自带方法操作字符串

在 Eclipse 中创建项目`StringUtil`，新建一个`com.shiyanlou.course`包，并创建`StringTools`类。

这里会用到`trim()`、`substring()`、`equals()`和`length()`方法，

*   `String trim()`，该方法返回一个新字符串，新的字符串删除了原始串中首尾的空格。
*   `String substring(int beginIndex, int endIndex)`，该方法返回一个新字符串，新的字符串包含原来字符串中从`beginIndex`到`endIndex-1`的所有内容。
*   `boolean equals(Object other)`，该方法用于比较两个字符串，如果原字符串与忽略大小写的情况下与`other`相等，则返回`true`。
*   `int length()`，该方法返回一个字符串的长度。

这个项目首先需要获取来自用户的输入，然后去除输入的字符串中首尾多余的空格，再与字符串`shiyanlou`相比较，如果两个字符串相同，则截取该字符串前 6 位部分作为新的字符串。

主要的代码如下：

**同样，你不必在实验环境中输入这些中文注释。**

```java
package com.shiyanlou.course;

import java.util.Scanner;

public class StringTools {

    public static void main(String[] args) {
        String a = new String();
        String b = new String();
        String c = new String();
        //创建 3 个字符串对象

        Scanner scan = new Scanner(System.in);// 创建扫描器
        System.out.println("Please input a sentence with 10 more letters include space(s) : ");
        // 获得用户输入的包含空格的字符串

        a = scan.nextLine();
        //将键盘上的输入赋到字符串 a 中

        b = a.trim();
        //将 a 中头部和尾部的空格去掉，赋值到 b 中

        if(b.equals("shiyanlou"))
            c = b.substring(0, 6);
        else {
            c = "";
        }
        //如果 b 字符串为 shiyanlou，则取其第 0 个到第 6 个字符作为子串，赋值到 c 中
        //如果 b 字符串不为 shiyanlou，则 c 的值为空

        System.out.println("a :"+a);
        System.out.println("The length of a is :"+a.length());
        System.out.println("b :"+b);
        System.out.println("The length of b is :"+b.length());
        System.out.println("c :"+c);
        System.out.println("The length of c is :"+c.length());
        //分别输出 a、b、c 三个字符串及其长度
    }

} 
```

点击编译并运行，在控制台先输入 5 个空格，紧接着输入`shiyanlou`，再输入 4 个空格并回车，可以看到输出结果如下：

![字符串](img/document-uid85931labid1093timestamp1436163201062.jpg)

你可能想问：我们在比较字符串的时候为什么不用`==`而用`equals()`呢？ 你可以[点此了解更多关于"=="和 equals 的信息](http://www.cnblogs.com/zhxhdean/archive/2011/03/25/1995431.html)。

## 三、认识包装类

### 1\. 什么是包装类

我们知道，Java 语言是面向对象的，但 Java 中的基本数据类型却不是面向对象的。这在实际使用过程中造成了诸多不便。为了解决此问题，Java 语言的设计者们在设计类时，为每种基本数据类型也设计了一个对应的类。这八个和基本数据类型对应的类统称为`包装类`(Wrapper Class)，也称为外覆类或数据类型类。

包装类均位于 java.lang 包中，下表给出了包装类和基本数据类型的对应关系：

```java
| 基本数据类型 | 对应包装类    |
| ------------ |:-------------:|
| byte         | Byte          |
| boolean      | Boolean       |
| short        | Short         |
| char         | Character     |
| int          | Integer       |
| long         | Long          |
| float        | Float         |
| double       | Double        |
```

包装类的用途主要包含两种：

*   作为和基本数据类型对应的类类型存在，方便涉及到对象的操作。
*   包含每种基本数据类型的相关属性如最大值、最小值等，以及相关的操作方法。

下面我们通过一个例子来进一步认识包装类。

### 2\. 比较 Double 类型

首先你应该知道`double`和`Double`的区别，前者是基本的数据类型，后者是引用类型，即包装类。我们可以直接使用普通运算符“`==`”对基本数据类型进行比较，但如果将“`==`”用于比较引用类型的话，只会判断其内存地址是否相同，并且结果通常是否定的。

在 Eclipse 中创建项目`DoubleCompare`，新建一个`com.shiyanlou.course`包，并创建`DoubleCompare`类。

在该类的`main()`方法中，定义两个含有数字的字符串并将其转换为 Double 类型的数据。

```java
String s_1 = "123.321";
String s_2 = "567.765";
//显然，这里的 123.321 和 567.765 都不是数值，而是“一句话”
//你也可以自定义这些数字

Double num_1 = Double.parseDouble(s_1);
Double num_2 = Double.parseDouble(s_2);
//定义了 Double 类型的数据，并将字符串转换为双精度浮点数赋予其值 
```

上面的`parseDouble(String s)`就是`Double`类中自带的将字符串中的数字转换为`double`类型的方法。

我们再将其输出。

```java
System.out.println("number 1: " + num_1);
System.out.println("number 2: " + num_2); 
```

然后我们来比较这两个数据，主要代码如下：

```java
switch (num_1.compareTo(num_2)) {
    case -1:
        System.out.println("number 1 is smaller than number 2");
        break;
    case 0:
        System.out.println("number 1 is equal to number 2");
        break;
    case 1:
        System.out.println("number 1 is bigger than number 2");
        break;
} 
```

相信你能够理解这个分支语句的含义。需要特别说明的是，`compareTo(Double anotherDouble)`也是 Double 类提供的一个方法，用于比较两个 Double 类型数据的大小，返回值为`int`类型，`-1`、`0`、`1`分别代表小于、等于和大于。

该案例的完整代码如下图所示：

![完整代码](img/document-uid85931labid1093timestamp1435821447812.jpg)

点击编译并运行，可以在控制台看到输出结果：

![结果](img/document-uid85931labid1093timestamp1435821397383.jpg)

## 四、实验总结

在本章我们主要学习了字符串的定义和常用的生成子串、去除空格、比较等方法，和包装类的定义及其主要的操作方法。参考文档中为你提供了更多关于它们的知识，不妨试试字符串拼接等本课程还未用到的方法。在实验楼学习结束后，也不要忘记多看看这些 API 文档，多动手写写代码，天道酬勤！

## 五、作业

请使用 String 类的`toLowerCase()`和`toUpperCase()`方法写一个字符串大小写转换工具。

## 参考文档

关于字符串 API，你可以查看 Java SE 官方文档获取更多更好玩的内容：

*   [Java SE 官方 API 文档 - Java.lang.String](http://docs.oracle.com/javase/7/docs/api/java/lang/String.html#method_detail)

以下是 Java 中一些包装类的官方技术文档：

*   [Java SE 官方 API 文档 - Java.lang.Byte](http://docs.oracle.com/javase/7/docs/api/java/lang/Byte.html)
*   [Java SE 官方 API 文档 - Java.lang.Boolean](http://docs.oracle.com/javase/7/docs/api/java/lang/Boolean.html)
*   [Java SE 官方 API 文档 - Java.lang.Integer](http://docs.oracle.com/javase/7/docs/api/java/lang/Integer.html)

我们没有给出所有的包装类参考文档，请你自己动手`Ctrl+F`体会一下查找的过程。