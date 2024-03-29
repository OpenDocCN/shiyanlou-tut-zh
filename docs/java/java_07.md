# 第 7 节 枚举和泛型

## 一、实验简介

在本章我们将学习 Java 中的枚举类型和

本章知识点

*   枚举类型
*   泛型

## 二、认识枚举类型

在有些情况下，变量的取值是限定在一个有限的集合内的。比如说，性别只有`男`和`女`，星期也只有`星期一`、`星期二`、`星期三`、…、`星期天`等。你可以将这些元素编码为`1`、`2`、`3`之类的，但这么做可能会让你的程序存在一些隐患，比如`0`通常还有`false`的含义。

从 JDK 5.0 开始，开发者可以自定义`枚举类型`，它的含义如下：

>枚举类型包含有限个命名的值。

比如将星期声明为枚举类型的变量，你可以这样做：

```java
enum Week { MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY }; 
```

这个声明定义的类型实际上是一个类，其中有 7 个实例。

和所有的类一样，你可以枚举类型中添加其构造器，以及一些域和方法。所以上面的声明你可以再补充一些内容：

```java
package com.shiyanlou.course

public enum Week {

    MONDAY("MON"), TUESDAY("TUES"), WEDNESDAY("WED"), THURSDAY("THUR"),
    FRIDAY("FRI"), SATURDAY("SAT"), SUNDAY("SUN");
    //必须要先定义枚举类型，才能定义方法

    private String abbr;
    //声明的一个字符串，用于保存缩写信息

    private Week(String abbr) {
        this.abbr = abbr;
    }
    //声明的一个私有构造方法，该方法只能在构造枚举常量时被调用，可以使枚举元素具有指定的缩写信息

    public String getAbbr() {
        return abbr;
    }
    //通过该方法获得枚举元素的缩写
} 
```

所有的枚举类型都继承了`java.lang.Enum`类，所以`Enum`类的一些方法也适用于这些枚举类型。例如：

> - equals(Object other)，当指定对象等于此枚举常量时，返回 true。

*   toString()，返回枚举常量的名称，它包含在声明中。
*   valueOf(Class<T> enumType, String name)，返回带指定名称的指定枚举类型的枚举常量。

下面通过一个例子来学习枚举元素的基本特性，请在 Eclipse 中新建项目`LearnEnum`，创建包`com.shiyanlou.course`，在这个包里面创建一个上述的`Week`类（代码在上方）和`EnumPractice`类。我们在代码的注释中继续来讲解它们的用法。

`Week`类你应该已经写好了，下面我们在`EnumPractice`类中添加一些代码：

```java
package com.shiyanlou.course;

public class EnumPractice {

    public static void main(String[] args) {
        for(Week week : Week.values()) {
            System.out.println("The order of " + week + " is " + week.ordinal());
            //使用 ordinal()方法查看枚举元素的顺序

            System.out.println("Compare to MONDAY : " + week.compareTo(Week.MONDAY));
            //用 compareTo()方法来将当前枚举元素与 Week.MONDAY 比较

            System.out.println("Equal to MONDAY ? " + week.equals(Week.MONDAY));
            //用 equals()方法来将当前枚举元素与 Week.MONDAY 比较

            System.out.println("Equal to MONDAY by == ? " + ( week == Week.MONDAY ));
            //用比较运算符==来将当前枚举元素与 Week.MONDAY 比较
            //从这里你应该可以看到，可以直接使用==来比较两个枚举元素

            System.out.println("Name : " + week.name());
            //使用 name()方法获得枚举元素的名称

            System.out.println("Abbreviation : " + week.getAbbr());
            //使用自定义的 getAbbr()方法输出星期的缩写

            System.out.println("-------------------");
            //在每次循环快要结束时，打印一条分割线用于区分
        }
    }
} 
```

这段程序中的 for 循环使用了下列条件格式：

```java
for (循环变量类型 循环变量名称 : 要被遍历的对象) {

    循环体
} 
```

检查一下代码，点击编译并运行，在控制台输出信息中再来理解一下各个方法的含义。这里我们截取了部分结果，如下图所示：

![认识枚举元素](img/document-uid85931labid1098timestamp1436324660119.jpg)

## 三、认识泛型

泛型是程序设计语言的一种特性，它允许程序员在编写代码时定义一些可变的部分，而这些部分必须在使用前作出指明。

>Java 泛型的参数只能代表类，不能代表个别对象。在对参数命名时，通常使用单个大写字母，例如对于任意类型可以使用大写字母 T。

所谓的泛型类就是包含一个或多个类型参数的类。定义泛型类时只需要在类名后面加上`<`和`>`并在尖括号内指明类型参数即可。具体的规则如下：

>1\. 泛型的类型参数只能是类类型（包括自定义类），不能是基本数据类型。

1.  同一种泛型可以对应多个版本（因为参数类型是不确定的），不同版本的泛型类实例是不兼容的。
2.  泛型的类型参数可以有多个。例如 K 和 V。
3.  泛型的参数类型可以使用 extends 语句。例如<T extends superclass>。习惯上称为“有界类型”。
4.  泛型的参数类型还可以是通配符类型。例如 Class<?> classType = Class.forName("java.lang.String");。

我们用泛型在 Java 中实现一个具有栈的基本功能的类。

请在 Eclipse 中新建项目`GenericStack`，创建包`com.shiyanlou.course`。我们将会定义两个类，分别是栈结构的泛型类`GenericStack`和测试类`GenericStackTest`。

首先在`GenericStack`类中添加代码。我们需要做的是选择基本的容器来制作这个栈（这里选择了集合类中的 LinkedList 作为容器），并为栈添加入栈和出栈的方法。当然，你还可以使这个栈具有更易用的特性，为其添加判断空栈的方法。主要的代码如下：

```java
package com.shiyanlou.course;

import java.util.LinkedList;

public class GenericStack<T> {
    //定义了一个参数类型为 T 的泛型类

    private LinkedList<T> stackContainer = new LinkedList<T>();
    //使用 T 类型的 LinkedList（链表）作为栈的容器

    public void push(T t) {
        //自定义了一个入栈的方法，其中调用了 addFirst()方法
        //addFirst()方法可以在栈的顶端加入元素

        stackContainer.addFirst(t);
    }

    public T pop() {
        //定义了一个出栈的方法，其中调用了 removeFirst()方法
        //removeFirst()方法可以将栈顶端的元素移除并取出作为返回值

        return stackContainer.removeFirst();
    }

    public boolean isEmpty() {
        //定义了一个判断是否是空栈的方法，调用了 LinkedList 本身的 isEmpty(0 方法
        return stackContainer.isEmpty();
    }
} 
```

接着我们在测试类`GeniricStackTest`中来验证一下我们对于泛型类的定义是否正确。代码片段如下：

```java
package com.shiyanlou.course;

public class GenericStackTest {

    public static void main(String[] args) {
        GenericStack<String> stack = new GenericStack<String>();
        //我们在创建 GenericStack 对象时填入了 String 类型，表明这个栈里面只能存放字符串元素
        //声明了一个 GenericStack 对象，名字为 stack

        System.out.println("Now add some words into stack.");

        stack.push("ShiYanLou");
        System.out.println("word - 'ShiYanLou' added.");
        stack.push("OnlineLearning");
        System.out.println("word - 'OnlineLearning' added.");
        //添加两个元素到栈里面去
        //Q：现在哪个词在栈顶？

        System.out.println("Now pop words from stack.");

        while(!stack.isEmpty()) {
            //只要栈不为空，就取出其中的元素并打印出来

            String s = (String)stack.pop();
            //这里为了确保下一步执行的正确，用到了强制类型转换

            System.out.println("word - " + s + "has been taken out.");
        }
    }
} 
```

检查一下代码是否有误，点击编译并运行，在控制台可以看到下面这样的输出结果：

![泛型结果](img/document-uid85931labid1098timestamp1436336222841.jpg)

当然你也可以自定义这些栈中的信息。

至此，你应该学会了泛型类的定义、声明即其中包含的方法的使用。

## 四、实验总结

本章我们学习了枚举类型和泛型。古人有云： >“此类繁多，不能枚举。” ——清 李渔·《闲情偶寄》

再对比一下 Java 中枚举的定义，详细你应该有更深的体会。

然而泛型的使用还与反射机制有关系，希望你查阅资料进行更加深入的学习。总之，泛型的好处是在编译的时候检查类型安全，并且所有的强制转换都是自动和隐式的，能够提高代码的重用率。

## 五、作业

请仿照上面泛型类的例子，自定义一个数组的泛型类`GenericArray`，要求：

1.  具有基本的数组特征；
2.  具有在指定位置插入元素的方法`put(int index, T item)`；
3.  能够获得指定位置元素的方法`get(int index)`；

## 参考文档

*   [Jinuxwu 的专栏 - Java 泛型详解](http://blog.csdn.net/jinuxwu/article/details/6771121)
*   [Java SE 官方 API 文档 - java.lang.Enum](http://docs.oracle.com/javase/7/docs/api/java/lang/Enum.html)