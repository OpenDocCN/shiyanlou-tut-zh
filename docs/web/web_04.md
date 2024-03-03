# 用 CSS 和 jQuery 打造一个简单的图片编辑器

利用 CSS 的 filter 属性和 jQuery 实现简单的图片编辑，调透明度，灰度，亮度等

## 实验简介

本文主要是利用 CSS 的`filter`和简单的 Jquery 代码来实现一个简单的图片编辑器，包括对图片的透明度，黑白，图片亮度等调节。

## 实验准备

打开命令行终端，来到桌面：使用`wget`获取小游戏所需要的图片：

```js
cd Desktop

wget http://labfile.oss.aliyuncs.com/courses/376/editor.zip 
```

解压，在命令行执行：

```js
unzip editor.zip 
```

得到`editor/`文件夹之后，直接拖到 Brackets 编辑器进行开发就可以。我们后续的`js`代码都是写在`js/main.js`中，还有的 HTMl 部分就直接写在`index.html`中了。

## CSS filter

我们首先来探讨一下 filter。

首先来说明一下`filter`，在 CSS 里面要实现`filter`，其实很简单，使用类似下面的声明方式：

```js
.example {
  filter: <filter-function> [<filter-function>];
} 
```

比如说，我们给图片添加一点`灰度(grayscale)`特效，就可以这样：

```js
.example {
  filter: grayscale(90%);
} 
```

当然，为了浏览器兼容，我们最好这样写：

```js
.example {
  -webkit-filter: grayscale(90%);
  filter: grayscale(90%);
} 
```

需要注意的是：`filter`的属性值的单位通常可能是从`0`到`1`之间，但是有些不是这样的，比如`blur`是使用像素`'px'`来作为单位的，而`hue-rotate`则使用角度`deg`来作为基本单位；

```js
.example {
  filter: blur(10px);
}

.example-2 {
  filter: hue-rotate(90deg);
} 
```

但是如果每次只能使用一个`filter`就比较麻烦了，所以 CSS 提供了更加方便的书写形式，直接并排着写：

```js
.example {
  filter: grayscale(0.5) blur(10px);
} 
```

这样就可以实现对一个元素添加多个`filter`属性。

简单地说完`filter`之后，我们来动手创建一个简单的图片编辑器。

## 创建基本的 HTML 文件

在这里我们创建一个`index.html`，代码也比较简单：

```js
<!DOCTYPE HTML>
<html>
    <head>
        <meta charset="utf-8">
        <title>Image Editor</title>

        <link href = "css/main.css" type="text/css" rel="stylesheet">
    </head>

    <body>
        <div class="wrapper">
            <h1>Image Editor</h1>

            <!--Form for collecting image URL -->
            <form id="urlBox" class = "center">
                <input class="url-box" type="url" id="imgUrl" placeholder="Paste any image link and hit enter to start playing.">
            </form>

            <!--Controls for CSS filters via range input-->
            <div class="sliders">
                <form id="imageEditor">
                    <p>
                        <label for="gs">Grayscale</label>
                        <input id="gs" name="gs" type="range" min=0 max=100 value=0>
                    </p>

                    <p>
                        <label for="blur">Blur</label>
                        <input id="blur" name="blur" type="range" min=0 max=10 value=0>
                    </p>

                    <p>
                        <label for="br">Brightness</label>
                        <input id="br" name="br" type="range" min=0 max=200 value=100>
                    </p>

                    <p>
                        <label for="ct">Contrast</label>
                        <input id="ct" name="ct" type="range" min=0 max=200 value=100>
                    </p>

                    <p>
                        <label for="huer">Hue Rotate</label>
                        <input id="huer" name="huer" type="range" min=0 max=360 value=0>
                    </p>

                    <p>
                        <label for="opacity">Opacity</label>
                        <input id="opacity" name="opacity" type="range" min=0 max=100 value=100>
                    </p>

                    <p>
                        <label for="invert">Invert</label>
                        <input id="invert" name="invert" type="range" min=0 max=100 value=0>
                    </p>

                    <p>
                        <label for="saturate">Saturate</label>
                        <input id="saturate" name="saturate" type="range" min=0 max=500 value=100>
                    </p>

                    <p>
                        <label for="sepia">Sepia</label>
                        <input id="sepia" name="sepia" type="range" min=0 max=100 value=0>
                    </p>

                    <input type="reset" form="imageEditor" id="reset" value="Reset" />

                </form>
            </div>  

            <!--container where image will be loaded-->
            <div id="imageContainer" class="center">
                <img src="images/aviary_heibai.jpg"/>
            </div>
        </div>

        <script type="text/javascript" src="js/jquery-2.1.0.js"></script>
        <script type="text/javascript" src="js/main.js"></script>

    </body>
</html> 
```

这个文件里，我们引入了`main.css`和`main.js`，`main.css`其实是对编辑器的一些排版起的作用，并没有对图片的`filter`效果做出实际的影响，我们做的是编辑器，所以在用户改变某个`filter`的值的时候，我们可以实时让用户看到效果，于是这些实现`filter`的代码应该就放在`main.js`里面。

上面的每一个 `<form id="imageEditor">`下面的`<p>`元素下面的`input`都是`filter`的一个属性设置，因为我们可以同时用多个`filter`来对图片产生特效，所以我每个`filter`的属性值都设置为可以调节的状态。

上面的`index.html`还要说明的是，在最上面我们提供一个输入框，用于给用户输入图片的 URL，当用户点击回车的时候，我们就将这张图片显示到编辑区域。使用的是下面的简单`js`代码：

```js
function addImage(e) {
    var imgUrl = $("#imgUrl").val();
    if (imgUrl.length) {
        $("#imageContainer img").attr("src", imgUrl);
    }
    e.preventDefault();
}

//on pressing return, addImage() will be called
$("#urlBox").submit(addImage); 
```

上面的 js 代码也是写到`main.js`当中。有了可以用户自己添加图片之后，我们就可以实现对图片的编辑了：

每次用户在滑动进度条的时候，我们就可以将效果展示给用户看，于是我们来监听用户的`mousemove`事件（请注意看下面）：

```js
$("input[type=range]").mousemove(editImage); 
```

也就是说，每次用户在移动控制条的时候，我们都执行`editImage`函数。 但是这样的体验可能还不是最好，因为在最后用户的鼠标离开控制条的时候，我们还可以监听`change`事件，把这一刻的变化也交给`editImage`函数处理，所以可以将上面的代码写成这样：

```js
$("input[type=range]").mousemove(editImage).change(editImage); 
```

## 编写 editImage 函数

上面我们将`input[type=range]`的`mousemove`和`change`事件交给了`editImage`函数处理，所以，我们来编写一下`editImage`的函数代码：

```js
function editImage() {
    var gs = $("#gs").val(); // grayscale
    var blur = $("#blur").val(); // blur
    var br = $("#br").val(); // brightness
    var ct = $("#ct").val(); // contrast
    var huer = $("#huer").val(); //hue-rotate
    var opacity = $("#opacity").val(); //opacity
    var invert = $("#invert").val(); //invert
    var saturate = $("#saturate").val(); //saturate
    var sepia = $("#sepia").val(); //sepia

    $("#imageContainer img").css("filter", 'grayscale(' + gs+
                                                     '%) blur(' + blur +
                                                     'px) brightness(' + br +
                                                     '%) contrast(' + ct +
                                                     '%) hue-rotate(' + huer +
                                                     'deg) opacity(' + opacity +
                                                     '%) invert(' + invert +
                                                     '%) saturate(' + saturate +
                                                     '%) sepia(' + sepia + '%)');

    $("#imageContainer img").css("-webkit-filter", 'grayscale(' + gs+
                                                     '%) blur(' + blur +
                                                     'px) brightness(' + br +
                                                     '%) contrast(' + ct +
                                                     '%) hue-rotate(' + huer +
                                                     'deg) opacity(' + opacity +
                                                     '%) invert(' + invert +
                                                     '%) saturate(' + saturate +
                                                     '%) sepia(' + sepia + '%)'); 

} 
```

其实很简单，我们在每次用户滑动控制条的时候，我们就通过类似`var gs = $("#gs").val();`的语句取得相对应地值，然后通过 Jquery 的`css()`方法直接为图片加上`filter`效果，而且相信你也看得出来，这个函数的后半段就是实现浏览器兼容的

```js
$("#imageContainer img").css("-webkit-filter",...) 
```

这段代码其实就是在`img`元素实现了类似下面的效果；

```js
<img src="images/aviary_heibai.jpg" style="-webkit-filter: grayscale(0%) blur(0px) brightness(100%) contrast(100%) hue-rotate(0deg) opacity(100%) invert(0%) saturate(100%) sepia(0%);"> 
```

最后，如果你不想将某些特效加到图片上面去，你可以点`reset`然后将图片重置到原始状态：

```js
$('#imageEditor').on('reset', function () {
    setTimeout(function() {
        editImage();
    },0);
}); 
```

这里需要说明一下的是，这里的`setTimeout`函数就是为了将`reset`的效果最快地展现出来，如果写成下面的形式：

```js
$('#imageEditor').on('reset', function () {

        editImage();
    }); 
```

这个时候，`reset`效果执行起来其实是有一点延迟的，你明显可以看到等待的时候，它并不是很快。

浏览器打开`index.html`，就可以看到相应的调节效果了。你可以拖动一些设置项的控制条来查看效果。