# 一百二十行代码实现简单的即时搜索

本实验利用 Meteor 和 MongoDB 实现一个简单的即时搜索服务

## 实验简介

本实验意在实现一个简单的即时搜索服务，对于前端有所了解(特别是 js)和接触过 MongoDB 的同学，相信很快就可以上手，而新同学也不用怕，我尽量写得详细一点。

实验 Demo：

![此处输入图片的描述](img/document-uid8504labid1221timestamp1438069391869.jpg)

本实验基于：

[`zhengqm.github.io/code/2015/06/26/instant-search-in-meteor/`](http://zhengqm.github.io/code/2015/06/26/instant-search-in-meteor/)

## 说明

即时搜索其实我们基本每天都在用，像百度，Google 都是这方面的典型例子，它最大的特点就是在用户输入关键字的同时返回搜索结果给用户，用户体验很棒。

知道了即时搜索的初步概念，我们首先明确一下本次实验的几个实现目标：

1.  在用户输入关键字的时候进行即时搜索
2.  对关键字的条件匹配 比如 > < 等
3.  可能的话，对关键字进行正则匹配

知道我们的实现目标之后，我们需要来整理一下整个即时搜索的思路：

```js
用户输入关键字 -->  通过监听用户输入获取关键字 --> 将关键字发送到服务器端 --> 服务器端根据关键字进行查询 --> 将查到的数据返回给客户端 --> 将数据展示给用户 
```

以上基本上就是整个即时搜索的实现思路，虽然步骤上可能看着有点多，但是这些都是可以在很短的时间内完成的。不过如果是自己单纯地从零开始进行编写代码，实现起来可能就很难了。所以，这个时候，[Meteor](https://www.meteor.com/)就可以派上用场了。关于 Meteor 的更多资料和介绍，你可以直接到官网查看，你可以简单地将它理解为一个用 js 快速制作 Web App 的平台。

## 数据说明和准备

我们这次做的是对一些软件开发数据的简单收集和统计。最基础且最重要的数据是软件项目的代码库，其中包含了项目的所有代码文件、代码的所有版本、代码提交者的想关信息。我们会将这些数据储存在 MongoDB 中，对于每一个数据我们储存了软件项目的以下信息：

*   名称 (prj)
*   所托管的网站 (repo)
*   代码库所在位置 (src_loc)
*   提交日志所在位置 (log_loc)
*   提交人数 (n_peo)
*   提交的版本数 (n_cmt)
*   所使用的版本控制系统 (vcs)
*   项目的起止时间 (btime/etime)
*   时间跨度等信息 (span)

以 Hadoop 为例， 它在 Mongodb 中的一条记录大概是这样的：

```js
{ 
    "_id" : "1430277742.791925", 
    "prj" : "hadoop-common.git",   
    "repo" : "git.apache.org",    
    "src_loc" : "/path/to/datastore/git/git.apache.org_hadoop-common.git", 
    "log_loc" : "/path/to/datastore/git/git.apache.org/hadoop-common.git", 
    "n_peo" : 36, 
    "n_cmt" : 5825，
    "vcs" : "git"
    "b_time" : 200601, 
    "e_time" : 201005, 
    "span" : 52,
    "script" : "", 
} 
```

这里简要说明一下，MongoDB 中，每一条记录你可以简单地看成一个 JSON，这得受益于 MongoDB 的 NoSQL(Not Only SQL)特性。

准备数据： 获取我们需要的数据：

```js
cd Desktop

wget http://labfile.oss.aliyuncs.com/courses/386/Search.zip 
```

然后解压，并进入到项目目录之下：

```js
unzip Search.zip

cd Search/ 
```

到这里，你会得到一个`Search`文件夹，你可以直接将这个文件夹拖到 Brackets 中进行编写代码，我们主要编写的就是`SRSearch.js` 和 `SRSearch.html`。不过在开始之前我们需要一些数据准备，所以先准备好数据再编写代码：

启动 MongoDB，在命令行执行：

```js
sudo mkdir -p /data/db
sudo mongod 
```

这里创建的`/data/db`目录为 MongoDB 的数据目录存储路径。

启动 MongoDB 之后，就可以插入相应的数据，在项目目录之下`(Search/)`命令行执行：

```js
mongoimport -h localhost:27017 --db meteor --collection log_info --type json --file mongo.json --jsonArray 
```

这里需要注意的地方是`--collection log_info`，我们在后面主要就是使用到`log_info`这个`collection`.

之后你大概会看到下面的信息

```js
connected to: localhost:27017
imported 10 documents 
```

这里我们便于小项目，直接使用十条数据，如果有更多的数据，也是可以跑起来的。数据准备完毕之后，我么就可以开始编写代码了。

## 开始编写代码

首先，需要说明的是，这里你完全不用担心代码的编写，因为代码不会超过 120 行。所以让我们简简单单 地就开始吧。为了方便起见，我们可以将客户端、服务器端的代码放在同一个 `javascript`文件中，也就是我们就只需要引入一个`js`文件就可以了。如果你觉得客户端和服务端的代码在一个文件中有点奇怪，没有关系，先继续往下看一点点，`Meteor`会帮你解决这个问题。

既然是做搜索，其实就是搜索我们存在数据库里面的内容，所以我们首先来获取 Mongodb 中存储元数据的 `collection`:

```js
Items = new Mongo.Collection("log_info"); 
```

没错，就是这样简单的一行代码，我们就可以获取到`log_info`（就是上面插入数据时候制定的 collection）这个`collection`里面的所有数据记录了。这里需要说明的是，这个对象既可在客户端代码中使用，也可在服务器端代码使用， `Meteor` 会基于 DDP 协议帮我们搞定数据在服务器、客户端之间的传输问题。获取到这些数据`(Items)`之后，我们首先实现的是：在服务器端根据用户输入的关键字对数据`(Items)`进行查询

```js
if (Meteor.isServer) {
  Meteor.publish('items', function (queryString) {
      return query(Items, queryString)
  })
} 
```

代码说明：首先`if (Meteor.isServer)`就是限定这里面的一段代码只在服务端运行，所以回头看看刚刚叫你不用担心的问题`客户端和服务端的代码在一个文件...`。然后，我们再看里面的代码，我们使用 Meteor 的`publish`(发布)方法将我们的数据发布出去，但是我们不能总是将所有的`Items`发布给客户端，我们需要根据用户的输入`(queryString)`进行筛选，所以最后在`publish`方法的回调函数中，我们执行一个自定义的`query`方法对 Items 的所有数据进行查询和筛选。

服务端代码写好之后，我们就可以编写客户端的代码了：

```js
if (Meteor.isClient) {
  Session.set('queryString', '')
  Meteor.subscribe('items', '')
Template.body.events({
    "keyup #search-box":_.throttle(function(event){
      Session.set('queryString', event.target.value)
      Meteor.subscribe('items', event.target.value)
    }, 50)
  })
} 
```

这里的代码需要说明的几个点就是：

首先我们通过`if (Meteor.isClient)`来限定里面的代码段就只在客户端执行。紧接着`Session.set('queryString', '')`用`Session`声明`queryString`为空，通过`Meteor.subscribe('items', '')`声明`items`为空。就是简单的声明变量一样。

`Template.body` 代表的就是 HTML 中的<body>标签，在 Meteor 的 HTML 模版文件中，它会自动绑定整个<body>标签，并将它存储在 Template 的 body 属性当中。所以你可以简单地理解为：`Template.body.events`就是可以对整个<body>进行时间监听，不过我们通过`"keyup #search-box"`来指明更具体的事件和更具体的件套元素，这里的前半段就是代表事件类型，后半段就是简单的元素选择器。对`#search-box`的`keyup`事件，我们使用`_.throttle 方`法防治每一次的用户输入都马上提交给服务端，我们将提交时间间隔设置为 50 毫秒。然后，`Session.set('queryString', event.target.value)`这一段就是将查询字符串直接存储到 Session 当中。在这里的`event.target.value`就是用户输入的关键字的值，你可以用`console.log(event.target.value)`来查看一下。最后，也就是很关键的一步了，我们使用`subscribe`(订阅)方法来订阅服务器发布`(publish)`的数据，`subscribe`方法跟`publish`方法一起使用，你可能也会注意到，我们就是通过`subscribe('items')`和`publish('items')`中的`items`来指名发布和订阅的匹配。最后我们将用户的输入值`(event.target.value)`传给服务端.

这时候，其实在客户端与服务端的通道已将打通了。不过这时候我们还不能看到数据，因为我们并没有使用模板将他们展示出来。所以，实现之：

```js
Template.body.helpers({
    items: function () {
      return query(Items, Session.get('queryString'))
    }
  }); 
```

这个可以简单地理解为`Template.body`的一个辅助函数。这是就是将符合查询条件的`items`返回给模板。这个 items 我们后面在写模板的时候会用到的。

以上的代码都实现之后，我们其实就差一个`query`函数还没实现了，它需要做的是以下几件事：

1.  根据用户的输入构造查询数据库的条件
2.  对数据库进行查询
3.  返回查询结果

```js
function query(collections, queryString){
  var limit = 40 //限制搜索结果返回最多 40 个
  var query = queryString.split(' ') // split 将用户输入的 queryString 分成一个个字母

  var andArray = [] // 储存条件搜索的数组
  for (var i = query.length - 1; i >= 0; i--) {
    if (query[i] == '') {
      continue
    }
    var testSpecial  = isSpecial(query[i])
    // 通过 isSpecial 函数判断是否存在 > = <
    // console.log(testSpecial); 可以查看
    if (testSpecial != null) {
      andArray.push(testSpecial)

    } else {
      var regEx = new RegExp(query[i], 'ig')
      andArray.push({prj: regEx})
    }
  }

  if (andArray.length != 0){
    //如果 andArray 存在特殊字符 < = > ，将条件判断也作为条件发起查询
    return collections.find({$and: andArray}, {limit:limit})
  } else {
    // 如果不存在特殊字符 < = > 直接查询
    return collections.find({},{limit:limit})
  }
} 
```

`query`函数接受两个参数，一个是`collections`，一个是`queryString`，我们在前面通过 `query(Items, Session.get('queryString'))`传入了对应的参数。具体的详解可以参考代码的注释，接下来就是实现一下`isSpecial`函数了，就是判断用户输入的关键字是否存在`< = >`等字符

```js
function isSpecial(str){
  var relation
  var result = {}
  if (str.indexOf('<') != -1){
    //是否存在 < 号
    relation = str.split('<')
    // 存在的话，将其作为分割符把字符串分成两个部分 如 n_cmt<20 就会分成 n_cmt 和 20 两个部分
    result[relation[0]] = { $lt: Number(relation[1])}
    //这里的 relation[0]和 relation[1]分别代表上例中的 n_cmt 和 20
    return result
  } else if (str.indexOf('>') != -1){
    relation = str.split('>')
    result[relation[0]] = { $gt: Number(relation[1])}
    return result
  } else if (str.indexOf('=') != -1){
    relation = str.split('=')
    result[relation[0]] = relation[1]
    return result
  } else if (str.indexOf(':') != -1){
    relation = str.split(':')
    result[relation[0]] = new RegExp(relation[1], 'ig')
    return result
  } 
  return null;
} 
```

`isSpecial`主要是通过条件判断语句来确认用户输入的关键字是否存在`< = >`等特殊字符，这些都是用于条件查询，而最后的`:`判断则是为了满足正则表达式的查询。详细的代码思路可以看注释部分，其他的触类旁通。

到这里，其实 js 代码基本上就可以说是写完了。然后这个时候，我们需要回头看一下，在上面的`Template.body.helpers({})`我们就提到过模板用来展示内容，但是直到现在，我们还是没有模板文件(HTML 文件)来展示内容，所以我们现在来写一写：

SRsearch.html 的代码内容：

```js
<head>
  <title>SRSearch</title>
  <link rel="stylesheet" href="bootstrap.min.css">
  <link rel="stylesheet" href="bootstrap-theme.min.css">
</head>

<body>
</body> 
```

在上面的代码中，我们直接就去掉了<html>标签，因为 Meteor 会帮我们处理这件事。然后我们直接使用 bootstrap，就是为了节省时间。

基本架构有了，我门需要一个表单来提交用户输入的内容：

```js
 <div class="container">
    <h1>Source Repo Search</h1>
      <form class="form-horizontal" onsubmit="return false;">
      <div class="form-group">
        <input type="text" class="form-control" id="search-box" 
        placeholder="Type project keyword to search">
      </div>
      </form>
      <div id='help'>
        <small>Possible options: repo | b_time | e_time | span | vcs | n_cmt | n_peo </small><br>
        <small>Possible operators:  &gt; | = | &lt; | : </small><br>
        <small>RegEx supported.</small>
      </div>

  </div> 
```

在上面的 form 中，我们首先指定`onsubmit="return false;"`不让 form 执行默认提交。然后在 form 里面，我们给用户一个 input 输入框，这里需要注意的是，这个 input 的 id 必须和`Template.body.events({"keyup #search-box"})`中的选择器一致，不然就触发不了事件啦。而 id 为`help`的 div 里面，我们的目的只是给用户一些提示。表单写完之后，我们需要将搜索结果实时反馈出来：

```js
 <small>RegEx supported.</small>
      </div>
      {{#each items}}
        {{> item}}
      {{/each}}

  </div> 
```

在上面的代码中，我们需要关注的是下面这个部分：

```js
 {{#each items}}
     {{> item}}
{{/each}} 
```

还记得我们在`Template.body.helpers({})`指定的变量`items`么，这里的{`{#each items}}`就是循环输出那个 items 里面的每一个搜索结果。然后`{{> item}}`指定输出的模板，这里的`item` 为模板的名字，我们需要创建一个`name="item"`的模板：

```js
<template name="item">
    <dl class="dl-horizontal">
    <dt>Project</dt>            <dd>{{prj}}</dd>
    <dt>Repository</dt>         <dd>{{repo}}</dd>
    <dt>Time Span</dt>          <dd>{{b_time}} - {{e_time}}</dd>
    <dt>Version Control</dt>    <dd>{{vcs}}</dd>
    <dt># Commit</dt>           <dd>{{n_cmt}}</dd>
    <dt># People</dt>           <dd>{{n_peo}}</dd>
    <dt>Source Location</dt>    <dd>{{src_loc}}</dd>
    <dt>Log Location</dt>       <dd>{{log_loc}}</dd>
    <hr>
    </dl>
</template> 
```

我们使用<template>标签声明这是模板文件，这个标签放在<body>标签之内就可以了，对应的名字就是`item`，也就是我们`{{> item}}`指定的名字。所以当我们一旦有搜索放回结果的时候，我们会通过这个模板来展示我们每个结果的`{{prj}}`, `{{repo}}`等各个属性值。

到这里，全部的代码就编写完成了。现在就可以在项目的根目录执行`meteor run`，

```js
=> Started proxy.
=> Started MongoDB.
=> Started your app.

=> App running at: http://localhost:3000/ 
```

之后浏览器访问`http://localhost:3000/`就可以见证奇迹了。

## 作业

多尝试几次搜索，然后发现这个实现的过程中是否还有体验和功能都做得不够好的地方，比如条件匹配搜索还有没有可以改进的地方。考虑怎么改进