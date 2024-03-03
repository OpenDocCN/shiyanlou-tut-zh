# 第 21 节 弹出框

## 1\. 案例

为页面内容添加一个小的覆盖层，就像 iPad 上的效果一样，为页面元素增加额外的信息。

先来看几个简单的静态案例效果图

![](img/149.jpg)

效果比较简单主要就是静态的弹出的小窗体，分为窗体标题和窗体内容。

```
<div class="bs-example bs-example-popover">
  <div class="popover top">
    <div class="arrow"></div>
    <h3 class="popover-title">Popover top</h3>
    <div class="popover-content">
      <p>Sed posuere consectetur est at lobortis. Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.</p>
    </div>
  </div>

  <div class="popover right">
    <div class="arrow"></div>
    <h3 class="popover-title">Popover right</h3>
    <div class="popover-content">
      <p>Sed posuere consectetur est at lobortis. Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.</p>
    </div>
  </div>

  <div class="popover bottom">
    <div class="arrow"></div>
    <h3 class="popover-title">Popover bottom</h3>

    <div class="popover-content">
      <p>Sed posuere consectetur est at lobortis. Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.</p>
    </div>
  </div>

  <div class="popover left">
    <div class="arrow"></div>
    <h3 class="popover-title">Popover left</h3>
    <div class="popover-content">
      <p>Sed posuere consectetur est at lobortis. Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.</p>
    </div>
  </div>

  <div class="clearfix"></div>
</div> 
```

但是我们还是需要给元素设置简单的基本布局

```
<style type="text/css">
 .bs-example-popover .popover {
      position: relative;
      display: block;
      float: left;
      width: 240px;
      margin: 20px;
}
</style> 
```

## 2\. 动态演示

先来看效果图

![](img/150.jpg)

一个按钮，点击按钮的时候就会弹出右侧的小窗体。

看代码，其实也很简单。

```
<a id="a2" class="btn btn-lg btn-danger" data-placement="right" data-content="即对拥有矮、胖、穷、丑、矬、呆、撸等属性特征的各种雷人行径及想法表示轻蔑。屌丝（或写作“吊丝”）可以说是由骂人话“屌死”、“吊死”、“叼死”演变而来。“屌丝男”主要是指大多出身清贫之家，如乡村或许多城市底层小市民家庭，没有更多的背景，许多初中即停学，进城务工，或成了餐厅服务员，或成了网吧网管，在城市的富贵之中分得一杯苦羹；或是宅男、无业游民，可是通常又不肯承认，个人一般自称为自由职业者。" title=""  href="#" data-original-title="屌丝本义">Please Click to toggle popover
</a> 
```

就一个 a 标签，但是赋予了按钮的样式类，然后给与几个属性，主要用于展示弹出框的：

第一个：data-original-title ——标题

第二个：data-content——内容

第三个：data-placement——位置（上下左右 top、bottom、left、right）

不过现在如果你来运行，按钮是有了，你点击按钮弹出框被不会出现，原来很简单，就是我们还没有给它初始化，就像上一节中的 tooltip 一样的。

只需要添加简单的 JavaScript 代码就可以了。

```
 <script type="text/javascript">
     $("#a1").popover();
 </script> 
```

## 3\. 四个方向

![](img/151.jpg)

```
<div  style="margin-left:200px;margin-top:100px;margin-bottom:200px;" class="bs-example tooltip-demo">
  <div  class="bs-example-tooltips">
    <button type="button" class="btn btn-default" data-container="body" data-toggle="popover" data-placement="left" data-content="Vivamus sagittis lacus vel augue laoreet rutrum faucibus.">
      左侧弹框
    </button>
    <button type="button" class="btn btn-default" data-container="body" data-toggle="popover" data-placement="top" data-content="Vivamus sagittis lacus vel augue laoreet rutrum faucibus.">
      上方弹框
    </button>
    <button type="button" class="btn btn-default" data-container="body" data-toggle="popover" data-placement="bottom" data-content="Vivamus sagittis lacus vel augue laoreet rutrum faucibus.">
      下方弹框
    </button>
    <button type="button" class="btn btn-default" data-container="body" data-toggle="popover" data-placement="right" data-content="Vivamus sagittis lacus vel augue laoreet rutrum faucibus.">
      右侧弹框
    </button>
  </div>
</div> 
```

然后用 JavaScript 来激活

```
<script type="text/javascript">
    $("#a1").popover();
    $("[data-toggle=popover]").popover();
</script> 
```

```
选择性加入的功能

出于性能方面的考虑，工具提示和弹框组件的 data 属性 api 是选择性加入的，也就是说你必须自己初始化他们。

弹出框在按钮组和输入框组中使用时，需要额外的设置

当提示框与.btn-group 或 .input-group 联合使用时，你需要指定 container: 'body'选项（见下面的文档）以避免不需要的副作用（例如，当弹出框显示之后，与其合作的页面元素可能变得更宽或是去圆角）。

在禁止使用的页面元素上使用弹出框时需要额外增加一个元素将其包裹起来

为了给 disabled 或.disabled 元素添加弹出框时，将需要增加弹出框的页面元素包裹在一个<div>中，然后对这个<div>元素应用弹出框。 
```

## 4\. 用法

通过 JavaScript 启用弹出框：

```
$('#example').popover(options) 
```

## 5\. 选项

可以将选项通过 data 属性或 JavaScript 传递。对于 data 属性，需要将选项名称放到 data-之后，例如 data-animation=""。

![](img/152.jpg)

## 6\. 方法

```
$().popover(options) 
```

为一组元素初始化弹出框。

```
$('#element').popover('show') 
```

显示弹出框。

```
$('#element').popover('hide') 
```

隐藏弹出框。

```
$('#element').popover('toggle') 
```

展示或隐藏弹出框。

```
$('#element').popover('destroy') 
```

隐藏并销毁弹出框。

## 7\. 事件

![](img/153.jpg)