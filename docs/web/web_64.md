# 第 3 节 CSS 基本样式（一）

## 1.CSS 背景：

css 允许应用纯色作为背景，也允许使用背景图像创建相当复杂的效果

```js
属性|描述
----|----
background-attachment|背景图像是否固定或者随着页面的其余部分滚动
background-color|设置元素的背景颜色
background-image|把图片设置为背景
background-position|设置背景图片的起始位置
background-repeat|设置背景图片是否及如何重复
```

表格中的属性我们将会通过下面两个例子给大家一一讲述。 **例子 1：背景颜色**

index.html

```js
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <link rel="stylesheet" href="style.css" type="text/css">
    </head>
    <body>
        <p>I love shiyanlou</p>
    </body>
</html> 
```

style.css

```js
/\*背景颜色设置为红色\*/
body{
   background-color: red;
}

p{
    width: 150px;
    padding: 10px;
    background-color: #0014ff;
} 
```

**例子 2：设置背景图**

我们需要在工程文件夹下放置一张图片，我们的举例当中放置一个名为 python.jpg 的图片，该图片用于 css 设置背景图时的引用。图片如下：

![此处输入图片的描述](img/10-1.jpg)

index.html

```js
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <link rel="stylesheet" href="style.css" type="text/css">
    </head>
    <body>
        <p>I love shiyanlou</p>
    </body>
</html> 
```

style.css

```js
/\*把 python.jpg 设置为背景图片\*/
body{
   background-image: url("python.jpg")
} 
```

**运行结果：**

![此处输入图片的描述](img/10-2.jpg)

大家可以看到显示出来的背景图有很多个 python 图片，这是因为默认情况下背景图显示重复，我们可以`background-repeat`属性来设置图片是否可重复。 css.css 添加以下内容

```js
body{
   background-image: url("python.jpg");
   background-repeat: no-repeat;
} 
```

>**no-repeat 表示不能重复，repeat 可重复，repeat-x 表示 x 轴重复，repeat-y 表示 y 轴重复**

运行结果： ![此处输入图片的描述](img/10-3.jpg)

`background-position`用来设置图片的起始位置 下面的例子在 body 元素中将一个背景图从中间顶部放置：

```js
body{
   background-image: url("python.jpg");
   background-repeat: no-repeat;
   background-position:center top;
   /\*大家可以尝试去掉 top 来对比显示效果\*/
} 
```

![此处输入图片的描述](img/10-4.jpg)

为 background-position 属性提供值有很多方法。首先，可以使用一些关键字：top、bottom、left、right 和 center。通常，这些关键字会成对出现，不过也不总是这样。还可以使用长度值，如 100px 或 5cm，最后也可以使用百分数值。不同类型的值对于背景图像的放置稍有差异。我们这里不详细阐述属性值，需要用到的时候大家可以在 w3school 上面查找。

**背景关联** 如果网页比较长，那么当网页向下滚动时，背景图像也会随之滚动。当网页滚动到超过图像的位置时，图像就会消失。 我们可以通过`background-attachment`属性防止这种滚动。通过这个属性，可以声明图像相对于可视区是固定的（fixed），因此不会受到滚动的影响：

```js
body{
   background-image: url("python.jpg");
   background-repeat: no-repeat;
   background-attachment:fixed;
} 
```

防止背景图滚动相信大家大家也是经常见到，尤其是一些烦人的广告。

## 2.css3 背景

```js
属性|描述
---|---
background-size|规定背景图片的尺寸
background-origin|规定背景图片的定位区域
background-clip|规定背景的绘制区域
```

我们还是引用上面的例子来讲述这些属性。

```js
body{
   background-image: url("python.jpg");
   background-repeat: no-repeat;
   background-size：100px 100px;
   /\*图片大小设置成为 100px*100px\*/
} 
```

运行结果：

![此处输入图片的描述](img/10-5.jpg)

剩下的两个属性使用起来也是非常简单，大家可以自己测试一下效果，这里就不一一给大家讲述了。

## 第二节 css 样式-文本

**CSS 文本属性可定义文本的外观。 通过文本属性，您可以改变文本的颜色、字符间距，对齐文本，装饰文本，对文本进行缩进，等等。**

```js
属性|描述
--|--
color|文本颜色
direction|文本方向
line-height|行高
letter-spacing|字符间距
text-align|对齐元素中的文本
text-decoration|向文本添加修饰
text-indent|缩进元素中文本的首行
text-transform|元素中的字母
unicode-bidi|设置文本方向
white-space|元素中空白的处理方式
word-spacing|字间距
```

上面表格中的属性我们不会一一讲述，只讲述一些常用的属性，主要是让大家边学边练。其他内容大家可以自己测试，w3c 上面也可以查看每个属性的具体内容。

**例子 1 颜色 color**

index.html

```js
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <link rel="stylesheet" href="style.css" type="text/css">
    </head>
    <body>
        <p>查看颜色</p>
        <h1>标题查看颜色</h1>
    </body>
</html> 
```

style.css

```js
body{
   color: aqua;
} 
```

运行结果：

![此处输入图片的描述](img/10-6.jpg)

**大家可以看到我们是给 body 设置的颜色，但是 p 标签和 h1 的标签也显示了 body 设置的字体颜色，说明了两个标签继承了 body 的字体颜色。**

**例子 2 text-align**

`text-align`是一个基本的属性，它会影响一个元素中的文本行互相之间的对齐方式。它的前 3 个值相当直接，不过第 4 个和第 5 个则略有些复杂。

值 left、right 和 center 会导致元素中的文本分别左对齐、右对齐和居中。

西方语言都是从左向右读，所有`text-align`的默认值是 left。文本在左边界对齐，右边界呈锯齿状（称为“从左到右”文本）。 index.html

```js
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <link rel="stylesheet" href="style.css" type="text/css">
    </head>
    <body>
        <p>hello shiyanlou</p>
    </body>
</html> 
```

style.css

```js
body{
   color: red;
   text-align: center;
} 
```

运行结果： ![此处输入图片的描述](img/10-7.jpg)

**例子 3 text-indent**

缩进文本 把 Web 页面上的段落的第一行缩进，这是一种最常用的文本格式化效果。 CSS 提供了`text-indent`属性，该属性可以方便地实现文本缩进。 通过使用`text-indent`属性，所有元素的第一行都可以缩进一个给定的长度，甚至该长度可以是负值。 这个属性最常见的用途是将段落的首行缩进，下面的规则会使所有段落的首行缩进 5 em：

```js
p {text-indent: 5em;} 
```

下面的 index.html 中代码为：

```js
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <link rel="stylesheet" href="style.css" type="text/css">
    </head>
    <body>
        <div>
            <h3>实验楼</h3>
            <p>做最真实的 IT 学习环境，</p>
            <p>做最真实的 IT 学习环境。</p>
            <p>做最真实的 IT 学习环境，</p>
            <p>做最真实的 IT 学习环境。</p>

        </div>
    </body>
</html> 
```

这时如果不加任何的 css 修饰。显示的效果为： ![此处输入图片的描述](img/10-8.jpg)

我们的 style.css 代码中加入以下内容缩进让实验楼三个字缩进 2 个字节。

```js
 h3{
   text-indent: 5em;
 } 
```

运行结果：

![此处输入图片的描述](img/10-9.jpg)