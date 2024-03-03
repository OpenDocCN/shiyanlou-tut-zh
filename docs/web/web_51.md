# 第 16 节 JavaScript 插件概览

插件可以单个引入（使用 Bootstrap 提供的单个*.js 文件），或一次性全部引入（使用 bootstrap.js 或压缩版的 bootstrap.min.js）。

```js
不要将两份文件全部引入

bootstrap.js 和 bootstrap.min.js 同样是包含了所有插件。区别是：一个没有压缩，一个进行了压缩。

插件之间的依赖

某些插件和 CSS 组件依赖于其它插件。如果你是单个引入每个插件的，请确保在文档中检查插件之间的依赖关系。注意，所有插件都依赖 jQuery（也就是说，jQuery 必须在所有插件之前引入页面）。 bower.json 文件中列出了所支持的 jQuery 版本。 
```

## 1\. Data 属性

你可以仅仅通过 data 属性 API 就能使用所有的 Bootstrap 插件，无需写一行 JavaScript 代码。这是 Bootstrap 中的一等 API，也应该是你的首选方式。

话又说回来，在某些情况下可能需要将此功能关闭。因此，我们还提供了关闭 data 属性 API 的方式，即解除绑定到文档命名空间上的所有事件 data-api。就像下面这样：

```js
$(document).off('.data-api') 
```

另外，如果是针对某个特定的插件，只需在 data-api 前面添加那个插件的名称作为命名空间，如下：

```js
$(document).off('.alert.data-api') 
```

## 2\. 编程式 API

我们还提供了所有 Bootstrap 插件的纯 JavaScript API。所有公开的 API 都是支持单独或链式调用的，并且返回其所操作的元素集合（注：和 jQuery 的调用形式一致）。

```js
$(".btn.danger").button("toggle").addClass("fat") 
```

所有方法都可以接受一个可选的 option 对象作为参数，或者一个代表特定方法的字符串，或者什么也不提供（在这种情况下，插件将会以默认值初始化）：

```js
$("#myModal").modal()                      // 使用默认值初始化
$("#myModal").modal({ keyboard: false })  
$("#myModal").modal('show') 
```

每个插件还通过 Constructor 属性暴露了其自身的构造器函数：\$.fn.popover.Constructor。如果你想获取某个插件的实例，可以直接从页面元素内获取：$('[rel=popover]').data('popover')。

## 3\. 避免冲突

某些时候可能需要将 Bootstrap 插件与其他 UI 框架共同使用。在这种情况下，命名空间冲突随时可能发生。如果不行发生了这种情况，你可以通过调用插件的.noConflict 方法恢复原始值。

```js
var bootstrapButton = $.fn.button.noConflict() 
$.fn.bootstrapBtn = bootstrapButton 
```

## 4\. 事件

Bootstrap 为大部分插件所具有的动作提供了自定义事件。一般来说，这些事件都有不定式和过去式两种动词形式，例如，不定式形式的动词表示其在事件开始时被触发；而过去式动词表示其在动作直接完毕之后被触发。

从 3.0.0 开始，所有的 Bootstrap 事件都采用了命名空间。

所有以不定式形式的动词命名的事件都提供了 preventDefault 功能。这就赋予你在动作开始执行前将其停止的能力。

```js
$('#myModal').on('show.bs.modal', function (e) {
  if (!data) return e.preventDefault() 
}) 
```

```js
第三方工具库

Bootstrap 官方不提供对第三方 JavaScript 工具库的支持，例如 Prototype 或 jQuery UI。除了.noConflict 和采用命名空间的事件，还可能会有兼容性方面的问题，这就需要你自己来处理了 
```

## 二、模态框

## 1\. 案例

模态框经过了优化，更加灵活，以弹出对话框的形式出现，具有最小和最实用的功能集。

```js
不支持模态框重叠

千万不要在一个模态框上重叠另一个模态框。要想同时支持多个模态框，需要自己写额外的代码来实现。 
```

**静态案例**

以下模态框包含了模态框的头、体和一组在放置于底部的按钮。

```js
<div class="modal fade">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
        <h4 class="modal-title">Modal title</h4>
      </div>
      <div class="modal-body">
        <p>One fine body&hellip;</p>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
        <button type="button" class="btn btn-primary">Save changes</button>
      </div>
    </div>
  </div>
</div> 
```

首先最外层的是 model，然后里面嵌套了一个 model-dialog,model-dialog 里面又嵌套 model-content,当中包含“header”、“title”、"footer"。

**动态演示**

点击下面的按钮即可通过 JavaScript 启动一个模态框。此模态框将从上到下、逐渐浮现到页面前。

```js
<button class="btn btn-primary btn-lg" data-toggle="modal" data-target="#myModal">
  Launch demo modal
</button>
<div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
        <h4 class="modal-title" id="myModalLabel">Modal title</h4>
      </div>
      <div class="modal-body">
        One fine body&hellip;
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
        <button type="button" class="btn btn-primary">Save changes</button>
      </div>
    </div>
  </div>
</div> 
```

同样的，不过首先是一个按钮，按钮中添加了两个 data 属性，要设置 data-toggle="modal" data-target="#myModal"。

然后下面最大的是一个 modal，并且给与属性 id 赋值为上面 button 中的 data-target ，进行交互。

```js
增强模态框的可访问性

请确保为.modal 添加了 role="dialog"；aria-labelledby="myModalLabel"属性指向模态框标题；aria-hidden="true"告诉辅助性工具略过模态框的 DOM 元素。

另外，你还应该为模态框添加描述性信息。为.modal 添加 aria-describedby 属性用以指向描述信息。 
```

![](img/134.jpg)

## 2\. 用法--通过 data 属性

不需写 JavaScript 代码也可激活模态框。通过在一个起控制器作用的页面元素（例如，按钮）上设置 data-toggle="modal"，并使用 data-target="#foo"或 href="#foo"指向特定的模态框即可。就像本例中的

```js
<button class="btn btn-primary btn-lg" data-toggle="modal" data-target="#myModal">
  Launch demo modal
</button> 
```

**用法--通过 JavaScript 调用**

只需一行 JavaScript 代码，即可通过 id myModal 调用模态框：

```js
$('#myModal').modal(options) 
```

**选项**

可以将选项通过 data 属性或 JavaScript 传递。对于 data 属性，需要将选项名称放到 data-之后，例如 data-backdrop=""。

![](img/135.jpg)

**方法**

*   .modal(options)

将你指定的内容作为模态框启动。其接受一个可选的 object 类型的参数。

```js
$('#myModal').modal({
  keyboard: false
}) 
```

*   .modal('toggle')

手动启动或隐藏模态框。

```js
$('#myModal').modal('toggle') 
```

手动打开一个模态框。

```js
$('#myModal').modal('show') 
```

手动隐藏一个模态框。

```js
$('#myModal').modal('hide') 
```

将上面的示例代码进行修改，其主要变化在于按钮上

```js
 <script type="text/javascript">
    function test()
    {
        $('#myModal').modal('show');
                alert(1);
    }    
</script>
<button onClick="test()" class="btn btn-primary btn-lg">
  Launch demo modal
</button>
<div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
        <h4 class="modal-title" id="myModalLabel">Modal title</h4>
      </div>
      <div class="modal-body">
        One fine body&hellip;
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
        <button type="button" class="btn btn-primary">Save changes</button>
      </div>
    </div>
  </div>
</div> 
```

并且为按钮添加了 onclick 事件，也就是通过点击按钮触发事件来进行模态框的弹出。 你可以测试一下看下效果。

**事件**

Bootstrap 的模态框类暴露了一些事件用于截获并执行自己的代码。

![](img/136.jpg)

```js
<script type="text/javascript">
    $('#myModal').on('hide.bs.modal', function () {
        alert(11);
    });    
</script> 
```

可以发现执行关闭的时候会弹出对话框。

最后把所有代码代码贴出来

```js
<!DOCTYPE html>
 <html lang="zh-CN">
 <head>
 <title>Bootstrap</title>
 <meta charset="UTF-8">
 <meta name="viewport" content="width=device-width, initial-scale=1.0">
 <!-- Bootstrap -->
 <link href="css/bootstrap.min.css" rel="stylesheet" media="screen">
<style type="text/css">
    .btn-group:hover>.dropdown-menu{display:block;}
</style>
 <!--[if lt IE 9]>
 <script src="http://labfile.oss.aliyuncs.com/html5shiv/3.7.0/html5shiv.js"></script>
 <script src="http://labfile.oss.aliyuncs.com/respond.js/1.3.0/respond.min.js"></script>
 <![endif]-->
 </head>
 <body>
 <div class="container">
 <!DOCTYPE html>
 <html lang="zh-CN">
 <head>
 <title>Bootstrap</title>
 <meta charset="UTF-8">
 <meta name="viewport" content="width=device-width, initial-scale=1.0">
 <!-- Bootstrap -->
 <link href="css/bootstrap.min.css" rel="stylesheet" media="screen">
<style type="text/css">
    .btn-group:hover>.dropdown-menu{display:block;}
</style>
 <!--[if lt IE 9]>
 <script src="http://labfile.oss.aliyuncs.com/html5shiv/3.7.0/html5shiv.js"></script>
 <script src="http://labfile.oss.aliyuncs.com/respond.js/1.3.0/respond.min.js"></script>
 <![endif]-->
 </head>
 <body>
 <h1></h1>
 <script type="text/javascript">
    function test()
    {
        $('#myModal').modal('show');
                alert(1);
    }    
</script>
<button onClick="test()" class="btn btn-primary btn-lg">
  Launch demo modal
</button>
<div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
        <h4 class="modal-title" id="myModalLabel">Modal title</h4>
      </div>
      <div class="modal-body">
        One fine body&hellip;
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
        <button type="button" class="btn btn-primary">Save changes</button>
      </div>
    </div>
  </div>
</div>
 <script src="js/jquery-2.0.3.min.js"></script>
 <script src="js/bootstrap.min.js"></script>
 <script type="text/javascript">
    $('#myModal').on('hide.bs.modal', function () {
        alert(11);
    });    
 </script>
 </body>
 </html> 
```