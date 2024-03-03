# 第 1 节 PHP 留言本

## 一、实验说明及简介

### 1\. 环境登录

*   无需密码自动登录，系统用户名 shiyanlou
*   数据库采用 MySQL, 用户名 root，密码为空

### 2\. 环境介绍

本实验环境采用带桌面的 Ubuntu Linux 环境，实验中会用到桌面上的程序：

1.  Xfce 终端（XfceTerminal）: 虚拟终端，多用有好处

2.  Firefox： 浏览器.

3.  sublime： 桌面左下角/应用程序菜单/开发/sublime。

4.  MySQL： 数据库，热心的实验楼管理员已经替我们安装好了，课下可以自己试着安装（通过命令行下创建数据库及表）。

    ```php
    #连接 mysql 出错，请先开启 mysql。默认未开启服务
    #启动 mysqld 进程，权限不够加 sudo
    $ sudo service mysql start
    #连接 mysql，密码为空
    $ mysql -u root -p #或者 mysql -u root 
    ```

5.  服务器：为了快速方便的运行我们的项目，所以使用 PHP 内置的服务器： `sh $ php -S localhost:8080`

### 3\. 项目介绍

#### （1）简介

> 相信大家都见过或者用过留言本，与空间的留言、新闻的评论部分相类似，它常用作网站的意见或建议的反馈部分，它帮助网站提升用户体验，可谓是大功臣！本项目将会带着大家一起实现类似的功能。

##### 技术要点：

*   包含了小型登陆系统，发布系统，简单的字符处理，正则匹配
*   使用了 Ajax，可以使用 Smarty 等模板引擎技术进一步把逻辑和试图分离，可以使用 MVC 来改写，便于后期升级维护
*   注意各文件的路径问题，字符串拼接， 根据错误信息定位问题
*   **以上所有代码包括建库建表语句，建议大家手动敲入**

##### 强调的问题：

*   SQL 语句的拼接，如发现不了错误，就直接将 SQL 给 echo 出来，通过命令行输入到 mysql 中，多动手
*   WEB 开发安全 **“过滤输入，小心输出”**

#### （2）意义

*   本项目旨在引导大家做出一个简单的留言本。以后还可以放在自己的个人站点中。遇到不懂的就赶紧查文档吧。

#### （3）目标

废话不多说，让咱们先睹为快。

![index](img/d6c7f58adca261042feffdaa2582d5d5.jpg)

#### （4）项目源码

本项目完整源码下载：

```php
$ wget http://labfile.oss.aliyuncs.com/courses/171/guestbook.tar 
```

#### （5）关于练习

*   设置的练习也都与项目中用到的技术有关，未提供答案。实验楼已提供相关课程（如 mysql）

*   [MySQL 参考手册中文版](https://www.shiyanlou.com/courses/28)

    请同学们直接前往课程学习，暂未提供参考答案，请自行 Google。

#### （6）后续学习

*   为使课程对新手更加友好，后续会将重点知识点提取出来单独讲，文档持续更新中......

*   可以用 MVC 的思想撸一个留言本，哈哈

*   看到了同学们的一些评论，开发的时候，可以打开 PHP 的报错配置。这样可以更快地定位到问题。**发布到生产环境，请关闭**这些错误信息。

```php
 <?php
  ini_set('display_errors', 'On');
  error_reporting(E_ALL); 
```

## 二、需求分析

需要实现的功能：

*   用户匿名发留言、查看留言
*   管理员回复留言、删除(锁定)留言

## 三、 建库建表（mysql 的使用）

看到部分学生的截图中，没有连上数据库就输入建表 SQL 语句。 结果可想而知：`CREATE? NOT FOUND`

首先你得有个 `数据库`：

```php
CREATE DATABASE guestbook DEFAULT CHARSET utf8 COLLATE utf8_general_ci; 
```

> 指定存储编码非常重要，可避免出现乱码。

`留言表 Table`：

```php
CREATE TABLE `guestbook` (
  `id` int(10) unsigned PRIMARY KEY AUTO_INCREMENT,
  `nickname` char(16) NOT NULL DEFAULT '',
  `email` varchar(60) DEFAULT NULL,
  `content` text NOT NULL,
  `createtime` int(10) unsigned NOT NULL DEFAULT '0',
  `reply` text DEFAULT NULL,
  `replytime` int(10) unsigned DEFAULT NULL,
  `status` tinyint(1) unsigned DEFAULT '0'
) ENGINE=MyISAM  DEFAULT CHARSET=utf8 AUTO_INCREMENT=1; 
```

>同样注意设置字符编码！其中的 status 字段表示留言的状态，0：表示正常显示。1：表示对用户不可见，表现出删除的效果。

`用户表 Table`：

```php
CREATE TABLE user(
    `uid` INT(10) NOT NULL PRIMARY KEY AUTO_INCREMENT,
    `nickname` varchar(30) NOT NULL,
    `password` varchar(32) NOT NULL,
    `createtime` INT(10) unsigned NOT NULL,
    `level` TINYINT(1) unsigned DEFAULT '0'
)ENGINE=MyISAM CHARSET=utf8 AUTO_INCREMENT=1; 
```

> * 由于可以匿名发布留言，这个表仅需存一个人：管理员！管理留言需要权限控制。大家可以想想其他办法去存管理员的信息。

*   其中的 level 字段表示管理员的等级，在一个完整的系统中，管理员往往不止一个，不同的管理等级决定了他们所能管理的权限。这个字段在本实验中仅供示例作用。也可自行拓展开发。
*   这里可以使用 md5 加密函数，md5('admin') = 21232f297a57a5a743894a0e4a801fc3，当然你也可以采用其他加密方法，可以使用 PHP 自带方法，如 password_hash()，更加安全。对于不可逆加密方法如 md5，通过比较两者 md5 的值来判断是否相等，而可逆加密，可以解密后比较，也可加密两者比较。

```php
INSERT INTO user(nickname, password, createtime, level)    values( 'shiyanlou','21232f297a57a5a743894a0e4a801fc3', 1423018916, 9); 
```

* * *

**知识点练习之 mysql 的基本使用** 直接通过虚终端链接 mysql 服务器，进行练习操作。 > 1.创建一个名为 shiyanlou 的数据库，并指定字符编码。(提示：create database xxx ...)

> 2.在上述 shiyanlou 库中，新建一张名为 students 的表，暂有两个字段（uid, uname），其中 uid 为主键，且为自增字段。(提示：create table ...)

> 3.在第二题中建好的 students 表中插入一条数据 uname 为“I love php”。(提示：insert)

> 4.从 students 表中查出名为“I love php”的学生的 uid。（提示：select ...）

> 5.更新 uid 为第四题结果的学生的昵称为“我爱 PHP”。（提示：update ...）

> 6.删除昵称（uname）为“我爱 PHP”的这个学生（¬_¬）。（提示：delete ...）

> 7.建表语句看不懂，INT(10)，这个 10 又是什么意思呢？去看看 MYSQL 相关课程吧。

## 四、需求实现

> 先想想需要干些什么，再去动手写，所谓“磨刀不误砍柴工”。

**根据需求分析，需要以下几个页面：**

*   index.php 分页展示部分最新留言，并提供发布表单
*   post.php 留言发布页
*   admin/login.php 管理员登录页
*   admin/admin.php 管理员页面，提供浏览、锁定、回复留言等功能入口
*   admin/reply.php 留言回复页
*   admin/lock.php 留言锁定页
*   config.php 全局配置文件，例如存数据库链接信息，每页显示多少条留言等
*   mysql.class.php 数据库（mysql）工具方法文件

**写各个页面：**

> **这里仅突出重要逻辑部分代码，没有贴出全部代码。上面已提供完整代码下载地址和方式。**

### 1\. config.php (配置文件)

统一配置，方便更改，就不用去动逻辑代码了

```php
<?php

define('DB_HOST', 'localhost');
define('DB_USER', 'root');
define('DB_PWD', 'fulisiwo');
define('DB_NAME', 'guestbook');
define('GB_TABLE_NAME', 'guestbook');
define('ADMIN_TABLE_NAME', 'user');
define('PER_PAGE_GB', 5);

//调试用，类似与某些框架的几种模式，生产环境，产品环境
$debug = true;
if ($debug) {
      //开启错误提示并显示所有异常
    ini_set("display_errors", 1);
    error_reporting(E_ALL);
}
/**end of file**/ 
```

**配置文件练习**

> 为什么要将一些参数写到配置文件中呢？

当然是从实践中总结出来的，可做到数据与逻辑相分离，改错一个逗号咋办！！！。相信玩过开源程序（如 dizcuzz 论坛程序），搭建一个 bbs，只需修改配置文件即可，一点儿也不懂程序的也可以按照教程搭建好。想想现在要部署到一个新的服务器上，更改 mysql 的服务器配置。

所以，练习是**将配置文件的内容替换到各对应文件中**（如每页显示 8 条留言），确保能跑起来。完成上述步骤后，更改每页显示的留言数。

（是不是感觉有点儿麻烦，体验到统一配置的好处了吧！）

* * *

### 2\. index.php

展示最新的 N 条可见的（这个数值在 config.php 中设置）留言，并附上留言表单

取 N 条留言，加以处理展示。

**Step1**. 连接数据库，方法在 mysql.class.php 中，与数据库相关的配置信息放在 config.php 中，包括数据库地址，用户名，密码，数据库名，表名。

```php
DB::connect(); 
```

**Step2**. 编写 SQL，拿出你想要的。说白了，就是告诉数据库，按什么条件，在哪个表里拿些什么数据，拿多少。尽量按需所取。

a) . 根据留言表的结构，和需要展示的内容，编写 SQL 如下：

```php
//需要那些字段就拿哪些字段
$pagedata_sql = 'SELECT  nickname,content,email,createtime,reply,replytime FROM ' . GB_TABLE_NAME . ' WHERE status = 0 ORDER BY createtime DESC LIMIT ' . $offset . ',' . PER_PAGE_GB; 
```

b). 需要拿多少，分页展示

```php
//做分页，在 MySQL 中需要提供了非常方便的 limit, 它有两个参数，一个偏移量（offset）, 一个是返回记录行的最大数目（count）。
//统计出留言的总条数，考虑到需要分页
$gb_count_sql = 'SELECT count(*) FROM ' . GB_TABLE_NAME . ' WHERE status = 0';
$gb_count_res = mysqli_query(DB::$con,$gb_count_sql);//自己编写的 SQL，就没必要过滤特殊字符了
$gb_count = mysqli_fetch_row($gb_count_res)[0];    //总记录数

//这里的$offset 需要根据页面数(第一页还是第几页)计算，PER_PAGE_GB 在 config.php 中
$page = isset($_GET['page']) ? intval($_GET['page']) : 1;
$pagenum = ceil($gb_count / PER_PAGE_GB);    //总的分页数
if ($page > $pagenum || $page < 0) {
    $page = 1;
}
$offset = ($page - 1) * PER_PAGE_GB; 
```

**Step3**. 处理留言，样式随心设置,我这里用到的是 bootstrap 的样式，注意过滤特殊字符，防止前端注入（比如页面变黑啥的），php 提供了 htmlspecialchars()，相关练习见练习第一题。

```php
//查询留言的 sql 语句
$pagedata_sql = 'SELECT  nickname,content,email,createtime,reply,replytime FROM ' . GB_TABLE_NAME . ' WHERE status = 0 ORDER BY createtime DESC LIMIT ' . $offset . ',' . PER_PAGE_GB;
$sql_page_result = mysqli_query(DB::$con,$pagedata_sql);
while($temp = mysqli_fetch_array($sql_page_result)) {
    $sql_page_array[] = $temp;
}
DB::close();    //这个要在使用完 MYSQL 之后，关闭连接。可以试试放在 mysql_fetch_array 之前，看看结果
if (!empty($sql_page_array)) {    //若结果集非空
    //循环输出数据库中满足条件 id 留言内容
    foreach($sql_page_array as $key => $value){
        echo '<div style="background-color:#F7F7F9"><li class="list-group-item list-group-item-success">留言者：<span>'. $value['nickname'].'</span> ' . (empty($value['email']) ? '' : ' &nbsp;&nbsp;  |  &nbsp;&nbsp; 邮箱：'.$value['email']);
        echo '<span style="float:right;">时间：' . $value['createtime'] .'</span></li>';
        echo '<li class="list-group-item l">内容：' . $value['content'] .'</li>';
        if (!empty($value['reply'])) {
            echo '<li class="list-group-item list-group-item-warning">管理员回复：' . $value['reply'] ;
            echo '<span style="float:right;">回复时间：' . $value['replytime'] .'</span></li>';
        }
        echo '</div><hr>';
    }
}
//你可以自己设置样式-CSS
//已知总条数，每页需要显示的数量，总页数也就不难得出，记得向上取整, 出现半页就不好了
echo '共 '.$gb_count.' 条留言  ';
if ($pagenum > 1) {
    for($i = 1; $i <= $pagenum; $i++) {
        if($i == $page) {
            echo '&nbsp;&nbsp;['.$i.']';
        } else {
            echo '<a href="?page='. $i .'">&nbsp;' . $i . '&nbsp;</a>';
        }
    }
} 
```

**Step4**. 提交留言的表单，当然，你也可以把它单独放一个页面

```php
<!--静态表单，ajax 提交留言-->
<form name="message_submit" id="form" method="post" class="form-horizontal">
       <div class="form-group">
          <label for="" class="col-sm-2 control-label">姓名：</label>
          <div class="col-sm-4">
            <input type="text" name="nickname" class="form-control" id="nickname" class="from-control" placeholder="必填(不超过 10 个字符)" required=""  maxlength="10" />
          </div>
      </div>
       <div class="form-group">
          <label for="" class="col-sm-2 control-label">内容：</label>
          <div class="col-sm-4">
            <input type="text" name="contents" class="form-control" id="contents" class="from-control" placeholder="必填(不超过 50 个字符)" required="" maxlength="50" />
          </div>
      </div>
       <div class="form-group">
          <label for="" class="col-sm-2 control-label">E-MAIL:</label>
          <div class="col-sm-4">
            <input type="email" class="form-control" id="email" placeholder="Email">
          </div>
      </div>
       <div class="form-group">
          <label for="" class="col-sm-2 control-label"></label>
          <div class="col-sm-6">
              <button type="button"  name="sub" id="sub">留言</button>
              <button type="reset"  id="reset">重置</button>
          </div>
      </div>
    </form> 
```

另外，再引入

```php
<?php require 'common/footer.php';?>
<!--简单校验，及 ajax 提交表单-->
<script type="text/javascript" src="//cdn.bootcss.com/jquery/2.2.3/jquery.min.js"></script>
<script type="text/javascript" src="js/ajax_submit.js"></script> 
```

**Step5**. 前端表单校验

看不懂下面的代码的同学请先看看 jQuery，并完成练习部分 jQuery 习题。

```php
$(document).ready(function() {
    $("#sub").click(function() {
        var nickname = $("#nickname").val();
        var content = $("#contents").val();
        var email = $("#email").val();
        if (nickname == "" || content == "") { //必填字段
            alert("Nicknames or content cannot be empty"); //这个方法太暴力，可以想想其他的
            return false;
        } else if (nickname.length > 10) {
            alert('Nickname length too long');
            return false;
        } else if (content.length > 50) {
            alert('Message length too long');
            return false;
        } else{
            var data = {
                nickname : nickname,
                content : content,
                email : ''
            };
            if (email !== "") {
                //正则表达式过滤邮箱
                var email_reg = /\w+([-+.]\w+)*@\w+([-.]\w+)*.\w+([-.]\w+)*/;
                if (!email_reg.test(email)) {
                    alert('mail address not legal');
                    return false;
                }else{
                    data.email = email;
                }
            }
        }
    $.post('../post.php', data, function(data, textStatus, xhr) {
        if (textStatus == 'success') {
            var data = $.parseJSON(data);
            if (data.error == '0') {
                alert(data.msg);
                window.location.href = '?page=1';//刷新页面，回到第一页。这样用户就能看到他的留言了
            } else {
                alert(data.msg);
            }
        }
    });

    });
}); 
```

> 页面了乱码了？请在页面头部加入 header(xxx)(具体写法自己查。。。)，告诉浏览器这是个 html 文件，且字符编码为 utf-8。记住，不能在 header 前有任何内容输出。不信你可以在 header 前加一句 echo 'test';

### 3\. mysql.class.php

mysql 工具类简单封装了下，可以按需封装。上面 Index.php 中 DB 就来自这里。

> 要养成一个页面用完立即关闭连接的好习惯

```php
<?php

class DB {
    public static $con;    //静态连接实例

    public static function connect() {
        if (!self::$con) {
            self::$con = new mysqli(DB_HOST,DB_USER,DB_PWD,DB_NAME);
            if (mysqli_connect_error()) {
                exit('database error');
            }
        }
        return self::$con;
    }

    static function close() {
        if (self::$con) {
            self::$con->close();
            self::$con = null;
        }
    }
} 
```

* * *

### 4\. post.php（接收用户提交的留言）

和 index.php 一样，存数据，取数据的步骤是：连库-->输入 SQL-->拿到想要的结果-->关闭链接（容易忘掉！！！）

**step1**. 拿到从 index 页面传过来的数据，初步验证+预处理（前端校验容易被绕过） 去掉首尾空格，必填字段非空，选填字段（如邮箱）格式可用 preg_match 方法处理。若不符合规范，退出程序并返回 `json` 数据。

> WEB 开发中的那句 **“过滤输入，小心输出”**，要牢记在心，运用到实际中

```php
DB::connect();
$nickname = DB::$con->real_escape_string($_POST['nickname']);
$content = DB::$con->real_escape_string($_POST['content']);
if (empty($nickname) || empty($content)) {
    exit('{"error":"1","msg":"Nickname or content cannot empty"}');
}
if (mb_strlen($nickname) > 10 || mb_strlen($content) > 50) {
    exit('{"error":"1","msg":"Length incorrect"}');
}
if (!empty($_POST['email'])) {
    $email = DB::$con->real_escape_string($_POST['email']);
    $email_reg = '/\w+([-+.]\w+)*@\w+([-.]\w+)*.\w+([-.]\w+)*/'; //邮箱正则
    if(!preg_match($email_reg, $email)) {
        exit('{"error":1, "msg":"Email address not legal"}');
    }
} 
```

**step2**. 拼接 SQL 语句，屏蔽特殊字符，保证数据库的安全，毕竟 WEB 安全更加重要 php 提供了原生方法 mysql*real*escape_string，传入插入的字段值，不要图简单，把拼接好的 SQL 语句传入（因为这个方法会将一些特殊字符转义，可能会导致语句无法执行）

> Tips： 如果语句插入不成功，可通过命令行手动插入 SQL 语句到数据库中，查看哪里出错

```php
$sql_insert = 'insert into ' . GB_TABLE_NAME . '(nickname, content, createtime, email) values( ' . "'{$nickname}', '{$content}', '{$create_time}' , '{$email}')"; 
```

刚开始，字符串拼接可能会让你眼花，可以先 echo 出来看看拼接结果，再执行查询操作。

**step 3**. 关闭数据库连接 根据 SQL 语句结果，echo（不能 return）出相应 JSON 串。可定义一个返回码，和消息体如 {"error":1, "msg":"昵称或内容不能为空"}

留言的提交以及展示已经可以跑起来了，下面来看看留言的管理

### 5\. admin/index.html

**step 1**. 这里是一个管理员登陆表单

```php
 <form class="form-horizontal" action="login.php" method="post">
          <div class="form-group">
            <label for="inputEmail3" class="col-sm-2 control-label">NickName</label>
            <div class="col-sm-8">
              <input type="uname" class="form-control" id="uname" name="uname" placeholder="UserName">
            </div>
          </div>
          <div class="form-group">
            <label for="inputPassword3" class="col-sm-2 control-label">Password</label>
            <div class="col-sm-8">
              <input type="password" class="form-control" id="inputPassword3" name="password" placeholder="Password">
            </div>
          </div>
          <div class="form-group">
            <div class="col-sm-offset-2 col-sm-10">
              <button type="submit" class="btn btn-default">Sign in</button>
            </div>
          </div>
        </form> 
```

* * *

### 6\. admin/login.php

*   由于 HTTP 是无状态的，记住用户是否登陆，是否是管理员，都可通过 SESSION 来实现

**Step 1**. 和前面的留言表单提交一样，校验数据，这里是查询，同样需要过滤特殊字符

```php
if (empty($_POST['uname']) || empty($_POST['password'])) {
    header('location:index.html');
} 
```

有错误的话就将其显示到登录页。

**Step 2**. 从用户表中查管理员的用户名和密码，密码不要用明文存，这里用 md5 加密，php 也提供了 password_hash() 加密方法。

> 小技巧，先查出用户名对应的密码，再和用户输入比对，安全性就增加了，想想这是为什么？如果查是否有个用户名为 xxx 且密码为 xxx 的用户，被注入的风险是不是变大了呢。

```php
$sql_login = 'SELECT password FROM ' . ADMIN_TABLE_NAME . ' WHERE level=9 AND nickname = '. "'{$user}'" . ' LIMIT 1'; //管理员不重名，加上 LIMIT 1 能快一点儿 
```

**Step 3**. 登录成功，跳转到 admin.php，开启 SESSION

```php
//md5 加密是不可逆的，所以把用户输入的参数采用同样的方式加密，再与库中的比对
if (md5($pwd) === $password) {
    //save to session
    session_start();
    $_SESSION['admin'] = true;
    header('location:admin.php'); 
```

### 7\. admin/admin.php

**Step 1**. 只有已经登录了才可以进来，所以这里首**先得检查管理员是否登录（检查 SESSION）**

```php
session_start();
if (!$_SESSION['admin']) {
    header('location:index.html');
} 
```

**Step 2**. 剩下部分同样是展示用户的留言，只不过展示的东西多一点，比如邮箱等你不想展示的一些信息 所以，逻辑展示代码同 index.php

管理员就应该比普通用户多一点权限。

*   可以回复用户的留言，可以“删除”用户的留言（可以采用更新留言的状态，当初留言表设计了 status 字段，如果你想直接删除，当然也可以，这样可以练习删除一条数据即 delete 操作了）

**Step 3**. 回复留言，更新对应留言的回复字段

a). 首先，这个权利的入口就得检查，删除也一样

```php
session_start();
if (!$_SESSION['admin']) {
    return false;
} 
```

你想回复哪条留言，可以提供 ID，就像身份证号一样，可以通过隐藏域让 ID 藏起来备用

```php
echo '<input type="hidden" value="' . $value['id'] . '" />'; 
```

b). 拼接更新语句，原则上也需要校验提交的数据，管理员也是我们的用户，玩坏了可不好。

```php
$reply_sql = 'UPDATE ' . GB_TABLE_NAME . 'SET reply = ' . $reply . 'WHERE id = ' . $id; 
```

**Step 4**. 锁定留言，锁定的的留言将不会显示在 index.php 中，我们只需要在数据库中将留言的 `status` 字段设置为 1。

权限同回复留言的权限，先得检查权限。

既然可以锁定留言，当然也可以对留言解除锁定， 所以处理的时候需要区分是锁定操作还是解除锁定操作。

```php
 if ($option == 'lock') {
        $option_sql = 'UPDATE ' . GB_TABLE_NAME . ' SET status = 1 ' . 'WHERE id = ' . $id;
    } elseif ($option == 'unlock') {
        $option_sql = 'UPDATE ' . GB_TABLE_NAME . ' SET status = 0 ' . 'WHERE id = ' . $id;
    } 
```

## 五、作业练习

**1\. htmlspecialchars 这个名字如此长的方法是干嘛的？**

> 想想怎么让自己的留言板页面上出现异常（有几个字很大，颜色还不同）

`**试试输入“<h1>我的字体会很大！！！</h1>”。**`

看到这条留言没？ 只是看到两个 h1 标签了？现在把 index.php 中的 htmlspecialchars 方法暂时屏蔽掉。再试试输入一些带有 html 标签、css 和 JavaScript 代码的留言。

*   a.提交留言，让页面上出现大号字体留言。（提示：可输入 html 相关标签，也可结合 css 实现）

*   b.提交留言，让留言本首页背景变为黑色。哦？这也可以（提示：css 代码）

*   c.提交留言，让首页弹出一个内容为“我爱你”的对话框。（提示：js 代码，alert()）

*   d. 选取以上任意一题的结果，输入它的 htmlspecial 的结果。（提示：echo htmlspecialchars(xx)）

以上提到的是一些比较简单的前端注入的结果。。这部分内容很丰富，很有趣。

**2\. mysql.class.php 中的 cleanSql 方法的作用是？**

先屏蔽这个方法

*   a. 输入带有单引号，双引号等字符的留言内容。

*   b. 来到管理员登陆页面，试着构造用户名或密码，冒充管理员进入留言管理页。（提示：SQL 注入，不过在管理员登陆验证时的逻辑是先查出用户名对应的密码，可能很难注入成功）

**3\. 不用 ajax 技术提交留言行不行？**

当然可以不用，项目中管理员登陆页面就没有使用这个技术，比较它与留言发布页面在使用上的区别。

**4\. jQuery（JavaScript 的一款流行框架）小练习**

它可以作很多事情，可以很快帮你实现很炫的效果，可以使你写出简洁的 ajax 代码，在你了解了原生的 js 语法等基础知识后。它很可能让你爱上 JavaScript。

*   a.表单提交页面中，通过 jQuery 选择器获得了某个节点（如昵称输入框）的某个属性值 当昵称称输入框（即 name="nickname" 的输入框）获得焦点（光标进入输入框）时，弹出提示消息（如，你想说点儿啥？。（提示：给这个节点绑定一个事件。）

*   b. 当昵称输入框失去焦点时，弹出用户输入的值（即 value）。（提示：给这个输入绑定对应事件，并弹出它的值）

*   c. 捣蛋，当上述输入框失去焦点后，改掉输入框的值，并弹出“就是这么任性”。 （提示：节点的属性值可通过 js 更改的）

**5\. 正则表达式**

正则不属于某一语言，是一门独立的重要的工具（感觉措辞不当）。奥莱利（动物书）有本叫《精通正则表达式》，讲解得非常细致透彻。

*   a. 匹配连续的 6 个字符（如 ab4x,.）

*   b. 匹配手机号码（如 17012345678）

**@update 2016-08-16**

[001]: https://dn-anything-about-doc.qbox.me/userid18983labid485time1423108330284 "PHP 留言本"