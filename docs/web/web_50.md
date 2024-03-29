# 第 15 节 一、进度条

提供工作或动作的实时反馈，只用简单且灵活的进度条。

```js
跨浏览器兼容性

进度条使用了 CSS3 的 transition 和 animation 属性来完成一些效果。这些特性在 Internet Explorer 9 或以下版本中、Firefox 的老版本中没有被支持。Opera 12 不支持 znimation 属性。 
```

## 1\. 基本案例

默认的进度条。

```js
<div class="progress">
   <div class="progress-bar" role="progressbar" aria-valuenow="60" aria-valuemin="0" aria-valuemax="100" style="width: 60%;">
     <span class="sr-only">60% Complete</span>
   </div>
</div> 
```

![](img/116.jpg)

## 2\. 有意义的替换

```js
<div class="progress">
  <div class="progress-bar progress-bar-success" role="progressbar" aria-valuenow="40" aria-valuemin="0" aria-valuemax="100" style="width: 40%">
    <span class="sr-only">40% Complete (success)</span>
  </div>
</div>
<div class="progress">
  <div class="progress-bar progress-bar-info" role="progressbar" aria-valuenow="20" aria-valuemin="0" aria-valuemax="100" style="width: 20%">
    <span class="sr-only">20% Complete</span>
  </div>
</div>
<div class="progress">
  <div class="progress-bar progress-bar-warning" role="progressbar" aria-valuenow="60" aria-valuemin="0" aria-valuemax="100" style="width: 60%">
    <span class="sr-only">60% Complete (warning)</span>
  </div>
</div>
<div class="progress">
  <div class="progress-bar progress-bar-danger" role="progressbar" aria-valuenow="80" aria-valuemin="0" aria-valuemax="100" style="width: 80%">
    <span class="sr-only">80% Complete</span>
  </div>
</div> 
```

![](img/117.jpg)

## 3\. 条纹效果

用一个渐变可以创建条纹效果，在 IE8 中不可用。

```js
<div class="progress progress-striped">
  <div class="progress-bar progress-bar-success" role="progressbar" aria-valuenow="40" aria-valuemin="0" aria-valuemax="100" style="width: 40%">
    <span class="sr-only">40% Complete (success)</span>
  </div>
</div>
<div class="progress progress-striped">
  <div class="progress-bar progress-bar-info" role="progressbar" aria-valuenow="20" aria-valuemin="0" aria-valuemax="100" style="width: 20%">
    <span class="sr-only">20% Complete</span>
  </div>
</div>
<div class="progress progress-striped">
  <div class="progress-bar progress-bar-warning" role="progressbar" aria-valuenow="60" aria-valuemin="0" aria-valuemax="100" style="width: 60%">
    <span class="sr-only">60% Complete (warning)</span>
  </div>
</div>
<div class="progress progress-striped">
  <div class="progress-bar progress-bar-danger" role="progressbar" aria-valuenow="80" aria-valuemin="0" aria-valuemax="100" style="width: 80%">
    <span class="sr-only">80% Complete (danger)</span>
  </div>
</div> 
```

![](img/118.jpg)

## 4\. 运动效果

给.progress-striped 加上.active 使它由右向左运动。

```js
<div class="progress progress-striped active">
  <div class="progress-bar"  role="progressbar" aria-valuenow="45" aria-valuemin="0" aria-valuemax="100" style="width: 45%">
    <span class="sr-only">45% Complete</span>
  </div>
</div> 
```

![](img/119.jpg)

## 5\. 堆叠效果

把多个进度条放入同一个.progress，使它们堆叠。

```js
<div class="progress">
  <div class="progress-bar progress-bar-success" style="width: 35%">
    <span class="sr-only">35% Complete (success)</span>
  </div>
  <div class="progress-bar progress-bar-warning" style="width: 20%">
    <span class="sr-only">20% Complete (warning)</span>
  </div>
  <div class="progress-bar progress-bar-danger" style="width: 10%">
    <span class="sr-only">10% Complete (danger)</span>
  </div>
</div> 
```

![](img/120.jpg)

## 二、媒体对象

这是一个抽象的样式，用以构建不同类型的组件，这些组件都具有在文本内容的左或右对齐的图片（就像 blog 内容或 Tweets 等）。

## 1\. 默认媒体

默认的媒体允许在一个内容块的左边或右边浮动一个媒体对象（图像，视频，音频）

```js
<div class="media">
  <a class="pull-left" href="#">
    <img class="media-object" src="http://placehold.it/64x64" alt="...">
  </a>
  <div class="media-body">
    <h4 class="media-heading">Media heading</h4>
    <p>Cras sit amet nibh libero, in gravida nulla. Nulla vel metus scelerisque ante sollicitudin commodo. Cras purus odio, vestibulum in vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.</p>
  </div>
</div> 
```

![](img/121.jpg)

## 2\. 媒体列表

用一点点额外的标记，就能在列表内使用媒体（对评论或文章列表很有用）。

```js
<ul class="media-list">
  <li class="media">
    <a class="pull-left" href="#">
      <img class="media-object" src="http://placehold.it/64x64" alt="...">
    </a>
    <div class="media-body">
      <h4 class="media-heading">Media heading</h4>
      <p>Cras sit amet nibh libero, in gravida nulla. Nulla vel metus scelerisque ante sollicitudin commodo. Cras purus odio, vestibulum in vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.</p>
      <ul class="media-list">
          <li class="media">
            <a class="pull-left" href="#">
              <img class="media-object" src="http://placehold.it/64x64" alt="...">
            </a>
            <div class="media-body">
              <h4 class="media-heading">Media heading</h4>
              <p>Cras sit amet nibh libero, in gravida nulla. Nulla vel metus scelerisque ante sollicitudin commodo. Cras purus odio, vestibulum in vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.</p>
              <ul class="media-list">
                  <li class="media">
                    <a class="pull-left" href="#">
                      <img class="media-object" src="http://placehold.it/64x64" alt="...">
                    </a>
                    <div class="media-body">
                      <h4 class="media-heading">Media heading</h4>
                      Cras sit amet nibh libero, in gravida nulla. Nulla vel metus scelerisque ante sollicitudin commodo. Cras purus odio, vestibulum in vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.
                    </div>
                  </li>
                </ul>
            </div>
          </li>
          <li class="media">
                    <a class="pull-left" href="#">
                      <img class="media-object" src="http://placehold.it/64x64" alt="...">
                    </a>
                    <div class="media-body">
                      <h4 class="media-heading">Media heading</h4>
                      Cras sit amet nibh libero, in gravida nulla. Nulla vel metus scelerisque ante sollicitudin commodo. Cras purus odio, vestibulum in vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.
                    </div>
                  </li>
      </ul>
    </div>
  </li>
</ul> 
```

![](img/122.jpg)

## 三、列表组

列表组是灵活又强大的组件，不仅仅用于显示简单的成列表的元素，还用于复杂的定制的内容。

## 1\. 基本案例

最简单的列表只是无顺序列表，列表条目和正确的类。

```js
<ul class="list-group">
  <li class="list-group-item">Cras justo odio</li>
  <li class="list-group-item">Dapibus ac facilisis in</li>
  <li class="list-group-item">Morbi leo risus</li>
  <li class="list-group-item">Porta ac consectetur ac</li>
  <li class="list-group-item">Vestibulum at eros</li>
</ul> 
```

![](img/123.jpg)

## 2\. 徽章

给列表组加入徽章，它会自动地放在右面。

```js
<ul class="list-group">
  <li class="list-group-item"><span class="badge">14</span>Cras justo odio</li>
  <li class="list-group-item"><span class="badge">33</span>Dapibus ac facilisis in</li>
  <li class="list-group-item"><span class="badge">11</span>Morbi leo risus</li>
  <li class="list-group-item"><span class="badge">41</span>Porta ac consectetur ac</li>
  <li class="list-group-item"><span class="badge">21</span>Vestibulum at eros</li>
</ul> 
```

![](img/124.jpg)

## 3\. 链接条目

用< a>标签而不是< li>标签（也就是说父元素是< div>而不是< ul>）。没必要给每个元素都加一个父元素。

```js
<div class="list-group">
  <a href="#" class="list-group-item active">
    Cras justo odio
  </a>
  <a href="#" class="list-group-item">Dapibus ac facilisis in</a>
  <a href="#" class="list-group-item">Morbi leo risus</a>
  <a href="#" class="list-group-item">Porta ac consectetur ac</a>
  <a href="#" class="list-group-item">Vestibulum at eros</a>
</div> 
```

![](img/125.jpg)

## 4\. 定制内容

在里面可以加几乎任何 HTML，甚至是像下面的带链接的列表组。

```js
<div class="list-group">
  <a href="#" class="list-group-item active">
    <h4 class="list-group-item-heading">List group item heading</h4>
    <p class="list-group-item-text">Cras sit amet nibh libero, in gravida nulla. n vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.</p>
  </a>
    <a href="#" class="list-group-item">
    <h4 class="list-group-item-heading">List group item heading</h4>
    <p class="list-group-item-text">Cras sit amet nibh libero, in gravida nulla. n vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.</p>
  </a>
    <a href="#" class="list-group-item">
    <h4 class="list-group-item-heading">List group item heading</h4>
    <p class="list-group-item-text">Cras sit amet nibh libero, in gravida nulla. n vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.</p>
  </a>
</div> 
```

![](img/126.jpg)

## 四、面板

虽然不总是必须，但是某些时候你可能需要将某些内容放到一个盒子里。对于这种情况，可以试试面板组件。

## 1\. 基本案例

默认的.panel 所做的只是提供基本的边界和内部，来包含内容。

```js
<div class="panel panel-default">
  <div class="panel-body">
    Basic panel example
  </div>
</div> 
```

![](img/127.jpg)

## 2\. 带标题的面版

用.panel-heading 可以简单地加入一个标题容器。您也可以用< h1>-< h6>和.panel-title 类加入预定义样式的标题。

```js
<div class="panel panel-default">
  <div class="panel-heading">Panel heading without title</div>
  <div class="panel-body">
    Panel content
  </div>
</div> 
```

![](img/128.jpg)

## 3\. 带脚注的面版

把按钮或次要的文本放入.panel-footer。注意面版的脚注不会从带意义的替换中继承颜色，因为它不是在前面的内容。

```js
<div class="panel panel-default">
  <div class="panel-body">
    Panel content
  </div>
  <div class="panel-footer">Panel footer</div>
</div> 
```

![](img/129.jpg)

## 4\. 有意义的替换

像其它组件一样，可以简单地通过加入有意义的状态类，给特定的内容使用更有意义的面版。

```js
<div class="panel panel-primary">
  <div class="panel-heading">Panel heading without title</div>
  <div class="panel-body">
    Panel content
  </div>
</div>
<div class="panel panel-success">
  <div class="panel-heading">Panel heading without title</div>
  <div class="panel-body">
    Panel content
  </div>
</div>
<div class="panel panel-info">
  <div class="panel-heading">Panel heading without title</div>
  <div class="panel-body">
    Panel content
  </div>
</div>
<div class="panel panel-warning">
  <div class="panel-heading">Panel heading without title</div>
  <div class="panel-body">
    Panel content
  </div>
</div> 
```

![](img/130.jpg)

## 5\. 带表格的面版

为了无缝的设计，在面版中加入.table。如果有.panel-body，就在表格的上方加上一个用于分割的边界。

```js
<div class="panel panel-default">
  <!-- Default panel contents -->
  <div class="panel-heading">Panel heading</div>
  <div class="panel-body">
    <p>Some default panel content here. Nulla vitae elit libero, a pharetra augue. Aenean lacinia bibendum nulla sed consectetur. Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum. Nullam id dolor id nibh ultricies vehicula ut id elit.</p>
    <table class="table">  
      <thead>  
        <tr>  
          <th>First Name</th>  
          <th>Last Name</th>
          <th>User Name</th>  
        </tr>  
      </thead>  
      <tbody>  
        <tr>  
          <td>aehyok</td>  
          <td>leo</td> 
          <td>@aehyok</td>  
        </tr>
        <tr>  
          <td>lynn</td>  
          <td>thl</td> 
          <td>@lynn</td>  
        </tr>
      </tbody>  
    </table>  
  </div> 
```

![](img/131.jpg)

如果没有.panel-body，面版标题会和表格连接起来，没有空隙。

![](img/132.jpg)

## 6\. 带列表组的面版

```js
<div class="panel panel-default">
  <!-- Default panel contents -->
  <div class="panel-heading">Panel heading</div>
  <div class="panel-body">
    <p>Some default panel content here. Nulla vitae elit libero, a pharetra augue. Aenean lacinia bibendum nulla sed consectetur. Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum. Nullam id dolor id nibh ultricies vehicula ut id elit.</p> 
  </div>
    <ul class="list-group">
    <li class="list-group-item">Cras justo odio</li>
    <li class="list-group-item">Dapibus ac facilisis in</li>
    <li class="list-group-item">Morbi leo risus</li>
    <li class="list-group-item">Porta ac consectetur ac</li>
    <li class="list-group-item">Vestibulum at eros</li>
  </ul>
  </div> 
```

![](img/133.jpg)