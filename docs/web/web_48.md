# 第 13 节 一、分页

## 1\. 默认分页

发出了这个简单的分页，用在应用或搜索结果中超级棒。这个大块容易看见，容易缩放并有大块的点击区域。

```js
<ul class="pagination">
  <li><a href="#">&laquo;</a></li>
  <li><a href="#">1</a></li>
  <li><a href="#">2</a></li>
  <li><a href="#">3</a></li>
  <li><a href="#">4</a></li>
  <li><a href="#">5</a></li>
  <li><a href="#">&raquo;</a></li>
</ul> 
```

![](img/98.jpg)

**激活和禁用状态**

链接在不同情况下可以定制。给不能点击的链接用.disabled 并且用.active 显示是当前页。

```js
<ul class="pagination">
  <li class="disabled"><a href="#">&laquo;</a></li>
  <li class="active"><a href="#">1</a></li>
  <li><a href="#">2</a></li>
  <li><a href="#">3</a></li>
  <li><a href="#">4</a></li>
  <li><a href="#">5</a></li>
  <li><a href="#">&raquo;</a></li>
</ul> 
```

![](img/99.jpg)

你还可以将 active 或 disabled 应用于< span>标签，这样就可以让其保持需要的样式并移除点击功能。

```js
<ul class="pagination">
  <li class="disabled"><a href="#">&laquo;</a></li>
  <li class="active"><span>1 <span class="sr-only">(current)</span></span></li>
  <li><a href="#">2</a></li>
  <li><a href="#">3</a></li>
  <li><a href="#">4</a></li>
  <li><a href="#">5</a></li>
  <li><a href="#">&raquo;</a></li>
</ul> 
```

你可以通过点击测试，看是否移除了点击功能。

**尺寸**

想要更小或更大的分页？要得到更多尺寸，加上.pagination-lg 或.pagination-sm 吧。

```js
<p></p>
<ul class="pagination pagination-lg">
  <li class="disabled"><a href="#">&laquo;</a></li>
  <li class="active"><span>1 <span class="sr-only">(current)</span></span></li>
  <li><a href="#">2</a></li>
  <li><a href="#">3</a></li>
  <li><a href="#">4</a></li>
  <li><a href="#">5</a></li>
  <li><a href="#">&raquo;</a></li>
</ul>  
<p></p> 
<ul class="pagination">
  <li class="disabled"><a href="#">&laquo;</a></li>
  <li class="active"><span>1 <span class="sr-only">(current)</span></span></li>
  <li><a href="#">2</a></li>
  <li><a href="#">3</a></li>
  <li><a href="#">4</a></li>
  <li><a href="#">5</a></li>
  <li><a href="#">&raquo;</a></li>
</ul> 
<p></p>
<ul class="pagination pagination-sm">
  <li class="disabled"><a href="#">&laquo;</a></li>
  <li class="active"><span>1 <span class="sr-only">(current)</span></span></li>
  <li><a href="#">2</a></li>
  <li><a href="#">3</a></li>
  <li><a href="#">4</a></li>
  <li><a href="#">5</a></li>
  <li><a href="#">&raquo;</a></li>
</ul> 
```

![](img/100.jpg)

## 2\. 翻页

用轻便的标记和样式，就能做个上一页和下一页的简单翻页。用在像博客和杂志这样的简单站点上棒极了。

**默认案例**

在默认的翻页中，链接居中。

```js
<ul class="pager">
  <li><a href="#">Previous</a></li>
  <li><a href="#">Next</a></li>
</ul> 
```

![](img/101.jpg)

**对齐链接**

您可以把链接向两端对齐作为替代。

```js
<ul class="pager">
  <li class="previous"><a href="#">&larr; Older</a></li>
  <li class="next"><a href="#">Newer &rarr;</a></li>
</ul> 
```

![](img/102.jpg)

**可选的禁用状态**

翻页链接也用分页中的.disabled 工具类。

```js
<ul class="pager">
  <li class="previous disabled"><a href="#">&larr; Older</a></li>
  <li class="next"><a href="#">Newer &rarr;</a></li>
</ul> 
```

![](img/103.jpg)

## 二、徽章

给链接，Bootstrap 导航等等加入< span class="badge">，可以容易地高亮新的或未读的条目。

```js
<a href="#">42<span class="badge">42</span></a> 
```

![](img/104.jpg)

**自动消失（self colapsing）**

当没有新的或未读的条目时，里面没有内容的徽章会消失（通过 CSS 的:empty 选择器实现）

```js
<a href="#">42<span class="empty">42</span></a> 
```

![](img/105.jpg)

```js
跨浏览器兼容性

徽章在 Internet Explorer 8 中不会自动消失，因为它需要对:empty 选择器的支持。 
```

**适应导航的激活状态**

在胶囊式导航和列表式导航中的徽章有内置的样式。

```js
<ul class="nav nav-pills nav-stacked">
  <li class="active">
    <a href="#">
      <span class="badge pull-right">42</span>
      Home
    </a>
  </li>
  <li><a href="#">School</a></li>
  <li><a href="#">School</a></li>
  <li><a href="#">School</a></li>
</ul> 
```

![](img/106.jpg)