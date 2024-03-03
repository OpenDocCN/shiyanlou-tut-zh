# 第 22 节 警示框

## 1\. 案例

通过这个插件可以为所有警告框增加关闭功能。

```js
<div id="alert1"  class="alert alert-warning fade in">
<button id='alert1' type="button" class="close" data-dismiss="alert" aria-hidden="true">&times;</button>
<strong>Holy guacamole!</strong> Best check yo self, you're not looking too good.
</div> 
```

![](img/154.jpg)

再来一个小例子

```js
<div class="alert alert-danger fade in">
<button type="button" class="close" data-dismiss="alert" aria-hidden="true">&times;</button>
<h4>Oh snap! You got an error!</h4>
<p>Change this and that and try again. Duis mollis, est non commodo luctus, nisi erat porttitor ligula, eget lacinia odio sem nec elit. Cras mattis consectetur purus sit amet fermentum.</p>
<p>
  <button type="button" class="btn btn-danger">Take this action</button>
  <button type="button" class="btn btn-success">Or do this</button>
</p>
</div> 
```

![](img/155.jpg)

通过 data-dismiss 属性即可开始关闭警告框的功能。无须任何的 JavaScript 的代码。只需为关闭按钮设置 data-dismiss="alert"即可自动为警告框赋予关闭功能。

## 2\. 用法

如果通过 JavaScript 启用警告框关闭功能：

我们来修改一下第一简单的小例子

```js
<div id="alert1"  class="alert alert-warning fade in">
<button id='alert1' type="button" class="close" onclick="Test()" aria-hidden="true">&times;</button>
<strong>Holy guacamole!</strong> Best check yo self, you're not looking too good.
</div> 
```

我们主要是去掉了关闭按钮的 data-dismiss 属性，然后添加了一个 onclick 的单击按钮事件，也就是关闭警示框的事件。

来看一下如何通过 JavaScript 来关闭警示框

```js
 <script type="text/javascript">
 function Test()
 {
     $("#alert1").alert('close');
 }
 </script> 
```

为所有警告框加入关闭功能。如果希望警告框被关闭时呈现动画效果，请确保为其添加了.fade 和 .in。

## 3\. 事件

Bootstrap 中的警告框暴露了一组事件，允许你进行监听。

![](img/156.jpg)