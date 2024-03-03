# 第 1 节 基于信令验证技术的身份验证功能

* * *

## 一、实验简介

* * *

### 1.1 说明

区别于短信验证方式，CIA 易验证通过信令验证技术，验证过程更可靠，用户本机号码验证可自动完成验证，免去输入短信验证码的繁琐过程，验证更准确、速度更快。

### 1.2 知识点

*   bootstrap 前端框架
*   JavaScript 实现数据传递
*   php 实现 REST API 中的验证 API 的使用

### 1.3 效果

![此处输入图片的描述](img/d1698adfb0d31811b38b3d71d516d0d5.jpg)

## 二、实验步骤

* * *

### 2.1 CIA 易验证

#### 2.1.1 简介

CIA（Communication & Internet Authentication） 易验证是通过专利技术开发一款移动互联网身份验证产品，基于电信网络与互联网，采用“信令交互认证技术”完成身份验证，信令交互不使用短信网关通道，而是电话呼叫信号，从而可以替代短信验证码，兼顾短信验证码及语音验证码两种方式的优势，是下一代移动互联网身份验证技术。

CIA 易验证基于手机号通过呼入电话的信令进行验证，即当用户输入本机号码发起验证后，系统呼入的一个来电的后四位就是验证码。

短信验证码送达率在 90%就已经是一个不错的效果，CIA 易验证送达率可以达到 99%，从而可以有效的提高用户注册环节的用户转化率。另外，短信验证码容易被第三方应用所拦截，如果开发者选择一些廉价的短信通道，这种情况会更加严重，造成的结果就是用户流失。应用场景基本与短信验证码相同，包括注册用户、找回密码、确认手机号、绑定手机号、异常登录及账户变动。

#### 2.1.2 使用

访问网址 `http://www.ciaapp.cn/ciaWebsite/`，注册一个账号。

![cia 后台](img/13dc2751bc2458a158be3cb5187cc2c8.jpg)

点击"应用->新增"，添加一个应用，获得 APP ID 和授权码。

![添加应用](img/8dc5209beb9134a9c5f92f981f4364d6.jpg)

### 2.2 编码

打开 `Xfce`，进入 `Code\`目录，然后在 `Code\` 目录下新建一个 `project`，并在 `project` 中创建 `index.html` 和 `agent.php`:

```php
cd Code
mkdir project
cd project
vim index.html
vim agent.php 
```

#### 2.2.1 bootstrap

使用前端框架 bootstrap3 的模板和组件来快速搭建一个简单的注册界面。

`index.html`:

```php
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>CIA Test</title>
  <!--Bootstrap 核心 CSS 文件，基于国内云厂商的免费 CDN 加速服务-->
  <link rel="stylesheet" href="//cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css">
</head>
<body>
  <div class="container">
    <div class="panel panel-primary" style="width:50%;margin-left:auto;margin-right:auto;">
      <div class="panel-body">
        <legend>CIA 电话验证</legend>
        <form class="form-horizontal">
          <div class="form-group">
            <label for="phoneNumber" class="col-sm-2 control-label">手机号</label>
            <div class="col-sm-10">
              <input type="text" name="number" id="number" class="form-control" placeholder="请输入手机号" style="width:80%;"></input>
            </div>
          </div>
          <div class="form-group">
            <div class="col-sm-offset-2 col-sm-10">
              <input type="button" class="btn btn-default" value="获取验证码" onclick="getCode()"></input>
            </div>
          </div>
          <div class="form-group">
            <label for="code" class="col-sm-2 control-label">验证码</label>
            <div class="col-sm-10">
              <input type="text" name="authenticateCode" id="authenticateCode" class="form-control" placeholder="未接来电后四位" style="width:80%;"></input>
            </div>
          </div>
          <div class="form-group">
            <div class="col-sm-offset-2 col-sm-10">
              <input type="button" class="btn btn-default" value="注册" onclick="Authenticate()"></input>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</body>
<!-- jQuery 文件。-->
<script src="//cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>
<!-- 最新的 Bootstrap 核心 JavaScript 文件。-->
<script src="//cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>

</html> 
```

#### 2.2.2 JavaScript

从上面的前端代码可以看到，我们在实现"获取验证码"和"注册"两个按钮的时候，给这两个按钮都添加了 `onclick` 属性，当鼠标点击按钮时，会触发两个函数。下面我们就来通过 JavaScript 实现这两个函数：

```php
<script>
  //定义全局变量，用来保存验证码
  var result;
  function getCode(){
    //从 input 输入框获取输入的手机号
    var number = document.getElementById("number").value;
    //将手机号 AJAX POST 到 php 实现的后台程序，将返回的验证码赋值给 result。
    $.post('agent.php',{'number':number},function(data){
      alert("发送成功!");
      result = data;
    });
  }
  function Authenticate(){
    //从 input 输入框获取输入的验证码
    var code = document.getElementById("authenticateCode").value;
    //验证获取的验证码是否正确
    if(code===result){
      alert("注册成功!");
    }
    else{
      alert("注册失败!");
    }
  }
</script> 
```

#### 2.2.3 php

根据 CIA 易验证提供的 REST API 实现的后台程序,用注册 CIA 易验证时获得的 APP ID 和授权码粘贴在代码相应位置：

`agent.php`:

```php
<?php
//APP Id
$appID = "填写在 CAI 易验证获得的 APP ID";
//授权码
$authKey = "填写在 CAI 易验证获得的授权码";
//待验证手机号
$pn = $_POST["number"];
$version = "1.0";
$url = "https://api.ciaapp.cn/{$version}/agent";
$headerList = array("Host:api.ciaapp.cn",
                "Accept:application/json",
                "Content-Type:application/json;charset=utf-8",
                "appId:{$appID}",
                "authKey:{$authKey}",
                "pn:{$pn}"
            );
//使用 cURL  的 POST 调用 api

$ch = curl_init($url);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_TIMEOUT,100);
curl_setopt($ch, CURLOPT_RETURNTRANSFER,1);
curl_setopt($ch, CURLOPT_HTTPHEADER, $headerList);
curl_setopt($ch, CURLOPT_POSTFIELDS, $params);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
$returnData = curl_exec($ch);
//对返回的 json 格式的字符串进行解析，并截取 authCode 后四位作为验证码
$return = json_decode($returnData);
$returnCode = $return->authCode;
$code = substr($returnCode,-4);
curl_close($ch);
echo $code;
?> 
```

### 2.3 发布

PHP 5.4 之后内置了 web 服务器，在当前目录下，执行：

```php
php -S localhost:8000 
```

ps. 如果端口被占用，可以尝试其他端口，如 8001。

打开浏览器访问：

![此处输入图片的描述](img/b0b8a784f9f4885bb885424bb21eb395.jpg)

* * *

## 三、总结

* * *

通过这门课程，我们学习了如何利用 CIA 易验证 提供的 REST API 来实现信令验证，利用未接来电取代短信验证码的形式实现用户身份验证。实验原理和代码都很简单，同学们可以私下多加尝试，将这种验证方式利用在其他更多场景。

* * *

代码：

```php
wget http://labfile.oss.aliyuncs.com/courses/626/CIA.zip 
```