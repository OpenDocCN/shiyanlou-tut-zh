# 第 4 节 事件

## 实验简介

JavaScript 是事件驱动的，本节实验介绍什么是事件，并举例说明一些常用的事件。

## 一、什么是事件

JavaScript 是 **基于对象** 的语言。这与 Java 不同，Java 是 **面向对象** 的语言。

**基于对象** 的基本特征，就是采用 **事件驱动**。

通常鼠标或热键的动作我们称之为 **事件**（Event），而由鼠标或热键引发的一连串程序的动作，称之为 **事件驱动**。对事件进行处理程序或函数，我们称之为 **事件处理程序**。

## 二、事件处理程序

在某个事件发生的时候，执行特定的 JavaScript 代码（事件处理程序）。

比如点击页面的某个按钮这是一个很常见的事件，执行对应的事件处理程序，弹出提示小窗口，代码如下：

```
<html>
<head>
  <script>
    function click_button() {     // 事件处理程序，弹出提示窗
      alert('welcome to shiyanlou');
    }
  </script>
</head>

<body align="center">
  <br></br>
  <button onclick="click_button()">click me</button>  <!--发生事件 onclick 的时候，执行 click_button()-->
</body>
</html> 
```

![0401](img/0401.jpg)

## 三、常用事件

除了刚才提到的 onclick 事件，还有这些常用的事件：

*   onclick 单击
*   ondblclick 双击
*   onfocus 元素获得焦点
*   onblur 元素失去焦点

* * *

*   onmouseover 鼠标移到某元素之上
*   onmouseout 鼠标从某元素移开

* * *

*   onmousedown 鼠标按钮被按下
*   onmouseup 鼠标按键被松开

* * *

*   onkeydown 某个键盘按键被按下
*   onkeyup 某个键盘按键被松开
*   onkeypress 某个键盘按键被按下并松开

* * *

其中，onmouseover 和 onmouseout 事件可用于在鼠标移至 HTML 元素上和移出元素时触发函数。比如这一例子：

```
<html>
<head></head>

<body>
<div style="background-color:green;width:200px;height:50px;margin:20px;padding-top:10px;color:#ffffff;font-weight:bold;font-size:18px;text-align:center;"

onmouseover="this.innerHTML='good'"

onmouseout="this.innerHTML='you have moved out'"

>move your mouse to here</div>
</body>

</html> 
```

鼠标移入时，显示“good”，鼠标移出时显示“you have moved out”：

![0402](img/0402.jpg)

onmousedown, onmouseup 是鼠标 **压下** 和 **松开** 的事件。首先当点击鼠标按钮时，会触发 onmousedown 事件，当释放鼠标按钮时，会触发 onmouseup 事件。举例说明：

```
<html>
<head>
  <script>

    function mDown(obj)    // 按下鼠标 的 事件处理程序
    {
    obj.style.backgroundColor="#1ec5e5";
    obj.innerHTML="release your mouse"
    }

    function mUp(obj)     // 松开鼠标 的 事件处理程序
    {
    obj.style.backgroundColor="green";
    obj.innerHTML="press here"
    }

  </script>
</head>

<body>
<div style="background-color:green;width:200px;height:35px;margin:20px;padding-top:20px;color:rgb(255,255,255);font-weight:bold;font-size:18px;text-align:center;"

onmousedown="mDown(this)"

onmouseup="mUp(this)"

>press here</div>
</body>

</html> 
```

运行结果可见，按下鼠标时，显示“release your mouse”，背景变为蓝色；松开鼠标后，显示为“press here”，背景变为绿色。

![0403](img/0403.jpg)

## 作业

请实现一个验证输入的功能，当输入中为空，失去焦点时弹出对话框提示“input cannot be empty”。