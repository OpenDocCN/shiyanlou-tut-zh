# 第 3 节 抽象工厂模式

## 一、本节目标

我们本节课程主要介绍【抽象工厂模式】的相关知识，将会涉及以下内容：

> * 什么是抽象工厂模式

*   抽象工厂模式类图
*   抽象工厂模式代码
*   抽象工厂模式应用
*   工厂方法模式、抽象工厂模式区别

## 二、什么是 抽象工厂模式

抽象工厂模式（Abstract Factory Pattern）是一种软件开发设计模式。抽象工厂模式提供了一种方式，可以将一组具有同一主题的单独的工厂封装起来。如果比较抽象工厂模式和工厂模式，我们不难发现前者只是在工厂模式之上增加了一层抽象的概念。抽象工厂是一个父类工厂，可以创建其它工厂类。所以我们也叫它 “工厂的工厂”。（想想上节课的 “女娲娘娘”，这简直就是 “女娲娘娘的亲娘” 啊...）

## 三、 抽象工厂模式类图

“女娲娘娘”只有一个，而我们的工厂却可以有多个，因此在这里用作例子就不合适了。作为“女娲娘娘”生产出来的男人女人们，那就让我们来当一次吃货吧。（吃的东西总可以任性多来一点...）

现在，假设我们有 A、B 两个厨房。每个厨房拥有的餐具和食品都不一样，但是用户搭配使用的方式，比如刀子和苹果、杯子和牛奶等等，我们假设是一致的。

![图片描述信息](img/userid46108labid867time1428915237612.jpg)

## 四、 抽象工厂模式示例代码

Talk is cheap, show me the code.

```java
// 抽象工厂
public interface KitchenFactory {
    public Food getFood();
    public TableWare getTableWare();
}

// 抽象食物
public interface Food {
    public String getFoodName();
}

// 抽象餐具
public interface TableWare {
    public String getToolName();
}

// 以具体工厂 AKitchen 为例
public class AKitchen implements KitchenFactory {

    public Food getFood() {
       return new Apple();
    }

    public TableWare getTableWare() {
       return new Knife();
    }
}

// 具体食物 Apple 的定义如下
public class Apple implements Food{
    public String getFoodName() {
       return "apple";
    }
}

// 具体餐具 Knife 的定义如下
public class Knife implements TableWare { 
    public String getToolName() {
       return "knife";
    }
}

// 吃货要开吃了
public class Foodaholic {

    public void eat(KitchenFactory k) {
       System.out.println("A foodaholic is eating "+ k.getFood().getFoodName()
              + " with " + k.getTableWare().getToolName() );
    }

    public static void main(String[] args) {
       Foodaholic fh = new Foodaholic();
       KitchenFactory kf = new AKitchen();
       fh.eat(kf);

    }
} 
```

## 五、抽象工厂模式应用

抽象工厂模式特别适合于这样的一种产品结构：产品分为几个系列，在每个系列中，产品的布局都是类似的，在一个系列中某个位置的产品，在另一个系列中一定有一个对应的产品。这样的产品结构是存在的，这几个系列中同一位置的产品可能是互斥的，它们是针对不同客户的解决方案，每个客户都只选择其一。

## 六、工厂方法模式、抽象工厂模式区别

工厂方法模式、抽象工厂模式，傻傻分不清楚...

为了解释得更清楚，先介绍两个概念：

*   **产品等级结构**：比如一个抽象类是食物，其子类有苹果、牛奶等等，则抽象食物与具体食物名称之间构成了一个产品等级结构。食物是抽象的父类，而具体的食物名称是其子类。

*   **产品族**：在抽象工厂模式中，产品族是指由同一个工厂生产的，位于不同产品等级结构中的一组产品。如 AKitchen 生产的苹果、刀子，苹果属于食物产品等级结构中，而刀子则属于餐具产品等级结构中。

因此工厂方法模式、抽象工厂模式最大的区别在于：

工厂方法模式：针对的是 **一个产品等级结构**。

抽象工厂模式：针对 **多个产品等级结构**。

吃货们，懂了吧？

## 七、小结

> 国际惯例，总个结。本次课程我们做了一回吃货，学习了【抽象工厂模式】的相关知识。

## 八、参考文档

> * [Java Design Pattern: Abstract Factory](http://www.programcreek.com/2013/02/java-design-pattern-abstract-factory/) > * [抽象工厂模式解析例子](http://tianli.blog.51cto.com/190322/32545)