# 第 19 节 滚动监听

## 1\. 案例

滚动监听插件可以根据滚动条的位置自动更新所对应的导航标记。你可以试试滚动这个页面，看看左侧导航的变化。

先把实现的代码上了，你可以通过测试代码先来看看效果。

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
     <style type="text/css">
       .scrollspy-example 
        { 
            height: 200px; 
            overflow: auto;
            position: relative;
            border:1px solid red;
        }
    </style>
 </head>
 <body>
<div class="container" >
      <nav id="navbar-example" class="navbar navbar-default navbar-static" role="navigation">
        <div class="navbar-header">
          <button class="navbar-toggle" type="button" data-toggle="collapse" data-target=".bs-js-navbar-scrollspy">
            <span class="sr-only">Toggle navigation</span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
          </button>
          <a class="navbar-brand" href="#">Project Name</a>
        </div>
        <div class="collapse navbar-collapse bs-js-navbar-scrollspy">
          <ul class="nav navbar-nav">
            <li class="active"><a href="#fat">@fat</a></li>
            <li><a href="#mdo">@mdo</a></li>
            <li class="dropdown">
              <a href="#" id="navbarDrop1" class="dropdown-toggle" data-toggle="dropdown">Dropdown <b class="caret"></b></a>
              <ul class="dropdown-menu" role="menu" aria-labelledby="navbarDrop1">
                <li><a href="#one" tabindex="-1">one</a></li>
                <li><a href="#two" tabindex="-1">two</a></li>
                <li class="divider"></li>
                <li><a href="#three" tabindex="-1">three</a></li>
              </ul>
            </li>
          </ul>
        </div>
      </nav>
      <div data-offset="0" class="scrollspy-example" data-spy="scroll" data-target="#navbar-example">
        <h4 id="fat">@fat</h4>
        <p>Ad leggings keytar, brunch id art party dolor labore. Pitchfork yr enim lo-fi before they sold out qui. Tumblr farm-to-table bicycle rights whatever. Anim keffiyeh carles cardigan. Velit seitan mcsweeney's photo booth 3 wolf moon irure. Cosby sweater lomo jean shorts, williamsburg hoodie minim qui you probably haven't heard of them et cardigan trust fund culpa biodiesel wes anderson aesthetic. Nihil tattooed accusamus, cred irony biodiesel keffiyeh artisan ullamco consequat.</p>
        <h4 id="mdo">@mdo</h4>
        <p>Veniam marfa mustache skateboard, adipisicing fugiat velit pitchfork beard. Freegan beard aliqua cupidatat mcsweeney's vero. Cupidatat four loko nisi, ea helvetica nulla carles. Tattooed cosby sweater food truck, mcsweeney's quis non freegan vinyl. Lo-fi wes anderson +1 sartorial. Carles non aesthetic exercitation quis gentrify. Brooklyn adipisicing craft beer vice keytar deserunt.</p>
        <h4 id="one">one</h4>
        <p>Occaecat commodo aliqua delectus. Fap craft beer deserunt skateboard ea. Lomo bicycle rights adipisicing banh mi, velit ea sunt next level locavore single-origin coffee in magna veniam. High life id vinyl, echo park consequat quis aliquip banh mi pitchfork. Vero VHS est adipisicing. Consectetur nisi DIY minim messenger bag. Cred ex in, sustainable delectus consectetur fanny pack iphone.</p>
        <h4 id="two">two</h4>
        <p>In incididunt echo park, officia deserunt mcsweeney's proident master cleanse thundercats sapiente veniam. Excepteur VHS elit, proident shoreditch +1 biodiesel laborum craft beer. Single-origin coffee wayfarers irure four loko, cupidatat terry richardson master cleanse. Assumenda you probably haven't heard of them art party fanny pack, tattooed nulla cardigan tempor ad. Proident wolf nesciunt sartorial keffiyeh eu banh mi sustainable. Elit wolf voluptate, lo-fi ea portland before they sold out four loko. Locavore enim nostrud mlkshk brooklyn nesciunt.</p>
        <h4 id="three">three</h4>
        <p>Ad leggings keytar, brunch id art party dolor labore. Pitchfork yr enim lo-fi before they sold out qui. Tumblr farm-to-table bicycle rights whatever. Anim keffiyeh carles cardigan. Velit seitan mcsweeney's photo booth 3 wolf moon irure. Cosby sweater lomo jean shorts, williamsburg hoodie minim qui you probably haven't heard of them et cardigan trust fund culpa biodiesel wes anderson aesthetic. Nihil tattooed accusamus, cred irony biodiesel keffiyeh artisan ullamco consequat.</p>
        <p>Keytar twee blog, culpa messenger bag marfa whatever delectus food truck. Sapiente synth id assumenda. Locavore sed helvetica cliche irony, thundercats you probably haven't heard of them consequat hoodie gluten-free lo-fi fap aliquip. Labore elit placeat before they sold out, terry richardson proident brunch nesciunt quis cosby sweater pariatur keffiyeh ut helvetica artisan. Cardigan craft beer seitan readymade velit. VHS chambray laboris tempor veniam. Anim mollit minim commodo ullamco thundercats.
        </p>
      </div>
 </div>   
 <script src="js/jquery-2.0.3.min.js"></script>
 <script src="js/bootstrap.min.js"></script>
 </body>
 </html> 
```

然后运行后，在内容下，也就是有滚动条哪里滚动鼠标齿轮，即可看到效果。

## 2\. 用法 1--通过 data 属性

通过为需要监听的页面元素（一般是<body>）不过在上面添加在了 Div 上面，你可以自己看看代码就明白了。然后给 div 添加属性 data-spy="scroll"就可很轻松的为顶部导航条添加滚动监听功能。然后为其添加 data-target 属性，此属性的值为任何 Bootstrap 中.nav 组件的父元素的 ID 或 class。

```
<div data-offset="0" class="scrollspy-example" data-spy="scroll" data-target="#navbar-example">
........
</div> 
```

```
导航链接地址必须有对应的目标

导航条内的链接地址必须有对应的页面元素具有同样的 ID 值。例如，<a href="#home">home</a>必须对应 DOM 中例如<div id="home"></div>。 
```

## 3\. 用法 2--通过 JavaScript

通过 JavaScript 启动滚动监听：

```
<script type="text/javascript">
  $(function () {
    $('.scrollspy-example').scrollspy({ target:'#navbar-example' });
  })
</script> 
```

通过将样式类为 scrollspy-example 的 div，去掉它的 data-target 属性。这样同样可以进行鼠标滚轮的切换。

**方法**

.scrollspy('refresh')

使用滚动监听插件时，每当页面中从 DOM 中增加或删除页面元素时，都需要调用此方法以，如下：

```
$('[data-spy="scroll"]').each(function () {
  var $spy = $(this).scrollspy('refresh')
}) 
```

**选项**

可以将选项通过 data 属性或 JavaScript 传递。对于 data 属性，需要将选项名称放到 data-之后，例如 data-offset=""。

![](img/144.jpg)

**事件**

![](img/145.jpg)

```
<script type="text/javascript">
    $('#navbar-example').on('activate.bs.scrollspy', function () {
          alert(1);
    })
</script> 
```

最后注意:针对滚动监听的内容当然要添加滚动条，也就是要预先添加样式

```
<style type="text/css">
.scrollspy-example 
{ 
    height: 200px; 
    overflow: auto;
    position: relative;
    border:1px solid red;
}
</style> 
```

给与 Div 内容一定的高度。