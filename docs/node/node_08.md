# 第 7 节 Markdown 格式实验文档模板-实验名称

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

## 二、课程介绍

这个实验主要学习 Node.js 中的进程，涉及的模块是 Cluster、Porcess 和 child_process。

## Process 模块

process 模块在前面的实验已经用过了，使用时直接通过全局变量 process 访问，而不需要通过 require 方法加载。process 用来和当前 Node.js 程序进程互动。

process 是 EventEmitter 的一个实例，process 主要包含退出事件、信号事件以及一些属性。

### 退出事件（exit）

当退出当前进程时，会促发 exit 事件，exit 事件的回调函数中只接受同步操作，并且回调函数只接受一个参数，即退出代码，如：

```js
process.on('exit', function(code) {
    setTimeout(function() {
        console.log('This will not run');
    }, 0);

    console.log('exit code: ', code);
}); 
```

运行上面的代码，其中 setTimeout 方法中的回调函数是不会被执行的，因为 exit 事件中只会运行同步操作，而不会运行异步操作。

在 exit 事件之前还有一个 beforeExit 事件会被触发，在 beforeExit 的回调函数中可以执行异步操作。值得注意的是，通过 process.exit()退出程序或者因为发生错误而退出程序是不会触发 beforeExit 事件的。顺便说一下，当有错误未被捕获时，就会触发 uncaughtException 事件。

### 信号事件

信号事件就是接收到某个特定信号才会被触发的事件。

比如`SIGINT`事件的触发方式是`ctrl+c`：

```js
// sigint.js
process.stdin.resume();

process.on('SIGINT', function() {
    console.log('Got SIGINT.  Press Control-D to exit.');
}); 
```

运行代码：

```js
$ node sigint.js 
```

然后按住`control`键，再按`C`键就会触发 SIGINT 事件。

### 属性

process 模块提供了很多属性，其中关于 IO 输入输出的主要有三个：

```js
process.stdin  // 标准输入
process.stdout // 标准输出
process.stderr // 标准错误 
```

举例：

```js
// stdin.js
process.stdin.setEncoding('utf8');

process.stdin.on('readable', function() {
    var chunk = process.stdin.read();
    if (chunk !== null) {
        process.stdout.write('data: ' + chunk);
    }
});

process.stdin.on('end', function() {
    process.stdout.write('end');
}); 
```

运行：

```js
node stdin.js 
```

输入任意字符，Node.js 会把输入的字符打印出来，输入`ctrl+D`触发 end 事件。

还有其他属性，比如`process.argv`是一个包含了命令行参数的数组。

### 方法

process 模块还有很多实用的方法，比如：

```js
process.cwd()   // 返回脚本运行工作目录
process.chdir() // 切换工作目录
process.exit()  // 退出当前进程
process.on()    // 添加监听事件
//... 
```

## child_process 模块

child_process 用于创建子进程。

### child_process.spawn()方法

通过当前命令启动一个新的进程。如：

```js
// test_spawn.js
var spawn = require('child_process').spawn,
    ls    = spawn('ls', ['-lh', '/usr']);

ls.stdout.on('data', function (data) {
    console.log('stdout: ' + data);
});

ls.stderr.on('data', function (data) {
    console.log('stderr: ' + data);
});

ls.on('close', function (code) {
    console.log('child process exited with code ' + code);
}); 
```

运行命令：

```js
$ node test_spawn.js 
```

从结果可以看出，子进程成功运行了`ls -lh /usr`命令。

### child_process.exec()方法

在 shell 中运行一个命令，并缓存其输出。如：

```js
// test_exec.js
var exec = require('child_process').exec,
    child;

child = exec('cat *.js bad_file | wc -l',
    function (error, stdout, stderr) {
        console.log('stdout: ' + stdout);
        console.log('stderr: ' + stderr);
    if (error !== null) {
        console.log('exec error: ' + error);
    }
}); 
```

运行：

```js
$ node test_exec.js 
```

因为没有 bad_file 这个文件，所以会看到终端打印出了相关错误信息。

### child_process.execFile()方法

与 exec 方法类似，执行特定程序文件，参数通过一个数组传送。，如：

```js
// test_execfile.js
var child_process = require('child_process');

// exec: spawns a shell
child_process.exec('ls -lh /usr', function(error, stdout, stderr){
    console.log(stdout);
    console.log('******************');
});

// execFile: executes a file with the specified arguments
child_process.execFile('/bin/ls', ['-lh', '/usr'], function(error, stdout, stderr){
    console.log(stdout);
}); 
```

运行：

```js
$ node test_execfile.js 
```

### child_process.fork()方法

直接创建一个子进程，此进程是 node 命令的子进程，`fork('./sub.js')`相当于`spwan('node', './sub.js')`。fork 还会在父进程与子进程之间，建立一个通信管道，通过`child.send()`发送消息。如：

```js
// main.js
var cp = require('child_process');

var n = cp.fork(__dirname + '/sub.js');

n.on('message', function(m) {
  console.log('PARENT got message:', m);
});

n.send({ hello: 'world' }); 
```

```js
// sub.js
process.on('message', function(m) {
  console.log('CHILD got message:', m);
});

process.send({ foo: 'bar' }); 
```

运行：

```js
$ node main.js 
```

运行 main.js 会看到主进程收到了来自子进程的消息，而子进程也收到了来自主进程的消息。

## cluster 模块

单个的 Node 实例运行在单个线程中。要发挥多核系统的能力，需要启动一个 Node 进程集群来处理负载。cluster 模块就用于创建共享服务器端口的子进程。

举个栗子：

```js
// test_cluster.js

var cluster = require('cluster');
var http = require('http');
var numCPUs = require('os').cpus().length;  // 获取 CPU 内核数

// master 是主进程
// 此处判断是否为 master 进程
// 是则根据 CPU 内核数创建 worker 进程
if (cluster.isMaster) {
    // worker 是运行节点
    // 根据 CPU 数量启动 worker
    // Fork workers
    for (var i = 0; i < numCPUs; i++) {
        cluster.fork();
    }

    Object.keys(cluster.workers).forEach(function(id) {
        console.log('I am running with ID : ' + cluster.workers[id].process.pid);
    });
    cluster.on('exit', function(worker, code, signal) {
        console.log('worker ' + worker.process.pid + ' died');
    });
} else {
    // cluster.isWorker == true
    // 运行到 else 中的代码
    // 说明当前进程是 worker 进程
    // 那么此 worker 进程就启动一个 http 服务
    http.createServer(function(req, res) {
        res.writeHead(200);
        res.end("hello world\n");
    }).listen(8000);
} 
```

运行程序：

```js
$ node test_cluster.js 
```

在终端会看到根据 CPU 内核数创建的子进程信息。

每个 worker 进程都是通过 child_process.fork()方法产生的，所以它们可以通过 IPC（进程间通信）与 master 进程通信。

cluster.worker 是 worker 进程对象，其中有 `worker.id`、`worker.process`等属性，还有`worker.send()`等方法。