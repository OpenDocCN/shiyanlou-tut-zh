# 第 3 节 增强型表单标签

### 注意

实验中如需使用 jQuery，可以直接引入：

```
http://labfile.oss.aliyuncs.com/jquery/1.11.1/jquery.min.js 
```

## 一、增型表单标签

HTML5 中，新标准把文本框提示信息、表单校验、日期选择控件、颜色选择控件、范围控件、进度条、标签跨表单等功能直接加入新的表单标签中。 但在众多现代浏览器中，最新版本的 Opera 浏览器对新型表单的支持才最为完美。

### 1\. Number 类型 input 标签

```
<input type="number" name="demoNumber" min="1" max="100" step="1"/> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

**name:** 标识表单提交时的 key 值 **min:** 标识当前输入框输入的最小值 **max:** 标识当前输入框输入的最大值 **step:** 标识点击增大/减小的时候，增加/减小的步长

### 2\. Email 类型 input 标签

```
<input type="email" name="email" placeholder="请输入注册邮箱"/> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

当表单在提交前，此文本框会自动校验是否符合邮箱的正则表达式。

### 3\. URL 类型的 input 标签

```
<input type="url" placeholder="请输入网址" name="url"/> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

### 4\. Tel 类型的 input 标签

```
<input type="tel" placeholder="输入电话" name="phone"/> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

### 5\. range 类型的 input 标签

```
<input type="range" min="0" max="50" step="5" name="rangedemo" value="0"/> 
```

此类型标签的加入，输入范围内的数据变得非常简单容易，而且非常标准，用户输入可感知体验非常好。另外此标签可以跟表单新增加的 output 标签一块使用，达到一个联动的效果。

```
<form oninput="output.value=parseInt(range.value)"/>
    <input type="range" min="0" max="100" step="5" name="range" value="0"/>
    <output name="output">0<output/>
</form> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

### 6\. 新的日期、时间、月份、星期 input 标签

Web 项目开发，一定会遇到相关的 js 日期控件，在 HTML5 中新加入的表单属性将会使 Web 开发变得更加简洁。

```
<input type="date" name="datedemo"/> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

相关的日期属性还包括：`month`、`time`、`week`、`datetime-local`、`datetime`

### 7\. 颜色选择 input 标签

```
<input type="color" name="colordemo"/> 
```

### 8\. input 标签自动完成功能

有的项目会要求实现自动完成或者输入提示功能，在 HTML5 的支持下将变得简单。

```
<input type="text" autocomplete="on" name="demoAutoComplete" list="autoNames" />
<datalist id="autoNames">
       <option  value="实验楼" ></option>
       <option  value="HTML5" ></option>
       <option  value="input 标签" ></option>
</datalist> 
```

## 二、HTML5 表单新属性

### 1\. input 表单新增加的特有属性

*   `autofocus`属性，demo:`<input type="text" autofocus="autofocus"/>`此属性可以设置当前页面中 input 标签加载完毕后获得焦点。

*   `max`、`min`、`step`：这些上面介绍过，都是跟数字相关。

*   `placeholder`：提示信息属性。

*   `multiple`：用于文件上传控件，设置此属性后，允许多个文件。

*   校验属性：设置了`required`属性后预示着当前文本框在提交前必须有数据输入，而这一切都是由浏览器自动完成。还添加了`pattern`正则表达式校验：

```
<input type="text" autofocus="autofocus" required pattern="\d+"/> 
```

*   另外一个较大的改进就是增加了`form`属性，也就是说，任何一个标签都可以指定它所属于一个表单，而不是必须在<form></form>中进行包裹了。

```
<input type="text" form="demoform" name="demo"/> 
```

### 2\. form 表单新增加的属性

*   `novalidate`属性规定在提交表单时不应该验证 `form`或`input`域

```
<form action="" method="POST" novalidate="true"></form> 
```

*   `autocomplete`属性规定`form`或`input`域应该拥有自动完成功能

## 三、综合实例

```
<fieldset>
    <legend>表单演示：最新 Opera 浏览器支持效果良好</legend>
    <form action="" method="POST" id="form1" oninput="output.value=parseInt(range.value)">
        <input type="text" autofocus="autofocus" required pattern="\d+" name="auto" placeholder="必填项测试" />
        <input type="number" name="demoNumber" min="1" max="100" step="2" />
        <input type="email" placeholder="请输入邮箱" name="mail" />
        <input type="url" name="url" placeholder="输入正确的网址" />
        <br />
            日期：<input type="datetime" name="time" />
            颜色：<input type="color" name="color" /><br />
        <br />
        <input type="range" min="0" max="50" step="5" name="range" value="0" />
        <output name="output">0</output>
        <br />
        <input type="submit" value="提交表单" />
    </form>
    表单外的 input 标签：
    <input type="text" form="form1" name="demo" />
</fieldset> 
```

## 四、总结

在 HMTL5 的新版本表单中的确添加了很多常用的功能，然后浏览器帮我们实现了很多我们原先需要自己用 js 实现的一些功能。对开发者来说，这的确是很不错的一件事情。

## ## 一、文件操作 API

在之前我们操作本地文件都是使用 flash、silverlight 或者第三方的 activeX 插件等技术，由于使用了这些技术后就很难进行跨平台、或者跨浏览器、跨设备等情况下实现统一的表现，从另外一个角度来说就是让我们的 web 应用依赖了第三方的插件，而不是很独立。 在 HTML5 标准中，默认提供了操作文件的 API 让这一切直接标准化。有了操作文件的 API，让我们的 Web 应用可以很轻松的通过 JS 来控制文件的读取、写入、文件夹、文件等一系列的操作。接下来主要给大家介绍文件读取的几个 API。

## ## 二、几个重要的 JS 对象

### 1\. FileList 对象

它是 File 对象的一个集合，在 HTML4 标准中文件上传控件只接受一个文件，而在新标准中，只需要设置`multiple`，就支持多文件上传，所以从此标签中获取的`files`属性就是`FileList`对象实例。 demo：`<input type="file" multiple="multiple" name="fileDemo" id="fileDemo" />`; 下面是关于`FileList`对象的 API 的原型：

```
interface FileList {
      getter File? item(unsigned long index);
      readonly attribute unsigned long length;
}; 
```

### 2\. Blob 对象

其实就是一个原始数据对象，它提供了`slice`方法可以读取原始数据中的某块数据。另外有两个属性：`size`（数据的大小），`type`（数据的 MIME 类型; 看下面的是 W3C 的 API 原型:

```
interface Blob {
    readonly attribute unsigned long long size;
    readonly attribute DOMString type;
    //slice Blob into byte-ranged chunks     
    Blob slice(
        optional long long start,
        optional long long end,
        optional DOMString contentType
    ); 
}; 
```

### 3\. File 对象

继承自 Blob 对象，指向一个具体的文件，它还有两个属性：`name`（文件名）,`lastModifiedDate`（最后修改时间）; W3C 的标准：

```
interface File : Blob {
    readonly attribute DOMString name;
    readonly attribute Date lastModifiedDate;
}; 
```

### 4\. FileReader 对象

设计用来读取文件里面的数据，提供三个常用的读取文件数据的方法，另外读取文件数据使用了异步的方式，非常高效。 W3C 的标准：

```
[Constructor]
interface FileReader: EventTarget {
    // async read methods
    void readAsArrayBuffer(Blob blob);
    void readAsBinaryString(Blob blob);
    void readAsText(Blob blob, optional DOMString encoding);
    void readAsDataURL(Blob blob);

    void abort();

    // states  
    const unsigned short EMPTY = 0;
    const unsigned short LOADING = 1;
    const unsigned short DONE = 2;

    readonly attribute unsigned short readyState;

    // File or Blob data
    readonly attribute any result;
    readonly attribute DOMError error;

    // event handler attributes
    attribute [TreatNonCallableAsNull] Function? onloadstart;
    attribute [TreatNonCallableAsNull] Function? onprogress;
    attribute [TreatNonCallableAsNull] Function? onload;
    attribute [TreatNonCallableAsNull] Function? onabort;
    attribute [TreatNonCallableAsNull] Function? onerror;
    attribute [TreatNonCallableAsNull] Function? onloadend;
}; 
```

这个对象是非常重要第一个对象，它提供了四个读取文件数据的方法，这些方法都是异步的方式读取数据，读取成功后就直接将结果放到属性`result`中。所以一般就是直接读取数据，然后监听此对象的`onload`事件，然后在事件里面读取`result`属性，再做后续处理。当然`abort`就是停止读取的方法。

**FileReader 对象的三个读取文件数据方法**

*   `readAsBinaryString(Blob blob)` 传入一个 Blob 对象，然后读取数据的结果作为二进制字符串的形式放到 FileReader 的 result 属性中。
*   `readAsText(Blob blob, optional DOMString encoding)` 第一个参数传入 Blog 对象，然后第二个参数传入编码格式，异步将数据读取成功后放到 result 属性中，读取的内容是普通的文本字符串的形式。
*   `readAsDataURL(Blob blob)` 传入一个 Blob 对象，读取内容可以做为 URL 属性，也就是说可以将一个图片的结果指向给一个 img 的 src 属性。

## ## 三、读取文件上传控件里的文件并将内容已不同的方式展现到浏览器

在展示代码之前，之前我们操作一个图片文件，都是先将图片上传到服务器端，然后再使用一个`img`标签指向到服务器的`url`地址，然后再进行一个使用第三方插件进行图片处理，而现在这一切都不需要服务器端了，因为`FileReader`对象提供的几个读取文件的方法变得异常简单，而且全部是客户端 js 的操作。

实例一：获取上传文件的文件名（注：需要引入 jQuery）

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html >
<head>
    <title></title>
    <script src="Scripts/jquery-1.5.1.js" type="text/javascript"></script>
    <script type="text/javascript">
        $(function () {
            $("#btnGetFile").click(function (e) {
                var fileList = document.getElementById("fileDemo").files;
                for (var i = 0; i < fileList.length; i++) {
                    if (!(/image\/\w+/.test(fileList[i].type))) {
                         $("#result").append("<span>type:"+fileList[i].type+"--******非图片类型*****--name:"+fileList[i].name+"--size:"+fileList[i].size+"</span><br />");
                    }
                    else {
                        $("#result").append("<span>type:"+fileList[i].type+"--name:"+fileList[i].name+"--size:"+fileList[i].size+"</span><br />");
                    }
                }
            });
        });
    </script>
</head>
<body>
    <form action="/home/index" method="POST" novalidate="true">
        <input type="file" multiple="multiple" name="fileDemo" id="fileDemo" /><br/>
        <input type="button" value="获取文件的名字" id="btnGetFile"/>
        <div id="result"></div>
    </form>
    <hr/>
</body>
</html> 
```

实例二：读取上传文件内容，然后将文件内容直接读取到浏览器上（注：需要引入 jQuery）

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html >
<head>
    <title></title>
    <script src="Scripts/jquery-1.5.1.js" type="text/javascript"></script>
    <script type="text/javascript">
        if(typeof FileReader == "undified") {
            alert("您老的浏览器不行了！");
        }

        function showDataByURL() {
            var resultFile = document.getElementById("fileDemo").files[0];
            if (resultFile) {
                var reader = new FileReader();
                reader.readAsDataURL(resultFile);
                reader.onload = function (e) {
                    var urlData = this.result;
                    document.getElementById("result").innerHTML += "<img src='" + urlData + "' alt='" + resultFile.name + "' />";
                }; 
            }
        } 

        function showDataByBinaryString() {
            var resultFile = document.getElementById("fileDemo").files[0];
            if (resultFile) {
                var reader = new FileReader();
                //异步方式，不会影响主线程
                reader.readAsBinaryString(resultFile);
                reader.onload = function(e) {
                    var urlData = this.result;
                    document.getElementById("result").innerHTML += urlData;
                };
            }
        }

        function showDataByText() {
            var resultFile = document.getElementById("fileDemo").files[0];
            if (resultFile) {
                var reader = new FileReader();
                reader.readAsText(resultFile,'gb2312');
                reader.onload = function (e) {
                    var urlData = this.result;
                    document.getElementById("result").innerHTML += urlData;
                };
            }
        }

    </script>
</head>
<body>
    <input type="file" name="fileDemo" id="fileDemo" multep/>
    <input type="button" value="readAsDataURL" id="readAsDataURL" onclick="showDataByURL();"/>
    <input type="button" value="readAsBinaryString"  id="readAsBinaryString" onclick="showDataByBinaryString();"/>
    <input type="button" value="readAsText"  id="readAsText" onclick="showDataByText();"/>
    <div id="result">
    </div>
</body>
</html> 
```

## ## 四、总结

有了文件操作的 API 后，让 JS 进一步的操作本地文件的得到空前的加强，HTML5 对于客户端 Web 应用得到进一步功能的提升，HTML5 的趋势让 Web 更加富客户端化，而这些都需要让我们的 HTML 或者 JS 变得更加强大，而 HTML5 正是适时地推出了 File API。

## ## 一、认识 Canvas

Canvas 就是一个画布，可以进行画任何的线、图形、填充等一系列的操作，而且操作的画图就是 js，所以让 js 编程到了嗑药的地步。另外 Canvas 不仅仅提供简单的二维矢量绘图，也提供了三维的绘图，以及图片处理等一系列的 api 支持。

## ## 二、Canvas 的 Context 对象

1.  要使用 Canvas 来绘制图形必须在页面中添加 Canvas 的标签

```
<canvas id="demoCanvas" width="500" height="500">
    <p>
      请使用支持 HTML5 的浏览器查看本实例
    </p>
</canvas> 
```

1.  只有上面的标签，只能是创建好了一个画布，其中 width 和 height 属性就是设置画布的大小。Id 属性也是必须的，后面要用 Id 来拿到当前的 Canvas 的 Dom 对象。通过此 Canvase 的 Dom 对象就可以获取他的上下文（Context）了，Canvas 绘制图形都是靠着 Canvas 对象的上下文对象

```
<script type="text/javascript">
    //第一步：获取 canvas 元素
    var canvasDom = document.getElementById("demoCanvas");
    //第二步：获取上下文
    var context = canvasDom.getContext('2d');
</script> 
```

1.  Context 上下文默认两种绘制方式： 第一种：绘制线（stroke） 第二种：填充（fill）

## ## 三、Canvas 绘制立体透明矩形

### 1\. Canvas 绘制步骤：

*   创建 HTML 页面，设置画布标签
*   编写 js，获取画布 dom 对象
*   通过 canvas 标签的 Dom 对象获取上下文
*   设置绘制样式、颜色
*   绘制矩形，或者填充矩形

代码如下：

```
<body>
    <canvas id="demoCanvas" width="500" height="500">
        <p>请使用支持 HTML5 的浏览器查看本实例</p>
    </canvas>
    <!---下面将演示一种绘制矩形的 demo--->
    <script type="text/javascript">
        //第一步：获取 canvas 元素
        var canvasDom = document.getElementById("demoCanvas");
        //第二步：获取上下文
        var context = canvasDom.getContext('2d');
        //第三步：指定绘制线样式、颜色
        context.strokeStyle = "red";
        //第四步：绘制矩形，只有线。内容是空的
        context.strokeRect(10, 10, 190, 100);
        //以下演示填充矩形。
        context.fillStyle = "blue";
        context.fillRect(110,110,100,100);
    </script>
</body> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

## ## 四、Canvas 绘制线条

Context 对象的 beginPath 方法表示开始绘制路径，moveTo(x, y)方法设置线段的起点，lineTo(x, y)方法设置线段的终点，stroke 方法用来给透明的线段着色。moveto 和 lineto 方法可以多次使用。最后，还可以使用 closePath 方法，自动绘制一条当前点到起点的直线，形成一个封闭图形，省却使用一次 lineto 方法。

代码如下：

```
<body>
    <canvas id="demoCanvas" width="500" height="600">
    </canvas>
    <script type="text/javascript">
        //通过 id 获得当前的 Canvas 对象
        var canvasDom = document.getElementById("demoCanvas");
        //通过 Canvas Dom 对象获取 Context 的对象
        var context = canvasDom.getContext("2d");
        context.beginPath(); // 开始路径绘制
        context.moveTo(20, 20); // 设置路径起点，坐标为(20,20)
        context.lineTo(200, 200); // 绘制一条到(200,20)的直线
        context.lineTo(400, 20);
        context.closePath();
        context.lineWidth = 2.0; // 设置线宽
        context.strokeStyle = "#CC0000"; // 设置线的颜色
        context.stroke(); // 进行线的着色，这时整条线才变得可见
    </script>
</body> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

## ## 五、Canvas 绘制文本

Context 上下文对象的 fillText(string, x, y)方法是用来绘制文本，它的三个参数分别为文本内容、起点的 x 坐标、y 坐标。使用之前，需用 font 设置字体、大小、样式（写法类似与 CSS 的 font 属性）。与此类似的还有 strokeText 方法，用来添加空心字。另外注意一点：fillText 方法不支持文本断行，即所有文本出现在一行内。所以，如果要生成多行文本，只有调用多次 fillText 方法。

代码如下：

```
<canvas id="demoCanvas" width="500" height="600"></canvas>
<script type="text/javascript">
    //通过 id 获得当前的 Canvas 对象
    var canvasDom = document.getElementById("demoCanvas");
    //通过 Canvas Dom 对象获取 Context 的对象
    var context = canvasDom.getContext("2d");
    context.moveTo(200,200);
    // 设置字体
    context.font = "Bold 50px Arial";
    // 设置对齐方式
    context.textAlign = "left";
    // 设置填充颜色
    context.fillStyle = "#005600";
    // 设置字体内容，以及在画布上的位置
    context.fillText("老马!", 10, 50);
    // 绘制空心字
    context.strokeText("blog.itjeek.com!", 10, 100);
</script> 
```

## ## 六、Canvas 绘制圆形和椭圆

Context 上下文的 arc 方法就是绘制圆形或者椭圆，arc 方法的 x 和 y 参数是圆心坐标，radius 是半径，startAngle 和 endAngle 则是扇形的起始角度和终止角度（以弧度表示），anticlockwise 表示做图时应该逆时针画（true）还是顺时针画（false）。

代码如下：

```
<canvas id="demoCanvas" width="500" height="600"></canvas>
<script type="text/javascript">
    //通过 id 获得当前的 Canvas 对象
    var canvasDom = document.getElementById("demoCanvas");
    //通过 Canvas Dom 对象获取 Context 的对象
    var context = canvasDom.getContext("2d");
    context.beginPath();//开始绘制路径
    //绘制以 （60,60）为圆心，50 为半径长度，从 0 度到 360 度（PI 是 180 度），最后一个参数代表顺时针旋转。
    context.arc(60, 60, 50, 0, Math.PI * 2, true);
    context.lineWidth = 2.0;//线的宽度
    context.strokeStyle = "#000";//线的样式
    context.stroke();//绘制空心的，当然如果使用 fill 那就是填充了。
</script> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

## ## 七、Canvas 绘制图片

Canvas 绘制图片应该是他的一大特点或者是亮点吧。当然配合 File 的 API，让 JS 变得无可匹敌。那接下里给大家演示一下怎样绘制图片，并且做出一个立体效果出来。

代码如下：

```
<canvas id="demoCanvas" width="500" height="600"></canvas>
<script type="text/javascript">
    //通过 id 获得当前的 Canvas 对象
    var canvasDom = document.getElementById("demoCanvas");
    //通过 Canvas Dom 对象获取 Context 的对象
    var context = canvasDom.getContext("2d");
    var image = new Image();//创建一张图片
    image.src = "Images/a.jpg";//设置图片的路径
    image.onload = function() {//当图片加载完成后
     for (var i = 0; i < 10; i++) {//输出 10 张照片
            //参数：（1）绘制的图片  （2）坐标 x，（3）坐标 y
            context.drawImage(image, 100 + i * 80, 100 + i * 80);
        }
    };
</script> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

## ## 八、总结

Canvas 总算介绍了最基本的用法了。当然本文大量借鉴了其他网站的例子。作者也是把最基本的精华部分罗列了一下。总体来说 Canvas 绘制图片和文本、形状都不是很难，API 代码上手难度基本就是初级。但是由此而带来的非常多的可能，让 Html5 真正的强大到无可比拟的地步。

当然本文并没有涉及到 Canvas3D 绘制的相关内容，而且关于 Canvas 绘制渐变色、绘制阴影、图片的相关处理操作等，这些内容，如果读者确实需要的可以自行搜索查找相关资料或者直接阅读 Html5 的最新标准文档。

## 一、本地存储由来的背景

由于 HTML4 时代`Cookie`的大小、格式、存储数据格式等限制，网站应用如果想在浏览器端存储用户的部分信息，那么只能借助于`Cookie`。但是`Cookie`的这些限制，也就导致了`Cookie`只能存储一些 ID 之类的标识符等简单的数据。

下面是`Cookie`的限制：

*   大多数浏览器支持最大为 4096 字节的 `Cookie`。

*   浏览器还限制站点可以在用户计算机上存储的 `Cookie` 的数量。大多数浏览器只允许每个站点存储 20 个`Cookie`；如果试图存储更多 `Cookie`，则最旧的 `Cookie` 便会被丢弃。

*   有些浏览器还会对它们将接受的来自所有站点的 `Cookie` 总数作出绝对限制，通常为 300 个。

*   `Cookie`默认情况都会随着 Http 请求发送到后台服务器，但并不是所有请求都需要`Cookie`的，比如：js、css、图片等请求则不需要`Cookie`。

为了破解`Cookie`的一系列限制，HTML5 通过 JS 的新的 API 就能直接存储大量的数据到客户端浏览器，而且支持复杂的本地数据库，让 JS 更有效率。 HTML5 支持两种的`WebStorage`：

*   永久性的本地存储（`localStorage`）
*   会话级别的本地存储（`sessionStorage`）

## 二、永久性的本地存储：localStorage

在最新的 JS 的 API 中增加了`localStorage`对象，以便于用户存储永久存储的 Web 端的数据。而且数据不会随着 Http 请求发送到后台服务器，而且存储数据的大小机会不用考虑，因为在 HTML5 的标准中要求浏览器至少要支持到 4MB.所以，这完全是颠覆了`Cookie`的限制，为 Web 应用在本地存储复杂的用户痕迹数据提供非常方便的技术支持。接下来就将介绍`localStorage`的常用的方法。

`localStorage`提供了四个方法来辅助我们进行对本地存储做相关操作。

*   `setItem(key,value)`添加本地存储数据。两个参数，非常简单就不说了。
*   `getItem(key)`通过 key 获取相应的 Value。
*   `removeItem(key)`通过 key 删除本地数据。
*   `clear()`清空数据。

代码如下：

```
<script type="text/javascript">
    //添加 key-value 数据到 sessionStorage
    localStorage.setItem("demokey", "http://www.shiyanlou.com");
    //通过 key 来获取 value
    var dt = localStorage.getItem("demokey");
    alert(dt);
    //清空所有的 key-value 数据。
    //localStorage.clear();
    alert(localStorage.length);
</script> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

## 三、会话级别的本地存储：sessionStorage

在 HTML5 中增加了一个 Js 对象：`sessionStorage`；通过此对象可以直接操作存储在浏览器中的会话级别的 WebStorage。存储在`sessionStorage`中的数据首先是 Key-Value 形式的，另外就是它跟浏览器当前会话相关，当会话结束后，数据会自动清除，跟未设置过期时间的 Cookie 类似。

`sessionStorage`提供了四个方法来辅助我们进行对本地存储做相关操作。

*   `setItem(key,value)`添加本地存储数据。两个参数，非常简单就不说了。
*   `getItem(key)`通过 key 获取相应的 Value。
*   `removeItem(key)`通过 key 删除本地数据。
*   `clear()`清空数据。

代码如下：

```
<script type="text/javascript">
    //添加 key-value 数据到 sessionStorage
    sessionStorage.setItem("demokey", "http://blog.itjeek.com");
    //通过 key 来获取 value
    var dt = sessionStorage.getItem("demokey");
    alert(dt);
    //清空所有的 key-value 数据。
    //sessionStorage.clear();
    alert(sessionStorage.length);
</script> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。

## 四、强大的本地数据

虽然 HTML5 已经提供了功能强大的`localStorage`和`sessionStorage`，但是他们两个都只能提供存储简单数据结构的数据，对于复杂的 Web 应用的数据却无能为力。逆天的是 HTML5 提供了一个浏览器端的数据库支持，允许我们直接通 JS 的 API 在浏览器端创建一个本地的数据库，而且支持标准的 SQL 的 CRUD 操作，让离线的 Web 应用更加方便的存储结构化的数据。接下里介绍一下本地数据的相关 API 和用法。

操作本地数据库的最基本的步骤是：

*   第一步：`openDatabase`方法：创建一个访问数据库的对象。
*   第二步：使用第一步创建的数据库访问对象来执行`transaction`方法，通过此方法可以设置一个开启事务成功的事件响应方法，在事件响应方法中可以执行 SQL.
*   第三步：通过`executeSql`方法执行查询，当然查询可以是：CRUD。

接下来分别介绍一下相关的方法的参数和用法。

### 1\. openDatabase 方法

```
//Demo：获取或者创建一个数据库，如果数据库不存在那么创建之
var dataBase = openDatabase("student", "1.0", "学生表", 1024 * 1024, function () { }); 
```

`openDatabase`方法打开一个已经存在的数据库，如果数据库不存在，它还可以创建数据库。几个参数意义分别是：

*   数据库名称。
*   数据库的版本号，目前来说传个 1.0 就可以了，当然可以不填；
*   对数据库的描述。
*   设置分配的数据库的大小（单位是 kb）。
*   回调函数(可省略)。

初次调用时创建数据库，以后就是建立连接了。

### 2\. db.transaction 方法

可以设置一个回调函数，此函数可以接受一个参数就是我们开启的事务的对象。然后通过此对象可以执行 SQL 脚本。

### 3\. executeSql 方法执行查询

```
ts.executeSql(sqlQuery,[value1,value2..],dataHandler,errorHandler) 
```

参数说明：

*   `qlQuery`：需要具体执行的 sql 语句，可以是`create`、`select`、`update`、`delete`;
*   `[value1,value2..]`：sql 语句中所有使用到的参数的数组，在 executeSql 方法中，将 s>语句中所要使用的参数先用“?”代替，然后依次将这些参数组成数组放在第二个参数中;
*   `dataHandler`：执行成功是调用的回调函数，通过该函数可以获得查询结果集;
*   `errorHandler`：执行失败时调用的回调函数;

## 综合实例

```
<head>
 <script src="Scripts/jquery-1.5.1.js" type="text/javascript"></script>
    <script type="text/javascript">
        function initDatabase() {
            var db = getCurrentDb();//初始化数据库
            if(!db) {alert("您的浏览器不支持 HTML5 本地数据库");return;}
            db.transaction(function (trans) {//启动一个事务，并设置回调函数
                //执行创建表的 Sql 脚本
                trans.executeSql("create table if not exists Demo(uName text null,title text null,words text null)", [], function (trans, result) {
                }, function (trans, message) {//消息的回调函数 alert(message);});
            }, function (trans, result) {
            }, function (trans, message) {
            });
        }
        $(function () {//页面加载完成后绑定页面按钮的点击事件
            initDatabase();
            $("#btnSave").click(function () {
                var txtName = $("#txtName").val();
                var txtTitle = $("#txtTitle").val();
                var txtWords = $("#txtWords").val();
                var db = getCurrentDb();
                //执行 sql 脚本，插入数据
                db.transaction(function (trans) {
                    trans.executeSql("insert into Demo(uName,title,words) values(?,?,?) ", [txtName, txtTitle, txtWords], function (ts, data) {
                    }, function (ts, message) {
                        alert(message);
                    });
                });
                showAllTheData();
            });
        });
        function getCurrentDb() {
            //打开数据库，或者直接连接数据库参数：数据库名称，版本，概述，大小
            //如果数据库不存在那么创建之
            var db = openDatabase("myDb", "1.0", "it's to save demo data!", 1024 * 1024); ;
            return db;
        }
        //显示所有数据库中的数据到页面上去
        function showAllTheData() {
            $("#tblData").empty();
            var db = getCurrentDb();
            db.transaction(function (trans) {
                trans.executeSql("select * from Demo ", [], function (ts, data) {
                    if (data) {
                        for (var i = 0; i < data.rows.length; i++) {
                            appendDataToTable(data.rows.item(i));//获取某行数据的 json 对象
                        }
                    }
                }, function (ts, message) {alert(message);var tst = message;});
            });
        }
        function appendDataToTable(data) {//将数据展示到表格里面
            //uName,title,words
            var txtName = data.uName;
            var txtTitle = data.title;
            var words = data.words;
            var strHtml = "";
            strHtml += "<tr>";
            strHtml += "<td>"+txtName+"</td>";
            strHtml += "<td>" + txtTitle + "</td>";
            strHtml += "<td>" + words + "</td>";
            strHtml += "</tr>";
            $("#tblData").append(strHtml);
        }
    </script>
</head>
    <body>
        <table>
            <tr>
                <td>用户名：</td>
                <td><input type="text" name="txtName" id="txtName" required/></td>
            </tr>
               <tr>
                <td>标题：</td>
                <td><input type="text" name="txtTitle" id="txtTitle" required/></td>
            </tr>
            <tr>
                <td>留言：</td>
                <td><input type="text" name="txtWords" id="txtWords" required/></td>
            </tr>
        </table>
        <input type="button" value="保存" id="btnSave"/>
        <hr/>
        <input type="button" value="展示所哟数据" onclick="showAllTheData();"/>
        <table id="tblData">
        </table>
    </body>
</html> 
```

可以使用 GVim 按照上述代码编写，并用 Firefox 浏览器打开编写的 html 文件查看效果。