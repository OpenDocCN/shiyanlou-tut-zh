# 第 3 节 栅格系统案例

## 1\. 栅格选项

通过下面的截图可以比较清楚的来查看 Bootstrap 的栅格系统是如何在多种不同的移动设备上面进行工作的。

![](img/8.jpg)

从上面的截图可以看出来，Bootstrap 针对不同尺寸的屏幕（包括手机、平板、PC 等等）设置了不同的样式类，这样让开发人员在开发时可以有更多的选择。根据我的理解：如果在一个元素上使用多个不同的上面的样式类，那么元素会根据在不同尺寸选择最合适（匹配最理想的）的样式类。简单的举例进行说明：比如在一个元素上我们使用了两个样式类：.col-md-和.col-lg。可以对照上面的截图来看:

第一种情况：尺寸》=1200px;那么会选择.col-lg。

第二种情况：尺寸》=992px 并且尺寸《=1200px;那么会选择.col-md。

第三种情况如果尺寸《992px;那么这两个样式类都将不会作用于元素上。

## 2\. 从堆叠到水平排列

栅格选项中的四个样式类的使用都是依附于.row 类的基础上，而.row 类通常都会在.container 的里进行。

```
 <div class="container">
     <div class="row">
        <div class="col-md-1"></div>
    </div>
 </div> 
```

就是在合适的容器中使用合适的样式类。

使用单一的一组.col-md-*栅格 class，你就可以创建一个基本的栅格系统，在手机和平板设备上一开始是堆叠在一起的（超小屏幕到小屏幕这一范围），在桌面（中等）屏幕设备上变为水平排列。将列(col-*-*)放置于任何.row 内即可。

```
 <div class="container"> 
     <div class="page-header">
    <h1>案例:从堆叠到水平排列</h1>
    </div>
    <div class="row">
      <div class="col-md-1">.col-md-1</div>
      <div class="col-md-1">.col-md-1</div>
      <div class="col-md-1">.col-md-1</div>
      <div class="col-md-1">.col-md-1</div>
      <div class="col-md-1">.col-md-1</div>
      <div class="col-md-1">.col-md-1</div>
      <div class="col-md-1">.col-md-1</div>
      <div class="col-md-1">.col-md-1</div>
      <div class="col-md-1">.col-md-1</div>
      <div class="col-md-1">.col-md-1</div>
      <div class="col-md-1">.col-md-1</div>
      <div class="col-md-1">.col-md-1</div>
    </div>
    <h1></h1>
    <div class="row">
      <div class="col-md-8">.col-md-8</div>
      <div class="col-md-4">.col-md-4</div>
    </div>
    <h1></h1>
    <div class="row">
      <div class="col-md-4">.col-md-4</div>
      <div class="col-md-4">.col-md-4</div>
      <div class="col-md-4">.col-md-4</div>
    </div>
    <h1></h1>
    <div class="row">
      <div class="col-md-6">.col-md-6</div>
      <div class="col-md-6">.col-md-6</div>
    </div>
 </div> 
```

可以通过控制浏览器的宽度查看堆叠和水平排列的效果

![](img/9.jpg)

很明显这就是堆叠的效果，就是将浏览器的宽度调为比较小的时候。

![](img/10.jpg)

现在便是水平排列。将浏览器的宽度调为稍宽之后。你也可以使用其他三个类进行测试效果。

## 3\. 移动设备和桌面

从上面的案例我们可以发现，当小尺寸的屏幕的时候使用.col-md-*的时候它会呈现堆叠的状态，那么开发人员肯定有时候需要小尺寸的屏幕页面展示也要是水平排列的。那么我们的.col-xs-*（小于 768px 屏幕的样式类）就派上用场了。

```
<div class="page-header">
    <h1>案例:移动设备和桌面</h1>
</div>
<div class="row">
  <div class="col-xs-12 col-md-8">.col-xs-12 .col-md-8</div>
  <div class="col-xs-6 col-md-4">.col-xs-6 .col-md-4</div>
</div>
<h1></h1>
<div class="row">
  <div class="col-xs-6 col-md-4">.col-xs-6 .col-md-4</div>
  <div class="col-xs-6 col-md-4">.col-xs-6 .col-md-4</div>
  <div class="col-xs-6 col-md-4">.col-xs-6 .col-md-4</div>
</div>
<h1></h1>
<div class="row">
  <div class="col-xs-6">.col-xs-6</div>
  <div class="col-xs-6">.col-xs-6</div>
</div>
<h1></h1> 
```

通过代码可以发现针对前两个的.row 样式类中的 div，我们在每个元素上面都设置了两个样式类。

![](img/11.jpg)

这是在稍大尺寸的屏幕上展示的效果，针对每个元素的两个样式会在不同尺寸下进行合适的选择，那么在稍大的情况下，选择的样式类就会是.col-md-*。

所以：

第一行：8 和 4 的份比。

第二行：三个 4 平平均分成三份。

第三行：6 和 6 虽然是大尺寸因为只有这一个样式，也是平均分成两份。

![](img/12.jpg)

现在就是页面屏幕小于一定程度的时候，针对每个元素进行重新选择样式类。现在真对前两个.row 选择的都是 col-xs-*。

所以：

第一行：两个元素分为 12 和 6，而一行呢就是 12 份，所以第二个元素会进行换行，然后占据 6 份一半的位置。

第二行：3 个 6 份。而一行呢就是 12 份。所以第三个元素会进行换行，然后占据 6 份一般的位置。

## 4\. Responsive column resets

通过上面两个案例的解析，可以通过这四个栅格 class 设计出比较轻松的设计出比较复杂的页面布局了。但是还是会有一些情况中出现一行中的某一列比其他的列高的情况。可能现在你还不太清楚我说的意思，不过没关系，我们直接上案例。

```
<div class="page-header">
    <h1>案例:Responsive column resets</h1>
</div>
<div class="row">
  <div class="col-xs-6 col-sm-3">.col-xs-6 .col-sm-3(通过调整浏览器的宽度或在手机上即可查看这些案例的实际效果。)</div>
  <div class="col-xs-6 col-sm-3">.col-xs-6 .col-sm-3</div>
  <div class="col-xs-6 col-sm-3">.col-xs-6 .col-sm-3</div>
  <div class="col-xs-6 col-sm-3">.col-xs-6 .col-sm-3</div>
</div> 
```

首先来看一下大屏幕下的页面展示效果

![](img/13.jpg)

第一个元素的高度的确不太一样。然后看一下小屏幕的。

![](img/14.jpg)

看现在是这样进行排列的，因为小屏幕下选择的都是.col-xs-*的样式类，而且都是占用 6 份的。不知道这样的效果是不是你想要的。原来是不是想在小屏幕下看到这四个元素在两行然后每行两个元素呢？

接下来我们将上面的代码稍微改动一下，其实也就是添加了一行代码

```
<div class="page-header">
    <h1>案例:Responsive column resets</h1>
</div>
<div class="row">
  <div class="col-xs-6 col-sm-3">.col-xs-6 .col-sm-3(通过调整浏览器的宽度或在手机上即可查看这些案例的实际效果。)</div>
  <div class="col-xs-6 col-sm-3">.col-xs-6 .col-sm-3</div>
  <div class="clearfix visible-xs"></div>
  <div class="col-xs-6 col-sm-3">.col-xs-6 .col-sm-3</div>
  <div class="col-xs-6 col-sm-3">.col-xs-6 .col-sm-3</div>
</div> 
```

![](img/15.jpg)

这样的效果还是达到了两行两列的。当然你也可以通过响应式工具，这个会在以后进行详细说明，这里暂时不进行示例演示了。

## 5\. 列偏移

这个其实很简单就是通过一个样式类，通过.col-md-offset-*可以将列偏移到右侧。这些 class 通过使用*选择器将所有列增加了列的左侧 margin。例如，.col-md-offset-4 将.col-md-4 向右移动了 4 个列的宽度。

```
<div class="page-header">
    <h1>案例:列偏移</h1>
</div>
<div class="row">
  <div class="col-md-4">.col-md-4</div>
  <div class="col-md-4 col-md-offset-4">.col-md-4 .col-md-offset-4</div>
</div>
<div class="row">
  <div class="col-md-3 col-md-offset-3">.col-md-3 .col-md-offset-3</div>
  <div class="col-md-3 col-md-offset-3">.col-md-3 .col-md-offset-3</div>
</div>
<div class="row">
  <div class="col-md-6 col-md-offset-3">.col-md-6 .col-md-offset-3</div>
</div> 
```

通过阅读上面的代码大概可以想象出它的排版布局了吧。

![](img/16.jpg)

## 6\. 嵌套列

为了使用内置的栅格进行内容的嵌套，通过添加一个新的.row 和一系列的.col-md-*列到已经存在的.col-md-*列内即可实现。嵌套列所包含的列加起来应该等于 12。

```
<div class="page-header">
    <h1>案例:列嵌套</h1>
</div>
<div class="row">
  <div class="col-md-9">
    Level 1: .col-md-9
    <div class="row">
      <div class="col-md-6">
        Level 2: .col-md-6
      </div>
      <div class="col-md-6">
        Level 2: .col-md-6
      </div>
    </div>
    <div class="row">
      <div class="col-md-3">
        Level 3: .col-md-3
      </div>
      <div class="col-md-6">
        Level 3: .col-md-6
      </div>
    </div>
  </div> 
</div> 
```

通过上面的代码可以发现，首先定义了一个 row,然后在此 row 中添加了一个.col-md-9 的列，代表这个元素占有 9 列。

然后在这个占有 9 列的元素里面添加了两个不同的 row。

即第一个 row:将第一个 row 分成了两份，每份占有的 6 列，这 12 列，但是其总宽度和它外面的占有 9 列的元素的宽度是一样的。

第二个 row:将第二个 row 分成了两份，第一份占有 3 列，第二份占有 6 列，然后剩余的 3 列没有进行填充。

![](img/17.jpg)

## 7\. 列排序

中文网的解释是：通过使用.col-md-push-* 和 .col-md-pull-*就可以很容易的改变列的顺序。

```
<div class="page-header">
    <h1>案例:列嵌套</h1>
</div>
<div class="row">
  <div class="col-md-4">.col-md-4</div>
  <div class="col-md-4">.col-md-4</div>
  <div class="col-md-4">.col-md-4</div>
</div>
<div class="row">
  <div class="col-md-2 col-md-push-4">.col-md-2 .col-md-push-4</div> 
</div>
<div class="row">
  <div class="col-md-7"></div>
  <div class="col-md-5 col-md-pull-4">.col-md-5 .col-md-push-4</div> 
</div> 
```

可以看出，我定义了三行。

第一行被平均分成三份，每份占有四列。这一行主要是为了与下面两行进行对照的。

第二行里只有一个元素且是占有两列，然后对此元素也添加了.col-md-push-4 的样式类。（让占有两个列的元素又向右移动了 4 列那么现在就是占据了第五列和第六列。）

第三列被分为两份，第一份占有七列，不过是空的，然后第二份占有五列，并且对此元素也添加了一个.col-md-pull-4 的样式类，当然此类和上一行附加的类应该正好有相反的效果。（让占有 5 列的元素又向左移动了四列，原来是从第八列开始的，那么现在是从第四列开始的。）

现在来看一下效果展示

![](img/18.jpg)