# 第 5 节 Node.js 的 http 模块

## 实验简介

http 模块主要用于创建 http server 服务，此次实验还会讲到 url 模块和 path 模块，同时也会用到前面讲过的 fs 模块。url 模块用于解析 url，path 模块用于处理和转换文件路径。

通过前面的实验，相信大家对 Node.js 模块的使用已经比较熟悉。在这个实验中，我们就通过编写一个简单的 http server 来学习 http 模块。

## 一、创建 http server

通过 Node.js 创建 http server 非常简单，示例代码如下：

```js
// 文件名：demo.js

// 引入 http 模块
var http = require('http');

// 创建 http server
http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('Hello World\n');
}).listen(1337, '127.0.0.1');

console.log('Server running at http://127.0.0.1:1337/'); 
```

运行此文件：

```js
$ node demo.js 
```

然后打开虚拟机浏览器，访问“http://127.0.0.1:1337/”，就会看到页面上显示了“Hello World”，说明我们的 http server 创建成功了。

当然，我们在这个实验要做的比这个稍微复杂一点。

在这个实验中，我们会创建一个简单的 http server，所有的代码都放在 app 这个文件夹中。首先，新建一个文 app 件夹，在文件夹中新建`server.js`文件，输入如下代码（其中的注释为代码解释）：

```js
//
// 创建 http server
//

// 加载所需模块
var http = require('http');
var url = require('url');
var fs = require('fs');

// 设置 ip 和端口
// 实际应用中，可以把这些写到配置文件中
var host = '127.0.0.1',
    port = 8080;

// 创建 http server
function start(route, handle) {
    // 参数
    // route  判断 url 是否存在，存在则调用 handle 处理，不存在则返回 404
    // handle 处理不同的 url 请求

    // 处理 request 请求
    function onRequest(req, res) {
        // 使用 url.parse()方法解析 url
        // 它会把 url string 转化为一个 object
        // 这样我们就可以很方便的获取 url 中的 host、port、pathname 等值了
        var pathname = url.parse(req.url).pathname;
        console.log('Request for ' + pathname + ' received.');

        // 判断并处理不同 url 请求
        // 后面介绍此方法
        route(handle, pathname, res, req);
    }

    // 使用 http.createSserver()方法创建 http server
    // 并传入 onRequest()方法
    // 然后使用 listen()方法监听指定地址
    http.createServer(onRequest).listen(port, host);
    console.log('Server has started and listening on ' + host + ':' + port);
}

// 导出 start 方法
exports.start = start; 
```

在文件的最后，我们导出了 start 方法，以便在主程序中使用。你肯定注意到了，在代码中使用了`route()`方法，它用于处理判断请求的 url 是否存在，现在我们就来编写这个方法。

## 二、创建路由

在 app 文件夹中新建`router.js`，输入如下代码：

```js
var fs = require('fs');

// 路由函数
// 处理不同 url 的请求
// 并返回相应内容

function route(handle, pathname, res, req) {
    console.log('About to route a request for ' + pathname);

    // 判断此 url 是否存在特定处理函数
    // 存在则调用 handle 处理
    // 不存在则返回 404 页面
    if (typeof handle[pathname] === 'function') {
        // 后面介绍 handle 函数
        handlepathname;
    } else {
        console.log('No request handler found for ' + pathname);

        // 读取 404 页面
        // 所有页面都存放在 view 文件夹下
        var content = fs.readFileSync('./views/404.html');
        res.writeHead(404, { 'Content-Type': 'text/html' });
        res.write(content);
        res.end();
    }
}
// 导出 route 方法
exports.route = route; 
```

在此方法中，调用了`handle()`方法，这个方法用于处理不同的 url 请求。

在 app 文件夹中新建`requestHandlers.js`文件，输入如下代码：

```js
// 处理 url 请求

var fs = require('fs');

// home.html 主页
function home(res) {
    console.log('Request handler "home" was called.');

    // 读取 home.html 文件
    var content = fs.readFileSync('./views/home.html');
    res.writeHead(200, { 'Content-Type': 'text/html' });
    res.write(content);
    res.end();
}

// about.html 关于页面
function about(res) {
    console.log('Request handler "about" was called.');

    // 读取 about.html 文件
    var content = fs.readFileSync('./views/about.html');
    res.write(200, { 'Content-Type': 'text/html' });
    res.write(content);
    res.end();
}

// 导出页面处理函数
exports.home = home;
exports.about = about; 
```

这个方法比较简单，就是读取文件，然后输出到 response。

## 三、创建主程序

创建 http server，判断 url，处理 url 都写完了，那么我们可以写主程序来运行 http server 了，在 app 文件夹新建`main.js`文件，输入如下代码：

```js
// 主程序

// 引入 server，router 及 requestHandler
var server = require('./server');
var router = require('./router');
var requestHandlers = require('./requestHandlers');

// 保存 url 处理方法
var handle = {};
handle['/'] = requestHandlers.home;
handle['/about'] = requestHandlers.about;

// 启动 http server
server.start(router.route, handle); 
```

到此，所有的服务器代码都写完了，那么我们来添加代码中用到的两个 html 文件吧。

## 四、创建 HTML 文件

在 app 文件夹中新建 views 文件夹，在 views 文件夹中，新建`home.html`文件、`about.html`文件和`404.html`文件。

文件中的代码如下所示：

`home.html`文件：

```js
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>Home page</title>
    </head>
    <body>
        <p>home page</p>
    </body>
</html> 
```

`about.html`文件：

```js
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>About page</title>
    </head>
    <body>
        <p>about page</p>
    </body>
</html> 
```

`404.html`文件：

```js
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>404 page</title>
    </head>
    <body>
        <p>404 page not found</p>
    </body>
</html> 
```

HTML 文件的代码写得比较简单，可自由发挥。

那么现在我们来运行程序吧：

```js
$ node main.js 
```

运行成功后，打开虚拟机桌面的浏览器，访问“http://127.0.0.1:8080”就会看到页面显示“home page”，访问“http://127.0.0.1:8080/about”就会看到页面显示“about page”，访问“http://127.0.0.1:8080”下的其他页面就会看到页面显示“404 page not found”。