# 第 3 节 Node.js Events 模块

## 实验简介

在 Node.js 中，很多对象都会发出事件。比如，`fs.readStream`打开文件时会发出一个事件。所有发出事件的对象都是`events.EventEmitter`的实例，可以通过`require("events");`获得`event`模块。

通常，事件名采用“驼峰式”命名方式，但是，并没有严格规定。这只是推荐的命名方法。

函数可以添加给对象，对象发出事件时，对应函数就会被执行。这些函数被称作监听器（listeners）。在监听器函数中，`this`引用的是它（指此监听器函数）添加到的`EventEmitter`对象。

## Class: events.EventEmitter

通过`require('events').EventEmitter`得到 EventEmitter 类。

当 EventEmitter 对象遇到错误时，通常会触发`error`事件。`error`事件在 Node.js 中是一种特殊情况，如果没有监听器，那么默认会打印出栈跟踪器并退出程序。

## 添加监听器

为事件绑定事件处理程序，可以使用`emitter.addListener(event, listener)`和`emitter.on(event, listener)`，它们的作用是完全一样的。传入的参数是事件（event）和处理函数（listener）。

在虚拟机桌面新建文件`test1.js`，输入如下代码并保存：

```js
var http = require('http');
var server = http.createServer();

// 为 request 事件绑定处理函数
// 也可以使用 server.addListener
server.on('request', function(req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.write('shiyanlou');
    console.log('shiyanlou');
    res.end();
});

server.listen(1337, '127.0.0.1');
console.log('Server running at http://127.0.0.1:1337/'); 
```

运行代码：

```js
shiyanlou@sdf234jh4:~$ cd Desktop
shiyanlou@sdf234jh4:~/Desktop$ node test1.js 
```

然后打开虚拟机桌面的 Firefox 浏览器，在地址栏输入 127.0.0.1:1337，即可看到页面上打印出了“shiyanlou”字样，同时 console 界面也会输出'shiyanlou'字样。

## 只执行一次的监听器

使用`emitter.once(event, listener)`绑定的事件监听器只会执行一次，然后就会被删除掉。

在虚拟机桌面新建文件`test2.js`，输入如下代码并保存：

```js
var http = require('http');
var server = http.createServer();

// 为 request 事件绑定处理函数，事件只会执行一次
server.once('request', function(req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.write('shiyanlou');
    console.log('shiyanlou');
    res.end();
});

server.listen(1337, '127.0.0.1');
console.log('Server running at http://127.0.0.1:1337/'); 
```

运行代码：

```js
shiyanlou@sdf234jh4:~/Desktop$ node test2.js 
```

打开虚拟机桌面的 Firefox 浏览器，在地址栏输入 127.0.0.1:1337，即可看到页面上打印出了“shiyanlou”字样，再次刷新此页面，就不会再显示，因为此事件只会执行一次。

## 移除监听器

移除监听器使用`emitter.removeListener(event, listener)`。

在虚拟机桌面新建文件`test3.js`，输入如下代码并保存：

```js
var http = require('http');
var server = http.createServer();

function callback(req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.write('Hello World');
    console.log('Hello World');
    res.end();
}

server.on('request', callback);

// 移除绑定的监听器 callback
server.removeListener('request', callback);

server.on('request', function(req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.write('shiyanlou');
    console.log('shiyanlou');
    res.end();
});

server.listen(1337, '127.0.0.1');
console.log('Server running at http://127.0.0.1:1337/'); 
```

运行代码：

```js
shiyanlou@sdf234jh4:~/Desktop$ node test3.js 
```

打开虚拟机桌面的 Firefox 浏览器，在地址栏输入 127.0.0.1:1337，即可看到页面上打印出了“shiyanlou”字样，为什么没有显示“Hello World”呢？因为显示“Hello World”的监听器被移除了。

## 移除所有监听器

移除所有监听器使用`emitter.removeAllListeners([event])`。

在虚拟机桌面新建文件`test4.js`，输入如下代码并保存：

```js
var http = require('http');
var server = http.createServer();

server.on('request', function(req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.write('shiyanlou,111');
    console.log('shiyanlou,111');
    res.end();
});

server.on('request', function(req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.write('shiyanlou,222');
    console.log('shiyanlou,222');
    res.end();
});

// 移除绑定的所有监听器
server.removeAllListeners('request');

server.on('request', function(req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.write('shiyanlou');
    console.log('shiyanlou');
    res.end();
});

server.listen(1337, '127.0.0.1');
console.log('Server running at http://127.0.0.1:1337/'); 
```

运行代码：

```js
shiyanlou@sdf234jh4:~/Desktop$ node test4.js 
```

打开虚拟机桌面的 Firefox 浏览器，在地址栏输入 127.0.0.1:1337，即可看到页面上打印出了“shiyanlou”字样，说明前面的监听器被移除了，都没有执行，所以没有显示，同时 console 界面也只会输出'shiyanlou'字样。

## 设置监听器最大绑定数

`emitter.setMaxListeners(n)`可以设置同一事件的监听器最大绑定数，默认情况下，超过 10 个就会警告提示，这能帮我们快速找到类存泄露的地方。显然，不是所有的事件触发器都限制在 10 个监听器，通过这个方法可以设置，如果设置为 0 就是无限制。

## 自定义事件

使用`emitter.emit(event, [arg1], [arg2], [...])`可以触发自定义的事件。

在虚拟机桌面新建文件`test5.js`，输入如下代码并保存：

```js
var http = require('http');
var server = http.createServer();

// 绑定自定义事件 myevent
server.on('myevent', function(arg) {
    console.log(arg);
});

// 触发自定义事件
server.emit('myevent', 'shiyanlou');

server.listen(1337, '127.0.0.1');
console.log('Server running at http://127.0.0.1:1337/'); 
```

运行代码：

```js
shiyanlou@sdf234jh4:~/Desktop$ node test5.js 
```

可以看到 console 界面输出了'shiyanlou'字样，说明触发自定义事件成功。

## 查看事件绑定的监听器个数

使用`EventEmitter.listenerCount(emitter, event)`可以查看事件监听器数量。

在虚拟机桌面新建文件`test6.js`，输入如下代码并保存：

```js
var http = require('http');
var events = require('events'); // 加载 events 模块
var server = http.createServer();

server.on('request', function(req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.write('shiyanlou,111');
    console.log('shiyanlou,111');
    res.end();
});

server.on('request', function(req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.write('shiyanlou,222');
    console.log('shiyanlou,222');
    res.end();
});

server.listen(1337, '127.0.0.1');
console.log('Server running at http://127.0.0.1:1337/');

// 查看 server 绑定的'request'事件的监听器个数
var num = events.EventEmitter.listenerCount(server, 'request');
console.log(num); 
```

运行代码：

```js
shiyanlou@sdf234jh4:~/Desktop$ node test6.js 
```

可以看到 console 界面输出了数字“2”，因为 server 绑定了两个监听器到'request'事件。