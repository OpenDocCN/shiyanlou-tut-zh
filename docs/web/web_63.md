# 第 2 节 CSS 基础选择器

## 一、派生选择器

**派生选择器** **通过依据元素在其位置的上下文关系来定义样式，可以使标记更加简洁。**

派生选择器允许你根据文档的上下文关系来确定某个标签的样式。通过合理地使用派生选择器，我们可以使 HTML 代码变得更加整洁。 比方说，你希望列表中的 strong 元素变为红色，而不是通常的黑色，可以这样定义一个派生选择器：

```
li strong{
    color: red;
} 
```

请注意在 HTML 中标记为<li><strong> 的代码的上下文关系

```
<p><strong>我是黑色，因为我不在列表当中，所以这个规则对我不起作用</strong></p>
        <u1>
            <li><strong>我是红色。这是因为 strong 元素位于 li 元素内。</li>
        </u1> 
```

**注意：**实验楼环境中没有中文输入法，代码中涉及的中文主要是为了方便大家理解，大家可以英文做相应的替代。

### 完整代码如下：

index.html

```
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <link rel="stylesheet" href="mycss.css" type="text/css">
    </head>
    <body>
        <p><strong>我是黑色，因为我不在列表当中，所以这个规则对我不起作用</strong></p>
        <u1>
            <li><strong>我是红色，这是因为 strong 元素位于 li 元素内。</strong></li>
        </u1>
    </body>
</html> 
```

mycss.css

```
li strong{
    color: red;
} 
```

运行结果：

![图片描述信息](img/userid20407labid249time1423473784761.jpg)

**在 css 中定义的 li strong 的样式，只会影响上面 html 文件中的`<li><strong>`,而不会影响`<p><strong>`中的内容**

## 二、id 选择器

**1.id 选择器：** id 选择器可以为标有 id 的 HTML 元素指定特定的样式 id 选择器以“#”来定义

**2.id 选择器和派生选择器：** 目前比较常用的方式是 id 选择器常常用于建立派生选择器

上面两点单从字面意思很难理解，我们通过案例进行讲述。

### 程序举例

index.html 代码 body 中的 p 标签和 div 标签包含了两个 id 属性，值分别为 pid 和 divid，在 css 文件中会以#+属性值引用。注意：id 属性值只能在每个 HTML 文档中出现一次。

```
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <link  href="MyCss.css" type="text/css" rel="stylesheet">
    </head>
    <body>
        <p id="pid">hello css<a href="www.shiyanlou.com">shiyanlou</a></p>
        <div id="divid">this is a div</div>

    </body>
</html> 
```

MyCss.css `#divid{}`就是一个独立的 id 选择器，而`#pid a{}`就是我们前文提到的 id 选择器用于建立派生选择器,相当于是一个嵌套。

```
#pid a{
    color:#00755f;
}
#divid {
    color: red; 
```

运行结果： ![图片描述信息](img/userid20407labid249time1423478143796.jpg)

**说明**：brackets 可以采用下面的方式调节颜色(在颜色值处右键)

![](img/2-3.jpg)

## 三、类选择器

### (1)在 CSS 中，类选择器以一个点号显示：

```
.divclass {
    color: red;
} 
```

在下面的 html 代码中，div 元素含有 divclass 类，意味着它要遵守`.divclass`的规则。

```
<div class="divclass">
hello div
</div> 
```

**注意：**类名的第一个字符不能使用数字！它无法在 Mozilla 或 Firefox 中起作用。

### (2)和 id 一样，class 也可被用作派生选择器：

```
.pclass a{
    color: green; 
```

上述内容的全部代码如下

### 程序举例

index.html

```
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <link  href="MyCss.css" type="text/css" rel="stylesheet">
    </head>
    <body>
    <!--p 标签中嵌套了一个 a 标签，在下面的 css 引用过程中我们可以看到的.pclass a 即为 class 被用作派生选择器-->
        <p class="pclass">这是一个 class 显示效果<a href="hhtp://www.shiyanlou.com">效果</a></p>
        <div class="divclass">hello div</div>
    </body>
</html> 
```

MyCss.css

```
.pclass a{
    color: green;
}
.divclass {
    color: red;
} 
```

运行结果： ![图片描述信息](img/userid20407labid249time1423539361459.jpg)

## 四、属性选择器

**对带有指定属性的 HTML 元素设置样式。**

### (1)下面的例子为带有 title 属性的所有元素设置样式：

```
[title]
{
color:red;
} 
```

### (2)属性和值选择器

下面的例子为 title="te" 的所有元素设置样式：

```
[title=te]{
                color: red;
            } 
```

### 程序代码举例

index.html

```
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <style type="text/css">
            [title]{
                color: #00ff14;
            }
            [title=te]{
                color: red;
            }

        </style>    
    </head>
    <body>
       <p title=>属性选择器</p>
        <p title="te">属性和值选择器</p>
    </body>
</html> 
```

运行结果：

![](img/2-6.jpg)