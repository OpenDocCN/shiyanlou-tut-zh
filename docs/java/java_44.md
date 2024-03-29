# 第 7 节 泛型

* * *

## 实验介绍

本实验将学习泛型的相关知识

## 一、为什么需要泛型

　　在 Java SE 5.0 以前操作集合有许多缺点，一是从集合取出对象时，需要执行类型转换操作，我们在前面讲过，集合中存储都是以 Object 对象进行存储的，这无疑让我们的操作变得麻烦。二是由于没有类型检查，可以向集合添加任意对象，不便于我们对集合的管理，有时候甚至会导致严重的错误。

　　而泛型就帮我们提供了类型参数，让我们提取规定好集合对象的类型。在前面的实验中我们已经接触过泛型了。

例如：

```java
//注意：引入了泛型以后，List<Object>, List<String>是两种不同的类型

ArrayList<String> files = new ArrayList<String>();

/*
*下面的代码是非法的
*List<Object> lo = files;
*/ 
```

　　我们在集合的类型后面添加上一对`<>`，尖括号中填上我们需要规范的集合里对象的类型，我们可以指定一个或多个类型参数的名字，同时也可以对类型参数的取值范围进行限定，多个类型参数之间用`,`分隔。这样编辑器便会在编译时帮我们检查出不符合规范的元素添加。

　　下面我们来看一看定义泛型的规则吧，同学们先有个印象，我们在后面定义泛型类和方法，以及使用泛型的时候，大家在结合我们这里讲解的规则，去深入地理解泛型吧！

1.  泛型的类型参数只能是类类型（包括自定义类），不能是简单类型。
2.  同一种泛型可以对应多个版本（因为参数类型是不确定的），不同版本的泛型类实例是不兼容的。
3.  泛型的类型参数可以有多个。
4.  泛型的参数类型可以使用 extends 语句，例如 <T extends superclass>。习惯上称为“有界类型”。
5.  泛型的参数类型还可以是通配符类型。例如 Class<?> classType = Class.forName("java.lang.String");

## 二、定义带类型参数的类

接下来我们来定义一个泛型类，它有一个成员，成员的类型待定。

```java
package com.shiyanlou;

/*
使用 T 代表类型，无论何时都没有比这更具体的类型来区分它。如果有多个类型参数，我们可能使用字母表中 T 的临近的字母，比如 S。
*/
class Test<T> {
    private T ob;

    /*
    定义泛型成员变量，定义完类型参数后，可以在定义位置之后的方法的任意地方使用类型参数，就像使用普通的类型一样。
    注意，父类定义的类型参数不能被子类继承。
    */

    //构造函数
    public Test(T ob) {
        this.ob = ob;
    }

    //getter 方法
    public T getOb() {
        return ob;
    }

    //setter 方法
    public void setOb(T ob) {
        this.ob = ob;
    }

    public void showType() {
        System.out.println("T 的实际类型是: " + ob.getClass().getName());
    }
}

public class TestDemo {
    public static void main(String[] args) {
        // 定义泛型类 Test 的一个 Integer 版本
        Test<Integer> intOb = new Test<Integer>(88);
        intOb.showType();
        int i = intOb.getOb();
        System.out.println("value= " + i);
        System.out.println("----------------------------------");
        // 定义泛型类 Test 的一个 String 版本
        Test<String> strOb = new Test<String>("Hello Gen!");
        strOb.showType();
        String s = strOb.getOb();
        System.out.println("value= " + s);
    }
} 
```

## 三、使用泛型方法

我们先来看一看代码：

1、定义一个 Animal 类

```java
public class Animal {
    public Animal(){
        System.out.println("我是动物");
    }
} 
```

2、定义一个 Dog 类

```java
public class Dog extends Animal {
    public Dog(){
        System.out.println("我是狗");
    }
} 
```

3、定义一个 Test 类

```java
public class Test {

/*
注意：定义带类型参数的方法，其主要目的是为了表达多个参数以及返回值之间的关系。例如本例子中 T 和 S 的继承关系， 返回值的类型和第一个类型参数的值相同。
*/

    public<T, S extends T> T testDemo(T t, S s){
        System.out.println("我是 T 类型，我的类型是" + s.getClass().getName());
        System.out.println("我是 S 类型，我的类型是" + t.getClass().getName());
        return t;
    }
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        Test test = new Test();
        Dog d = new Dog();
        Animal a0 = new Animal();
        Animal a1  = test.testDemo(a0, d);
        System.out.println("我是整数 a，我的类型是" + a1.getClass().getName());
    }

} 
```

4、上面的例子中我们对类型参数赋予了具体的类型，当然我们有时候也无法确定类型参数的类型，这个时候我们便可以使用通配符。如果仅仅是想实现多态，请优先使用通配符解决。

例如：

```java
import java.util.List;

public class Test {

    public void testDemo(List<?> s){
        for(Object obj:s){
            System.out.println("我的类型是" + obj.getClass().getName());
        }
    }

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        Test test = new Test();
        Dog a0 = new Dog();
        Animal a1 = new Animal();
        List<Animal> s = new ArrayList<Animal>();
        s.add(a0);
        s.add(a1);
        test.testDemo(s);
    }

} 
```

## 四、作业 ##

　到此为止我们的 java 核心 API 就学完了，但是里面还有很多知识点我们并没有详细地讲解，希望同学们下来的时候多查查资料，多敲敲代码。