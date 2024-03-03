# 第 12 节 导航条

## 1\. 默认的导航条

导航条是在您的应用或网站中作为导航标头的响应式元组件。它们在移动设备上可以折叠（并且可开可关），且在可用的视口宽度增加时变为水平展开模式。

*   定制折叠模式与水平模式的阈值

根据你所放在导航条上的内容的长度，也许你需要调整导航条进入折叠模式和水平模式的阈值。通过改变@grid-float-breakpoint 变量的值或加入您自己的媒体查询 CSS 代码均可实现你的需求。

```
<nav class="navbar navbar-default" role="navigation">
  <div class="navbar-header">
      <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
          <span class="sr-only">Toggle navigation</span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">Brand</a>
  </div>
  <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
    <ul class="nav navbar-nav">
      <li class="active"><a href="#">Link</a></li>
      <li><a href="#">Link</a></li>
      <li class="dropdown">
        <a href="#" class="dropdown-toggle" data-toggle="dropdown">Dropdown <b class="caret"></b></a>
        <ul class="dropdown-menu">
          <li><a href="#">Action</a></li>
          <li><a href="#">Another action</a></li>
          <li><a href="#">Something else here</a></li>
          <li class="divider"></li>
          <li><a href="#">Separated link</a></li>
          <li class="divider"></li>
          <li><a href="#">One more separated link</a></li>
        </ul>
      </li>
    </ul>
    <form class="navbar-form navbar-left" role="search">
      <div class="form-group">
        <input type="text" class="form-control" placeholder="Search">
      </div>
      <button type="submit" class="btn btn-default">Submit</button>
    </form>
    <ul class="nav navbar-nav navbar-right">
      <li><a href="#">Link</a></li>
      <li class="dropdown">
        <a href="#" class="dropdown-toggle" data-toggle="dropdown">Dropdown <b class="caret"></b></a>
        <ul class="dropdown-menu">
          <li><a href="#">Action</a></li>
          <li><a href="#">Another action</a></li>
          <li><a href="#">Something else here</a></li>
          <li class="divider"></li>
          <li><a href="#">Separated link</a></li>
        </ul>
      </li>
    </ul>
  </div>
</nav> 
```

这段代码有点长，先上点效果吧。

![](img/93.jpg)

接下来慢慢的放缩浏览器，也就是让浏览器的宽度小一些。

![](img/94.jpg)

首先最外面的容器是 nav 标签，并添加 nav-bar 样式类，表示这里面属于导航条。

然后在浏览器放缩到一定程度的时候，可以看到的 Header。

```
<div class="navbar-header">
  <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
      <span class="sr-only">Toggle navigation</span>
      <span class="icon-bar"></span>
      <span class="icon-bar"></span>
      <span class="icon-bar"></span>
  </button>
  <a class="navbar-brand" href="#">Brand</a>
</div> 
```

![](img/95.jpg)

按钮标签里嵌套了三个 span 的 icon。然后给与 navbar-toggle 样式类和属性 collapse(收起)，点击的时候目标为 data-target。

再接下来就比较简单，嵌套了下拉菜单、form 表单、再是下拉菜单。

```
增强导航条的可访问性

要增强可访问性，一定要给每个导航条加上 role="navigation"。 
```

## 2\. 表单

将表单放置于.navbar-form 之内可以呈现很好的垂直对齐，并在较窄的 viewport 中呈现折叠状态。使用对齐选项可以确定其在导航条上出现的位置。

通过使用.navbar-form 和 .form-inline 共享了很多代码。

```
<nav class="navbar navbar-default" role="navigation">
<form class="navbar-form navbar-left" role="search">
  <div class="form-group">
    <input type="text" class="form-control" placeholder="Search">
  </div>
  <button type="submit" class="btn btn-default">Submit</button>
</form>
</nav> 
```

![](img/96.jpg)

```
为输入框添加 label 标签

如果你没有为输入框添加 label 标签，屏幕阅读器将会遇到问题。对于导航条内的表单，可以通过.sr-only class 隐藏 label 标签。 
```

## 3\. 按钮

对于不包含在< form>中的< button>元素，加上.navbar-btn 这个 class 后可以让它在导航条里垂直居中。

```
<button type="button" class="btn btn-default navbar-btn">Sign in</button> 
```

![](img/97.jpg)

## 4\. 文本

把文本包裹在.navbar-text 中时，为了有正确的行距和颜色，通常使用< p>标签。

```
<p class="navbar-text">Signed in as Mark Otto</p> 
```

## 5\. 非导航的链接

或许你希望在标准的导航组件之外添加标准链接，那么，使用.navbar-link class 可以让链接有正确的默认颜色和反色。

```
<p class="navbar-text navbar-right">Signed in as <a href="#" class="navbar-link">Mark Otto</a></p> 
```

## 6\. 组件对齐

用.navbar-left 或者.navbar-right 工具类给导航链接、表单、按钮或文本对齐。两种类都用到在特定方向的 CSS 浮动样式。例如，要对齐导航链接，就要把它们放在个分开的、应用了工具类的< ul>里。

这些 class 是.pull-left 和.pull-right 的 mixin 版本，但是他们被限定在了媒体查询中，这样可以更容易的在各种尺寸的屏幕上处理导航条组件。

## 7\. 固定在顶部

添加.navbar-fixed-top 可以让导航条固定在顶部。

```
<nav class="navbar navbar-default navbar-fixed-top" role="navigation">
<form class="navbar-form navbar-left" role="search">
  <div class="form-group">
    <input type="text" class="form-control" placeholder="Search">
  </div>
  <button type="submit" class="btn btn-default">Submit</button>
</form>
<button type="button" class="btn btn-default navbar-btn">Sign in</button>
</nav> 
```

```
需要为 body 标签设置内补（padding）

这个固定的导航条会遮住页面上的其它内容，除非你给<body>的上方设置了 padding。用你自己的值，或用下面给出的代码都可以。提示：导航条的默认高度是 50px。

body { padding-top: 70px; }

一定要放在 Bootstrap CSS 的核心文件之后。 
```

## 8\. 固定在底部

用.navbar-fixed-bottom 代替。

```
 <nav class="navbar navbar-default navbar-fixed-bottom" role="navigation">
    <form class="navbar-form navbar-left" role="search">
      <div class="form-group">
        <input type="text" class="form-control" placeholder="Search">
      </div>
      <button type="submit" class="btn btn-default">Submit</button>
    </form>
    <button type="button" class="btn btn-default navbar-btn">Sign in</button>
 </nav> 
```

```
需要为 body 标签设置内部（padding）

这个固定的导航条会遮住页面上的其它内容，除非你给<body>底部设置了 padding。用你自己的值，或用下面给出的代码都可以。提示：导航条的默认高度是 50px。

body { padding-bottom: 70px; }

一定要在加载 Bootstrap CSS 的核心后使用它。 
```

## 9\. 静止在顶部

通过添加.navbar-static-top 即可创建一个与页面的导航条。它会随着页面向下滚动而消失。和.navbar-fixed-*类不同的是，你不用给 body 添加 padding。

```
 <nav class="navbar navbar-default navbar-static-top" role="navigation">
    <form class="navbar-form navbar-left" role="search">
      <div class="form-group">
        <input type="text" class="form-control" placeholder="Search">
      </div>
      <button type="submit" class="btn btn-default">Submit</button>
    </form>
    <button type="button" class="btn btn-default navbar-btn">Sign in</button>
 </nav> 
```

## 10\. 反色的导航条

通过添加.navbar-inverse 类可以改变导航条的外观。

```
<nav class="navbar navbar-default navbar-inverse" role="navigation">
<form class="navbar-form navbar-left" role="search">
  <div class="form-group">
    <input type="text" class="form-control" placeholder="Search">
  </div>
  <button type="submit" class="btn btn-default">Submit</button>
</form>
<button type="button" class="btn btn-default navbar-btn">Sign in</button>
</nav> 
```