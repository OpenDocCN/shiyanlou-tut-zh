# 第 10 节 输入框组

## 1\. 基本案例

通过在基于文本的输入框前面，后面或是两边加上文字或按钮，可以扩展对表单的控制。用带有.input-group-addon 的.input-group，可以给.form-control 前面或后面追加元素。

```js
跨浏览器兼容性

这里请避免使用 <select> 元素，因为 WebKit 浏览器不能完全支持它的样式。

不要和.form-group 混用

不要直接将.input-group 和.form-group 混合使用，因为.input-group 是一个独立的组件。 
```

```js
<div class="input-group">
  <span class="input-group-addon">@</span>
  <input type="text" class="form-control" placeholder="Username">
</div>

<div class="input-group">
  <input type="text" class="form-control">
  <span class="input-group-addon">.00</span>
</div>

<div class="input-group">
  <span class="input-group-addon">$</span>
  <input type="text" class="form-control">
  <span class="input-group-addon">.00</span>
</div> 
```

![](img/77.jpg)

通过效果就可以看到，可在前面加，可以在后面加，也可以在前后同时加，使用起来很方便。

## 2\. 尺寸

给.input-group 添加标明尺寸的 class，它自己和其中的内容都会自动调整尺寸。没必要给每个元素都重复添加尺寸 class。

```js
<div class="input-group input-group-lg">
  <span class="input-group-addon">@</span>
  <input type="text" class="form-control" placeholder="Username">
</div>

<div class="input-group">
  <span class="input-group-addon">@</span>
  <input type="text" class="form-control" placeholder="Username">
</div>

<div class="input-group input-group-sm">
  <span class="input-group-addon">@</span>
  <input type="text" class="form-control" placeholder="Username">
</div> 
```

![](img/78.jpg)

## 3\. 复选框和单选框

可以把复选框或单选框放在输入组里而不是文本前。

```js
<div class="row">
  <div class="col-lg-6">
    <div class="input-group">
      <span class="input-group-addon">
        <input type="checkbox">
      </span>
      <input type="text" class="form-control">
    </div>
  </div>
  <div class="col-lg-6">
    <div class="input-group">
      <span class="input-group-addon">
        <input type="radio">
      </span>
      <input type="text" class="form-control">
    </div>
  </div>
</div> 
```

这个简单的案例，首先使用了栅格系统，通过.row 样式类和.col-lg-*样式类的组合。

![](img/79.jpg) 首先看到的这个是在屏幕宽度足够大的情况下，两个组合控件在同一行进行排列。接下来我们通过收缩浏览器的宽度。可以看到如下效果

![](img/80.jpg)

## 4\. 附加按钮

输入组里的有点不同，它需要多加一层元素。 你要用 .input-group-btn 包住按钮而不是 .input-group-addon。这是因为默认的浏览器样式不能被覆盖。

```js
<div class="row">
  <div class="col-lg-6">
    <div class="input-group">
      <span class="input-group-btn">
        <button class="btn btn-default" type="button">Go!</button>
      </span>
      <input type="text" class="form-control">
    </div><!-- /input-group -->
  </div><!-- /.col-lg-6 -->
  <div class="col-lg-6">
    <div class="input-group">
      <input type="text" class="form-control">
      <span class="input-group-btn">
        <button class="btn btn-default" type="button">Go!</button>
      </span>
    </div><!-- /input-group -->
  </div><!-- /.col-lg-6 -->
</div> 
```

![](img/81.jpg)

## 5\. 带下拉菜单的按钮

```js
<div class="row">
  <div class="col-lg-6">
    <div class="input-group">
      <div class="input-group-btn">
        <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown">Action <span class="caret"></span></button>
        <ul class="dropdown-menu">
          <li><a href="#">Action</a></li>
          <li><a href="#">Another action</a></li>
          <li><a href="#">Something else here</a></li>
          <li class="divider"></li>
          <li><a href="#">Separated link</a></li>
        </ul>
      </div><!-- /btn-group -->
      <input type="text" class="form-control">
    </div><!-- /input-group -->
  </div><!-- /.col-lg-6 -->
  <div class="col-lg-6">
    <div class="input-group">
      <input type="text" class="form-control">
      <div class="input-group-btn">
        <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown">Action <span class="caret"></span></button>
        <ul class="dropdown-menu pull-right">
          <li><a href="#">Action</a></li>
          <li><a href="#">Another action</a></li>
          <li><a href="#">Something else here</a></li>
          <li class="divider"></li>
          <li><a href="#">Separated link</a></li>
        </ul>
      </div><!-- /btn-group -->
    </div><!-- /input-group -->
  </div><!-- /.col-lg-6 -->
</div><!-- /.row --> 
```

![](img/82.jpg)

## 6\. 分段按钮

```js
<div class="row">
  <div class="col-lg-6">
    <div class="input-group">
      <div class="input-group-btn">
        <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown">Action</button>
        <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown"><span class="caret"></span></button>
        <ul class="dropdown-menu">
          <li><a href="#">Action</a></li>
          <li><a href="#">Another action</a></li>
          <li><a href="#">Something else here</a></li>
          <li class="divider"></li>
          <li><a href="#">Separated link</a></li>
        </ul>
      </div><!-- /btn-group -->
      <input type="text" class="form-control">
    </div><!-- /input-group -->
  </div><!-- /.col-lg-6 -->
</div> 
```

![](img/83.jpg)