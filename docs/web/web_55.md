# 第 20 节 工具提示

受到 Jason Frame 开发的 jQuery.tipsy 插件的启发，Bootstrap 才把这个工具提示插件做的更好，而且此插件不依赖图片，只是使用 CSS3 来实现动画效果，并使用 data 属性存储标题。

将鼠标悬停在按钮、文本框、链接等等一些基本控件上就可以看到提示了，先上一个效果图

![](img/146.jpg)

主要实现了按钮的上下左右的 ToolTip，然后是文本框和链接的 ToolTip。

```
<div class="container" style="margin-top:40px;">
    <div class="bs-example tooltip-demo">
      <div class="bs-example-tooltips">
        <button type="button" class="btn btn-default" data-toggle="tooltip" data-placement="left" title="Tooltip on left">左侧 Tooltip</button>
        <button type="button" class="btn btn-default" data-toggle="tooltip" data-placement="top" title="Tooltip on top">上方 Tooltip</button>
        <button type="button" class="btn btn-default" data-toggle="tooltip" data-placement="bottom" title="Tooltip on bottom">下方 Tooltip</button>
        <button type="button" class="btn btn-default" data-toggle="tooltip" data-placement="right" title="Tooltip on right">右侧 Tooltip</button>
      </div>
    </div>
    <input type="text" id="testt" rel="tooltip" title="123456" />
    <a href="#" data-toggle="tooltip" title="Default tooltip" >dsafsdfasdfasdfasd</a>
</div> 
```

在按钮上添加 data-toggle="tooltip"的属性来开启它的工具提示功能，然后指定 data-placement=”left“工具提示的位置，可以是上下左右（top、bottom、left、right）。

```
选择性加入的功能

出于性能方面的考虑，工具提示和弹框组件的 data 属性 api 是选择性加入的，也就是说你必须自己初始化他们。 
```

因此针对上述六个控件，我们需要初始化它们，我们是通过 jQuery 的初始化事件进行的

```
<script type="text/javascript">
$(function() {
 $('.tooltip-demo').tooltip({
  selector: "[data-toggle=tooltip]",
  container: "body"
})
$("#testt").tooltip({})
$('a').tooltip()
})
</script> 
```

按钮、文本框、链接三种不同的控件的实现初始化稍微有点不同。

```
工具提示与按钮组和输入框组联合使用时需要一些特殊设置

在.btn-group 或 .input-group 内的元素上使用工具提示时，你需要指定 container: 'body'选项以避免不需要的副作用（例如，当工具提示显示之后，与其合作的页面元素可能变得更宽或是去圆角）。

在禁止使用的页面元素上使用工具提示时需要额外增加一个元素将其包裹起来

为了给 disabled 或.disabled 元素添加工具提示，将需要增加工具提示的页面元素包裹在一个< div>中，然后对这个< div>元素应用工具提示。 
```

## 1\. 用法

通过 JavaScript 激活工具提示：上面也已经使用过了

```
$('#example').tooltip(options) 
```

## 2\. 选项

![](img/147.jpg)

```
对单个工具提示使用 data 属性

使用 data 属性可以为单个工具提示指定额外选项，如下所示。 
```

## 3\. 标记

```
<a href="#" data-toggle="tooltip" title="first tooltip">Hover over me</a> 
```

## 4\. 方法

$().tooltip(options)——为一组元素应用工具提示。 .tooltip('show')——展示工具提示。

```
$('#element').tooltip('show') 
```

.tooltip('hide')——隐藏工具提示。

```
$('#element').tooltip('hide') 
```

.tooltip('toggle')——展示或隐藏工具提示。

```
$('#element').tooltip('toggle') 
```

.tooltip('destroy')——隐藏并销毁工具提示。

```
$('#element').tooltip('destroy') 
```

## 5\. 事件

![](img/148.jpg)

```
$('.tooltip-demo').on('hidden.bs.tooltip', function () {
    alert(1);
}) 
```