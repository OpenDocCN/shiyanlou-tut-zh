# 第 6 节 Ajax

## 一、摘要

本系列文章将带您进入 jQuery 的精彩世界,其中有很多作者具体的使用经验和解决方案,即使你会使用 jQuery 也能在阅读中发现些许秘籍。

本篇文章讲解如何使用 jQuery 方便快捷的实现 Ajax 功能。统一所有开发人员使用 Ajax 的方式。

## 二、前言

Ajax 让用户页面丰富起来,增强了用户体验。使用 Ajax 是所有 Web 开发的必修课。虽然 Ajax 技术并不复杂,但是实现方式还是会因为每个开发人员的而有所差异。jQuery 提供了一系列 Ajax 函数来帮助我们统一这种差异,并且让调用 Ajax 更加简单。

## 三、原始 Ajax 与 jQuery 中的 Ajax

首先通过实例，来看一下 jQuery 实现 Ajax 有多简单。下面是一个使用原始 Ajax 的示例：

```js
<!doctype html>
<html lang="zh">
<head>
  <meta charset="utf-8"/>
  <title>jQuery Ajax</title>
  <script src="jquery-1.11.2.min.js"></script>
  <script>
    $(function() {
      var xhr = new AjaxXmlHttpRequest();
      $("#btnAjaxOld").click(function(event) {
        var xhr = new AjaxXmlHttpRequest();
        xhr.onreadystatechange = function() {
          if (xhr.readyState == 4) {
            document.getElementById("divResult").innerHTML = xhr.responseText;
          }
        }
        //由于涉及到同源策略，需要服务器端的支持
        xhr.open("GET", "data/AjaxGetCityInfo.aspx?resultType=html", true);
        xhr.send(null);
      });
    });

    //跨浏览器获取 XmlHttpRequest 对象
    function AjaxXmlHttpRequest() {
      var xmlHttp;
      try {
        // Firefox, Opera 8.0+, Safari
        xmlHttp = new XMLHttpRequest();
      } catch (e) {
        // Internet Explorer
        try {
          xmlHttp = new ActiveXObject("Msxml2.XMLHTTP");
        } catch (e) {
          try {
            xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
          } catch (e) {
            alert("Your browser nonsupport AJAX！");
            return false;
          }
        }
      }
      return xmlHttp;
    }
  </script>
</head>
<body>    
  <button id="btnAjaxOld">original ajax call</button>
  <div id="divResult"></div>
</body>
</html> 
```

上面的实例中，`data/AjaxGetCityInfo.aspx?resultType=html`地址会返回一段 HTML 代码。

使用原始 Ajax，我们需要做较多的事情，比如创建`XmlHttpRequest`对象，判断请求状态，编写回调函数等。

而用 jQuery 的`Load`方法，只需要一句话：

```js
$("#divResult").load("data/AjaxGetCityInfo.aspx", { "resultType": "html" }); 
```

现在只是用 jQuery 的 Ajax 函数, 我的页面变得简洁了:

```js
<!doctype html>
<html lang="zh">
<head>
  <meta charset="utf-8"/>
  <title>jQuery Ajax</title>
  <script src="jquery-1.11.2.min.js"></script>
  <script>
    $(function() {            
      $("#btnAjaxJquery").click(function(event) {
        $("#divResult").load("data/AjaxGetCityInfo.aspx", { "resultType": "html" });
      });
    })        
  </script>
</head>
<body>    
  <button id="btnAjaxJquery">use jQuery load method</button>
  <div id="divResult"></div>
</body>
</html> 
```

## 四、jQuery Ajax 详解

jQuery 提供了几个用于发送 Ajax 请求的函数。其中最核心也是最复杂的是`jQuery.ajax(options)`，所有的其他 Ajax 函数都是它的一个简化调用。当我们想要完全控制 Ajax 时可以使用此结果，否则还是使用简化方法如`get`,`post`,`load`等更加方便。所以`jQuery.ajax(options)`方法放到最后一个介绍，先来介绍最简单的`load`方法：

#### **1\. load( url, [data], [callback] )**

**Returns:** jQuery 包装集

**说明:**

load 方法能够载入远程 HTML 文件代码并插入至 DOM 中。

默认使用 GET 方式, 如果传递了 data 参数则使用 Post 方式.

**讲解:**

`load`是最简单的 Ajax 函数，但是使用具有局限性：

1.  它主要用于直接返回 HTML 的 Ajax 接口
2.  `load`是一个 jQuery 包装集方法，需要在 jQuery 包装集上调用，并且会将返回的 HTML 加载到对象中，即使设置了回调函数也还是会加载

不过不可否认`load`接口设计巧妙并且使用简单。下面通过示例来演示`load`接口的使用:

```js
<!doctype html>
<html lang="zh">
<head>
  <meta charset="utf-8"/>
  <title>jQuery Ajax - Load</title>
  <script src="jquery-1.11.2.min.js"></script>
  <script>
    $(function() {
      $("#btnAjaxGet").click(function(event) {
        //发送 Get 请求
        $("#divResult").load("../data/AjaxGetMethod.aspx?param=btnAjaxGet_click" + "&timestamp=" + (new Date()).getTime());
      });

      $("#btnAjaxPost").click(function(event) {
        //发送 Post 请求
        $("#divResult").load("../data/AjaxGetMethod.aspx", { "param": "btnAjaxPost_click" });
      });

      $("#btnAjaxCallBack").click(function(event) {
        //发送 Post 请求, 返回后执行回调函数.
        $("#divResult").load("../data/AjaxGetMethod.aspx", { "param": "btnAjaxCallBack_click" }, function(responseText, textStatus, XMLHttpRequest)
        {
          responseText = " Add in the CallBack Function! <br/>" + responseText
          $("#divResult").html(responseText); //或者: $(this).html(responseText);
        });
      });

      $("#btnAjaxFiltHtml").click(function(event) {
        //发送 Get 请求, 从结果中过滤掉 "anshan" 这一项
        $("#divResult").load("../data/AjaxGetCityInfo.aspx?resultType=html" + "&timestamp=" + (new Date()).getTime() + " ul>li:not(:contains('anshan'))");
      });
    });
  </script>
</head>
<body>    
  <button id="btnAjaxGet">use load execute get request</button><br />
  <button id="btnAjaxPost">use load execute post request</button><br />
  <button id="btnAjaxCallBack">use has callback load method</button><br />
  <button id="btnAjaxFiltHtml">use selector filter response HTML</button>
  <br />
  <div id="divResult"></div>
</body>
</html> 
```

**提示:我们要时刻注意浏览器缓存, 当使用 GET 方式时要添加时间戳参数 (new Date()).getTime() 来保证每次发送的 URL 不同,可以避免浏览器缓存。**

**提示: 当在 url 参数后面添加了一个空格, 比如" "的时候,会出现"无法识别符号"的错误,请求还是能正常发送。但是无法加载 HTML 到 DOM. 删除后问题解决。**

#### **2\. jQuery.get( url, [data], [callback], [type] )**

**Returns:** XMLHttpRequest

**说明:**

通过远程 HTTP GET 请求载入信息。

这是一个简单的 GET 请求功能以取代复杂\$.ajax 。请求成功时可调用回调函数。如果需要在出错时执行函数，请使用 \$.ajax。

**讲解:**

此函数发送 Get 请求, 参数可以直接在 url 中拼接, 比如:

```js
$.get("../data/AjaxGetMethod.aspx?param=btnAjaxGet_click"); 
```

或者通过 data 参数传递：

```js
$.get("../data/AjaxGetMethod.aspx", { "param": "btnAjaxGet2_click" }); 
```

两种方式效果相同，data 参数会自动添加到请求的 url 中。

如果 url 中的某个参数，又通过 data 参数传递，不会自动合并相同名称的参数。

回调函数签名如下：

```js
function (data, textStatus) {
    // data could be xmlDoc, jsonObj, html, text, etc...
    this; // the options for this ajax request
} 
```

其中 data 是返回的数据，textStatus 表示状态码，可能是如下值：

**"timeout","error","notmodified","success","parsererror"**

type 参数是指 data 数据的类型, 可能是下面的值: **"xml", "html", "script", "json", "jsonp", "text"**

**默认为"html"**

`jQuery.getJSON( url, [data], [callback] )` 方法就相当于 `jQuery.get(url, [data],[callback], "json")`

#### **3\. jQuery.getJSON( url, [data], [callback] )**

**Returns:** XMLHttpRequest

相当于: `jQuery.get(url, [data], [callback], "json")`

**说明:**

通过 HTTP GET 请求载入 JSON 数据。

在 jQuery 1.2 中，您可以通过使用`JSONP`形式的回调函数来加载其他网域的`JSON 数据`，如 `"myurl?callback=?"`。jQuery 将自动替换 ? 为正确的函数名，以执行回调函数。

**注意：此行以后的代码将在这个回调函数执行前执行。**

**讲解:**

getJSON 函数仅仅将 get 函数的 type 参数设置为"JSON"而已. 在回调函数中获取的数据已经是按照 JSON 格式解析后的对象了:

```js
$.getJSON(
"../data/AjaxGetCityInfo.aspx", 
{ "resultType": "json" },
function(data, textStatus){
    alert(data.length);
    alert(data[0].CityName);
}); 
```

#### **4\. jQuery.getScript( url, [callback] )**

**Returns:** XMLHttpRequest

相当于: `jQuery.get(url, null, [callback], "script")`

**说明:**

通过 HTTP GET 请求载入并执行一个 JavaScript 文件。

jQuery 1.2 版本之前，`getScript` 只能调用同域 JS 文件。 1.2 中，您可以跨域调用 JavaScript 文件。注意：Safari 2 或更早的版本不能在全局作用域中同步执行脚本。如果通过 getScript 加入脚本，请加入延时函数。

**讲解:**

首先了解此函数的 jQuery 内部实现, 仍然使用`get`函数, jQuery 所有的 Ajax 函数包括`get`最后都是用的是`jQuery.ajax()`, getScript 将传入值为"script"的 type 参数, 最后在 Ajax 函数中对 type 为 script 的请求做了如下处理:

```js
var head = document.getElementsByTagName("head")[0];            
var script = document.createElement("script");
script.src = s.url; 
```

上面的代码动态建立了一个 script 语句块，并且将其加入到 head 中：

`head.appendChild(script);`

当脚本加载完毕后，再从 head 中删除：

```js
// Handle Script loading
if ( !jsonp ) {
    var done = false;
    // Attach handlers for all browsers
    script.onload = script.onreadystatechange = function(){
            if ( !done && (!this.readyState ||this.readyState == "loaded" || this.readyState == "complete") ) {
            done = true;
            success();
            complete();
            // Handle memory leak in IE
            script.onload = script.onreadystatechange = null;
            head.removeChild( script );
        }
    };
} 
```

演示如何使用 getScript 函数:

```js
$("#btnAjaxGetScript").click(function(event){
    $.getScript("../scripts/getScript.js", function(data, textStatus){
        alert(data);
        alert(textStatus);
        alert(this.url);
    });
});

$("#btnAjaxGetScriptCross").click(function(event){
    $.getScript("http://resource.elong.com/getScript.js", function(data, textStatus){
        alert(data);
        alert(textStatus);
        alert(this.url);
    });
}); 
```

#### **5\. jQuery.post( url, [data], [callback], [type] )**

**Returns:** XMLHttpRequest

**说明:**

通过远程 HTTP POST 请求载入信息。

这是一个简单的 POST 请求功能以取代复杂 `$.ajax` 。请求成功时可调用回调函数。如果需要在出错时执行函数，请使用 `$.ajax`。

**讲解:**

具体用法和`get`相同, 只是提交方式由`"GET"`改为`"POST"`.

#### **6\. jQuery.ajax( options )**

**Returns: **XMLHttpRequest

**说明:**

通过 HTTP 请求加载远程数据。

jQuery 底层 AJAX 实现。简单易用的高层实现见 `$.get`, `$.post` 等。

`$.ajax()`返回其创建的 `XMLHttpRequest` 对象。大多数情况下你无需直接操作该对象，但特殊情况下可用于手动终止请求。

`$.ajax()` 只有一个参数：参数 `key/value` 对象，包含各配置及回调函数信息。详细参数选项见下。

注意： 如果你指定了 `dataType` 选项，请确保服务器返回正确的`MIME`信息，(如 `xml` 返回`"text/xml"`)。错误的`MIME`类型可能导致不可预知的错误。

**注意：**如果`dataType`设置为`"script"`，那么所有的远程(不在同一域名下)的`POST`请求都将转化为`GET`请求。(因为将使用 DOM 的 script 标签来加载)

jQuery 1.2 中，您可以跨域加载 `JSON 数据`，使用时需将数据类型设置为 `JSONP`。使用 `JSONP` 形式调用函数时，如 `"myurl?callback=?"`jQuery 将自动替换`?` 为正确的函数名，以执行回调函数。数据类型设置为 `jsonp` 时，jQuery 将自动调用回调函数。

**讲解:**

这是 jQuery 中 Ajax 的核心函数,上面所有的发送 Ajax 请求的函数内部最后都会调用此函数。options 参数支持很多参数, 使用这些参数可以完全控制 ajax 请求。 在 Ajax 回调函数中的 this 对象也是 options 对象。

## 五、Ajax 相关函数

jQuery 提供了一些相关函数能够辅助 Ajax 函数。

#### **1\. jQuery.ajaxSetup( options )**

无返回值

**说明:**

设置全局 AJAX 默认 options 选项。

**讲解:**

有时我们希望设置页面上所有 Ajax 属性的默认行为.那么就可以使用此函数设置`options`选项, 此后所有的 Ajax 请求的默认`options`将被更改。

比如在页面加载时, 我使用下面的代码设置 Ajax 的默认 option 选项:

```js
 $.ajaxSetup({
    url: "../data/AjaxGetMethod.aspx",
    data: { "param": "ziqiu.zhang" },
    global: false,
    type: "POST",
    success: function(data, textStatus) { $("#divResult").html(data); }
}); 
```

此后我们可以使用无参数的`get()`,`post()`或者`ajax()`方法发送 ajax 请求.完整的示例代码如下:

```js
<!doctype html>
<html lang="zh">
<head>
  <meta charset="utf-8"/>
  <title>jQuery Ajax - Load</title>
  <script src="jquery-1.11.2.min.js"></script>
  <script>
    $(document).ready(function() {
      $.ajaxSetup({
          url: "../data/AjaxGetMethod.aspx",
          data: { "param": "ziqiu.zhang" },
          global: false,
          type: "POST",
          success: function(data, textStatus) {
            $("#divResult").html(data);
          }
      });
      $("#btnAjax").click(function(event) { $.ajax(); });
      $("#btnGet").click(function(event) { $.get(); });
      $("#btnPost").click(function(event) { $.post(); });
      $("#btnGet2").click(function(event) { $.get("../data/AjaxGetMethod.aspx",{ "param": "other" }); });
    });
  </script>
</head>  
<body>    
  <button id="btnAjax">nontransfer param call ajax() method</button><br />
  <button id="btnGet">nontransfer param call get() method</button><br />
  <button id="btnPost">nontransfer param call post() method</button><br />
  <button id="btnGet2">transfer param call get() method , use global default callback</button><br />
  <br />
  <div id="divResult"></div>
</body>
</html> 
```

注意当使用`get()`或者`post()`方法时,除了 type 参数将被重写为`GET`或者`POST`外, 其他参数只要不传递都是使用默认的全局`option`。如果传递了某一个选项, 比如最后一个按钮传递了`url`和参数,则本次调用会以传递的选项为准。没有传递的选项比如回调函数还是会使用全局`option`设置值。

#### **2.serialize( )**

**Returns:** String

**说明:**

序列表表格内容为字符串，用于 Ajax 请求。

序列化最常用在将表单数据发送到服务器端时。被序列化后的数据是标准格式, 可以被几乎所有的服务器端支持。

为了尽可能正常工作, 要求被序列化的表单字段都有 name 属性, 只有一个 eid 是无法工作的。

像这样写 name 属性: `<input id="email" name="email" type="text" />`

**讲解:**

`serialize()`函数将要发送给服务器的`form`中的表单对象拼接成一个字符串。便于我们使用 Ajax 发送时获取表单数据。这和一个`form`按照`Get`方式提交时, 自动将表单对象的名/值放到`url`上提交差不多。

#### **3.serializeArray( )**

**Returns:** Array< Object>

**说明：**

使用此函数获取到的是 JSON 对象,但是 jQuery 中没有提供将 JSON 对象转化为 JSON 字符串的方法。因此需要借助于插件，如原文中提到的`jquery.json`这个插件

## 六、全局 Ajax 事件

在`jQuery.ajaxSetup( options )` 中的`options`参数属性中,有一个`global`属性:

**global**

**类型:**布尔值

**默认值:** true

**说明:**是否触发全局的 Ajax 事件.

这个属性用来设置是否触发全局的 Ajax 事件。全局 Ajax 事件是一系列伴随 Ajax 请求发生的事件。主要有如下事件:

![Alt text](img/AjaxEvent.jpg)

用一个示例讲解各个事件的触发顺序：

```js
<!doctype html>
<html lang="zh">
<head>
  <meta charset="utf-8"/>
  <title>jQuery Ajax - AjaxEvent</title>
  <script src="jquery-1.11.2.min.js"></script>
  <script>
    $(document).ready(function() {
      $("#btnAjax").bind("click", function(event) {
        $.get("../data/AjaxGetMethod.aspx");
      });
      $("#divResult").ajaxComplete(function(evt, request, settings) { $(this).append('<div>ajaxComplete</div>'); });
      $("#divResult").ajaxError(function(evt, request, settings) { $(this).append('<div>ajaxError</div>'); });
      $("#divResult").ajaxSend(function(evt, request, settings) { $(this).append('<div>ajaxSend</div>'); });
      $("#divResult").ajaxStart(function() { $(this).append('<div>ajaxStart</div>'); });
      $("#divResult").ajaxStop(function() { $(this).append('<div>ajaxStop</div>'); });
      $("#divResult").ajaxSuccess(function(evt, request, settings) { $(this).append('<div>ajaxSuccess</div>'); });
    });
  </script>
</head>
<body>    
  <br /><button id="btnAjax">send Ajax request</button><br/>
  <div id="divResult"></div>
</body>
</html> 
```

我们可以通过将默认`options`的`global`属性设置为`false`来取消全局 Ajax 事件的触发。

## 七、注意事项

如果在 Get 请求发送的 url 中有两个同名参数, 比如两个 param 参数:

`http://localhost/AjaxGetMethod.aspx?param=Multiple&param=Multiple3`

使用服务器端方法获取 param 参数:

```js
if (!String.IsNullOrEmpty(HttpContext.Current.Request["Param"])) {
    param = HttpContext.Current.Request["Param"];
} 
```

此时获取到得`param`是一个用","分隔多个值的字符串:

`Multiple,Multiple3`

## 八、总结

本文介绍如何使用 jquery 实现 Ajax 功能。用于发送 Ajax 请求的相关函数如`load`, `get`, `getJSON`和`post`这些简便 Ajax 方法,对于核心的`ajax`方法没有过多介绍, 主要是通过配置复杂的参数实现完全控制 Ajax 请求。另外讲解了 ajax 的辅助函数比如用于序列化表单对象为字符串的`serialize()`方法,用于将表单对象序列化为 JSON 对象的`serializeArray()`方法。这些在使用脚本获取数据实现与服务器端交互时很有用,JSON 格式的数据在处理大对象编程时将我们从混乱的属性字符串中解放出来。

jQuery 还提供录入全局 ajax 事件这一个特殊的事件,并且可以在一个对象上设置这些事件, 在发送 Ajax 请求的各个生命周期上会调用这些事件,可以通过修改默认的`options`对象的`global`属性打开或关闭全局事件。

## 九、练习

请结合服务器端实现上面的操作。