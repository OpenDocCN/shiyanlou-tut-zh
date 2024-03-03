# 第 25 节 轮播

下面先来展示的就是此插件和相关组件制作的轮播案例。

![](img/163.jpg)

```
<body style="width:900px; margin-left:auto; margin-right:auto;">
    <div id="carousel-example-generic" class="carousel slide" data-ride="carousel">
      <!-- Indicators -->
      <ol class="carousel-indicators">
        <li data-target="#carousel-example-generic" data-slide-to="0" class="active"></li>
        <li data-target="#carousel-example-generic" data-slide-to="1"></li>
        <li data-target="#carousel-example-generic" data-slide-to="2"></li>
      </ol>

      <!-- Wrapper for slides -->
      <div class="carousel-inner" style="text-align:center">
        <div class="item active">
          <img alt="First slide" src="http://placehold.it/900x500/78EB09/FFEB09/&text=First slide" ></img>
        </div>
        <div class="item">
          <img alt="Second slide" src="http://placehold.it/900x500/78EB09/FFEB09/&text=Second slide" ></img>
        </div>
        <div class="item">
          <img alt="Third slide" src="http://placehold.it/900x500/78EB09/FFEB09/&text=Third slide"  ></img>
        </div>
      </div>

      <!-- Controls -->
      <a class="left carousel-control" href="#carousel-example-generic" data-slide="prev">
        <span class="glyphicon glyphicon-chevron-left"></span>
      </a>
      <a class="right carousel-control" href="#carousel-example-generic" data-slide="next">
        <span class="glyphicon glyphicon-chevron-right"></span>
      </a>
    </div>
 <script src="js/jquery-2.0.3.min.js"></script>
 <script src="js/bootstrap.min.js"></script>
 <script type="text/javascript">
//$('.carousel').carousel('next');
 </script>
 </body> 
```

```
Internet Explorer 8 & 9 不支持过渡动画效果

Bootstrap 基于 CSS3 实现动画效果，但是 Internet Explorer 8 & 9 不支持这些必要的 CSS 属性。因此，使用这两种浏览器时将会丢失过渡动画效果。而且，Bootstrap 并不打算使用基于 jQuery 实现替代功能。 
```

## 1\. 可选选项

在任何.item 中均可以通过添加.carousel-caption 从而为每帧幻灯片添加说明文字。也可以添加任何 HTML 代码，这些 HTML 代码将会被自动排列和格式化。

```
<div class="item active">
  <img alt="First slide" src="http://placehold.it/900x500/78EB09/FFEB09/&text=First slide" ></img>
  <div class="carousel-caption">
      <h4>First Thumbnail label</h4>
      <p>Cras justo odio, dapibus ac facilisis in, egestas eget quam. Donec id elit non mi porta gravida at eget metus. Nullam id dolor id nibh ultricies vehicula ut id elit.</p>
   </div>
</div> 
```

为三个项，分别加上，然后效果就有了额。

![](img/164.jpg)

```
可访问性问题

轮播组件并不兼容可访问性标准。如果需要兼容，请考虑其他展示幻灯片的方案。 
```

## 2\. 用法

**通过 data 属性**

通过 data 属性可以很容易的控制轮播的定位。data-slide 可以接受控制播放位置的 prev 或 next 关键字。另外，还可以通过 data-slide-to 传递以 0 开始的幻灯片下标。

data-ride="carousel"属性用来标记在页面加载之后即开始启动的轮播组件。

![](img/165.jpg)

在最外层的轮播容器中添加即可

```
<div id="carousel-example-generic" class="carousel slide" data-ride="carousel"> 
```

**通过 JavaScript**

手动启动轮播组件（上面我们通过使用 data-ride 属性进行自动开启轮播组件。）：

```
$('.carousel').carousel() 
```

**选项**

可以将选项通过 data 属性或 JavaScript 传递。对于 data 属性，需要将选项名称放到 data-之后，例如 data-interval=""。

![](img/166.jpg)

**方法**

```
$("").carousel(options) 
```

初始化轮播组件，接受一个可选的 object 类型的 options 参数，并开始幻灯片循环。

```
$('.carousel').carousel({
  interval: 2000
}) 
```

.carousel('cycle') 从左到右循环各帧。 .carousel('pause') 停止轮播。 .carousel(number) 将轮播定位到指定的帧上（帧下标以 0 开始，类似数组）。 .carousel('prev') 返回到上一帧。 .carousel('next') 转到下一帧。

**事件**

Bootstrap 的轮播组件暴露了两个事件用于监听。

![](img/167.jpg)

就这样为轮播组件绑定事件，然后在相应的运行时就会执行的。这个在之前的 JavaScript 插件中讲解的也比较多，形式都是通用的，所以只要会用就可以了。