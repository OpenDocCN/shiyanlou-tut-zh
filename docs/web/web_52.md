# 第 17 节 下拉菜单

通过此插件可以为几乎所有东西添加下拉菜单，包括导航条、标签页、胶囊式按钮，本文主要来介绍下导航条里面的下拉菜单吧，其实道理都是一样的。

## 1\. 用于导航条

先来看一下之前做过的一个简单的导航条

导航条分为四个部分。第一部分导航头，第二部分导航列，第三部分 form 查询表单，第四部分导航列。

```
<nav class="navbar navbar-default" role="navigation">
  <div class="navbar-header">
    <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#myCollapse">
      <span class="sr-only">Toggle navigation</span>
      <span class="icon-bar"></span>
      <span class="icon-bar"></span>
      <span class="icon-bar"></span>
    </button>
    <a class="navbar-brand" href="#">Brand</a>
  </div>

  <div class="collapse navbar-collapse" id="myCollapse">
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

![](img/137.jpg)

## 2\. 用法一--通过 data 属性

通过向链接或按钮添加 data-toggle="dropdown"可以打开或关闭下拉菜单。

```
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
```

即上面的那个小例子就是如此。

## 3\. 用法二--通过 JavaScript

通过 JavaScript 打开或关闭下拉菜单：

![](img/138.jpg)

通过将 data-toggle 属性值设置为空，或者直接删除 data-toggle 属性，那么再次点击就不会出现下来菜单了，接下来我们通过给这个 a 标签添加一个 onclick 事件。

```
<a href="#" class="dropdown-toggle" id="myDropDown" data-toggle="" onClick="Test()">Dropdown <b class="caret"></b></a> 
```

并添加相应的 JavaScript 的函数代码

```
<script type="text/javascript">
    function Test()
    {
        $('#myDropDown').dropdown();
    }
</script> 
```

## 4\. 事件

![](img/139.jpg)

可以绑定事件，然后在元素进行相应处理的时候会自动绑定到元素，并进行执行相关的代码。

```
$('#myDropdown').on('show.bs.dropdown', function () {
  // 在显示的时候做一些处理代码
}) 
```