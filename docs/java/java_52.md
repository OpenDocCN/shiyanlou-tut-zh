# 第 8 节  Java 封装

* * *

## 实验介绍

　　本实验将学习 Java 面向对象的三大特征之一的封装。

## 一、封装的定义

　　面向对象有三大特性：封装、继承、多态。今天我们就来学习封装。

　　封装就是将类的信息隐藏在类的内部，不允许外部程序直接访问，而是通过该类提供的方法来实现对隐藏信息的操作和访问。那这样的好处是什么呢？

　　　1\. 只能通过规定的方法访问数据

　　　2\. 隐藏类的实例细节，方便修改和实现。

　　我们在开汽车的时候，只用去关注如何开车，我们并不在意车子是如何实现的，这就是封装。

如何去实现类的封装呢？

1.  修改属性的可见性，在属性的前面添加修饰符(private 后面会讲)

2.  创建 getter/setter 方法，用来读写属性

3.  在 getter/setter 方法里加入属性的控制语句，例如我们可以加一个判断语句，对于非法输入给予否定。

![封装的步骤](img/document-uid79144labid1072timestamp1434933673483.jpg)

　　大家看一下，这是我们上一节课的例子。由于我们没有在属性前面添加任何修饰符，我们通过对象就可以直接对属性值进行修改，没有体现封装的特性。这在许多程序设计中都是不安全的，所以我们需要利用封装，来改进我们的代码。

![类](img/document-uid79144labid1072timestamp1434939959939.jpg)

![对象](img/document-uid79144labid1072timestamp1434939984638.jpg)

接下来，我们就改改上面的代码吧。首先我们的类里要将属性前添加`private`修饰符。然后定义`getter`和`setter`方法。

```java
package com.shiyanlou;

public class People {
//属性（成员变量）有什么，前面添加了访问修饰符 private
//变成了私有属性，必须通过方法调用
    private double height;     //身高

//属性已经封装好了，如果用户需要调用属性
//必须用 getter 和 setter 方法进行调用
//getter 和 setter 方法需要程序员自己定义
    public double getHeight(){    
    //getter 方法命名是 get 关键字加属性名（属性名首字母大写）
    //getter 方法一般是为了得到属性值
        return height;
    }

//同理设置我们的 setter 方法
//setter 方法命名是 set 关键字加属性名（首字母大写）
//setter 方法一般是给属性值赋值，所以有一个参数
    public void setHeight(double newHeight){
        height = newHeight;
    }
} 
```

然后在我们的 main 函数里的对象，不能再直接调用属性了，只能通过`getter`和`setter`方法进行调用。

```java
package com.shiyanlou;

public class LiLei {

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        People LiLei = new People();    //创建了一个 People 对象 LiLei

        //利用 setter 方法为属性赋值
        LiLei.setHeight(170.0);

        //利用 getter 方法取属性值
        System.out.println("LiLei 的身高是"+LiLei.getHeight());
    }
} 
```

## 二、包

　　我有时候在封装的时候会遇到这样的问题，就是我们的类名可能是重复的。这时候我们就可以用`包`的概念来解决我们的问题。

　　包的作用就是管理 Java 文件，解决同名文件冲突。这和我们生活当中的管理相类似。我们的衣柜是不是有不同的隔断和抽屉，我们将衣服分门别类地放好，是不是有利于我们管理。

　　我们在以前就学习过了如何定义一个包，我们使用`package`关键字，加上我们的包名。

定义包语法：

```java
package 包名
//注意：必须放在源程序的第一行，包名可用"."号隔开 
```

例如：

```java
//我们在定义文件夹的时候利用"/"来区分层次
//包中我们用"."来分层
package com.shiyanlou.Java 
```

　　比如我们在游戏里会用到音乐类，在电影里我们也会用到音乐类，但是他们的类又不相同，我们就可以利用包进行区分。

　　不仅是我们这样利用包名来区分类，系统也是这样做的。

![Java 系统中的包](img/document-uid79144labid1072timestamp1434937042272.jpg)

　　那我们怎么在不同包中使用另一个文件中的类呢？这时候就需要用到`import`关键字。比如我们要导入实验楼下`People`这个类。`import com.shiyanlou.Peole`，同时如果`import com.shiyanlou.*`这是将包下的所有文件都导入进来。

　　这里要注意一点，包的命名规范是全小写字母拼写。

## 三、Java 中的访问修饰符

　　我们在前面的代码中经常用到`private`和`public`修饰符，这些修饰符的作用和意义是什么呢？接下来我们就来学习 Java 中得访问修饰符。

　　访问修饰符可以用来修饰属性和方法的访问范围。

![访问范围](img/document-uid79144labid1072timestamp1434941168916.jpg)

　　如图所示，代表了不同的访问修饰符的访问范围，比如`private`修饰的属性或者方法，只能在本类中访问或者使用。默认是什么修饰符都不加，默认在当前类中和同一包下都可以访问和使用。

　　我们可以举一个例子，比如 money，如果我们用`private`修饰代表着这是私有的，只能我自己可以使用。如果是`protected`代表着我可以使用，和我有关系的人，比如儿子也可以用。如果是`public`就代表了所有人都可以使用。

　　大家在自己的代码上，修改一下访问修饰符，然后在不同的范围里调用一下你修改的属性和方法。（练习永远比理论更重要！）

## 四、Java 中的 this 关键字

　　我们在书写代码的过程中常常会用到`this`关键字，`this`关键字代表当前对象。使用`this.属性`操作当前对象的属性，`this.方法`调用当前对象的方法。

　　当我们封装对象属性的时候，经常会使用`this`关键字。

　　接下来我们来看看代码中是怎么实现的吧。在上面我们讲了用`private`修饰的属性，我们利用对象访问的时候，必须定义 getter 和 setter 方法，其实在 Eclipse 中可以自动帮我们定义。

1、首先在菜单栏里点击`Source`也可以在左侧右击选择`Source`

![创建 getter 和 setter 方法](img/document-uid79144labid1072timestamp1434943011307.jpg)

2、选择`Grenerate Getters and Setters`

![创建 getter 和 setter 方法](img/document-uid79144labid1072timestamp1434943091631.jpg)

3、系统会自动帮我们检测需要创建 getter 和 setter 方法的属性，我们根据需要勾选就行了。

　　当系统帮我们创建好了 getter 和 setter 方法后，我们会发现系统创建的方法中参数名和我们的属性名一样。为了区分参数名和我们的属性名，我们在属性名前面添加了`this`关键字。

```java
public void setAge(int age) {
        this.age = age;
    } 
```

　　此时就代表我们将一个参数的值赋给我们当前对象的属性。同理`this`关键字可以调用当前对象的方法，同学们自行验证一下吧。

## 五、Java 中的内部类

　　从字面意思我们就可以知道，内部类（ Inner Class ）就是定义在另外一个类里面的类，而包含内部类的类被称为外部类。

内部类的主要作用如下：

1.  内部类提供了更好的封装，可以把内部类隐藏在外部类之内，不允许同一个包中的其他类访问该类

2.  内部类的方法可以直接访问外部类的所有数据，包括私有的数据

3.  内部类所实现的功能使用外部类同样可以实现，只是有时使用内部类更方便

我们通过代码来详细学习一下内部类吧！

#### 成员内部类：

```java
package com.shiyanlou;

//外部类 People
public class People {
    private String name = "LiLei";         //外部类的私有属性
    //内部类 Student
    public class Student {
        String ID = "20151234";               //内部类的成员属性
        //内部类的方法
        public void stuInfo(){
            System.out.println("访问外部类中的 name：" + name);
            System.out.println("访问内部类中的 ID：" + ID);
        }
    }

    //测试成员内部类
    public static void main(String[] args) {
        People a = new People();     //创建外部类对象，对象名为 a
        Student b = a.new Student(); //使用外部类对象创建内部类对象，对象名为 b
        b.stuInfo();   //调用内部对象的 suInfo 方法
    }
} 
```

由此，我们可以知道，成员内部类的使用方法：

1.  Student 类相当于 People 类的一个成员变量，所以 Student 类可以使用任意访问修饰符

2.  Student 类在 People 类里，所以访问范围在类里的所有方法均可以访问 People 的属性（即内部类里可以直接访问外部类的方法和属性，反之不行）

3.  定义成员内部类后，必须使用外部类对象来创建内部类对象，即 `内部类 对象名 = 外部类对象.new 内部类();`

4.  如果外部类和内部类具有相同的成员变量或方法，内部类默认访问自己的成员变量或方法，如果要访问外部类的成员变量，可以使用 this 关键字

#### 静态内部类：

```java
package com.shiyanlou;

//外部类 People
public class People {
    private String name = "LiLei";         //外部类的私有属性

/*外部类的静态变量。
Java 中被 static 修饰的成员称为静态成员或类成员。它属于整个类所有，而不是某个对象所有，即被类的所有对象所共享。静态成员可以使用类名直接访问，也可以使用对象名进行访问。
/*
    static String ID = "510xxx199X0724XXXX"; 

    //静态内部类 Student
    public static class Student {
        String ID = "20151234";               //内部类的成员属性
        //内部类的方法
        public void stuInfo(){
            System.out.println("访问外部类中的 ID：" + People.ID);
            System.out.println("访问内部类中的 ID：" + ID);
        }
    }

    //测试成员内部类
    public static void main(String[] args) {
        Student b = new Student();   //直接创建内部类对象，对象名为 b
        b.stuInfo();                 //调用内部对象的 suInfo 方法
    }
} 
```

静态内部类是 static 修饰的内部类，这种内部类的特点是：

1.  静态内部类不能直接访问外部类的非静态成员，但可以通过 `new 外部类().成员` 的方式访问

2.  如果外部类的静态成员与内部类的成员名称相同，可通过`类名.静态成员`访问外部类的静态成员；如果外部类的静态成员与内部类的成员名称不相同，则可通过`成员名`直接调用外部类的静态成员

3.  创建静态内部类的对象时，不需要外部类的对象，可以直接创建 `内部类 对象名= new 内部类();`

#### 方法内部类

　　方法内部类就是定义在外部类的方法中的内部类，方法内部类只在该方法的内部可见，即只在该方法内可以使用。

例如：

```java
package com.shiyanlou;

//外部类 People
public class People {    
    //外部类中的方法
    public void peopleInfo() {
        final String sex = "man";  //外部类方法中的常量
        class Student {
            String ID = "20151234"; //内部类中的常量
            public void print() {
                System.out.println("访问外部类的方法中的常量 sex：" + sex);
                System.out.println("访问内部类中的变量 ID:" + ID);
            }
        }
        Student a = new Student();  //创建方法内部类的对象
        a.print();//调用内部类的方法
    }
    //测试方法内部类
    public static void main(String[] args) {
        People b = new People(); //创建外部类的对象
        b.peopleInfo();  //调用外部类的方法
    }
} 
```

运行结果：

![运行结果](img/document-uid79144labid1072timestamp1434952802822.jpg)

## 六、作业

　　好了，今天的知识有一点点多，希望同学们能将今天所有的代码，都自己在实验环境里敲一遍，实践才是检验真理的唯一标准，加油！