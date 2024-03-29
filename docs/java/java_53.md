# 第 9 节 Java 继承

* * *

## 实验介绍

　　本实验将介绍继承，以及父类和子类之间初始化顺序等相关知识。

## 一、继承的定义

　　继承可以看成是类与类之间的衍生关系。比如狗类是动物类，牧羊犬类又是狗类。于是我们可以说狗类继承了动物类，而牧羊犬类就继承了狗类。于是狗类就是动物类的子类（或派生类），动物类就是狗类的父类（或基类）。

　　在 Java 中一个类只有一个父类，所以 Java 中的继承是单继承。就像我们每个人只有一个亲生父亲，而我们如果有一个有钱的父亲我们就是富二代；有一个明星父亲，我们就是星二代。继承可以让子类直接拥有父类的所有属性和方法(属性和方法的修饰符是 private 就不行，因为是私有的嘛)。而继承的好处便是可以实现代码的复用，使我们程序猿的福利啊！ 　　 我们创建一个类需要它来继承某一个类的语法：

```java
class 子类 extends 父类 
```

例如我们定义了一个 Animal 类，我们在创建一个 Dog 类，我们需要它继承 Animal 类

```java
class Dog extends Animal {
    ...
} 
```

接下来我们就来练习一下吧！

我们先创建一个父类 Animal

```java
package com.shiyanlou;

public class Animal {
    public int legNum;     //动物四肢的数量

    //类方法
    public void bark() {
        System.out.println("动物叫！");
    }
} 
```

接下来创建一个子类，我们可以自己输入代码，也可以利用 Eclipse 帮我们自动建立继承关系。

1、点击创建 class 类

2、输入子类名称，点击`Superclass`后面的`Browse...`寻找父类，与之建立继承关系

![子类命名](img/document-uid79144labid1073timestamp1434956298887.jpg)

3、在`Choose a type:`下的输入框内输入要寻找的父类，然后选择，结束。

![搜索父类](img/document-uid79144labid1073timestamp1434956385044.jpg)

```java
package com.shiyanlou;

public class Dog extends Animal {
} 
```

上面便是系统生成的代码，Dog 类继承了父类 Animal，我们 Dog 类里什么都没有写，其实它继承了父类 Animal，所以 Dog 类拥有 Animal 类的全部方法和属性（除开私有的方法和属性）。我们创建一个测试类测试一下。

![继承代码示例](img/document-uid79144labid1073timestamp1434956888861.jpg)

## 二、方法的重写

　　虽然子类可以继承父类的方法，但如果子类对父类的方法不满意，子类想在里面加入适合自己的一些操作时，我们就需要将方法进行重写。并且子类在调用方法中，优先调用子类的方法 　　 　　比如上一个例子中 Animal 有`bark()`这个方法代表了动物叫，但是不同的动物有不同的叫法，比如狗是汪汪汪，猫是喵喵喵。 　　 　　当然在方法重写时我们需要注意，重写的方法一定要与原父类的方法语法保持一致，比如返回值类型，参数类型及个数，和方法名都必须一致。 　　 例如：

```java
package com.shiyanlou;

public class Dog extends Animal {
    //重写父类方法
        public void bark() {
        System.out.println("汪！汪！汪！");
    }
} 
```

我们在运行程序后，发现控制台上是不是输出了我们预想的结果呢？

## 三、继承的初始化顺序

　　我们学习了继承后，知道在一个继承关系中一定会有一个父类和至少一个子类，那么在程序运行的过程中，是为父类进行初始化，还是先调用的子类进行初始化的呢？ 　　 　　继承的初始化顺序是先初始化父类再初始化子类。

　　我们根据代码来验证一下。

```java
package com.shiyanlou;

public class Animal {

    public int legNum = 4; // 动物四肢的数量

    //Animal 的构造方法
    public Animal(){
        System.out.println("动物有" + legNum + "腿");
        System.out.println("父类的构造方法被调用！");
    }
} 
```

```java
package com.shiyanlou;

public class Dog extends Animal {

    //Dog 类的构造方法
    public Dog(){
        System.out.println("子类的构造方法被调用！");
    }
} 
```

```java
package com.shiyanlou;

public class test {

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        Dog a = new Dog(); //生成一个 Dog 类对象 a

    }

} 
```

最后输出结果：

![输出结果](img/document-uid79144labid1073timestamp1434959463229.jpg)

　　由此可知，系统先创建了父类对象，再创建了子类对象，先初始化了属性，再调用了构造函数。 　　 　　最后再讲一个小知识。

`final`关键字可以修饰类、方法、属性和变量

1.  final 修饰类，则该类不允许被继承

2.  final 修饰方法，则该方法不允许被覆盖（重写）

3.  final 修饰属性：则该类的属性不会进行隐式的初始化（类的初始化属性必须有值）或在构造方法中赋值（但只能选其一）

4.  final 修饰变量，则该变量的值只能赋一次值，即变为常量

`super`关键字在父类内部使用，代表父类对象。

1.  访问父类的属性 `super.属性名`

2.  访问父类的方法 `super.bark()`

同学们可以自行验证一下。

## 四、 作业

　　同学们，想想生活中的一些继承关系还有那些，比如交通工具，汽车，飞机等。那同学们来创建父类和子类，并用上`this`,`super`等关键字吧。