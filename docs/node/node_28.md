# 第 17 节 《使用 promise 替代回调函数》

## 知识点

1.  理解 Promise 概念，为什么需要 promise
2.  学习 q 的 API，利用 q 来替代回调函数(https://github.com/kriskowal/q )

## 课程内容

第五课(https://github.com/alsotang/node-lessons/tree/master/lesson5 )讲述了如何使用 async 来控制并发。async 的本质是一个流程控制。其实在异步编程中，还有一个更为经典的模型，叫做 Promise/Deferred 模型。

本节我们就来学习这个模型的代表实现：[q](https://github.com/kriskowal/q)

首先，我们思考一个典型的异步编程模型，考虑这样一个题目：读取一个文件，在控制台输出这个文件内容。

```js
var fs = require('fs');
fs.readFile('sample.txt', 'utf8', function (err, data) {
    console.log(data);
}); 
```

看起来很简单，再进一步: 读取两个文件，在控制台输出这两个文件内容。

```js
var fs = require('fs');
fs.readFile('sample01.txt', 'utf8', function (err, data) {
    console.log(data);
    fs.readFile('sample02.txt', 'utf8', function (err,data) {
        console.log(data);
    });
}); 
```

要是读取更多的文件呢?

```js
var fs = require('fs');
fs.readFile('sample01.txt', 'utf8', function (err, data) {
    fs.readFile('sample02.txt', 'utf8', function (err,data) {
        fs.readFile('sample03.txt', 'utf8', function (err, data) {
            fs.readFile('sample04.txt', 'utf8', function (err, data) {

            });
        });
    });
}); 
```

这段代码就是臭名昭著的邪恶金字塔(Pyramid of Doom)。可以使用 async 来改善这段代码，但是在本课中我们要用 promise/defer 来改善它。

## promise 基本概念

先学习 promise 的基本概念。

*   promise 只有三种状态，未完成，完成(fulfilled)和失败(rejected)。
*   promise 的状态可以由未完成转换成完成，或者未完成转换成失败。
*   promise 的状态转换只发生一次

promise 有一个 then 方法，then 方法可以接受 3 个函数作为参数。前两个函数对应 promise 的两种状态 fulfilled, rejected 的回调函数。第三个函数用于处理进度信息。

```js
promiseSomething().then(function(fulfilled){
        //当 promise 状态变成 fulfilled 时，调用此函数
    },function(rejected){
        //当 promise 状态变成 rejected 时，调用此函数
    },function(progress){
        //当返回进度信息时，调用此函数
    }); 
```

学习一个简单的例子：

```js
var Q = require('q');
var defer = Q.defer();
/**
 * 获取初始 promise
 * @private
 */
function getInitialPromise() {
  return defer.promise;
}
/**
 * 为 promise 设置三种状态的回调函数
 */
getInitialPromise().then(function(success){
    console.log(success);
},function(error){
    console.log(error);
},function(progress){
    console.log(progress);
});
defer.notify('in progress');//控制台打印 in progress
defer.resolve('resolve');   //控制台打印 resolve
defer.reject('reject');        //没有输出。promise 的状态只能改变一次 
```

## promise 的传递

then 方法会返回一个 promise，在下面这个例子中，我们用 outputPromise 指向 then 返回的 promise。

```js
var outputPromise = getInputPromise().then(function (fulfilled) {
    }, function (rejected) {
    }); 
```

现在 outputPromise 就变成了受 `function(fulfilled)` 或者 `function(rejected)`控制状态的 promise 了。怎么理解这句话呢？

*   当 function(fulfilled)或者 function(rejected)返回一个值，比如一个字符串，数组，对象等等，那么 outputPromise 的状态就会变成 fulfilled。

在下面这个例子中，我们可以看到，当我们把 inputPromise 的状态通过 defer.resovle()变成 fulfilled 时，控制台输出 fulfilled.

当我们把 inputPromise 的状态通过 defer.reject()变成 rejected，控制台输出 rejected

```js
var Q = require('q');
var defer = Q.defer();
/**
 * 通过 defer 获得 promise
 * @private
 */
function getInputPromise() {
    return defer.promise;
}

/**
 * 当 inputPromise 状态由未完成变成 fulfil 时，调用 function(fulfilled)
 * 当 inputPromise 状态由未完成变成 rejected 时，调用 function(rejected)
 * 将 then 返回的 promise 赋给 outputPromise
 * function(fulfilled) 和 function(rejected) 通过返回字符串将 outputPromise 的状态由
 * 未完成改变为 fulfilled
 * @private
 */
var outputPromise = getInputPromise().then(function(fulfilled){
    return 'fulfilled';
},function(rejected){
    return 'rejected';
});

/**
 * 当 outputPromise 状态由未完成变成 fulfil 时，调用 function(fulfilled)，控制台打印'fulfilled: fulfilled'。
 * 当 outputPromise 状态由未完成变成 rejected, 调用 function(rejected), 控制台打印'fulfilled: rejected'。
 */
outputPromise.then(function(fulfilled){
    console.log('fulfilled: ' + fulfilled);
},function(rejected){
    console.log('rejected: ' + rejected);
});

/**
 * 将 inputPromise 的状态由未完成变成 rejected
 */
defer.reject(); //输出 fulfilled: rejected

/**
 * 将 inputPromise 的状态由未完成变成 fulfilled
 */
//defer.resolve(); //输出 fulfilled: fulfilled 
```

*   当 function(fulfilled)或者 function(rejected)抛出异常时，那么 outputPromise 的状态就会变成 rejected

```js
var Q = require('q');
var fs = require('fs');
var defer = Q.defer();

/**
 * 通过 defer 获得 promise
 * @private
 */
function getInputPromise() {
    return defer.promise;
}

/**
 * 当 inputPromise 状态由未完成变成 fulfil 时，调用 function(fulfilled)
 * 当 inputPromise 状态由未完成变成 rejected 时，调用 function(rejected)
 * 将 then 返回的 promise 赋给 outputPromise
 * function(fulfilled) 和 function(rejected) 通过抛出异常将 outputPromise 的状态由
 * 未完成改变为 reject
 * @private
 */
var outputPromise = getInputPromise().then(function(fulfilled){
    throw new Error('fulfilled');
},function(rejected){
    throw new Error('rejected');
});

/**
 * 当 outputPromise 状态由未完成变成 fulfil 时，调用 function(fulfilled)。
 * 当 outputPromise 状态由未完成变成 rejected, 调用 function(rejected)。
 */
outputPromise.then(function(fulfilled){
    console.log('fulfilled: ' + fulfilled);
},function(rejected){
    console.log('rejected: ' + rejected);
});

/**
 * 将 inputPromise 的状态由未完成变成 rejected
 */
defer.reject();     //控制台打印 rejected [Error:rejected]

/**
 * 将 inputPromise 的状态由未完成变成 fulfilled
 */
//defer.resolve(); //控制台打印 rejected [Error:fulfilled] 
```

*   当 function(fulfilled)或者 function(rejected)返回一个 promise 时，outputPromise 就会成为这个新的 promise.

这样做有什么意义呢? 主要在于聚合结果(Q.all)，管理延时，异常恢复等等

比如说我们想要读取一个文件的内容，然后把这些内容打印出来。可能会写出这样的代码：

```js
//错误的写法
var outputPromise = getInputPromise().then(function(fulfilled){
    fs.readFile('test.txt','utf8',function(err,data){
        return data;
    });
}); 
```

然而这样写是错误的，因为 function(fulfilled)并没有返回任何值。需要下面的方式:

```js
var Q = require('q');
var fs = require('fs');
var defer = Q.defer();

/**
 * 通过 defer 获得 promise
 * @private
 */
function getInputPromise() {
    return defer.promise;
}

/**
 * 当 inputPromise 状态由未完成变成 fulfil 时，调用 function(fulfilled)
 * 当 inputPromise 状态由未完成变成 rejected 时，调用 function(rejected)
 * 将 then 返回的 promise 赋给 outputPromise
 * function(fulfilled)将新的 promise 赋给 outputPromise
 * 未完成改变为 reject
 * @private
 */
var outputPromise = getInputPromise().then(function(fulfilled){
    var myDefer = Q.defer();
    fs.readFile('test.txt','utf8',function(err,data){
        if(!err && data) {
            myDefer.resolve(data);
        }
    });
    return myDefer.promise;
},function(rejected){
    throw new Error('rejected');
});

/**
 * 当 outputPromise 状态由未完成变成 fulfil 时，调用 function(fulfilled)，控制台打印 test.txt 文件内容。
 *
 */
outputPromise.then(function(fulfilled){
    console.log(fulfilled);
},function(rejected){
    console.log(rejected);
});

/**
 * 将 inputPromise 的状态由未完成变成 rejected
 */
//defer.reject();

/**
 * 将 inputPromise 的状态由未完成变成 fulfilled
 */
defer.resolve(); //控制台打印出 test.txt 的内容 
```

## 方法传递

方法传递有些类似于 Java 中的 try 和 catch。当一个异常没有响应的捕获时，这个异常会接着往下传递。

方法传递的含义是当一个状态没有响应的回调函数，就会沿着 then 往下找。

*   没有提供 function(rejected)

```js
var outputPromise = getInputPromise().then(function(fulfilled){}) 
```

如果 inputPromise 的状态由未完成变成 rejected, 此时对 rejected 的处理会由 outputPromise 来完成。

```js
var Q = require('q');
var fs = require('fs');
var defer = Q.defer();

/**
 * 通过 defer 获得 promise
 * @private
 */
function getInputPromise() {
    return defer.promise;
}

/**
 * 当 inputPromise 状态由未完成变成 fulfil 时，调用 function(fulfilled)
 * 当 inputPromise 状态由未完成变成 rejected 时，这个 rejected 会传向 outputPromise
 */
var outputPromise = getInputPromise().then(function(fulfilled){
    return 'fulfilled'
});
outputPromise.then(function(fulfilled){
    console.log('fulfilled: ' + fulfilled);
},function(rejected){
    console.log('rejected: ' + rejected);
});

/**
 * 将 inputPromise 的状态由未完成变成 rejected
 */
defer.reject('inputpromise rejected'); //控制台打印 rejected: inputpromise rejected

/**
 * 将 inputPromise 的状态由未完成变成 fulfilled
 */
//defer.resolve(); 
```

*   没有提供 function(fulfilled)

```js
var outputPromise = getInputPromise().then(null,function(rejected){}) 
```

如果 inputPromise 的状态由未完成变成 fulfilled, 此时对 fulfil 的处理会由 outputPromise 来完成。

```js
var Q = require('q');
var fs = require('fs');
var defer = Q.defer();

/**
 * 通过 defer 获得 promise
 * @private
 */
function getInputPromise() {
    return defer.promise;
}

/**
 * 当 inputPromise 状态由未完成变成 fulfil 时，传递给 outputPromise
 * 当 inputPromise 状态由未完成变成 rejected 时，调用 function(rejected)
 * function(fulfilled)将新的 promise 赋给 outputPromise
 * 未完成改变为 reject
 * @private
 */
var outputPromise = getInputPromise().then(null,function(rejected){
    return 'rejected';
});

outputPromise.then(function(fulfilled){
    console.log('fulfilled: ' + fulfilled);
},function(rejected){
    console.log('rejected: ' + rejected);
});

/**
 * 将 inputPromise 的状态由未完成变成 rejected
 */
//defer.reject('inputpromise rejected');

/**
 * 将 inputPromise 的状态由未完成变成 fulfilled
 */
defer.resolve('inputpromise fulfilled'); //控制台打印 fulfilled: inputpromise fulfilled 
```

*   可以使用 fail(function(error))来专门针对错误处理，而不是使用 then(null,function(error))

```js
 var outputPromise = getInputPromise().fail(function(error){}) 
```

看这个例子

```js
var Q = require('q');
var fs = require('fs');
var defer = Q.defer();

/**
 * 通过 defer 获得 promise
 * @private
 */
function getInputPromise() {
    return defer.promise;
}

/**
 * 当 inputPromise 状态由未完成变成 fulfil 时，调用 then(function(fulfilled))
 * 当 inputPromise 状态由未完成变成 rejected 时，调用 fail(function(error))
 * function(fulfilled)将新的 promise 赋给 outputPromise
 * 未完成改变为 reject
 * @private
 */
var outputPromise = getInputPromise().then(function(fulfilled){
    return fulfilled;
}).fail(function(error){
    console.log('fail: ' + error);
});
/**
 * 将 inputPromise 的状态由未完成变成 rejected
 */
defer.reject('inputpromise rejected');//控制台打印 fail: inputpromise rejected

/**
 * 将 inputPromise 的状态由未完成变成 fulfilled
 */
//defer.resolve('inputpromise fulfilled'); 
```

*   可以使用 progress(function(progress))来专门针对进度信息进行处理，而不是使用 `then(function(success){},function(error){},function(progress){})`

```js
var Q = require('q');
var defer = Q.defer();
/**
 * 获取初始 promise
 * @private
 */
function getInitialPromise() {
  return defer.promise;
}
/**
 * 为 promise 设置 progress 信息处理函数
 */
var outputPromise = getInitialPromise().then(function(success){

}).progress(function(progress){
    console.log(progress);
});

defer.notify(1);
defer.notify(2); //控制台打印 1，2 
```

## promise 链

promise 链提供了一种让函数顺序执行的方法。

函数顺序执行是很重要的一个功能。比如知道用户名，需要根据用户名从数据库中找到相应的用户，然后将用户信息传给下一个函数进行处理。

```js
var Q = require('q');
var defer = Q.defer();

//一个模拟数据库
var users = [{'name':'andrew','passwd':'password'}];

function getUsername() {
return defer.promise;
}

function getUser(username){
    var user;
    users.forEach(function(element){
        if(element.name === username) {
            user = element;
        }
    });
    return user;
}

//promise 链
getUsername().then(function(username){
 return getUser(username);
}).then(function(user){
 console.log(user);
});

defer.resolve('andrew'); 
```

我们通过两个 then 达到让函数顺序执行的目的。

then 的数量其实是没有限制的。当然，then 的数量过多，要手动把他们链接起来是很麻烦的。比如

```js
foo(initialVal).then(bar).then(baz).then(qux) 
```

这时我们需要用代码来动态制造 promise 链

```js
var funcs = [foo,bar,baz,qux]
var result = Q(initialVal)
funcs.forEach(function(func){
    result = result.then(func)
})
return result 
```

当然，我们可以再简洁一点

```js
var funcs = [foo,bar,baz,qux]
funcs.reduce(function(pre,current),Q(initialVal){
    return pre.then(current)
}) 
```

看一个具体的例子

```js
function foo(result) {
    console.log(result);
    return result+result;
}
//手动链接
Q('hello').then(foo).then(foo).then(foo);                                     //控制台输出： hello
                                                                            //             hellohello
                                                                            //             hellohellohello

//动态链接
var funcs = [foo,foo,foo];
var result = Q('hello');
funcs.forEach(function(func){
    result = result.then(func);
});
//精简后的动态链接
funcs.reduce(function(prev,current){
    return prev.then(current);
},Q('hello')); 
```

对于 promise 链，最重要的是需要理解为什么这个链能够顺序执行。如果能够理解这点，那么以后自己写 promise 链可以说是轻车熟路啊。

## promise 组合

回到我们一开始读取文件内容的例子。如果现在让我们把它改写成 promise 链，是不是很简单呢？

```js
var Q = require('q'),
    fs = require('fs');
function printFileContent(fileName) {
    return function(){
        var defer = Q.defer();
        fs.readFile(fileName,'utf8',function(err,data){
          if(!err && data) {
            console.log(data);
            defer.resolve();
          }
        })
        return defer.promise;
    }
}
//手动链接
printFileContent('sample01.txt')()
    .then(printFileContent('sample02.txt'))
    .then(printFileContent('sample03.txt'))
    .then(printFileContent('sample04.txt'));   //控制台顺序打印 sample01 到 sample04 的内容 
```

很有成就感是不是。然而如果仔细分析，我们会发现为什么要他们顺序执行呢，如果他们能够并行执行不是更好吗? 我们只需要在他们都执行完成之后，得到他们的执行结果就可以了。

我们可以通过 Q.all([promise1,promise2...])将多个 promise 组合成一个 promise 返回。 注意：

1.  当 all 里面所有的 promise 都 fulfil 时，Q.all 返回的 promise 状态变成 fulfil
2.  当任意一个 promise 被 reject 时，Q.all 返回的 promise 状态立即变成 reject

我们来把上面读取文件内容的例子改成并行执行吧

```js
var Q = require('q');
var fs = require('fs');
/**
 *读取文件内容
 *@private
 */
function printFileContent(fileName) {
        //Todo: 这段代码不够简洁。可以使用 Q.denodeify 来简化
        var defer = Q.defer();
        fs.readFile(fileName,'utf8',function(err,data){
          if(!err && data) {
            console.log(data);
            defer.resolve(fileName + ' success ');
          }else {
            defer.reject(fileName + ' fail ');
          }
        })
        return defer.promise;
}

Q.all([printFileContent('sample01.txt'),printFileContent('sample02.txt'),printFileContent('sample03.txt'),printFileContent('sample04.txt')])
    .then(function(success){
        console.log(success);
    }); //控制台打印各个文件内容 顺序不一定 
```

现在知道 Q.all 会在任意一个 promise 进入 reject 状态后立即进入 reject 状态。如果我们需要等到所有的 promise 都发生状态后(有的 fulfil, 有的 reject)，再转换 Q.all 的状态, 这时我们可以使用 Q.allSettled

```js
var Q = require('q'),
    fs = require('fs');
/**
 *读取文件内容
 *@private
 */
function printFileContent(fileName) {
    //Todo: 这段代码不够简洁。可以使用 Q.denodeify 来简化
    var defer = Q.defer();
    fs.readFile(fileName,'utf8',function(err,data){
      if(!err && data) {
        console.log(data);
        defer.resolve(fileName + ' success ');
      }else {
        defer.reject(fileName + ' fail ');
      }
    })
    return defer.promise;
}

Q.allSettled([printFileContent('nosuchfile.txt'),printFileContent('sample02.txt'),printFileContent('sample03.txt'),printFileContent('sample04.txt')])
    .then(function(results){
        results.forEach(
            function(result) {
                console.log(result.state);
            }
        );
    }); 
```

## 结束 promise 链

通常，对于一个 promise 链，有两种结束的方式。第一种方式是返回最后一个 promise

如 `return foo().then(bar);`

第二种方式就是通过 done 来结束 promise 链

如 `foo().then(bar).done()`

为什么需要通过 done 来结束一个 promise 链呢? 如果在我们的链中有错误没有被处理，那么在一个正确结束的 promise 链中，这个没被处理的错误会通过异常抛出。

```js
var Q = require('q');
/**
 *@private
 */
function getPromise(msg,timeout,opt) {
    var defer = Q.defer();
    setTimeout(function(){
    console.log(msg);
        if(opt)
            defer.reject(msg);
        else
            defer.resolve(msg);
    },timeout);
    return defer.promise;
}
/**
 *没有用 done()结束的 promise 链
 *由于 getPromse('2',2000,'opt')返回 rejected, getPromise('3',1000)就没有执行
 *然后这个异常并没有任何提醒，是一个潜在的 bug
 */
getPromise('1',3000)
    .then(function(){return getPromise('2',2000,'opt')})
    .then(function(){return getPromise('3',1000)});
/**
 *用 done()结束的 promise 链
 *有异常抛出
 */
getPromise('1',3000)
    .then(function(){return getPromise('2',2000,'opt')})
    .then(function(){return getPromise('3',1000)})
    .done(); 
```

## 结束语

当你理解完上面所有的知识点时，你就会正确高效的使用 promise 了。本节只是讲了 promise 的原理和几个基本的 API，不过你掌握了这些之后，再去看 q 的文档，应该很容易就能理解各个 api 的意图。

授权教程地址：[`github.com/alsotang/node-lessons`](https://github.com/alsotang/node-lessons)。

特别推荐 [CNode 社区](https://cnodejs.org) - Node.js 专业中文社区。