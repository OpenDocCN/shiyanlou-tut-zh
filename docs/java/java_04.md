# 第 4 节 数学工具

## 一、实验简介

Java 为开发者提供了基本的数学工具类——`Math`类。Math 类包含用于执行基本数学运算的方法，如初等指数、对数、平方根和三角函数。利用这些方法，我们可以对数值进行更加复杂的运算。

本章知识点：

*   查看数学常量
*   三角函数计算
*   随机数生成

下面我们通过一些例子来看 Math 类中都有哪些内容。

## 二、Math 类中的常量

`Math`类自带了两个常量，分别是：

*   比任何其他值都更接近 e（即自然对数的底数）的 double 值。
*   比任何其他值都更接近 pi（即圆的周长与直径之比）的 double 值。

在 Eclipse 中创建一个`MathConstant`项目，新建一个`com.shiyanlou.course`包并创建一个`ShowConstant`类，你可以在`main()`方法中尝试打印出它们的值。

主要的代码如下：

```java
package com.shiyanlou.course;

public class ShowConstant {

    public static void main(String[] args) {

        System.out.println("The natural logarithm is " + Math.E);
        System.out.println("The circumference ratio is " + Math.PI);
    }
} 
```

点击编译并运行，你能得到如下图的结果：

![Math 类中的常量](img/document-uid85931labid1095timestamp1435830557193.jpg)

## 三、如何利用数学工具

### 3.1 三角函数计算

如你在初高中所学，三角函数是数学领域里十分重要的一个分支，很多的问题通过使用三角函数能够变得容易。而 Math 类也提供了常用的三角、反三角函数的实现，我们动手来利用三角函数计算一些角度的正弦、余弦、正切值。

在 Eclipse 中创建一个`MathTools`项目，新建一个`com.shiyanlou.course`包并创建一个`TriFunction`类。

编写`main()`函数，这里将再次用到上个例子中的数学常量π，主要的代码如下：

```java
package com.shiyanlou.course;

public class TriFunction {

    public static void main(String[] args) {
        System.out.println("sin30 = " + Math.sin(Math.PI / 6));
        // 计算 30°的正弦值，参数是用弧度表示的角，即π的六分之一

        System.out.println("cos30 = " + Math.cos(Math.PI / 6));
        // 计算 30°的余弦值，这些计算三角函数的方法，其参数和返回值的类型都为 double

        System.out.println("tan30 = " + Math.tan(Math.PI / 6));
        // 计算 30°的正切值
    }
} 
```

检查代码，点击编译并运行，可以在控制台看到计算的结果：

![三角函数](img/document-uid85931labid1095timestamp1435895555255.jpg)

也许你会觉得简单的计算很枯燥，但在合适的地方用到它们可以让你的程序（比如游戏中飞行轨迹的计算）更加精彩。

### 3.2 生成彩票号码

有了之前的基础，我们来做个更好玩的项目——彩票生成器。目前市面上彩票的种类很多，我们就以大乐透为例吧。大乐透的规则是：从 1~35 中随机选取 5 个不重复的数字，从 1~12 中随机选取 2 个不重复的数字，这些数字组成了一个七位数。如果你买的号码与摇出来的号码相同，那么你一夜之间可能就不会当程序员了。

因为彩票号码是随机生成的，所以这个项目中我们也会带大家认识随机数的使用方法。首先请在 Eclipse 中创建一个`GenerateLottery`项目，新建一个`com.shiyanlou.course`包并创建一个`Lottery`类。

对于该项目所需要用到的 API，你需要引入下面一些包：

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Random; 
```

由于彩票号码的前半段和后半段的规则和个数略有不同，所以我们分别创建生成它们的方法。

首先来创建生成前段号码的方法`getHeadNumber()`，该方法需要从 1 到 36 中随机选取一个数作为号码（即摇号的过程）。对于个位数，为了显示上更整齐，所以我们在其十位再放一个“0”。再者，为了结果更加易读，我们还需要调用`Collections.sort()`方法对结果进行排序。

代码片段如下：

```java
public List<String> getHeadNumber() {
        List<String> list = new ArrayList<String>(); 
        // 创建彩票的前半段号码集合
        String lotteryNumber = "";
        for (int i = 1; i < 36; i++) { 
            // 这个循环是为了初始化彩票的前半段号码集合

            if (i < 10) {
                list.add("0" + i + "  ");
                // 在集合中添加 0~9 的号码，因为是个位数，为了显示上更加人性化，所以需要在十位的地方添加“0”
            } else {
                list.add("" + i + "  ");
                // 向集合添加大于 9 的号码，即添加双位数
            }
        }

        int roundIndex = 0;
        // 创建并初始化随机数

        List<String> lotteryList = new ArrayList<String>(); 
        // 保存前段号码的 List 集合

        for (int j = 0; j < 5; j++) {
            int amount = list.size(); // 获取前半段号码的个数
            Random r = new Random(); // 创建并实例化 Random 的对象
            roundIndex = r.nextInt(amount); // 获取一个 0 到 amount-1 的随机数
            lotteryNumber = list.get(roundIndex); // 获取彩票数字，即摇号的号码
            lotteryList.add(lotteryNumber); // 将彩票号码添加 lotteryList 中
            list.remove(roundIndex); // 移除刚刚产生的号码
        }
        Collections.sort(lotteryList); 
        // 对前半段号码进行排序，排序的目的是为了让结果更具可读性
        return lotteryList;
        //返回前半段号码
    } 
```

此处用到了`Random()`方法，在创建一个 Random 对象`r`后，你可以调用该对象的`nextInt()`方法得到一个随机数。`r.nextInt(amount);`中的 amount 是随机数的上限，产生的随机数为 0 到 100（不含 100）的整数。 你可以[了解更多 Random 的用法](http://www.cnblogs.com/ningvsban/p/3590722.html)。

和前半段号码类似，我们接着来写生成后半段号码的方法`getRearNum()`，代码片段如下：

```java
public List<String> getRearNumber() {
        List<String> list = new ArrayList<String>(); 
        // 创建后半段号码集合，也就是最后两个球的数字

        String lotteryNumber = "";
        for (int i = 1; i < 13; i++) { 
            // 初始化后半段号码集合

            if (i < 10) {
                list.add("0" + i + "  ");
                // 添加 0~9 的号码，原理同前半段
            } else {
                list.add("" + i + "  ");
                // 添加大于 9 的号码
            }
        }
        int roundIndex = 0;
        //创建并初始化随机数

        List<String> lotteryList = new ArrayList<String>(); 
        // 保存后半段号码的 List 集合

        for (int j = 0; j < 2; j++) {
            int amount = list.size(); // 获取后半段号码的个数
            Random r = new Random(); // 创建并实例化 Random 的对象
            roundIndex = r.nextInt(amount); // 获取一个 0 到 amount-1 的随机数
            lotteryNumber = list.get(roundIndex); // 摇号
            lotteryList.add(lotteryNumber); // 将彩票号码添加 lotteryList 中
            list.remove(roundIndex); // 移除刚刚产生的号码
        }

        Collections.sort(lotteryList); 
        // 对后半段号码进行排序
        return lotteryList;
    } 
```

现在，根据规则我们已经能够产生对应的数字了。再接再厉，我们再做一个生成最终结果的方法，把这两个号段组装成整体的号码，并且按照用户的要求产生多少组号码，在控制台输出它们。

下面给出了这个方法的代码片段：

```java
public void generateLottery(String groupNum) {

        int groupNumber = 0;
        //为了避免不必要的错误，一般在创建变量时都要为其赋初始值

        groupNumber = Integer.parseInt(groupNum);
        StringBuilder sbuiler = new StringBuilder();
        // 创建字符串生成器对象，使用字符串生成器能够较为方便地在字符串中追加内容

        for (int i = 0; i < groupNumber; i++) {
            List<String> startList = getHeadNumber();
            // 获得彩票前半段号码的集合

            List<String> endList = getRearNumber();
            // 获得彩票后半段号码的集合

            for (int m = 0; m < startList.size(); m++) {
                sbuiler.append(startList.get(m));
                // append()即为追加方法，用于添加彩票的前半段号码到字符串生成器中
            }
            sbuiler.append("    ");
            for (int n = 0; n < endList.size(); n++) {
                sbuiler.append(endList.get(n));
                // 添加彩票的后半段号码到字符串生成器中
            }
            sbuiler.append("\n");
        }

        System.out.println(sbuiler.toString());
        //将每组生成好的彩票号码即时输出
    } 
```

为了验证我们的实现无误，在同一个包中创建一个名为`LotteryTest`的测试类，编写其`main()`方法。在其中创建一个彩票对象并调用其产生号码的方法。

`LotteryTest.java`中的内容为：

```java
package com.shiyanlou.course;

import java.util.Scanner;

public class LotteryTest {

    public static void main(String[] args) {

        Lottery l = new Lottery();
        Scanner scan = new Scanner(System.in);// 创建扫描器
        System.out.println("Please input the number of lottery group(s) that you want to generate : ");
        // 获得用户输入的需要生成的中奖号码个数

        String groupNum = scan.nextLine();
        l.generateLottery(groupNum);

    }

} 
```

检查一下代码，点击编译并运行，在控制台输入你要生成多少组的彩票号码，按下回车键你就能看到生成的结果。

![生成的彩票](img/document-uid85931labid1095timestamp1435907935784.jpg)

最后，希望这些号码能给你带来好运。

## 四、实验总结

我们在这些实验中学习了调用查看数学常量，使用三角函数的相关方法，并且调用了随机数的生成方法。

这一节的实验稍微复杂了一点，不过能用自己写的程序来机选号码也是很有趣的一件事儿呢。

为什么我们先写了各个子功能，最后才来写主方法呢？这是因为在开发过程中，一般情况下，最好是让各个功能模块各司其职，`main()`方法中只负责数据的输入输出和调用子功能。这种设计思想就叫[模块化](http://baike.baidu.com/link?url=gX5UOScH8s40gJmV65zxEisTo5osO8NH2VHQ8UHUYsB7FW5C0hEB9CfkY-LlfXyV5MIgTc9fGKnIu7fvv0s-8K#4)设计。

除了我们刚刚用到的这些，`Math`类中更多的方法可以在文末参考文献中提供的官方 API 文档中查看。

## 五、作业

在数学领域，角的大小可以通过`角度`和`弧度`来度量，请查阅 API 文档，使用`Math`类中的`toRadians()`和`toDegrees()`方法，实现角度和弧度之间的转换。

## 参考文档

*   [Java 数学工具包 java.lang.Math 类](http://blog.csdn.net/itlwc/article/details/10077305)
*   [Java SE 官方 API 文档 - Java.lang.Math](http://docs.oracle.com/javase/7/docs/api/java/lang/Math.html)
*   [Java SE 官方 API 文档 - Java.util.Random](http://docs.oracle.com/javase/7/docs/api/java/util/Random.html)