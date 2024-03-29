# 第 8 节 Swing 入门

## 一、实验简介

厌倦了在控制台使用键盘输入并显示结果的过程？是的，在你现在这台电脑上，已经很少有程序使用这种交互方式。本实验将带你初步进入图形用户界面（`GUI`）的世界，让你学会如何编写屏幕上那些具有特定大小和位置的窗体程序，并在其中添加文本，处理用户的输入。通过本实验，可以让你的程序真正地“有头有脸”——具有更好的人机交互性能。

本章知识点

*   GUI
*   MVC 基础
*   Swing

## 二、先聊聊 GUI 和 MVC

>图形用户界面（Graphical User Interface，简称 GUI，又称图形用户接口）是指采用图形方式显示的计算机操作用户界面。 ——来自 [百度百科 · GUI](http://baike.baidu.com/view/25309.htm)

你在系统中按下的每一个按钮、浏览器中输入网址的地址栏、以及无数个被打开和关闭的窗口，它们都是 GUI 的组成部分。这与我们在前面章节提到的模块化思想不谋而合。Swing 便是 Java 中的一个 GUI，它是基于`模型-视图-控制器`设计模式来进行设计的，通过`事件`对用户的输入进行反应。即使是最简单的一个按钮，也是包含了它的外观（什么颜色，有多大）、内容（按钮上面显示什么文字等）以及行为（对于用户按下时的反应）这三个要素。你可以[进一步了解设计模式](http://baike.baidu.com/view/66964.htm)、[MVC 框架](http://baike.baidu.com/view/5432454.htm)和[事件](http://baike.baidu.com/subview/10625/8069380.htm)这三个方面的知识来作为学习 Swing 的准备。

比如，你在实验楼进行试验的时候，摆在你面前、在你的电脑屏幕上显示的内容，就是`视图`；你在实验环境中每一次的鼠标点击、输入的内容，都有专门的模块来负责处理你的这些输入，可以理解为`控制器`；而你写的代码、实验时的环境，这些内容，都称之为`模型`。

下图表示了 MVC 组件类型的关系和功能。

![MVC](img/document-uid85931labid1099timestamp1436497022095.jpg)

## 三、初识 Swing

Swing 是在[抽象窗口工具箱（AWT）](http://baike.baidu.com/view/209544.htm)的架构上发展而来的一个用户界面库，整个可视组件库的基础构造块是 [JComponent](http://docs.oracle.com/javase/7/docs/api/javax/swing/JComponent.html)。它是所有组件的父类，为所有组件提供了绘制的基础架构。换言之，所有的 Swing 组件都是由它派生而来。

基于 Swing 制作的 Java 程序就是由一个一个的组件构成的，开发的过程有点像组装乐高积木。下面我们就通过实验来熟悉一些基本的组件。

请在 Eclipse 中新建项目`HelloSwing`，创建包`com.shiyanlou.course`，新建一个包含主方法的类`MySwingWindow`。在创建类时，你可以使用 Eclipse 来帮你生成此类的主方法，就像下图那样。

![生成主方法](img/document-uid85931labid1099timestamp1436497607932.jpg)

这样，在创建好指定的类之后，你可以在代码中看到自动生成的`main()`方法，然后就能接着增添更多功能模块了。

![生成主方法](img/document-uid85931labid1099timestamp1436497719587.jpg)

### 1\. 盛放控件的盘子——JFrame

JFrame 类就是一个容器，允许您把其他组件添加到它里面，把它们组织起来，并把它们呈现给用户。JFrame 在大多数操作系统中是以窗口的形式注册的，这意味着你可以对其进行最小化和最大化，甚至移动这个窗口。

如果要打个比方的话，你的脸就是一个容器，眼睛、耳朵、鼻子和嘴巴这些“控件”需要放在脸上，别人看到你这个“界面”实际上就是你的脸，以及上面的“控件”。

不同的教材对于 JFrame 的称呼是有差别的。为了帮助你理解，在本实验中，我们称之为“窗体”。

下面是它包含的一些方法，你最好在 Java SE 官方 API 中去查阅它们的用法和详细说明： >- `get/setTitle()`， 获取/设置窗体的标题。

*   `get/setState()`，获取/设置窗体的最小化、最大化等状态。
*   `is/setVisible()`，获取/设置窗体的可视状态，换言之，是否在屏幕上显示。
*   `get/setLocation()`，获取/设置窗体在屏幕上在什么位置出现。
*   `get/setsize()`，获取/设置窗体的大小。
*   `add()`，将组件添加到窗体中，这个过程把各个控件形成了一个整体。

对于 Swing 应用程序，我们如果要将组件放在 JFrame 上，则需要继承 JFrame 类。我们来尝试创建一个窗体吧。

主要的代码如下：

```java
package com.shiyanlou.course;

import javax.swing.JFrame;

public class MySwingWindow extends JFrame {
    //此处通过继承 JFrame 类来使我们自己的 MySwingWindow 具有窗体的一些属性和方法

    public MySwingWindow(){
        //在窗体的构造方法中设置窗体的各项属性

        super();
        //使用 super() 来引用父类的成分，用 this 来引用当前对象

        this.setSize(400, 300);
        //设置窗体的大小

        this.getContentPane().setLayout(null);
        //返回此窗体的 contentPane 对象，设置其布局
        //这一句不太懂的话也不用担心，先写着

        this.setTitle("My First Swing Window");
        //设置窗体的标题
    }

    public static void main(String[] args) {
        // TODO Auto-generated method stub

        MySwingWindow window = new MySwingWindow();
        //声明一个窗体对象 window

        window.setVisible(true);
        //设置这个窗体是可见的
    }
} 
```

你不必一开始就写`import`相关代码，通常在需要引入相应的包时，使用自动提示给出的方案即可，就像下面这样：

![修复错误](img/document-uid85931labid1099timestamp1436499083064.jpg)

和所有的 Java 程序一样，都需要`main()`方法才能让程序“跑”起来，所以我们只需要在其中创建好一个窗体对象并设置其可见就行了。

不妨编译并运行一下，你应该可以看到一个最原始的窗体程序。

![此处输入图片的描述](img/document-uid85931labid1099timestamp1436499552599.jpg)

### 2\. 个性化 Swing 应用程序

有了最基础的容器，我们就可以在上面添加各式各样的控件。Swing 中的控件数量是巨大的，但它们的使用方法都是相通的，你可以在 API 文档中查阅每种控件的属性及其设置方法、获取属性和数据的方法等等。

我们在本实验中先为大家介绍一些常用的控件，在上一步的基础上继续个性化我们的`MySwingWindow`。

首先添加 Swing 库中最基础的组件——标签`JLabel`。JLabel 可以用作文本描述和图片描述，常用的方法如下：

>- `get/setText()`， 获取/设置标签的文本。

*   `get/seticon()`， 获取/设置标签的图片。

你需要先声明这个控件对象。

```java
private JLabel myLabel; 
```

然后编写一个`getJLabel()`方法，用于返回一个 JLabel 实例.

```java
private JLabel getJLabel() {
    //此处的返回值类型为 JLabel

    if(myLabel == null) {

        myLabel = new JLabel();
        //实例化 myLabel 对象

        myLabel.setBounds(5, 10, 250, 30);
        //使用 setBounds()方法设置尺寸
        //四个参数的分别是 x,y,width,height
        //代表了横向、纵向的位置，以及标签自身的宽和高

        myLabel.setText("Hello! Welcome to shiyanlou.com");
        //使用 setText()方法设置要显示的文本
    }

    return myLabel;
    //返回创建的实例
} 
```

同样，我们来添加一个文本框 JTextFiled，它包含的方法与 JLabel 类似，你可以在 API 文档中查阅更多的方法。

```java
private JTextField getJTextField() {
    //此处的返回值类型为 JTextField

    if(myTextField == null) {
    //加上这个判断是为了防止出错

        myTextField = new JTextField();
        //实例化 myTextField 对象

        myTextField.setBounds(5, 45, 200, 30);
        //设置它的位置和尺寸

        myTextField.setText("Shi Yan Lou");
        //设定它要显示的字符串

    }

    return myTextField;
    //返回这个实例
} 
```

再来做一个按钮 JButton。与上述控件不同的是，我们在这里为它添加一个事件响应，当年按下按钮的时候它就能做一些事情了。

```java
private JButton getJButton() {
    //此处的返回值类型为 JButton 

    if(myButton == null) {

        myButton = new JButton();
        //实例化 myTextField 对象
        myButton.setBounds(5, 80, 100, 40);
        //设置它的位置和尺寸
        myButton.setText("Click me!");
        //设定它要显示的字符串
        myButton.addActionListener(new ActionListener() {
            //为其添加一个事件监听，从而使这个按钮可以响应用户的点击操作
            //ActionListener 是用于接收操作事件的侦听器接口。
            //对处理操作事件感兴趣的类可以实现此接口，而使用该类创建的对
            //可使用组件的 addActionListener 方法向该组件注册。
            //在发生操作事件时，调用该对象的 actionPerformed 方法。

            public void actionPerformed(ActionEvent e) {
                //该方法会在发生操作时被调用，我们要做的事情就可以写在这里面
                //比如我们下面要做的事情就是改变之前两个控件里面的文字颜色和背景色

                myLabel.setForeground(Color.RED);
                //设置此组件的前景色。

                myTextField.setBackground(Color.BLUE);
                //设置此组件的背景色。
            }
        });
    }

    return myButton;
    //返回这个实例
} 
```

代码`myButton.addActionListener(new ActionListener(){ ... });`中的`new ActionListener(){ ... }`是一种名为[匿名类](http://baike.baidu.com/view/10780707.htm)的用法。

最后，我们在这个窗体的构造方法`public MySwingWindow()`中，将这三个控件的获取方法添加进去。

```java
this.add(getJLabel(),null);
this.add(getJTextField(), null);
this.add(getJButton(),null);
//在自定义的 JFrame 构造方法中使用 add()方法来添加控件
//add()方法可以将指定组件添加到此容器的给定位置上
//第一个参数为待添加的组件，这里的组件来自我们的返回值
//第二个参数为描述组件的布局限制的对象，我们不加限制，所以填 null 
```

如果你不是很清楚整个代码的结构，可以参考下面的完整代码：

```java
package com.shiyanlou.course;

import java.awt.Color;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JTextField;

public class MySwingWindow extends JFrame {
    //此处通过继承 JFrame 类来使我们自己的 MySwingWindow 具有窗体的一些属性和方法

    /**
     * 
     */
    private static final long serialVersionUID = 8978037719568897634L;

    //首先需要声明各个控件
    private JLabel myLabel;
    private JTextField myTextField;
    private JButton myButton;

    public MySwingWindow(){
        //在窗体的构造方法中设置窗体的各项属性

        super();
        //使用 super() 来引用父类的成分，用 this 来引用当前对象

        this.setSize(400, 300);
        //设置窗体的大小

        this.getContentPane().setLayout(null);
        //返回此窗体的 contentPane 对象，设置其布局
        //这一句不太懂的话也不用担心，先写着

        this.setTitle("My First Swing Window");
        //设置窗体的标题

        this.add(getJLabel(),null);
        this.add(getJTextField(), null);
        this.add(getJButton(),null);
        //在自定义的 JFrame 构造方法中使用 add()方法来添加控件
        //add()方法可以将指定组件添加到此容器的给定位置上
        //第一个参数为待添加的组件，这里的组件来自我们的返回值
        //第二个参数为描述组件的布局限制的对象，我们不加限制，所以填 null
    }

    private JLabel getJLabel() {
        //此处的返回值类型为 JLabel

        if(myLabel == null) {

            myLabel = new JLabel();
            //实例化 myLabel 对象

            myLabel.setBounds(5, 10, 250, 30);
            //使用 setBounds 方法设置其位置和尺寸
            //四个参数的分别是 x,y,width,height
            //代表了横向、纵向的位置，以及标签自身的宽和高

            myLabel.setText("Hello! Welcome to shiyanlou.com");
            //使用 setText 方法设置要显示的文本
        }

        return myLabel;
        //返回创建的实例
    }

    private JTextField getJTextField() {
        //此处的返回值类型为 JTextField

        if(myTextField == null) {
        //加上这个判断是为了防止出错

            myTextField = new JTextField();
            //实例化 myTextField 对象

            myTextField.setBounds(5, 45, 200, 30);
            //设置它的位置和尺寸

            myTextField.setText("Shi Yan Lou");
            //设定它要显示的字符串

        }

        return myTextField;
        //返回这个实例
    }

    private JButton getJButton() {
        //此处的返回值类型为 JButton 

        if(myButton == null) {

            myButton = new JButton();
            //实例化 myTextField 对象
            myButton.setBounds(5, 80, 100, 40);
            //设置它的位置和尺寸
            myButton.setText("Click me!");
            //设定它要显示的字符串
            myButton.addActionListener(new ActionListener() {
                //为其添加一个事件监听，从而使这个按钮可以响应用户的点击操作
                //ActionListener 是用于接收操作事件的侦听器接口。
                //对处理操作事件感兴趣的类可以实现此接口，而使用该类创建的对
                //可使用组件的 addActionListener 方法向该组件注册。
                //在发生操作事件时，调用该对象的 actionPerformed 方法。

                public void actionPerformed(ActionEvent e) {
                    //该方法会在发生操作时被调用，我们要做的事情就可以写在这里面
                    //比如我们下面要做的事情就是改变之前两个控件里面的文字颜色和背景色

                    myLabel.setForeground(Color.RED);
                    //设置此组件的前景色。

                    myTextField.setBackground(Color.BLUE);
                    //设置此组件的背景色。
                }
            });
        }

        return myButton;
        //返回这个实例
    }

    public static void main(String[] args) {
        // TODO Auto-generated method stub

        MySwingWindow window = new MySwingWindow();
        //声明一个窗体对象 window

        window.setVisible(true);
        //设置这个窗体是可见的
    }
} 
```

检查一下代码，确认无误后点击编译并运行，自己动手做的第一个窗体程序就出来了。

![结果](img/document-uid85931labid1099timestamp1436507068850.jpg)

尝试一下点击那个按钮呢？是不是发现文字颜色改变了？

![此处输入图片的描述](img/document-uid85931labid1099timestamp1436507106499.jpg)

## 四、实验总结

通过本章的学习，我们应该了解到了 GUI 和 MVC 的一些知识，初步地学习到了如何建立一个带图形界面的应用程序。万里长征第一步，这仅仅是一个开始，更多的控件和事件处理我们会结合后续的课程继续为大家讲解。

为了更加深入地学习 Swing 组件，你可以继续在实验楼进行该实验——《[Java 版的计算器](https://www.shiyanlou.com/courses/185)》。

## 五、作业

在这个实验的基础上，继续为你的窗体添加一些控件吧！比如`JCheckBox`、`JMenuBar`等等。

## 参考文档

*   [Java SE 官方 API 文档 - java.swing 类](http://docs.oracle.com/javase/7/docs/api/javax/swing/package-frame.html)