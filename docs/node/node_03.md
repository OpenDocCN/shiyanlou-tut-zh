# 第 2 节 Node.js 模块

## 实验简介

Node.js 模块和 Node.js 包介绍。

## 一、Node.js 模块

每一个 Node.js 都是一个 Node.js 模块，包括 JavaScript 文件（.js）、JSON 文本文件（.json）和二进制模块文件（.node）。

### 1\. 模块的使用

编写一个模块：

在虚拟机桌面新建一个文件`mymodule.js`，输入如下代码并保存：

```js
function hello() {
    console.log('Hello');
}

function world() {
    console.log('World');
} 
```

这就是一个 Node.js 模块，但是怎么在其他模块中引入并使用这个模块呢？我们需要为模块提供对外的接口，这就要用到`module.exports`和`exports`。

我们可以这样写`mymodul.js`：

```js
function hello() {
    console.log('Hello');
}

function world() {
    console.log('World');
}

exports.hello = hello;
exports.world = world; 
```

在其他模块中，可以使用`require(module_name);`载入需要的模块，如，在虚拟机桌面新建`index.js`，输入如下代码并保存：

```js
var hello = require('./mymodule'); // 也可以写作 var hello = require('./mymodule.js');

// 现在就可以使用 mymodule.js 中的函数了
hello.hello(); // >> Hello
hello.world(); // >> World 
```

也可以这样写`mymodule.js`：

```js
function Hello() {
    this.hello = function() {
        console.log('Hello');
    };

    this.world = function() {
        console.log('World');
    };
}

module.exports = Hello; 
```

此时，`index.js`就要改成这样：

```js
var Hello = require('./mymodule');

var hello = new Hello();

hello.hello(); // >> Hello
hello.world(); // >> World 
```

### 2\. module.exports 和 exports

`module`是一个对象，每个模块中都有一个`module`对象，`module`是当前模块的一个引用。`module.exports`对象是 Module 系统创建的，而`exports`可以看作是对`module.exports`对象的一个引用。在模块中`require`另一个模块时，以`module.exports`的值为准，因为有的情况下，`module.exports`和`exports`它们的值是不同的。`module.exports`和`exports`的关系可以表示成这样：

```js
// module.exports 和 exports 相同的情况
var m = {};        // 表示 module
var e = m.e = {};  // e 表示 exports， m.e 表示 module.exports

m.e.a = 5;
e.b = 6;

console.log(m.e);  // Object { a: 5, b: 6 }
console.log(e);    // Object { a: 5, b: 6 } 
```

```js
// module.exports 和 exports 不同的情况
var m = {};        // 表示 module
var e = m.e = {};  // e 表示 exports， m.e 表示 module.exports

m.e = { c: 9 };    // m.e（module.exports）引用的对象被改了
e.d = 10;

console.log(m.e);  // Object { c: 9 }
console.log(e);    // Object { d: 10 } 
```

## 二、Node.js 包

### 1\. 包

包用于管理多个模块及其依赖关系，可以对多个模块进行封装，包的根目录必须包含`package.json`文件，`package.json`文件是 CommonJS 规范用于描述包的文件，符合 CommonJS 规范的 package.json 文件应该包含以下字段：

1.  name：包名。包名是唯一的，只能包含小写字母、数字和下划线。
2.  version：包版本号。
3.  description：包说明。
4.  keywords：关键字数组。用于搜索。
5.  homepage：项目主页。
6.  bugs：提交 bug 的地址。
7.  license：许可证。
8.  maintainers：维护者数组。
9.  contributors：贡献者数组。
10.  repositories：项目仓库托管地址数组。
11.  dependencies：包依赖。

下面是一个`package.json`示例：

```js
{
    "name": "shiyanlou",
    "description": "Shiyanlou test package.",
    "version": "0.1.0",
    "keywords": [
        "shiyanlou",
        "nodejs"
     ],
    "maintainers": [{
        "name": "test",
        "email": "test@shiyanlou.com"
    }],
    "contributors": [{
        "name": "test",
        "web": "http://www.shiyanlou.com/"
    }],
    "bugs": {
        "mail": "test@shiyanlou.com",
        "web": "http://www.shiyanlou.com/"
    },
    "licenses": [{
        "type": "Apache License v2",
        "url": "http://www.apache.org/licenses/apache2.html"
    }],
    "repositories": [{
        "type": "git",
        "url": "http://github.com/test/test.git"
    }],
    "dependencies": { 
        "webkit": "1.2",
        "ssl": { 
            "gnutls": ["1.0", "2.0"],
            "openssl": "0.9.8"
        }
    }
} 
```

### 2\. npm 包管理工具

由于实验楼环境网络限制，所以 npm 命令会连接 taobao 的源，而不会直接连接官方源。

npm 也可以从第三方网站（http://www.npmjs.org/）上下载第三方 Node.js 包。

在实验楼的环境中搜索包（默认会连接到 taobao 的 Node.js 源）：

```js
shiyanlou@cqqg0heZ:~$ sudo npm search express 
```

安装包：

```js
shiyanlou@cqqg0heZ:~$ sudo npm install -g express 
```

更新包：

```js
shiyanlou@cqqg0heZ:~$ sudo npm update express 
```

卸载包：

```js
shiyanlou@cqqg0heZ:~$ sudo npm uninstall express 
```