# 第 1 节 认识 HTML5

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

使用 GVim 编辑器输入实验所需的代码，然后使用 Firefox 浏览器打开代码文件，可以在虚拟环境中 Firefox 浏览器中按 F12 键打开 firebug 进行前端页面的调试。

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

### 4\. 版权声明

本课程基于 FlyDragon 的 HTML5 文档制作，感谢原作者授权，附原作者版权声明：

作者：FlyDragon

出处：http://www.cnblogs.com/fly_dragon/

关于作者：专注于微软平台项目架构、管理和企业解决方案。如有问题或建议，请多多赐教！

本文版权归作者和博客园共有，欢迎转载，但未经作者同意必须保留此段声明，且在文章页面明显位置给出原文连接，如有问题，可以通 malun666@126.com 联系我，非常感谢。

## ## 二、认识 HTML5

### 1\. 什么是 HTML5

HTML 5 草案的前身名为 Web Applications 1.0，是在 2004 年由 WHATWG 提出，再于 2007 年获 W3C 接纳，并成立了新的 HTML 工作团队。在 2008 年 1 月 22 日，第一份正式草案发布。WHATWG 表示该规范是目前仍在进行的工作，仍须多年的努力。目前 Firefox、Google Chrome、Opera、Safari（版本 4 以上）、Internet Explorer 9 已支援 HTML5 技术。

HTML5 本质并没有对之前 HTML4 版本的规范进行彻底的变革，而是一开始设计就考虑了跟之前的标准进行兼容，并且把最新的 WEB 开发的一些新技术新的规范引入进了新版本的标准中。

### 2\. HTML5 新特性

#### 2.1 新增拥有具体含义的标签

现在所有的站点基本上都是`div`+`css`布局，几乎所有的文章标题、内容、辅助介绍等都用`div`容器来承载。搜索引擎在抓取页面内容时，因为没有明确的容器的含义只能去猜测这些标签容器承载的是文章标题还是文章内容等。HTML5 新标准中直接添加了拥有具体含义的 HTML 标签比如：`<article>`、`<footer>`、`<header>`、`<nav>`、`<section>`

#### 2.2 新增更加智能的表单类型

之前的表单标签仅仅是简单的类型的约束，比如文本框、文本域、下拉列表等。而跟业务结合紧密的表单标签数据校验等控制都没有很好的支持，基本上都是跟第三方的 JS 控件进行结合使用，但是这些第三方总会涉及到版本控制、浏览器兼容性、非标准等一系列的问题。而在 HTML5 的标准中直接添加了智能表单，让这一切都变得那么的简单，比如 `calendar`、`date`、`time`、`email`、`url`、`search`

#### 2.3 让 Web 程序更加的独立，减少了对第三方插件的依赖。

在 HTML5 标准中原生的就支持音频、视频、画布等技术。让 WEB 程序更加独立，更好的适应多种形式的客户端。

#### 2.4 对本地离线存储的更好的支持

HTML5 提供了两种在客户端存储数据的新方法：

*   `localStorage` - 没有时间限制的数据存储
*   `sessionStorage` - 针对一个 session 的数据存储

#### 2.5 HTML5 即时二维绘图 ,既画布的引入

HTML5 的`canvas`元素使用 JavaScript 在网页上绘制图像。并拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法。

#### 2.6 JS 支持多线程

在不影响 UI update 及浏览器与用户交互的情况下, 前端做大规模运算，只能通过 `setTimeout` 之类的去模拟多线程 。而新的标准中，JS 新增的 HTML5 Web Worker 对象原生的就支持多线程。

#### 2.7 WebSockets 让跨域请求、长连接、数据推送变得简单

`WebSockets`是在一个(TCP)接口进行双向通信的技术，PUSH 技术类型。`WebSocket`是 html5 规范新引入的功能，用于解决浏览器与后台服务器双向通讯的问题，使用`WebSocket`技术，后台可以随时向前端推送消息，以保证前后台状态统一，在传统的无状态 HTTP 协议中，这是“无法做到”的。

#### 2.8 更好的异常处理

HTML5(text/html)浏览器将在错误语法的处理上更加灵活。HTML5 在设计时保证旧的浏览器能够安全地忽略掉新的 HTML5 代码。与 HTML4.01 相比，HTML5 给出了解析的完整规则，让不同的浏览器即使在发生语法错误时也能返回完全相同的结果。

#### 2.9 文件 API 让文件上传和操纵文件变得那么简单

由于项目中经常遇到用 Web 应用中控制操作本地文件，而之前都是使用一些富客户端技术比如`flash`，`ActiveX`，`Silverlight`等技术。在 HTML5 的新的提供的 `HTML5 File API` 让 JS 可以轻松上阵了。

** 在后续的实验中将带领大家更深入地了解 HTML5 **