# 第 1 节 校花是如何评选出来的

## 一、实验简介

### 1.1 实验目的

​ 本次实验将带领大家完成一个有趣的 PHP 项目：校花评选。主要包含的知识点：前端页面布局，使用 Jquery 控制页面效果和操作 Cookie，使用 Ajax 方式与后台交互，PHP 操作 MySQL 完成数据查询，PHP 实现[埃洛等级分系统](http://baike.baidu.com/link?url=5TaQ9N_gZjq6Mr876rdfeYWCaoUIA3yf8VtRjrIvp1UD6PHdUpqWFStWNwxKSrAUwH_cqokSSeGuJeDNEz0P2a)算法。通过本次实验的操作，我们不仅可以欣赏并评选美丽的校花，也可以大大提升编程技术。

### 1.2 实验环境

本实验环境采用带桌面的 Ubuntu Linux 环境，实验中会用到桌面上的程序：

1.  LX 终端（LXTerminal）: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令
2.  Firefox：浏览器，用于前端页面展示
3.  Sublime Text：非常好用且跨平台的文本编辑器。点击桌面左下角： 应用程序菜单/开发/sublime。
4.  PHP 环境. 实验楼给我们提供了一个很完美的快速学习平台。

数据库及服务器环境：

*   在线环境默认未开启数据库服务，输入以下命令开启：

    ```js
    $ sudo service mysql start 
    ```

*   由于本项目比较简单，所以我们可以直接使用 PHP 内置的服务器。通过以下命令就可使用：

    ```js
    $ php -S localhost:8080        #端口可自定义 
    ```

    服务器需要在我们的项目目录下开启。

### 1.3 项目源码

由于本项目需要前端页面制作，但是我们不可能在文章中一行一行的编写 HTML 代码，所以为了节约时间，我把完整的项目源码已打包，使用下面的方法获取：

```js
$ cd /home/shiyanlou/Code
$ wget http://labfile.oss.aliyuncs.com/courses/321/Beauty.tar
$ tar -vxf Beauty.tar 
```

我们的项目就是解压之后的 `Beauty` 目录。

进入项目目录，开启 PHP 内置的服务器。当然，现在还不能运行。因为数据库还没有准备好。

### 1.3 项目效果(人物图片来自网络)

![img](img/document-uid108299labid2003timestamp1472521629517.jpg)

评选过程：

![img2](img/document-uid108299labid2003timestamp1472521628725.jpg)

评选结果：

![result](img/document-uid108299labid2003timestamp1472521630512.jpg)

## 二、项目制作

### 2.1 项目简介

校花评比项目这个想法来自于电影《社交网络》 ——马克扎克伯格被女友甩了之后，一气之下黑了所有宿舍的相册，将所有女生的照片放在 Facemash 上供人评比。如今我们实验楼的教程教大家也来过把瘾，把这个实现过程来重映一下。请同学们好好利用实验楼里面的环境~~

评选过程：

* * *

> 介绍： 校花排名页面随机产生两个女生(名字做了处理) ， 比较两个女生的颜值， 然后利用鼠标点击选择一个你认为较漂亮的女生，随后数据自动提交到数据后台处理， 页面会自动刷新，再次随机产生两个女生，再次选择比较， 依次达到不断更新颜值的作用。 上方进度条可以显示你的进度，当进行 10 次选择以后， 页面自动弹到排名页面， 显示美女排名 ，以及校花。 > > 想法来自《社交网络》，所以这里教程的步骤也跟着扎克伯格的脚步来进行。

* * *

由于本项目需要，你应该提前准备好几张美女的图片用来评选。这里我为了方便，就直接在网上找了几张图片，我也会在项目源码中为大家提供。不过这都不是我们关心的重点，我们应该着眼于编码功能实现。

排名算法（资料参考：[百度百科](http://baike.baidu.com/link?url=5TaQ9N_gZjq6Mr876rdfeYWCaoUIA3yf8VtRjrIvp1UD6PHdUpqWFStWNwxKSrAUwH_cqokSSeGuJeDNEz0P2a)）：

> --- > > 我们将会使用 [埃洛等级分系统](http://baike.baidu.com/link?url=5TaQ9N_gZjq6Mr876rdfeYWCaoUIA3yf8VtRjrIvp1UD6PHdUpqWFStWNwxKSrAUwH_cqokSSeGuJeDNEz0P2a) 来实现对校花的评选。设定每个女生的基础颜值为 1400，供用户评选之后，将会通过该算法计算出新的颜值，并最终排名。 > > 算法示例：假设女生 A 和 B 的当前颜值分别为 Ra 和 Rb 。 > > A 对 B 的胜率期望值为：![](http://d.hiphotos.baidu.com/baike/s%3D149/sign=398c4e84708da9774a2f822f8950f872/622762d0f703918fd3f0b07d523d269759eec460.jpg) > > 同理，B 对 A 的胜率为：![](http://h.hiphotos.baidu.com/baike/s%3D148/sign=3ffcb07d523d26972ad30c596dfab24f/c8ea15ce36d3d539291082743987e950352ab05c.jpg) > > 假如一位女生在比赛中的真实得分（胜=1 分，和=0.5 分，负=0 分）和她的胜率期望值不同，则她的颜值要作相应的调整。例如，女生 A 颜值为 1613，与颜值为 1573 的女生 B 战平。若 K 取 32，则 A 的胜率期望值为: > > ![](http://e.hiphotos.baidu.com/baike/s%3D190/sign=d350f825ac345982c18ae19b3cf5310b/5d6034a85edf8db18f9e10350823dd54564e742a.jpg) ，因而 A 的新颜值分为 ![img](http://h.hiphotos.baidu.com/baike/s%3D269/sign=f789085c279759ee4e5067cd8bfa434e/5ab5c9ea15ce36d32a4aef613bf33a87e950b111.jpg) 。 > > 在不断的两两比较之后,由此可以得出他们的颜值排名。 > 最后就知道校花是谁了。 > > ---

### 2.2 数据库

首先按照上面提供的方法开启数据库服务。进入并创建数据库：

```js
$ mysql -u root
$ mysql> CREATE DATABASE beauty DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
$ mysql> use beauty; 
```

**设计 `stu` 数据表**：

数据库存储女生信息.（id，姓名，图片，颜值）

```js
CREATE TABLE `stu` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `stu` varchar(255) DEFAULT NULL,
  `img` varchar(255) DEFAULT NULL,
  `score` int(20) NOT NULL DEFAULT '1400',
  PRIMARY KEY (`id`)
) ENGINE=MyISAM AUTO_INCREMENT=78 DEFAULT CHARSET=utf8;
SET FOREIGN_KEY_CHECKS=1; 
```

填充部分示例数据：

```js
INSERT INTO `stu` VALUES ('1', 'Mary', 'imgs/1.jpg', '1400'),
 ('2', 'Nancy', 'imgs/2.jpg', '1400'),
 ('3', 'Kacy', 'imgs/3.jpg', '1400'),
 ('4', 'Judegli', 'imgs/4.jpg', '1400'),
 ('5', 'Kacy1', 'imgs/7.jpg', '1400'),
 ('6', 'Judegli1', 'imgs/6.jpg', '1400'); 
```

我在源码中也为大家提供了完整的 SQL 文件，可任意使用。

### 2.3 前端布局

前端设计采用 bootstrap 响应式布局，适合任何设备使用。主要布局如下(具体看下载下来的页面代码)：

**index.php** ：

```js
 <div class="container">
      <div class="main">
        <!-- 首次访问的提示语 -->
        <div class="alert alert-info alert-dismissible" role="alert" hidden="">
          <button type="button" class="close" data-dismiss="alert" aria-label="Close"><span aria-hidden="true">&times;</span></button>
          <strong>!</strong>Of the two girls, who do you think is more beautiful~
        </div>
                <!-- 评选进度 -->
        <div class="progress">
            <div class="progress-bar" role="progressbar" aria-valuenow="60" aria-valuemin="0" aria-valuemax="100" style="">
            <span class="">0% Complete</span>
            </div>
        </div>
        <!-- 循环展示女生的信息 -->
        <div class="row">
            <?php 
                foreach ($results as $key => $value) {
             ?>
            <div class="col-sm-6 col-md-6 rankimg<?php echo $key; ?>">
                <input type="hidden" name="" value="<?php echo $value['id'] ?>">
                <a href="#" class="thumbnail" id="rank<?php echo $key; ?>">
                    <img src=" <?php echo $value['img'] ?> " alt="..." class="img-rounded">
                </a>
                <span><a href="#" title=""><?php echo $value['stu'] ?></a></span>
            </div>
            <?php } ?>
        </div>
      </div>
    </div><!-- /.container --> 
```

展示之前需要查询相关数据，所以在 `index.php` 的顶部，需要加入如下 PHP 代码段：

```js
<?php 
require_once 'DBMysql.php';        //引入数据库操作文件
$db = DBMysql::connect();        //连接数据库
$sql = 'select * from stu order by rand() limit 2';        //随机查询两个女生信息
$result = $db->query($sql);
while ($row = $result->fetch_assoc()) {
    $results[] = $row;
}
 ?> 
```

同理，在排名列表中，我们也需要做类似的处理展示：

**ranklist.php** :

```js
 <tbody>
          <?php foreach ($results as $key => $value) {  ?>
            <tr>
              <th scope="row"><?php echo $key+1 ?></th>
              <td><a href="#" title=""><?php echo $value['stu'] ?></a></td>
              <td><?php echo $value['score'] ?></td>
            </tr>
            <?php } ?>
          </tbody> 
```

在 `ranklist.php` 顶部也需要加入 php 查询代码段：

```js
<?php 
require_once 'DBMysql.php';
$db = DBMysql::connect();
$sql = "SELECT * FROM `stu` ORDER BY `score` DESC";        //按照颜值从高到低查询
$result = $db->query($sql);
while ($row = $result->fetch_assoc()) {
    $results[] = $row;
}
 ?> 
```

### 2.4 JS 动态交互

前端页面中，我们需要使用 js 来控制页面展示效果：首次访问，显示提示语。点击其中一张美女图片，另外一张消失，页面刷新，再次随机分配两张图片，同时，上方进度条展示当前评选进度，当评选进度达到百分百时，页面跳转至排名展示。在排名展示页面中，可点击按钮再次进入评选页面。

`index.php` 中的 JS 代码段：

```js
 <script>
        $(document).ready(function() {
                  //获取 cookie 方法
            function getCookie(name) {
                var arr,reg=new RegExp("(^| )"+name+"=([^;]*)(;|$)");
                if(arr=document.cookie.match(reg)){
                    return (arr[2]);
                } else {
                    return null;
                }
            }
                  //若不存在 cookie，则显示提示语，并设置 cookie
            if(!getCookie("rankwoman"))
            {
                $(".alert-info").show();
                document.cookie="rankwoman=rankwoman";
                document.cookie="rankwomanper=0";
            }
                  //评选次数
            var rankwomanper=parseInt(getCookie("rankwomanper"));
                  //点击第一张图片，第二张图片消失，cookie 的评选次数加 1，调用 rank 方法提交评选数据
            $('#rank0').click(function() {
                $('#rank1').hide();
                rankwomanper=parseInt(getCookie("rankwomanper"))+1;
                document.cookie="rankwomanper="+rankwomanper;
                rank(0);
            });
                  //同上
            $('#rank1').click(function() {
                rankwomanper=parseInt(getCookie("rankwomanper"))+1;
                document.cookie="rankwomanper="+rankwomanper;
                $('#rank0').hide();
                rank(1);
            });
                  //提交评选数据方法，操作成功页面刷新
            function rank(i) {
                $.post('./Rank.php', {stu1: $('.rankimg0 input').val(),stu2:$('.rankimg1 input').val(),vid:i}, function(data, textStatus, xhr) {
                    if (textStatus == 'success') {
                        window.location.reload();
                    }
                });
            }
                  //若当前评选次数大于 10(0~9),页面跳转
            if(getCookie("rankwomanper")>9){
                window.location.href="./ranklist.php";
            } else {            //否则，展示进度条
                $(".progress-bar").width(rankwomanper+"0%");
                $(".progress-bar span").text(rankwomanper+"0% Competed");
            }
        });
    </script> 
```

### 2.5 PHP 后台处理

后台主要分为两个部分：数据库部分和颜值计算部分。

#### 2.5.1 数据库操作辅助类

使用此类可以很方便的操作数据库。

**DBMysql.php** ：

```js
<?php

class DBMysql
{
    public static function connect(){
        $dbc = new mysqli('localhost','root','','beauty') OR die('Could not connected to MySQL: '.mysql_error());
        $dbc->query('SET NAMES utf8');
        return $dbc;
    }
} 
```

定义了一个数据库连接方法，使用 mysqli 建立 mysql 连接，设置字符编码，返回连接对象。

#### 2.5.2 颜值计算类

此类根据[埃洛等级分系统](http://baike.baidu.com/link?url=5TaQ9N_gZjq6Mr876rdfeYWCaoUIA3yf8VtRjrIvp1UD6PHdUpqWFStWNwxKSrAUwH_cqokSSeGuJeDNEz0P2a)设计算法，实现读取、计算、更新颜值的操作。

```js
<?php 
/**
* 排名算法
*/
class Rank
{
    private static $K = 32;
    private static $db;        //数据库连接对象
    function __construct()
    {
        require_once 'DBMysql.php';
        self::$db = DBMysql::connect();
    }

  //根据 id 值查询颜值
    public function queryScore($id)
    {
        $sql = "SELECT * FROM stu WHERE `id` = $id";
        $info = mysqli_fetch_assoc(self::$db->query($sql));
        return $info['score'];
    }
    //更新颜值
    public function updateScore($Ra,$id)
    {
        self::$db->query("UPDATE `stu` SET `score` = $Ra WHERE `id` = $id");
    }

  //计算二者的胜率期望值
    public function expect($Ra,$Rb)
    {
        $Ea = 1/(1+pow(10,($Rb-$Rb)/400));
        return $Ea;
    }

    //计算最后得分
    public function calculateScore($Ra,$Ea,$num)
    {
        $Ra = $Ra + self::$K*($num-$Ea);
        return $Ra;
    }

  //获取本次参与评选的两位美女 id，以及获胜方 id：0,1
    public function selectStu()
    {
        $id1 = $_POST['stu1'];
        $id2 = $_POST['stu2'];
        $victoryid = $_POST['vid'];
        return $this->getScore($id1,$id2,$victoryid);
    }

  //计算得分
    public function getScore($id1,$id2,$victoryid)
    {
        $Ra = $this->queryScore($id1);
        $Rb = $this->queryScore($id2);
        if ($Ra & $Rb) {
            $Ea = $this->expect($Ra, $Rb);
            $Eb = $this->expect($Rb, $Ra);
            $Ra = $this->calculateScore($Ra, $Ea, 1-$victoryid);
            $Rb = $this->calculateScore($Rb, $Eb, $victoryid);
            $Rab = array($Ra,$Rb);
            $this->updateScore($Ra, $id1);
            $this->updateScore($Rb, $id2);
            return $Rab;
        } else {
            return false;
        }
    }
}
$Rank = new Rank();
$Rank->selectStu(); 
```

代码示例讲解：若前端页面展示的两位女生的 id 为 3 和 7，若用户选择 3，则 js 向后台传递的数据为 3,7,0，后台处理 女生 a 的 id 为 3，女生 b 的 id 为 7，获胜的 id 为 0 ，表示第一个获胜，则本轮真实得分 ：a 的得分为 1，b 的得分为 0。计算调整后分数时：`$Ra = $this->calculateScore($Ra, $Ea, 1); $Rb = $this->calculateScore($Rb, $Eb, 0);`，代码中使用 `1-$victoryid` 和 `$victoryid` 来处理。

现在进入 `Beauty` 目录，开启 PHP 内置服务器，打开浏览器就可以看到效果了。

## 三、总结

本次实验通过 `校花评选` 项目，让我们对前端布局和后端 PHP 编码能力有了较大提升。同时也了解并运用 [埃洛等级分系统](http://baike.baidu.com/link?url=5TaQ9N_gZjq6Mr876rdfeYWCaoUIA3yf8VtRjrIvp1UD6PHdUpqWFStWNwxKSrAUwH_cqokSSeGuJeDNEz0P2a) 算法对校花进行评比。本项目比较简单，容易上手，文章中只是对部分逻辑代码段进行了简单的说明，前端页面并未给出完整代码，大家可以参考我提供的源码理解学习。另外，如果大家在学习过程中有什么困惑，可以在课程下方给我留言，我会查看每条评论并一一解答，感谢大家的学习。