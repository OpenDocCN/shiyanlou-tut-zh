# 第 6 节 Node.js 中的网络编程

## 一、实验说明

下述介绍为实验楼默认环境，如果您使用的是定制环境，请修改成您自己的环境介绍。

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou

### 2\. 环境介绍

本实验环境采用带桌面的 Ubuntu Linux 环境，实验中会用到桌面上的程序：

1.  LX 终端（LXTerminal）: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令
2.  Firefox：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可
3.  GVim：非常好用的编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)

### 3\. 环境使用

使用 GVim 编辑器输入实验所需的代码及文件，使用 LX 终端（LXTerminal）运行所需命令进行操作。

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

* * *

## 实验简介

此实验主要讲解 TCP 和 UDP 的网络编程，net 模块提供了一个异步网络包装器，用于 TCP 网络编程，它包含了创建服务器和客户端的方法。dgram 模块用于 UDP 网络编程。

参考链接：https://nodejs.org/api/net.html, https://nodejs.org/api/dgram.html

## TCP Server

net 模块通过`net.createServer`方法创建 TCP 服务器，通过`net.connect`方法创建客户端去连接服务器。

通过 net 模块创建一个 TCP Server：

```js
// server.js

var net = require('net');

// 创建 TCP 服务器
var server = net.createServer(function(socket) {
    console.log('client connected');

    // 监听客户端的数据
    socket.on('data', function(data) {
        console.log('server got data from client: ', data.toString());
    });
    // 监听客户端断开连接事件
    socket.on('end', function(data) {
        console.log('connection closed');
    });
    // 发送数据给客户端
    socket.write('Hello\r\n');
});

// 启动服务
server.listen(8080, function() {
    console.log('server bound');
}); 
```

然后再创建一个客户端：

```js
// client.js

var net = require('net');

// 连接服务器
var client = net.connect({port: 8080}, function() {
    console.log('connected to server');
    client.write('World!\r\n');
});

// 接收服务端的数据
client.on('data', function(data) {
    console.log('client got data from server: ', data.toString());
    // 断开连接
    client.end();
});

// 断开连接
client.on('end', function() {
    console.log('disconnected from server');
}); 
```

运行测试：

在虚拟机中打开两个终端，先运行 TCP 服务器代码：

```js
$ node server.js 
```

然后在另一个终端运行 TCP 客户端代码：

```js
$ node client.js 
```

即可看到两个终端运行结果。

## 简易聊天室服务端

了解了基础的东西，下面我们来做一个简单的聊天室吧。

首先创建 TCP 服务器，和前面一样：

```js
// chatServer.js

var net = require('net');

// 创建 TCP 服务器
var server = net.createServer();

server.on('error', function(err) {
    console.log('Server error: ', err.message);
});

server.on('close', function() {
    console.log('Server closed');
});

server.listen(8080); 
```

接收客户端连接请求：

```js
// chatServer.js

var net = require('net');

// 创建 TCP 服务器
var server = net.createServer();

// 新增的代码
server.on('connection', function(socket) {
    console.log('Got a new connection');
});

server.on('error', function(err) {
    console.log('Server error: ', err.message);
});

server.on('close', function() {
    console.log('Server closed');
});

server.listen(8080); 
```

获取客户端发送过来的数据：

```js
// chatServer.js

var net = require('net');

// 创建 TCP 服务器
var server = net.createServer();

server.on('connection', function(socket) {
    console.log('Got a new connection');
    // 新增的代码
    socket.on('data', function(data) {
        console.log('Got data: ', data);
    ]);
});

server.on('error', function(err) {
    console.log('Server error: ', err.message);
});

server.on('close', function() {
    console.log('Server closed');
});

server.listen(8080); 
```

既然是聊天室，当然允许多个客户端用户同时连接，所以需要接收所有的用户连接：

```js
// chatServer.js

var net = require('net');

// 创建 TCP 服务器
var server = net.createServer();
// 新增的代码
var sockets = []; // 存储所有客户端 socket

server.on('connection', function(socket) {
    console.log('Got a new connection');

    // 新增的代码
    sockets.push(socket);

    socket.on('data', function(data) {
        console.log('Got data: ', data);
    ]);
});

server.on('error', function(err) {
    console.log('Server error: ', err.message);
});

server.on('close', function() {
    console.log('Server closed');
});

server.listen(8080); 
```

服务器广播数据，把来自客户端的数据转发送给其他所有客户端：

```js
// chatServer.js

var net = require('net');

// 创建 TCP 服务器
var server = net.createServer();
var sockets = []; // 存储所有客户端 socket

server.on('connection', function(socket) {
    console.log('Got a new connection');

    sockets.push(socket);

    socket.on('data', function(data) {
        console.log('Got data: ', data);

        // 新增代码
        sockets.forEach(function(otherSocket) {
            if (otherSoecket !== socket) {
                otherSocket.write(data);
            }
        });
    ]);
});

server.on('error', function(err) {
    console.log('Server error: ', err.message);
});

server.on('close', function() {
    console.log('Server closed');
});

server.listen(8080); 
```

最后，需要把关闭连接的客户端从服务器广播列表中给删除掉：

```js
// chatServer.js

var net = require('net');

// 创建 TCP 服务器
var server = net.createServer();
// 存储所有客户端 socket
var sockets = [];

server.on('connection', function(socket) {
    console.log('Got a new connection');

    sockets.push(socket);

    socket.on('data', function(data) {
        console.log('Got data: ', data);

        sockets.forEach(function(otherSocket) {
            if (otherSoecket !== socket) {
                otherSocket.write(data);
            }
        });
    ]);

    // 新增代码
    socket.on('close', function() {
        console.log('A client connection closed');
        var index = sockets.indexOf(socket);
        sockets.splice(index, 1);
    });
});

server.on('error', function(err) {
    console.log('Server error: ', err.message);
});

server.on('close', function() {
    console.log('Server closed');
});

server.listen(8080); 
```

## 简易聊天室客户端

聊天室的服务端功能基本就实现了，下面我们来写一个简单的客户端吧：

```js
// chatClient.js

var net = require('net');

process.stdin.resume();
process.stdin.setEncoding('utf8');

var client = net.connect({ port: 8080 }, function() {
    console.log('Connected to server');

    // 获取输入的字符串
    console.log('input: ');
    process.stdin.on('data', function(data) {
        // 发送输入的字符串到服务器
        console.log('input: ');
        client.write(data);

        // 输入 'close' 字符串时关闭连接
        if (data === 'close\n') {
            client.end();
        }
    });
});

// 获取服务端发送过来的数据
client.on('data', function(data) {
    console.log('Other user\'s input', data.toString());
});

client.on('end', function() {
    console.log('Disconnected from server');
    // 退出客户端程序
    process.exit();
}); 
```

客户端中用到了 process 这个模块，process 是一个 Node.js 的全局模块，可以在任何地方直接使用而无需通过 require 方法引入。process 模块允许你获得或者修改当前 Node.js 进程的设置。`process.stdin`用于获取来自标准输入的可读流（Readable Stream）。

现在来运行这个聊天室吧：

```js
$ node chatServer.js 
```

在打开多个终端窗口，运行：

```js
$ node chatClient.js 
```

然后在任意运行的客户端输入字符串并按回车键，会在服务端和其他客户端开到这个客户端的输入内容。这样就实现了一个简单的聊天室了^_^。

## UDP Server

UDP 和 TCP 类似，通过`dgram.createSocket`创建服务。

UDP 服务器：

```js
// udpServer.js

var dgram = require("dgram");

var server = dgram.createSocket("udp4");

server.on("error", function(err) {
    console.log("server error:\n" + err.stack);
    server.close();
});

// 接收来自客户端的消息
server.on("message", function(msg, rinfo) {
    console.log("server got: " + msg.toString() + " from " + rinfo.address + ":" + rinfo.port);
});

// 监听服务
server.on("listening", function() {
    var address = server.address();
    console.log("server listening on " + address.address + ":" + address.port);
});

server.bind(41234);
// server listening 0.0.0.0:41234 
```

UDP 客户端：

```js
// udpClient.js

var dgram = require('dgram');

var client = dgram.createSocket('udp4');
var message = new Buffer('hello shiyanlou');

client.send(message, 0, message.length, 41234, 'localhost', function(err, bytes) {
    client.close();
}); 
```

发送的消息必须通过 Buffer 创建。

然后运行服务端：

```js
$ node server.js 
```

再运行客户端：

```js
$ node client.js 
```

此时，服务端就会收到来自客户端的消息。

Node.js 基础的网络编程就讲到这里了。