# 第 3 节 集合类框架

## 一、实验简介

在 Java 基础语法中，你可能接触到了数组这一概念。

本章知识点：

*   集合类框架的概念
*   ArrayList
*   HashMap

## 二、什么是集合类框架

在 Java 语言中，Java 语言的设计者对常用的数据结构和算法做了一些规范（接口）和实现（具体实现接口的类）。所有抽象出来的数据结构和操作（算法）统称为 Java 集合框架（Java Collection Framework）。 这意味着我们在具体应用时不必考虑数据结构和算法实现细节，只需要用这些类创建出来一些对象并加以应用就可以了，从而提高了编程效率。你可以[了解更多关于 Java 集合框架的知识](http://baike.baidu.com/link?url=hScyy3wtV3jrp3OyyLUs831YjOcXNX96jVkQ7RCozzmIDsO0M_yutaBY2X_lWQwJ2nc2xVelzNlLXnO5_FuBi_)。

Java.util 中的集合类包含 Java 中某些最常用的类。最常用的集合类是 List 和 Map。List 的具体实现包括 ArrayList 和 Vector，它们是可变大小的列表，比较适合构建、存储和操作任何类型对象元素列表。List 适用于按数值索引访问元素的情形。

下图是 Java 集合框架的结构图，从此处你可以大致了解到各种类型的集合之间的关系。 ![此处输入图片的描述](img/document-uid85931labid1094timestamp1436168922546.jpg)

## 三、认识 ArrayList

ArrayList 就是动态数组，支持自动改变大小，可以灵活的插入和删除元素。你可以[进一步了解 ArrayList](http://baike.baidu.com/view/1827383.htm)。

ArrayList 定义了一些用于插入和删除元素的方法，了解一下：

*   `add(E e)`，将指定的元素添加到此列表的尾部
*   `clear()`，移除此列表中的所有元素
*   `contains(Object o)`，如果此列表中包含指定的元素，则返回 true
*   `get(int index)`，返回此列表中指定位置上的元素
*   `remove(int index)`，移除此列表中指定位置上的元素
*   `toArray()`，按从第一个到最后一个元素的顺序返回包含此列表中所有元素的数组

我们通过下面一个遍历 ArrayList 的实例来体会其用法，首先请在 Eclipse 中新建项目`ArrayListExp`，创建一个包`com.shiyanlou.course`和类`ArrayListTraversal`。

主要的代码如下，在注释里你可以看到对于各行代码的解说：

```java
package com.shiyanlou.course;

import java.util.ArrayList;
import java.util.List;

public class ArrayListTraversal {

    public static void main(String[] args) {
        List<Integer> list = new ArrayList<Integer>();
        // 创建列表 list

        for (int i = 0; i < 10; i++) {
            list.add(i);
            // 使用 add()方法，通过 for 循环向 list 中增加 10 个元素，每个元素即当前循环的次数值
        }

        System.out.println("Items in the list：");
        System.out.println(list);    
        // 输出列表中全部的元素

        System.out.println("Items in the list with odd index:");
        for (int i = 1; i < list.size(); i += 2) {
            // 输出列表中序号为奇数的元素，注意 i += 2 的用法

            System.out.print(list.get(i)+"  ");
            //使用 get()方法从 list 中取出元素
        }
    }
} 
```

编写完成后，点击编译并运行，我们可以在控制台看到下面的输出结果：

![此处输入图片的描述](img/document-uid85931labid1094timestamp1436242252041.jpg)

## 四、认识 HashMap

Java 自带了各种 Map 类，这些 Map 类可归为三种类型：

*   通用 Map（`HashMap`、`Hashtable`、`Properties`、`LinkedHashMap`、`IdentityHashMap`等）
*   专用 Map（`java.util.jar.Attributes`、`javax.print.attribute.standard.PrinterStateReasons`等）
*   一个用于帮助实现您自己的 Map 类的抽象类

其中，通用 Map 用于在应用程序中管理映射，通常在 `java.util` 程序包中实现，可以直接使用。

Map 定义了几个用于插入和删除元素的方法，通过这些方法可以更改 Map 中的内容。

*   `clear()`，从 Map 中删除所有映射
*   `remove(Object key)`，从 Map 中删除键和关联的值
*   `put(Object key, Object value)`，将指定值与指定键相关联
*   `clear()`，从 Map 中删除所有映射
*   `putAll(Map t)`，将指定 Map 中的所有映射复制到此 map

`HashMap`是基于哈希表的 Map 接口的实现。此实现提供所有可选的映射操作，并允许使用 `null` 值和 `null` 键。这里的键值对（`key - value`），你可以[进一步了解 HashMap](http://baike.baidu.com/link?url=fNN0X2wuqs_2NCbawoSSQbpLNtvubZULgy5mcykcpjvh81kyJbt-dqfRIxpYTZNZgwQMMI2iwX_iOePk681KZa)。

下面我们使用 HashMap 及其中的一些方法来做一个简单的电话簿，目标是让用户通过输入待查询的姓名来获取其对应的电话号码。

首先请在 Eclipse 中新建一个项目`PhoneBook`，创建包`com.shiyanlou.course`和类`PhoneBookByMap`。主要的代码如下：

```java
package com.shiyanlou.course;

import java.util.HashMap;
import java.util.Scanner;

public class PhoneBookByMap {

    public static void main(String[] args) {
        HashMap<String, String> phonebook = new HashMap<String, String>();
        //创建一个 HashMap 对象，名称为 phonebook，键值对的类型均为 String

        Scanner scan = new Scanner(System.in);
        //创建扫描器用于获取用户的输入

        String keyword = new String();
        //创建一个名为 keyword 的字符串用于存放用户输入的关键词

        phonebook = initPhoneBook(phonebook);
        //利用自写的 initPhoneBook 方法为电话簿装入数据，完成其初始化的步骤

        System.out.println("Please input a name that you want to search:");
        //提示用户输入待查询的姓名

        keyword = scan.nextLine();
        //将用户的输入信息装入字符串 keyword 中

        if(keyword.isEmpty()){
            //使用 isEmpty()方法判断用户是否真正输入了内容，如没有则提示其输入
            System.out.println("Please input a name!");
        }
        else {

            //若用户有输入某个关键词，则按照该关键词在 phonebook 中查找其对应的号码
            System.out.println("The result is :");
            System.out.println(queryPhone(phonebook, keyword));
            //这里调用了自写的 queryPhone()方法
        }

    }

    private static HashMap<String, String> initPhoneBook(HashMap<String, String> phonebook){
        //该方法用于完成指定 HashMap 的初始化，为其装入一些号码数据，你也可以自定义这些数据
        //Q：你还清楚形参和实参的区别吗？

        phonebook.put("Steve", "13012345678");
        phonebook.put("Bob", "028-80001234");
        phonebook.put("Peter", "182222233333");
        //使用 put()方法将姓名与电话号码相关联，存放在名为 phonebook 的 HashMap 中
        //put()方法中，第一个参数为关键词 key，第二个参数为其对应的值 value

        return phonebook;
        //返回修改后的 phonebook
    }

    private static String queryPhone(HashMap<String, String> phonebook, String keyword) {

        String result = new String();
        //创建存放结果的字符串 result

        result = phonebook.get(keyword);
        //使用 get()方法查询 keyword 所对应的电话号码，并赋给 result
        //put()方法中，参数为关键词 key，返回值为其对应的值 value，未找到对应值时，返回值为 null

        if(result == null)
            return "Can not find this user.";
            //如果未找到该用户的电话号码，则将查询结果修改为“未找到该用户”

        return result;
        //返回查询结果
    }

} 
```

这个例子稍显复杂，需要你弄清楚其中各个模块之间的关系。检查代码无误后，点击编译并运行，在控制台分别输入无效姓名、有效姓名（例如`Steve`，注意大小写）和空值，观察下程序返回的结果是否正常。

查找到号码的情况：

![此处输入图片的描述](img/document-uid85931labid1094timestamp1436242430829.jpg)

找不到号码的情况：

![此处输入图片的描述](img/document-uid85931labid1094timestamp1436242510986.jpg)

以及你什么都不输入，直接回车得到的提示：

![此处输入图片的描述](img/document-uid85931labid1094timestamp1436242566416.jpg)

## 五、实验总结

本章我们学习了 Java 中的集合类，并动手定义了 ArrayList 和 HashMap，使用了其自带的方法来完成了诸如存取数据的工作。在课程结束之后，你也应该结合 API 文档，动手再使用一下这些集合类中的其他类型及其方法。

## 六、作业

请查阅资料，回答下面几个问题：

1.  Collection 与 Collections 的区别？
2.  List、Set 和 Map 是否继承自 Collection 接口？
3.  ArrayList 与 Vector 的区别？
4.  HashMap 与 Hashtable 的区别？

## 参考文档

*   [Java 的集合框架最全详解（图）](http://davidisok.iteye.com/blog/2106563)
*   [Java SE 官方 API 文档 - java.util.Collection](http://docs.oracle.com/javase/7/docs/api/java/util/Collection.html)