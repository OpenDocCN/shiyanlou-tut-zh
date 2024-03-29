# 第 5 节 错误处理

## 一、实验简介

还记得在第一章“认识 J2SE”的例子中我们就见识了`try`和`catch`结构吗？它们就是用于捕获异常的关键词。

在真实的程序运行过程中，诸如除 0 溢出、数组越界、文件找不到等错误的发生，将阻止程序的正常运行。而在 Java 世界里，这些错误我们通常称之为异常。本章我们就将学习 Java 中的异常机制，观察程序中的错误是如何发生并解决的，你还有机会自己动手定义异常类。

本章知识点：

*   常见异常的产生
*   自定义异常类
*   捕获异常

## 二、常见异常

异常指不期而至的各种状况，它在程序运行的过程中发生。作为开发者，我们都希望自己写的代码永远都不会出现 bug，然而现实告诉我们并没有这样的情景。如果用户在程序的使用过程中因为一些原因造成他的数据丢失，这个用户就可能不会再使用该程序了。所以，对于程序的错误以及外部环境能够对用户造成的影响，我们应当及时报告并且以适当的方式来处理这个错误。

之所以要处理异常，也是为了增强程序的[鲁棒性](http://baike.baidu.com/view/45520.htm)。

异常都是从`Throwable`类派生出来的，而`Throwable`类是直接从`Object`类继承而来。你可以在 Java SE 官方 API 文档中获取更多关于它们的知识。

异常通常有四类：

*   Error：系统内部错误，这类错误由系统进行处理，程序本身无需捕获处理
*   Exception：可以处理的异常
*   RuntimeException：可以捕获，也可以不捕获的异常
*   继承 Exception 的其他类：必须捕获，通常在 API 文档中会说明这些方法抛出哪些异常

我们先来体验 3 种较为简单的异常。

### 1\. 算术异常（ArithmeticException）

Java SE 官方文档对于算术异常的定义是： >当出现异常的运算条件时，抛出此异常。例如，一个整数“除以零”时，抛出此类的一个实例。

关于除以零还有[更有趣的故事](http://www.guokr.com/article/440472/)。

所以，我们来抛出一个算术异常看看吧。在 Eclipse 中新建一个项目`AriExption`，创建`com.shiyanlou.course`包，新建一个`AriExceptionTest`类。

我们需要在这个类的`main()`方法中模拟出现算术异常的情况， 主要的代码如下：

```java
package com.shiyanlou.course;

public class AriExceptionTest {
    public static void main(String[] args) {
        System.out.println("Example 1:  -1.0 / 0 = " + (-1.0 / 0));
        // 演示负浮点数除 0

        System.out.println("Example 2:  +1.0 / 0 = " + (+1.0 / 0));
        // 演示正浮点数除 0

        System.out.println("Example 3:  -1 / 0 = " + (-1 / 0));
        // 演示负整数除 0

        System.out.println("Example 4:  +1 / 0 = " + (+1 / 0));
        // 演示正整数除 0
    }
} 
```

在控制台可以看到该异常的输出信息：

![算术异常](img/document-uid85931labid1096timestamp1436173181519.jpg)

可以看到，实际上程序在运行到 Example 3 的时候就已经出现了算数异常。当代码抛出一个异常的同时，也终止了对剩余代码的处理，所以 Example 4 根本没有机会运行。

那么 Example 1 和 2 中为什么能出现结果呢？

这是由于在 Java 中，浮点数（无论是 float 还是 double 类型的浮点数）被 0 除，并不会引发算术异常。具体说来，是操作系统为了保护应用软件而已经处理了该异常，不再抛出，最终运算结果是无穷大。

### 2\. 数组下标越界异常（ArrayIndexOutOfBoundsException）

Java SE 官方文档对于数组下标越界异常的定义是： >用非法索引访问数组时抛出的异常。如果索引为负或大于等于数组大小，则该索引为非法索引。

通俗的来说，例如你的数组大小是 6，你能访问的范围即 0 到 5，如果你用下标 13 去访问其中的元素，就会遇到此问题。

动手试一下，在 Eclipse 中新建一个项目`ArrayExption`，创建`com.shiyanlou.course`包，新建一个`ArrayExceptionTest`类。

主要的代码如下：

```java
package com.shiyanlou.course;

import java.util.Arrays;

public class ArrayExceptionTest {

    public static void main(String[] args) {
        int[] array = new int[5]; 
        // 声明一个长度为 5 的整型数组 array

        Arrays.fill(array, 8); 
        // 将该数组所有元素赋值为 8

        for (int i = 0; i < 6; i++) {
            // 用遍历的方式，输出所有数组元素，注意此处的控制条件 i < 6

            System.out.println("array[" + i + "] = " + array[i]);
            //下标将会增加到 5，显然是超出了数组 array 的范围（0 到 4），程序将在第 6 次循环前抛出异常
        }
    }
} 
```

![数组下标越界异常](img/document-uid85931labid1096timestamp1436173558774.jpg)

### 3\. 空指针异常（NullPointerException）

Java SE 官方文档对于空指针异常的定义是： >当应用程序试图在需要对象的地方使用 null 时，抛出该异常。这种情况包括：

>- 调用 null 对象的实例方法。

*   访问或修改 null 对象的字段。
*   将 null 作为一个数组，获得其长度。
*   将 null 作为一个数组，访问或修改其时间片。
*   将 null 作为 Throwable 值抛出。

>应用程序应该抛出该类的实例，指示其他对 null 对象的非法使用。

同样，我们来尝试抛出一个此类异常。在 Eclipse 中新建一个项目`NullPointerExption`，创建`com.shiyanlou.course`包，新建一个`NullPointerExceptionTest`类。

主要的代码如下：

```java
package com.shiyanlou.course;

public class NullPointerExceptionTest {

    public static void main(String[] args) {
        String s= null;
        // 将字符串设置为 null

        System.out.println(s.toUpperCase());
        // 尝试将字符串转换成大写，看看会发生什么
    }

} 
```

点击编译并运行，可以看到，红色的异常信息又“如约而至”了。

![空指针异常](img/document-uid85931labid1096timestamp1435913198302.jpg)

除了上述 3 种，异常的种类还有很多很多，比如`IllegalAccessException`、`FileNotFoundException`以及在操作数据库时抛出的异常，你可以在 API 文档中查看它们的产生原因。再者，例如在 Android 开发过程中，我们也会经常看到控制台报出的各式各样的错误信息，这些异常是平时我们没见过的，它们又是怎么来的呢？我们不妨来了解一下自定义的异常类。

## 三、自定义异常类

尽管 Java SE 的 API 以及为我们提供了数十种异常类，然而在实际的开发过程中，你仍然可能遇到未知的异常情况。此时，你就需要对异常类进行自定义。

自定义一个异常类非常简单，只需要让它继承 Exception 或其子类就行。在自定义异常类的时候，建议同时提供无参构造方法和带字符串参数的构造方法，后者可以为你在调试时提供更加详细的信息。

百闻不如一见，下面我们尝试自定义一个算术异常类。

在 Eclipse 中创建项目`MyException`，创建`com.shiyanlou.course`包，并添加一个`MyAriException`类。

主要的代码如下：

```java
package com.shiyanlou.course;
public class MyAriException extends ArithmeticException {            
    //自定义异常类，该类继承自 ArithmeticException

    public MyAriException() {

    }
    //实现默认的无参构造方法

    public MyAriException(String msg) {
        super(msg);
    }
    //实现可以自定义输出信息的构造方法，将待输出信息作为参数传入即可
} 
```

在同个包内再添加一个`ExceptionTest`类作为测试用，在该类的`main()`方法中，可以尝试抛出自定义的异常。

代码片段如下：

```java
package com.shiyanlou.course;

import java.util.Arrays;

public class ExceptionTest {
    public static void main(String[] args) {
        int[] array = new int[5];
        //声明一个长度为 5 的数组

        Arrays.fill(array, 5);
        //将数组中的所有元素赋值为 5

        for (int i = 4; i > -1; i--) {
            //使用 for 循环逆序遍历整个数组，i 每次递减

            if (i == 0) {
            // 如果 i 除以了 0，就使用带异常信息的构造方法抛出异常

                throw new MyAriException("There is a exception occured.");
            }

            System.out.println("array[" + i + "] / " + i + " = " + array[i] / i);
            // 如果 i 没有除以 0，就输出此结果
        }
    }
} 
```

检查一下代码，点击编译并运行，期待中的自定义错误信息就展现在控制台中了：

![此处输入图片的描述](img/document-uid85931labid1096timestamp1436250038493.jpg)

## 四、捕获异常

当我们在编程时遇到了异常不要紧，除了可以将异常抛出，还可以将异常捕获。通常使用`try`和`catch`语句块来捕获异常，有时候还会用到`finally`。

对于上述三个关键词所构成的语句块，`try`语句块是必不可少的，`catch`和`finally`语句块可以根据情况选择其一或者全选。你可以把可能发生错误或出现问题的语句放到`try`语句块中，将异常发生后要执行的语句放到`catch`语句块中，而`finally`语句块里面放置的语句，不管异常是否发生，它们都会被执行。

你可能想说，那我把所有有关的代码都放到`try`语句块中不就妥当了吗？可是你需要知道，捕获异常对于系统而言，其开销非常大，所以应尽量减少该语句块中放置的语句。

下面我们通过一个实例来练习如何捕获异常。请在 Eclipse 中新建项目`CatchExp`，创建包`com.shiyanlou.course`和类`CatchException`，添加下面的关键代码：

```java
package com.shiyanlou.course;

public class CatchException {
    public static void main(String[] args) {
        try {
            // 下面定义了一个 try 语句块

            System.out.println("I am try block.");

            Class<?> tempClass = Class.forName("");    
            // 声明一个空的 Class 对象用于引发“类未发现异常”
            System.out.println("Bye! Try block.");

        } catch (ClassNotFoundException e) {
            // 下面定义了一个 catch 语句块
            System.out.println("I am catch block.");

            e.printStackTrace();
            //printStackTrace()的意义在于在命令行打印异常信息在程序中出错的位置及原因

            System.out.println("Goodbye! Catch block.");

        } finally {
            // 下面定义了一个 finally 语句块
            System.out.println("I am finally block.");
        }
    }
} 
```

编写完成后，请点击编译并运行，可以在控制台看到如下的输出信息：

![捕获异常](img/document-uid85931labid1096timestamp1436252950635.jpg)

请你结合这些输出语句在源代码中的位置，再来体会一下三个语句块的作用。

## 四、实验总结

在本章，我们 Eclipse 中的控制台可谓是红成了一片海洋，希望你不要反感这些红色的异常信息。相反，这些信息还有利于你去定位程序中的错误。本章我们还学习了自定义异常类和捕获异常等手段。通过它们，你可以不断地增强程序的健壮性，真正使程序在错综复杂的运行和使用环境中具有“金刚不败之身”。

## 五、作业

试问，若在`try`语句块里有一个`return`语句，同时这个`try`语句块后面还有一个`finally`语句块，请问`finally`语句块里的代码会不会被执行？若答案是肯定的，那么会在什么时候被执行？是在`return`语句之前还是之后?

## 参考文档

*   [J2SE 官方 API 文档 - ArithmeticException](http://docs.oracle.com/javase/7/docs/api/java/lang/ArithmeticException.html)
*   [J2SE 官方 API 文档 - ArrayIndexOutOfBoundsException](http://docs.oracle.com/javase/7/docs/api/java/lang/ArrayIndexOutOfBoundsException.html)
*   [J2SE 官方 API 文档 - NullPointerException](http://docs.oracle.com/javase/7/docs/api/java/lang/NullPointerException.html)