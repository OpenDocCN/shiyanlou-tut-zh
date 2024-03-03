# 第 3 节 管理 jQuery 包装集

## 一、 摘要

在使用 jQuery 选择器获取到 jQuery 包装集后, 我们需要对其进行操作。本章首先讲解如何动态的创建元素, 接着学习如何管理 jQuery 包装集, 比如添加,删除,切片等。

## 二、前言

本系列的 2,3 篇上面列举了太多的 API。 不过这些基础还必须要讲, 基础要扎实。其实对于这些列表大家可以跳过, 等以后用到时再回头看或者查询官方的 API 说明。

本章内容很少, 主要讲解动态创建元素和操作 jQuery 包装集的各个函数。

## 三、动态创建元素

### 1\. 错误的编程方法

我们经常使用 javascript 动态的创建元素, 有很多程序员通过直接更改某一个容器的 HTML 内容。比如:

```js
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html >
    <head>
        <title>动态创建对象</title>
    </head>
    <body>
        <div id="testDiv">测试图层</div>
        <script type="text/javascript">
            document.getElementById("testDiv").innerHTML = "<div style=\"border:solid 1px #FF0000\">动态创建的 div</div>";
        </script>
    </body>
</html> 
```

上面的示例中我通过修改 testDiv 的内容,在页面上动态的添加了一个 div 元素。**但是请牢记,这是错误的做法! **

错误的原因:

(1) 在页面加载时改变了页面的结构。在 IE6 中如果网络变慢或者页面内容太大就会出现"终止操作"的错误。 也就是说"永远不要在页面加载时改变页面的 Dom 模型"。

(2) 使用修改 HTML 内容添加元素, 不符合 Dom 标准。 在实际工作中也碰到过使用这种方法修改内容后, 某些浏览器中并不能立刻显示添加的元素, 因为不同浏览器的显示引擎是不同的。 但是如果我们使用 Dom 的 CreateElement 创建对象, 在所有的浏览器中几乎都可以。 但是在 jQuery 中如果传入的而是一个完整的 HTML 字符串, 内部也是使用 innerHTML。 所以也不是完全否定 innerHTML 函数的使用。

所以从现在开始请摒弃这种旧知识, 使用下面介绍的正确方法编程。

### 2\. 创建新的元素

下面介绍两种正确的创建元素的方式.

#### （1）使用 HTML DOM 创建元素

*   什么是 DOM？ 通过 JavaScript，您可以重构整个 HTML 文档。您可以添加、移除、改变或重排页面上的项目。要改变页面的某个东西，JavaScript 就需要对 HTML 文档中所有元素进行访问的入口。这个入口，连同对 HTML 元素进行添加、移动、改变或移除的方法和属性，都是通过文档对象模型来获得的（DOM）。

关于使用 HTML DOM 创建元素本文不做详细介绍, 下面举一个简单的例子:

```js
//使用 Dom 标准创建元素
var select = document.createElement("select");
select.options[0] = new Option("加载项 1", "value1");
select.options[1] = new Option("加载项 2", "value2");
select.size = "2";
var object = testDiv.appendChild(select); 
```

通过使用 document.createElement 方法我们可以创建 Dom 元素, 然后通过 appendChild 方法为添加到指定对象上。

#### （2）使用 jQuery 函数创建元素

在 jQuery 中创建对象更加简单, 比如创建一个 Div 元素:

```js
$("<div style=\"border:solid 1px #FF0000\">动态创建的 div</div>") 
```

我们主要使用 jQuery 核心类库中的一个方法:

**jQuery( html, ownerDocument ) Returns:** jQuery

*根据 HTML 原始字符串动态创建 Dom 元素.*

其中 html 参数是一个 HTML 字符串, 在 jQuery1.3.2 中对此函数做了改进:

当 HTML 字符串是没有属性的元素是, 内部使用 document.createElement 创建元素, 比如:

```js
//jQuery 内部使用 document.createElement 创建元素:
$("<div/>").css("border","solid 1px #FF0000").html("动态创建的 div").appendTo(testDiv); 
```

否则使用 innerHTML 方法创建元素:

```js
//jQuery 内部使用 innerHTML 创建元素:
$("<div style=\"border:solid 1px #FF0000\">动态创建的 div</div>").appendTo(testDiv); 
```

### 3\. 将元素添加到对象上

我们可以使用上面两种方式创建一个元素, 但是上面已经提到一定不要在页面加载时就改变页面的 DOM 结构, 比如添加一个元素。 正确的做法是在页面加载完毕后添加或删除元素。

传统上, 使用 window.onload 完成上述目的:

```js
//DOM 加载完毕后添加元素
//传统方法
window.onload = function() { testDiv.innerHTML = "<div style=\"border:solid 1px #FF0000\">动态创建的 div</div>"; } 
```

虽然能够在 DOM 完整加载后, 在添加新的元素, 但是不幸的是浏览器执行 window.onload 函数不仅仅是在构建完 DOM 树之后, 也是在所有图像和其他外部资源完整的加载并且在浏览器窗口显示完毕之后。 所以如果某个图片或者其他资源加载很长时间, 访问者就会看到一个不完整的页面, 甚至在图片加载之前就执行了需要依赖动态添加的元素的脚本而导致脚本错误。

解决办法就是等 DOM 被解析后, 在图像和外部资源加载之前执行我们的函数。在 jQuery 中让这一实现变得可行:

```js
//jQuery 使用动态创建的$(document).ready(function)方法
$(document).ready(
        function() { testDiv.innerHTML = "<div style=\"border:solid 1px #FF0000\">使用动态创建的$(document).ready(function)方法</div>"; }
); 
```

或者使用简便语法:

```js
//jQuery 使用$(function)方法
$(function() { 
    testDiv.innerHTML += "<div style=\"border:solid 1px #FF0000\">使用$(function)方法</div>"; }); 
```

使用$()将我们的函数包装起来即可。而且可以在一个页面绑定多个函数, 如果使用传统的 window.onload 则只能调用一个函数。

所以请大家将修改 DOM 的函数使用此方法调用。 另外还要注意 document.createElement 和 innerHTML 的区别。 如果可以请尽量使用 document.createElement 和$("<div/>")的形式创建对象。

## 四、管理 jQuery 包装集元素

既然学会了动态创建元素, 接下来就会想要把这些元素放入我们的 jQuery 包装集中。

我们可以在 jQuery 包装集上调用下面这些函数, 用来改变我们的原始 jQuery 包装集, 并且大部分返回的都是过滤后的 jQuery 包装集。

jQuery 提供了一系列的函数用来管理包装集:

### 1\. 过滤 Filtering

![Alt text](img/Filtering.jpg)

### 2\. 查找 Finding

![Alt text](img/Finding.jpg)

### 3\. 串联 Chaining

![Alt text](img/Chaining.jpg)

## 五、总结

本篇文章内容较少, 主要讲解如何动态创建元素以及管理 jQuery 包装集。在下一章将会介绍如何使用 jQuery 操作元素的属性与样式。