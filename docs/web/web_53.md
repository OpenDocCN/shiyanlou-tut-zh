# 第 18 节 标签页

之前通过组件只是简单的学习过

```
<ul class="nav nav-tabs">
  <li class="active"><a href="#">Home</a></li>
  <li><a href="#">Profile</a></li>
  <li><a href="#">Messages</a></li>
</ul> 
```

当然效果就是这样，只是默认的激活了第一个标签 Home，然后不能点击。

![](img/140.jpg)

现在我们来优化一下。

我们给上面的先预定义一些 href 的标签 ID

```
<ul class="nav nav-tabs">
  <li><a href="#home" >Home</a></li>
  <li><a href="#profile" >Profile</a></li>
  <li><a href="#messages" >Messages</a></li>
  <li><a href="#settings" >Settings</a></li>
  <li class="dropdown">
      <a href="#" data-toggle="dropdown" class="dropdown-toggle">Test<b class="caret"></b></a>
      <ul class="dropdown-menu">
        <li ><a href="#AAA">@tag</a></li>
        <li ><a href="#BBB">@mag</a></li>
    </ul>
  </li>  
</ul> 
```

并且添加了一个下拉菜单。

![](img/141.jpg)

然后现在我们继续的修正代码

```
<ul class="nav nav-tabs">
  <li><a href="#home" data-toggle="tab">Home</a></li>
  <li><a href="#profile" data-toggle="tab">Profile</a></li>
  <li><a href="#messages" data-toggle="tab">Messages</a></li>
  <li><a href="#settings" data-toggle="tab">Settings</a></li>
  <li class="dropdown">
      <a href="#" data-toggle="dropdown" class="dropdown-toggle">Test<b class="caret"></b></a>
      <ul class="dropdown-menu">
        <li ><a data-toggle="tab" href="#AAA">@tag</a></li>
        <li ><a data-toggle="tab" href="#BBB">@mag</a></li>
    </ul>
  </li>  
</ul>

<div class="tab-content">
  <div class="tab-pane active" id="home">1..Raw denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.</div>
  <div class="tab-pane" id="profile">2..Raw denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.</div>
  <div class="tab-pane" id="messages">3..Raw denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.</div>
  <div class="tab-pane" id="settings">4..Raw denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.</div>
<div class="tab-pane" id="AAA">A..Raw denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.</div>
<div class="tab-pane" id="BBB">B..Raw denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.</div>
</div>
</div> 
```

将标签页中的 a 标签都添加了一个属性 data-toggle="tab"

然后在下面添加一个 div 的容器，并给与 tab-content 的样式类。

容器里面定义 div，然后在 div 上添加 id 属性，和上面的 href 的标签 ID 对应，并添加 tab-pane 的样式类，其中一个如下，当然这个里面还添加了一个 active 的样式类，目的就是默认激活

```
<div class="tab-pane active" id="home"> 
```

![](img/142.jpg)

最终现在每个标签页都可以进行点击，并且下拉菜单的菜单想也是可以点击，对应着我们为 tab-content 中定义的标签内容页。

可以看出上面的操作我们都是通过 data 属性就可以实现标签切换和内容展示的。

**下面我们就通过 JavaScript 来进行实现**

## 1\. 用法

通过 JavaScript 启动可切换标签页（每个标签页单独被激活）：

```
$('#myTab a').click(function (e) {
  e.preventDefault()
  $(this).tab('show')
}) 
```

将所有代码贴上

```
<!DOCTYPE html>
<html>
<head>
<title>Bootstrap</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<!-- Bootstrap -->
<link href="css/bootstrap.min.css" rel="stylesheet" media="screen">
<!--[if lt IE 9]>
<script src="http://labfile.oss.aliyuncs.com/html5shiv/3.7.0/html5shiv.js"></script>
<script src="http://labfile.oss.aliyuncs.com/respond.js/1.3.0/respond.min.js"></script>
<![endif]-->
</head>
<body>
<div class="container">
<ul class="nav nav-tabs" id="myTab">
  <li><a href="#home" >Home</a></li>
  <li><a href="#profile" >Profile</a></li>
  <li><a href="#messages" >Messages</a></li>
  <li><a href="#settings" >Settings</a></li>
  <li class="dropdown">
      <a href="#" data-toggle="dropdown" class="dropdown-toggle">Test<b class="caret"></b></a>
      <ul class="dropdown-menu">
        <li ><a href="#AAA">@tag</a></li>
        <li ><a href="#BBB">@mag</a></li>
    </ul>
  </li>  
</ul>

<div class="tab-content">
  <div class="tab-pane active" id="home">1..Raw denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.</div>
  <div class="tab-pane" id="profile">2..Rawwilliamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui. denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.</div>
  <div class="tab-pane" id="messages">3..Raw denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcherwilliamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui. voluptate nisi qui.</div>
  <div class="tab-pane" id="settings">4..Raw denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu bawilliamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.nh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.</div>
<div class="tab-pane" id="AAA">A..Raw denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irwilliamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.ure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.</div>
<div class="tab-pane" id="BBB">B..Raw denim you probably haven't heard of them jean shorts Austin. Nesciunt tofu stumptown aliqua, retro synth master cleanse. Mustache cliche tempor, williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irwilliamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.williamsburg carles vegan helvetica. Reprehenderit butcher retro keffiyeh dreamcatcher synth. Cosby sweater eu banh mi, qui irure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.ure terry richardson ex squid. Aliquip placeat salvia cillum iphone. Seitan aliquip quis cardigan american apparel, butcher voluptate nisi qui.</div>
</div>
</div>
<script src="js/jquery-2.0.3.min.js"></script>
<script src="js/bootstrap.min.js"></script>
<script type="text/javascript">    
 $('#myTab a').click(function (e) {
  e.preventDefault()
  $(this).tab('show')
})
</script>
</body>
</html> 
```

就是将前面的代码 a 标签中的 data-toggle 属性去掉，这样应该就找不到下面的 tab 内容了，所以内容无法进行切换。

不过我们可以通过上面的 JavaScript 进行点击切换实现。

可以有以下几种方式单独激活标签页：

```
$('#myTab a[href="#profile"]').tab('show') 
$('#myTab a:first').tab('show') 
$('#myTab a:last').tab('show')
$('#myTab li:eq(2) a').tab('show') 
```

只需要添加相应的事件进行调用就可以了。

只需为页面元素简单的添加 data-toggle="tab" 或 data-toggle="pill"就可以无需写任何 JavaScript 代码也能激活标签页或胶囊式导航。为 ul 添加.nav 和.nav-tabs classe 即可为其赋予 Bootstrap 标签页样式；而添加 nav 和 nav-pills class 可以为其赋予胶囊标签页。

可以通过 jQuery 来执行首次的加载

```
<script>
  $(function () {
    $('#myTab a:first').tab('show')
  })
</script> 
```

## 2\. 事件

![](img/143.jpg)

```
$('a[data-toggle="tab"]').on('shown.bs.tab', function (e) {
  e.target //通过此参数可以获得激活的 tab 信息 
  e.relatedTarget // 激活之前的那一个 tab 的信息
}) 
```