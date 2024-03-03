# 第 4 节 排版

## 1\. 标题

Html 中的所有标题标签，从< h1>到< h6>均可使用。另外还提供了.h1 到.h6 的 class,为的是给 inline 属性的文本赋予标题的样式。

```
<div class="container"> 
 <h1 class="page-header">标题</h1>
 <h1>h1\. Bootstrap heading</h1>
 <h2>h2\. Bootstrap heading</h2>
 <h3>h3\. Bootstrap heading</h3>
 <h4>h4\. Bootstrap heading</h4>
 <h5>h5\. Bootstrap heading</h5>
 <h6>h6\. Bootstrap heading</h6>
</div> 
```

直接看效果吧

![](img/19.jpg)

在标题内还可以包含< small>标签或.small 元素，可以用来标记副标题。

```
<div class="container"> 
 <h1 class="page-header">标题</h1>
 <h1>h1\. Bootstrap heading<small>Secondary text</small></h1>
 <h2>h2\. Bootstrap heading<small>Secondary text</small></h2>
 <h3>h3\. Bootstrap heading<small>Secondary text</small></h3>
 <h4>h4\. Bootstrap heading<small>Secondary text</small></h4>
 <h5>h5\. Bootstrap heading<small>Secondary text</small></h5>
 <h6>h6\. Bootstrap heading<small>Secondary text</small></h6>
</div> 
```

![](img/20.jpg)

## 2\. 页面主体

Bootstrap 将全局 font-size 设置为 14px，line-height 为 1.428 。这些属性直接赋给< body>和所有段落元素。另外，< p>（段落）还被设置了等于 1/2 行高的底部外边距（margin）（即 10px）。

```
<h1  class="page-header">页面主体</h1>
<div  style="border:1px solid ">
 <p style="border:1px solid ">Nullam quis risus eget urna mollis ornare vel eu leo. Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Nullam id dolor id nibh ultricies vehicula.

Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Donec ullamcorper nulla non metus auctor fringilla. Duis mollis, est non commodo luctus, nisi erat porttitor ligula, eget lacinia odio sem nec elit. Donec ullamcorper nulla non metus auctor fringilla.

Maecenas sed diam eget risus varius blandit sit amet non magna. Donec id elit non mi porta gravida at eget metus. Duis mollis, est non commodo luctus, nisi erat porttitor ligula, eget lacinia odio sem nec elit.</p>
</div> 
```

通过效果展示就很明显了。

![](img/21.jpg)

**Lead body copy**

通过添加.lead 可以让段落突出显示。

```
<h1  class="page-header">Lead Body Copy</h1>
<div  style="border:1px solid ">
 <p class="lead" style="border:1px solid ">Nullam quis risus eget urna mollis ornare vel eu leo. Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Nullam id dolor id nibh ultricies vehicula.

Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Donec ullamcorper nulla non metus auctor fringilla. Duis mollis, est non commodo luctus, nisi erat porttitor ligula, eget lacinia odio sem nec elit. Donec ullamcorper nulla non metus auctor fringilla.

Maecenas sed diam eget risus varius blandit sit amet non magna. Donec id elit non mi porta gravida at eget metus. Duis mollis, est non commodo luctus, nisi erat porttitor ligula, eget lacinia odio sem nec elit.</p>
</div>
<h1></h1> 
```

通过和上面页面主体的对比就可以看到明显的效果了。

![](img/22.jpg)

## 3\. 强调

直接使用 HTML 中用于标注强调的标签，并给他们赋予少许的样式。 **1.小号文本**

　　对于不需要强调的 inline 或 block 类型的文本，使用< small>标签包裹，其内的文本将被设置为父容器字体大小的 85%。标题元素中嵌套的< small>元素被设置不同的 font-size。

你还可以为行内元素赋予.small 以代替任何< small>标签。

```
<small>This line of text is meant to be treated as fine print.</small> 
```

**2.着重**

通过增加 font-weight 强调一段文本。

```
<strong>rendered as bold text</strong> 
```

**3.斜体**

用斜体强调一段文本。

```
<em>rendered as italicized text</em> 
```

**4.对齐 class**

通过文本对齐 class，可以简单方便的将文字重新对齐。

```
<p class="text-left">Left aligned text.</p>
<p class="text-center">Center aligned text.</p>
<p class="text-right">Right aligned text.</p> 
```

很明显第一行左对齐，第二行居中，第三行右对齐。 **5.强调 class**

这些 class 通过颜色来表示强调。也可以应用于链接，当鼠标盘旋于链接上时，其颜色会变深，就像默认的链接样式。

```
<h1>强调 Class</h1>
<p class="text-muted">Maecenas sed diam eget risus varius blandit sit amet non magna.</p>
<p class="text-primary">Maecenas sed diam eget risus varius blandit sit amet non magna.</p>
<p class="text-success">.Maecenas sed diam eget risus varius blandit sit amet non magna.</p>
<p class="text-info">Maecenas sed diam eget risus varius blandit sit amet non magna..</p>
<p class="text-warning">Maecenas sed diam eget risus varius blandit sit amet non magna..</p>
<p class="text-danger">.Maecenas sed diam eget risus varius blandit sit amet non magna.</p>
<h1></h1> 
```

![](img/23.jpg)

## 4\. 缩略图

当鼠标悬停在缩写和缩写词上时就会显示完整内容，Bootstrap 实现了对 HTML 的< abbr>元素的增强样式。缩略语元素带有 title 属性，外观表现为带有较浅的虚线框，鼠标移至上面时会变成带有“问号”的指针。如想看完整的内容可把鼠标悬停在缩略语上, 但需要包含 title 属性。 **基本缩略语**

如想看完整的内容可把鼠标悬停在缩略语上, 但需要包含 title 属性。

```
<abbr title="attribute">attr</abbr> 
```

看到效果了，就是没办法截到图。 **Initialism**

为缩略语添加.initialism 可以将其 font-size 设置的更小些。

```
<abbr title="HyperText Markup Language" class="initialism">HTML</abbr> 
```

还是只上代码自己看效果。　　

## 5\. 地址

让联系信息以最接近日常使用的格式呈现。在每行结尾添加<br>可以保留需要的样式。

```
<address>
  <strong>Twitter, Inc.</strong><br>
  795 Folsom Ave, Suite 600<br>
  San Francisco, CA 94107<br>
  <abbr title="Phone">P:</abbr> (123) 456-7890
</address>

<address>
  <strong>Full Name</strong><br>
  <a href="mailto:#">first.last@example.com</a>
</address> 
```

## 6\. 引用

在你的文档中引用其他来源的内容。 **默认样式的引用**

将任何 HTML 裹在< blockquote>之中即可表现为引用。对于直接引用，我们建议用<p>标签。

```
<blockquote>
  <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer posuere erat a ante.</p>
</blockquote> 
```

![](img/24.jpg)

**引用选项**

对于标准样式的< blockquote>，可以通过几个简单的变体就能改变风格和内容。

命名来源:添加< small>标签来注明引用来源。来源名称可以放在<cite>标签里面。

```
<blockquote>
  <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer posuere erat a ante.</p>
  <small>Someone famous in <cite title="Source Title">Source Title</cite></small>
</blockquote> 
```

![](img/25.jpg)

会多一个 Source Title **另一种展示风格**

使用.pull-right 可以让引用展现出向右侧移动、对齐的效果。

```
<blockquote class="pull-right">
  <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer posuere erat a ante.</p>
</blockquote> 
```

![](img/26.jpg)

向右对齐移动了额，当然也有相应的 pull-left。　　

## 7\. 列表

**无序列表**

顺序无关紧要的一列元素。

```
<ul>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
</ul> 
```

这个也很明显和 Html 的一样。 **有序列表**

顺序至关重要的一组元素。

```
<ol>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
</ol> 
```

同理有序列表。 **无样式列表**

移除了默认的 list-style 样式和左侧外边距的一组元素（只针对直接子元素）。这这是针对直接子元素，也就是说，你需要对所有嵌套的列表都添加此 class 才能具有同样的样式。

```
<ul class="list-unstyled">
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
</ul> 
```

![](img/27.jpg)

**内联列表**

通过设置 display: inline-block;并添加少量的内补，将所有元素放置于同一列。

```
<ul class="list-inline">
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
    <li>Lorem ipsum dolor sit amet</li>
</ul> 
```

效果当然就是在一行了。 **描述**

带有描述的短语列表。

```
<dl>
  <dt>.Lorem ipsum dolor sit amet</dt>
  <dd>Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet</dd>
</dl> 
```

记得这标签在 Html 当中也是存在的。 **水平排列的描述**

.dl-horizontal 可以让< dl>内短语及其描述排在一行。开始是像< dl>默认样式堆叠在一起，随着导航条逐渐展开而排列在一样。

```
<dl class="dl-horizontal">
  <dt>.Lorem ipsum dolor sit amet</dt>
  <dd>Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet</dd>
</dl> 
```

## 8\. 补充

**内联代码**：在正文中通过< code>标签包裹内联样式的代码片段。

```
For example, <code>&lt;section&gt;</code> should be wrapped as inline. 
```

简单的效果

![](img/28.jpg)

**基本代码快**：多行代码可以使用< pre>标签。为了正确的展示代码，注意将尖括号做转义处理。

```
<pre>&lt;p&gt;Sample text here...&lt;/p&gt;</pre> 
```

简单效果

![](img/29.jpg)

还可以使用.pre-scrollable class，其作用是设置 max-height 为 350px，并在垂直方向展示滚动条。