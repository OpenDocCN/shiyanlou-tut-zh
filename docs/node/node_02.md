# 第 1 节 Node.js 课程介绍

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou，该用户具备 sudo 的权限，可以执行安装软件等管理员操作。

### 2\. 环境介绍

本实验环境采用 Ubuntu Linux 桌面环境，实验中会用到桌面上的程序：

1.  Xfce 终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令
2.  Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可
3.  gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)
4.  gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

### 3\. 环境使用

使用编辑器输入实验所需的代码及文件，使用命令行终端运行所需命令进行操作。

“实验记录”页面可以在“我的主页”中查看，每次实验的截图及笔记，以及有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您在实验楼学习的真实性证明。

## 二、Node.js 介绍

### 1\. 概述

Node.js 是基于 Chrome JavaScript 运行时建立的一个平台，实际上它是对 Google Chrome V8 引擎进行了封装，它主要用于创建快速的、可扩展的网络应用。Node.js 采用事件驱动和非阻塞 I/O 模型，使其变得轻量和高效，非常适合构建运行在分布式设备的数据密集型的实时应用。

运行于浏览器的 JavaScript，浏览器就是 JavaScript 代码的解析器，而 Node.js 则是服务器端 JavaScript 的代码解析器，存在于服务器端的 JavaScript 代码由 Node.js 来解析和运行。

JavaScript 解析器只是 JavaScript 代码运行的一种环境，浏览器是 JavaScript 运行的一种环境，浏览器为 JavaScript 提供了操作 DOM 对象和 window 对象等的接口。Node.js 也是 JavaScript 运行的一种环境，Node.js 为 JavaScript 提供了操作文件、创建 HTTP 服务、 创建 TCP/UDP 服务等的接口，所以 Node.js 可以完成其他后台语言（Python、PHP 等）能完成的工作。

### 2\. 交互式运行环境：REPL

Node.js 提供了一个交互式运行环境，通过这个环境，可以立即执行 JavaScript 代码块，使用方法类似于 Chrome 浏览器中 Firebug 插件的 Console。

双击虚拟机桌面的 Xfce 终端，进入 Linux 终端：

输入`node`或`nodejs`进入 Node.js 的交互式运行环境（老版本的 Node.js，不支持`node`命令，只能使用`nodejs`命令），Ctrl+d 组合键可以退出此环境。

查看系统中安装的 Node.js 版本：

```js
$ node -v 
```

```js
$ node
> console.log('shiyanlou'); 
```

通过交互式环境也可以运行 JavaScript 文件。

在虚拟机桌面新建一个 JavaScript 文件，命名为 test.js，打开 gvim，输入如下代码并保存：

```js
var num = 100;

console.log(num);
console.log(num + 100); 
```

通过 Node.js 交互式环境运行文件：

```js
$ cd Desktop
Desktop$ node test.js 
```

## 三、Node.js 模块和包

### 1\. 模块

Node.js 官方提供了很多模块，这些模块分别实现了一种功能，如操作文件的模块`fs`，构建 http 服务的模块`http`等，每个模块都是一个 JavaScript 文件，当然也可以自己编写模块。

### 2\. 包

包可以将多个具有依赖关系的模块组织在一起，封装多个模块，以方便管理。Node.js 采用了 CommonJS 规范，根据 CommonJS 规范规定，一个 JavaScript 文件就是一个模块，而包是一个文件夹，包内必须包含一个 JSON 文件，命名为 package.json。一般情况下，包内的 bin 文件夹存放二进制文件，包内的 lib 文件夹存放 JavaScript 文件，包内的 doc 文件夹存放文档，包内的 test 文件夹存放单元测试。package.json 文件中需要包含的字段及包的使用，后面的实验再做详细介绍。

### 3\. npm 包管理工具

npm 是 Node.js 的包管理工具，npm 定义了包依赖关系标准，我们使用 npm 主要用来下载第三方包和管理本地下载的第三方包。