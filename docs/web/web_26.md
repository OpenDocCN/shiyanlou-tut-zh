# 第 1 节 认识 jQuery

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou，该用户具备 sudo 的权限，可以执行安装软件等管理员操作。

### 2\. 环境介绍

本实验环境采用 Ubuntu Linux 桌面环境，实验中会用到桌面上的程序：

1.  Xfce 终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令
2.  Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可
3.  gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)
4.  其他编辑器：如果 Vim 不熟悉可以使用 gedit 或 brackets，其中 brackets 比较适合开发前端代码。

### 3\. 环境使用

使用 GVim 编辑器输入实验所需的代码及文件，使用 Xfce 终端（XfceTerminal）运行所需命令进行操作。

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

本课程中的所有源码可以通过以下方式下载:

```
$ git clone http://git.shiyanlou.com/shiyanlou/jquery-learning 
```

### 4\. 课程来源

作者：张子秋 出处：http://www.cnblogs.com/zhangziqiu/ 本实验课程基于张子秋的博客《从零开始学习 jQuery》系列，可查看原版链接：http://www.cnblogs.com/zhangziqiu/archive/2009/04/30/jQuery-Learn-1.html

## 二、引言

### 1\. 课程概述

1）jQuery 是一套`Javascript`脚本库，注意 jQuery 是脚本库,而不是脚本框架。"库"不等于"框架"。jQuery 并不能帮助我们解决脚本的引用管理和功能管理,这些都是脚本框架要做的事。

脚本库能够帮助我们完成编码逻辑,实现业务功能。使用 jQuery 将极大的提高编写`javascript`代码的效率, 让写出来的代码更加优雅, 更加健壮。

2）使用这些功能函数,能够帮助我们快速完成各种功能,而且会让我们的代码异常简洁。

3）`javascript`脚本在不同浏览器的兼容性一直是 Web 开发人员的噩梦，比如在 jQuery 中的 Event 事件对象已经被格式化成所有浏览器通用的,从前要根据`event`获取事件触发者, 在 ie 下是`event.srcElements` 而 ff 等标准浏览器下是`event.target`。 jQuery 则通过统一`event`对象,让我们可以在所有浏览器中使用`event.target`获取事件对象。

4）jQuery 可以实现比如渐变弹出,图层移动等动画效果,让我们获得更好的用户体验。

5）大部分开发人员对于`javascript`存在错误的认识，比如在页面中编写加载时即执行的操作 DOM 的语句,在 HTML 元素或者`document`对象上直接添加`onclick`属性,不知道`onclick`其实是一个匿名函数等等。这样的程序是不健壮的。比如"在页面中编写加载时即执行的操作 DOM 的语句",当页面代码很小用户加载很快时没有问题, 当页面加载稍慢时就会出现浏览器"终止操作"的错误。

## 三、Hello World jQuery

按照惯例, 我们来编写 jQuery 的 Hello World 程序, 来迈出使用 jQuery 的第一步。

### 1\. 下载 jQuery 类库

提供三个路径下载：

**1.jQuery 的项目下载阿里云的 OSS 上, 下载地址:**

http://labfile.oss.aliyuncs.com/jquery/1.11.1/jquery.min.js

上面的地址是总下载列表, 里面有很多版本和类型的 jQuery 库, 主要分为如下几类:

`min`: 压缩后的 jQuery 类库, 在正式环境上使用.如:`jquery-1.3.2.min.js`

`vsdoc`: 在 Visual Studio 中需要引入此版本的 jquery 类库才能启用智能感知.如：`jquery-1.3.2-vsdoc2.js`

`release`包: 里面有没有压缩的 jquery 代码, 以及文档和示例程序. 如:`jquery-1.3.2-release.zip`

**2.也可在 jQuery 的官网中下载最新版本，下载地址：http://jquery.com/download/**

下载后是一个 js 文件，我们只需要在 html 文件中引入这个 js 文件，便可。

**3.在包含源码的 github 地址中也有 jQuery 的最新版本提供下载，地址为:**

https://github.com/shiyanlou/jQuery-base-code

**注：github 地址中包含的 jquery-1.11.2.js 版本是本次课程中用到的文件，jquery-1.11.2.min.js 是发布课程之时 jQuery 的最新版本，且是压缩版。**

### 2\. 编写程序

创建一个 html 页面，引入 jQuery 类库并且编写如下代码：

```
<!doctype html>
<html lang="zh">
  <head>
    <meta charset="utf-8"/>
    <title>Hello World jQuery!</title>
    <script src="jquery-1.11.2.min.js"></script>
  </head>
  <body>
    <div id="divMsg">Hello World!</div>
    <input id="btnShow" type="button" value="show" />
    <input id="btnHide" type="button" value="hidden" /><br/>
    <input id="btnChange" type="button" value="change content is Hello World, too!"/>
    <script>
      $("#btnShow").bind("click", function(event) {
        $("#divMsg").show();
      });

      $("#btnHide").bind("click", function(event) {
        $("#divMsg").hide();
      });

      $("#btnChange").bind("click", function(event) {
        $("#divMsg").html("Hello World, too!");
      });
    </script>
  </body>
</html> 
```

效果如下：

![Alt text](img/md0417238jquery1.jpg)

页面上有三个按钮，分别控制 Hello World 的显示、隐藏和修改其内容。

此示例使用了：

（1）jQuery 的 ID 选择器：`$("#btnShow")` （2）事件绑定函数：`bind()` （3）显示和隐藏函数：`show()`和`hide()` （4）修改元素内部 html 的函数：`html()`

## 四、总结

本文简单介绍了 jQuery，以及如何搭建脚本开发环境。示例程序没有复杂的功能，可能还无法让没有接触过 jQuery 的人认识到它的强大。但是仅凭借“多浏览器支持”这一特性，就足以让我们学习并使用 jQuery，因为如今想编写跨浏览器的脚本是一件困难的事情！

在后续的教程中我们将深入学习 jQuery 选择器、事件、工具函数、动画以及插件等。

## 五、练习

请实现一个验证码输入框，当输入框获取焦点时，验证码才会显示。