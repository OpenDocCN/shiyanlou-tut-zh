# 第 18 节 《何为 connect 中间件》

## 目标

1.  理解中间件的概念
2.  了解 Connect 的实现

## 课程内容

1.  原生 httpServer 遇到的问题
2.  中间件思想
3.  Connect 实现
4.  Express 简介

这是从 httpServer 到 Express 的升级过程。

## HTTP

Nodejs 的经典 httpServer 代码

```js
var http = require('http');

var server = http.createServer(requestHandler);
function requestHandler(req, res) {
  res.end('hello visitor!');
}
server.listen(3000); 
```

里面的函数 `requestHandler` 就是所有 http 请求的响应函数，即所有的请求都经过这个函数的处理，是所有请求的入口函数。

通过 requestHandler 函数我们能写一些简单的 http 逻辑，比如上面的例子，所有请求都返回 `hello visitor!`。

然而，我们的业务逻辑不可能这么简单。例如：需要实现一个接口，要做的是当请求过来时，先判断来源的请求是否包含请求体，然后判断请求体中的 id 是不是在数据库中存在，最后若存在则返回 true，不存在则返回 false。

```js
1\. 检测请求中请求体是否存在，若存在则解析请求体；
1\. 查看请求体中的 id 是否存在，若存在则去数据库查询；
1\. 根据数据库结果返回约定的值； 
```

我们首先想到的，抽离函数，每个逻辑一个函数，简单好实现低耦合好维护。

实现代码:

```js
function parseBody(req, callback) {
  //根据 http 协议从 req 中解析 body
  callback(null, body);
}
function checkIdInDatabase(body, callback) {
  //根据 body.id 在 Database 中检测，返回结果
  callback(null, dbResult);
}
function returnResult(dbResult, res) {
  if (dbResult && dbResult.length > 0) {
    res.end('true');
  } else {
    res.end('false')
  }
}
function requestHandler(req, res) {
  parseBody(req, function(err, body) {
    checkIdInDatabase(body, function(err, dbResult) {
      returnResult(dbResult, res);
    });
  });
} 
```

上面的解决方案解决了包含三个步骤的业务问题，出现了 3 个 `});` 还有 3 个 `err` 需要处理，上面的写法可以得达到预期效果。

然而，业务逻辑越来越复杂，会出发展成 30 个回调逻辑，那么就出现了 30 个 `});` 及 30 个 `err`异常。更严重的是，到时候写代码根本看不清自己写的逻辑在 30 层中的哪一层，极其容易出现 **多次返回** 或返回地方不对等问题，这就是 **回调金字塔** 问题了。

大多数同学应该能想到解决回调金字塔的办法，朴灵的《深入浅出 Node.js》里讲到的三种方法。下面列举了这三种方法加上 ES6 新增的 Generator，共四种解决办法。

*   [EventProxy](https://github.com/JacksonTian/eventproxy) —— 事件发布订阅模式(第四课讲到)
*   [BlueBird](https://github.com/petkaantonov/bluebird) —— Promise 方案(第十七课讲到)
*   [Async](https://github.com/caolan/async) —— 异步流程控制库(第五课讲到)
*   [Generator](http://es6.ruanyifeng.com/#docs/generator) —— ES6 原生 Generator

理论上，这四种都能解决回调金字塔问题。而 Connect 和 Express 用的是 `类似异步流程控制的思想` 。

<a name="next"></a> 关于异步流程控制库下面简要介绍下，或移步[@第五课](https://www.shiyanlou.com/courses/493)。 异步流程控制库首先要求用户传入待执行的函数列表，记为 funlist。流程控制库的任务是让这些函数 **顺序执行** 。

callback 是控制顺序执行的关键，funlist 里的函数每当调用 callback 会执行下一个 funlist 里的函数

我们动手实现一个类似的链式调用，其中 `funlist` 更名为 `middlewares`、`callback` 更名为 `next`，码如下：

<a name="middlewares" comment="middlewares 锚点"></a>

```js
var middlewares = [
  function fun1(req, res, next) {
    parseBody(req, function(err, body) {
      if (err) return next(err);
      req.body = body;
      next();
    });
  },
  function fun2(req, res, next) {
    checkIdInDatabase(req.body.id, function(err, rows) {
      if (err) return next(err);
      res.dbResult = rows;
      next();
    });
  },
  function fun3(req, res, next) {
    if (res.dbResult && res.dbResult.length > 0) {
      res.end('true');
    }
    else {
      res.end('false');
    }
    next();
  }
]

function requestHandler(req, res) {
  var i=0;

  //由 middlewares 链式调用
  function next(err) {

    if (err) {
      return res.end('error:', err.toString());
    }

    if (i<middlewares.length) {
      middlewaresi++;
    } else {
      return ;
    }
  }

  //触发第一个 middleware
  next();
} 
```

上面用 middlewares+next 完成了业务逻辑的 `链式调用`，而 middlewares 里的每个函数，都是一个 `中间件`。

整体思路是：

1.  将所有 `处理逻辑函数(中间件)` 存储在一个 list 中；
2.  请求到达时 `循环调用` list 中的 `处理逻辑函数(中间件)`；

## [Connect](https://github.com/senchalabs/connect)的实现

Connect 的思想跟上面阐述的思想基本一样，先将处理逻辑存起来，然后循环调用。

Connect 中主要有五个函数 PS: Connect 的核心代码是 200+行，建议对照<a href="https://github.com/senchalabs/connect/blob/master/index.js" target="_blank">源码</a>看下面的函数介绍。

```js
|函数名         |作用                          |
| -------------|:----------------------------:|
|createServer  |包装 httpServer 形成 app         |
|listen        |监听端口函数                   |
|use           |向 middlewares 里面放入业务逻辑   |
|handle        |上一章的 requestHandler 函数增强版|
|call          |业务逻辑的真正执行者            |
```

## createServer()

**输入**:

无

**执行过程**:

1.  app 是一个函数对象(包含 handle 方法)
2.  app 具有 Event 所有属性(详见[utils-merge](https://github.com/jaredhanson/utils-merge)，十行代码)
3.  app 有 route 属性(路由)、和 stack 属性(用于存储中间件，类似上面的 middlewares)

**输出**:

```js
 app is function(req, res, next) {...};
        |
    +---+---+
    |  has  |
  route   stack 
```

## app.use(route, fn)

作用是向 stack 中添加 `逻辑处理函数` (中间件)。

**输入**:

1.  route 可省略，默认'/'
2.  fn 具体的业务处理逻辑

**tips:**

上面的 fn 表示处理逻辑，它可以是

1.  一个普通的 `function(req,res[,next]){}`；
2.  一个[httpServer](https://lodejs.org/api/http.html#http_class_http_server)；
3.  另一个 connect 的 app 对象(**sub app 特性**)；

由于它们的本质都是 `处理逻辑`，都可以用一个 `function(req,res,next){}`将它们概括起来，Connect 把他们都转化为这个函数，然后把它们存起来。

如何将这三种分别转换为 function(req, res, next) {}的形式呢？

1.  不用转换；
2.  httpServer 的定义是“对事件'request'后 handler 的对象”，我们可以从 httpServer.listeners('request')中得到这个函数；
3.  另一个 connect 对象，而 connect()返回的 app 就是 function(req, res, out) {}；

**执行过程**:

1.  将三种`处理逻辑`统一转换为`function(req,res,next){}`的形式表示。
2.  把这个`处理逻辑`与 route 一起，放入`stack`中(存储处理逻辑，route 用来匹配路由)

核心代码片段

```js
//route 是路由路径，handle 是一个`function(req, res, next) {...}`形式的业务逻辑
this.stack.push({ route: path, handle: handle }); 
```

**返回**:

```js
//返回自己，可以完成链式调用
return this; 
```

**总结:**:

```js
var app = connect();
app.use('/api', function(req, res, next) {}); 
```

等价于

```js
var app = connect();
app.stack.push({route: '/api', handle: function(req, res, next) {}}); 
```

最后，app.stack 里 **顺序存储** 了所有的 **逻辑处理函数** (中间件)。

```js
app.stack = [function1, function2, function3, ... function30]; 
```

## app.handle(req, res, out)

这个函数就是请求到达时，负责 `顺序调用` 我们存储在 stack 中的 `逻辑处理函数` (中间件)函数，类似上一章的 requestHandler。

**输入:**

1.  req 是 Nodejs 本身的可读流，不做过多介绍
2.  res 是 Nodejs 本身的可写流，不做过多介绍
3.  out 是为了 Connect 的 **sub app 特性** 而设计的参数，这个特性可以暂时忽略，这个参数我们暂时不关心

**处理过程:**

可以回头看一下上面的 requestHandler 函数，handle 的实现是这个函数的增强版

1.  取得 stack(存储`逻辑处理函数`列表)，index(列表下标)
2.  构建 next 函数，next 的作用是执行下一个`逻辑处理函数`
3.  触发第一个 next，触发链式调用

**next 函数实现:**

next 函数实现在 handle 函数体内，用来`顺序执行处理逻辑`，它是异步流程控制库的核心，不明白它的作用请看上面的异步流程控制库简介

path 是请求路径，route 是`逻辑处理函数`自带的属性。

1.  取得下一个`逻辑处理函数`;
2.  若路由不匹配，跳过此逻辑;
3.  若路由匹配下面的 call 执行匹配到的`逻辑处理函数`

tips: 跟上一章最后的代码一样，每个`逻辑处理函数`调用`next`来让后面的函数执行，存储在 stack 中的函数就实现了`链式调用`。不一定所有的函数都在返回的时候才调用`next`，为了不影响效率，有的函数可能先调用 next，然而自己还没有返回，继续做自己的事情。

核心代码：

```js
//取下一个逻辑逻辑处理函数
1:  var layer = stack[index++];
//不匹配时跳过
2:  if (path.toLowerCase().substr(0, route.length) !== route.toLowerCase()) {
      return next(err);
    }
//匹配时执行
3:  call(layer.handle, route, err, req, res, next); 
```

**返回:**

无

**总结:**

画图总结

```js
request come
     |
     v
middleware1 :  不匹配路由，skip
     |
     v
middleware2 :  匹配路由，执行
     |
     v
middleware3 :  匹配路由，执行
     |
     v
middleware4 :  不匹配路由，skip
     |
     v
    end 
```

<a name="call"></a>

## call(handle, route, err, req, res, next)

> 这里有个比较有趣的知识，`console.log(Function.length)`会返回函数定义的参数个数。值跟在函数体内执行`arguments.length`一样。

Connect 中规定`function(err, req, res, next) {}`形式为错误处理函数，`function(req, res, next) {}`为正常的业务逻辑处理函数。那么，可以根据 Function.length 以判断它是否为错误处理函数。

**输入:**

```js
|参数名 |描述            |
|------|:---------------:|
|handle|逻辑处理函数     |
|route |路由            |
|err   |是否发生过错误   |
|req   |Nodejs 对象      |
|res   |Nodejs 对象      |
|next  |next 函数        |
```

**处理过程:**

1.  是否有错误，本次 handle 是否是错误处理函数;
2.  若有错误且 handle 为错误处理函数，则执行 handle，本函数返回;
3.  若没错误且 handle 不是错误处理函数，则执行 handle，本函数返回;
4.  如果上面两个都不满足，不执行 handle，本函数调用 next，返回;

**返回:**

无

**总结:**

call 函数是一个执行者，根据当前`错误情况`和`handle 类型`决定`是否执行`当前的 handle。

## listen

创建一个 httpServer，将 Connect 自己的业务逻辑作为 requestHandler，监听端口

代码

```js
var server = http.createServer(this);
return server.listen.apply(server, arguments); 
```

## 图解 Connect

Connect 将中间件存储在 app.stack 中，通过构造 handle 中的 next 函数在请求到来时依次调用这些中间件。

图形总结

```js
request               app(out)
   |                          yes
   +------------------>match?----->middleware1
                         | no          |
                         v             |
                        next<----------+
                         |
                         v    yes
                      match?------>middleware2
                         | no          |
                         v             |
                        next<----------+
                         |
                         v    yes
                      match?------>middleware3
                         | no          |
                         v             |
                        out<-----------+
                         |
   +---------------------+
   |
   v
  end(response 在处理过程中已经返回了) 
```

## Connect 的 subapp 特性

我们再看看 Connect 是怎么实现 subapp 的，比较有趣。

什么是 subapp?

```js
var sub_app = connect();
var app = connect();

app.use('/route1', sub_app);
// request path: '/route1/route2'
// 由 app 接收到请求后，切割 path 为'/route2'转交给 sub_app 的处理逻辑处理
// 再由 sub_app 返回到 app，由 app 继续向下执行处理逻辑 
```

结合上面的函数画图

```js
request               app(out1)                 sub_app(out2)
   |
   +--------------->middleware1     +------------>middleware1
                         |          |                 |
                        next        |                next
                         |          |                 |
                         v          |                 v
                    middleware2-----+            middleware2
                                                      |
                       next<--------+                next
                         |          |                 |
                         v          |                 v
                     middleware3    |            middleware3
                         |          |                 |
                         v          |                 v
                        out1        |                out2
                         |          |                 |
   +---------------------+          +-----------------+
   |
   v
  end(response 在处理过程中已经返回了) 
```

完成上面的 sub_app 只需要做到两点：

1.  从 app 的调用链进入到 sub_app 的调用链中;
2.  从 sub_app 的逻辑回到 app 的调用链中;

connect 在 handle 函数中的第三个参数`out`为这个特性实现提供可能。`out`的特点是`在 middlewares 链式调用完成以后调用`。**那么将 app 的 next 作为 sub*app 的 out 传入 sub*app 的 handle 中**可以做到 sub_app 自己的业务逻辑处理完后调用`out`，即处理权回到了本 app 的`next`手里。

上面图中的`sub_app.out2===app.next`，所以能完成逻辑的交接和 sub app 调用。

## [Express](https://github.com/strongloop/express)

大家都知道 Express 是 Connect 的升级版。

Express 不只是 Connect 的升级版，它还封装了很多对象来方便业务逻辑处理。Express 里的 Router 是 Connect 的升级版。

Express 大概可以分为几个模块

```js
|模块        |描述                   |
|-----------|:---------------------:|
|router     |路由模块是 Connect 升级版  |
|request    |经过 Express 封装的 req 对象 |
|response   |经过 Express 封装的 res 对象 |
|application|app 上面的各种默认设置     |
```

简要介绍一下每个模块

## Router

在 Connect 中间件特性的基础上，加入了如下特性，是 Connect 的升级版

1.  正则匹配 route;
2.  进行将 http 的方法在 route 中分解开;

## Request

在 Request 中集成了 http.IncomingMessage(可读流+事件)，并在其上增加了新的属性，方便使用，我们最常用的应该是 req.param。

## Response

在 Response 中集成了 http.ServerResponse(可写流+事件)，并在其上增加了很多方便返回的函数，有我们熟悉的 res.json、 res.render、res.redirect、res.sendFile 等等。

我们可以拓展它写一个 res.sendPersonInfoById。

>关于流的题外话：req.pipe(res)的形式可以“完成发什么就返回什么”，而 req.pipe(mylogic).pipe(res)可以添加自己的逻辑， 我们的业务逻辑是把流读为 String/Object 再进行逻辑处理，处理完再推送给另一个 stream，有没有可能在流的层面进行逻辑解 耦提供服务呢？求大神解答了…至少这种写法在大流量、逻辑简单的情况下是有用的。

## Application

除了上面的三个模块以外，还需要有个地方存储整个 app 的属性、设置等。比较常用的是 app.engine 函数设置模板引擎。

## Express 小结

Express 是一个中间件机制的 httpServer 框架，它本身实现了中间件机制，它也包含了中间件。比如 3.x 版本的 Express 本身自带 bodyParser、cookieSession 等中间件，而在 4.x 中去掉了。包括 TJ 也写了很多中间件，比如 node-querystring、 connect-redis 等。

实现业务逻辑解耦时，中间件是从纵向的方面进行的逻辑分解，前面的中间件处理的结果可以给后面用，比如 bodyParser 把解析 body 的结果放在 req.body 中，后面的逻辑都可以从 req.body 中取值。由于中间件是顺序执行的，errHandler 一般都放在最后，而 log 类的中间件则放在比较前面。

## 总结

Connect 用流程控制库的回调函数及中间件的思想来解耦回调逻辑； [Koa](https://github.com/koajs/koa)用 Generator 方法解决回调问题；

我们应该也可以用事件、Promise 的方式实现；

PS: 用事件来实现的话还挺期待的，能形成网状的相互调用。

授权教程地址：[`github.com/alsotang/node-lessons`](https://github.com/alsotang/node-lessons)。

特别推荐 [CNode 社区](https://cnodejs.org) - Node.js 专业中文社区。