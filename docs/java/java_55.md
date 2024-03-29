# 结合七牛搭建个人相册

本项目基于七牛云存储的 SDK 实现个人相册服务，学习并实践 Java Web 开发基本方法及七牛云存储 Java API 接口。

## 一、 实验说明

### 1.1 课程概述

相信很多人都知道网站一般会有很多图片，对于小型网站来说，图片放在网站服务器上不算什么，但当图片数量很大时，会造成服务器很臃肿，相应地对带宽要求也会提高，这就造成了成本的增加。其实现在已经流行云存储，我们可以把图片、大文件等放到第三方提供的云存储服务上，这会减少一部分成本。这门课程就介绍了 JavaWeb 结合七牛云存储来搭建个人相册服务。

### 1.2 预备知识

#### 1.2.1 Servlet

>Servlet（Server Applet），全称 Java Servlet，未有中文译文。是用 Java 编写的服务器端程序。其主要功能在于交互式地浏览和修改数据，生成动态 Web 内容。狭义的 Servlet 是指 Java 语言实现的一个接口，广义的 Servlet 是指任何实现了这个 Servlet 接口的类，一般情况下，人们将 Servlet 理解为后者。 Servlet 运行于支持 Java 的应用服务器中。从原理上讲， Servlet 可以响应任何类型的请求，但绝大多数情况下 Servlet 只用来扩展基于 HTTP 协议的 Web 服务器。具体内容请参考：[百度百科](http://baike.baidu.com/link?url=KwyP2gawzy1GSGXhamjoPaNkSWsBUutfYn-U1Wwg0Rzj94pp8OZmDUFHqo72b2A3FgwKTd_IhMKdBzPBezyJMK)。

### 1.2.2 JSP

>JSP 全名为 Java Server Pages ，中文名叫 java 服务器页面，其根本是一个简化的 Servlet 设计，它是由 Sun Microsystems 公司倡导、许多公司参与一起建立的一种动态网页技术标准。 JSP 技术有点类似 ASP 技术，它是在传统的网页 HTML（标准通用标记语言的子集）文件( .htm , .html )中插入 Java 程序段( Scriptlet )和 JSP 标记( tag )，从而形成 JSP 文件，后缀名为( *.jsp )。 用 JSP 开发的 Web 应用是跨平台的，既能在 Linux 下运行，也能在其他操作系统上运行。它实现了 Html 语法中的 java 扩展（以 <%, %>形式）。 JSP 与 Servlet 一样，是在服务器端执行的。通常返回给客户端的就是一个 HTML 文本，因此客户端只要有浏览器就能浏览。 JSP 技术使用 Java 编程语言编写类 XML 的 tags 和 scriptlets ，来封装产生动态网页的处理逻辑。网页还能通过 tags 和 scriptlets 访问存在于服务端的资源的应用逻辑。 JSP 将网页逻辑与网页设计的显示分离，支持可重用的基于组件的设计，使基于 Web 的应用程序的开发变得迅速和容易。 JSP ( JavaServer Pages )是一种动态页面技术，它的主要目的是将表示逻辑从 Servlet 中分离出来。具体内容请参考：[百度百科](http://baike.baidu.com/item/JSP/141543)。

#### 1.2.3 Bootstrap

> Bootstrap ，来自 Twitter ，是目前很受欢迎的前端框架。 Bootstrap 是基于 HTML、CSS、JAVASCRIPT 的，它简洁灵活，使得 Web 开发更加快捷。它由 Twitter 的设计师 Mark Otto 和 Jacob Thornton 合作开发，是一个 CSS/HTML 框架。 Bootstrap 提供了优雅的 HTML 和 CSS 规范，它即是由动态 CSS 语言 Less 写成。 Bootstrap 一经推出后颇受欢迎，一直是 GitHub 上的热门开源项目，包括 NASA 的 MSNBC（微软全国广播公司）的 Breaking News 都使用了该项目。国内一些移动开发者较为熟悉的框架，如 WeX5 前端开源框架等，也是基于 Bootstrap 源码进行性能优化而来。

## 二、 七牛云准备

项目开始前，需要有一个七牛云存储的标准用户账号，请自行登录七牛云注册。

### 2.1 创建 bucket

首先登陆[七牛云开发](https://portal.qiniu.com/create),创建一个名为 shiyanlouphoto 的空间（即 bucket ）。如图：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668316801.jpg)

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668416667.jpg)

### 2.2 得到密钥

在个人面板中选择：密钥管理，如图：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469671216420.jpg)

即得到 Access Key 和 Secret Key 。

## 三、 数据库准备

### 3.1 创建数据库

这里我们使用 MySQL 数据库，创建名称为 shiyanloudb 的数据库：首先打开桌面上的 Xfce ，执行以下命令，启动 mysql 服务：

```java
sudo service mysql start 
```

如图：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668427993.jpg)

```java
mysql -u root 
```

创建并选中数据库：

```java
create database shiyanloudb DEFAULT CHARSET=utf8;

use shiyanloudb; 
```

### 3.2 创建表

```java
CREATE TABLE `image` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(16) DEFAULT NULL,
  `url` varchar(255) DEFAULT NULL,
  `date` datetime DEFAULT NULL,
  `user_id` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(16) DEFAULT NULL,
  `password` varchar(16) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8; 
```

如图:

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668461807.jpg)

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668468397.jpg)

数据库准备完毕。退出 Xfce。

## 四、 创建 JavaWeb 项目

### 4.1 配置项目所需 jar 包

打开 eclipse，New，选择 web 下面的 Dynamic Web Project，创建一个名称为 ShiyanlouPhoto 的动态 Web 项目，如图：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668536956.jpg)

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668547966.jpg)

![此处输入图片的描述](img/document-uid122889labid256timestamp1469685834791.jpg)

获取所需的 jar 包，在 Xfce 中输入：

```java
wget http://labfile.oss.aliyuncs.com/courses/54/lib.tar.gz 
```

解压：

```java
tar -zvxf lib.tar.gz 
```

将解压好的 Jar 包复制到项目的 WebContext ，下面的 WEB-INF 的 lib 。

### 4.2 Bootstrap 准备

前端使用 Bootstrap ，实验楼已把 Bootstrap 所需的 css、fonts 和 js 文件打包压缩，我们只需要获取并解压，然后放到 WebContent 目录下。 获取命令，打开 Xfce ，输入：

```java
wget http://labfile.oss.aliyuncs.com/courses/54/bootstrap-3.3.5-dist.tar.gz 
```

解压命令

```java
tar -zvxf bootstrap-3.3.5-dist.tar.gz 
```

稍后，我们监理项目完成，选中如图三个文件夹拷贝到 eclipse 中项目的 WebContent 目录下。

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668505470.jpg)

项目目录如下：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668514534.jpg)

下面创建项目。

### 4.3 前台页面编写

#### 4.3.1 编写 index.jsp

在 WebContent 下创建

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>ShiyanlouPhoto</title>
    <link href="css/bootstrap.min.css" rel="stylesheet">
    <!--[if lt IE 9]>
      <script src="http://labfile.oss.aliyuncs.com/libs/html5shiv/3.7.2/html5shiv.min.js"></script>
      <script src="http://labfile.oss.aliyuncs.com/respond.js/1.4.2/respond.min.js"></script>
    <![endif]-->
  </head>
  <body>
    <div class="container">
        <div class="row">
            <div class="col-xs-12 text-center">
                <h2>ShiyanlouPhoto</h2>
            </div>
        </div>
        <div class="row">
            <div id="alert1"  class="alert alert-success fade in text-center col-xs-2 col-xs-offset-5 hide">
                <strong>RegisterSuccess</strong>
            </div>
        </div>
        <form id="form" class="form-horizontal" role="form" style="margin-top: 73px;">
          <div class="form-group"  >
            <label for="username" class="col-xs-2 control-label col-sm-offset-3" >Username</label>
            <div class="col-xs-2">
              <input type="text" class="form-control" id="username" rel="tooltip"/>
            </div>
          </div>
          <div class="form-group">
            <label for="password" class="col-xs-2 control-label col-sm-offset-3">Password</label>
            <div class="col-xs-2">
              <input type="password" class="form-control" id="password"/>
            </div>
          </div>
          <div class="form-group">
            <div class="col-sm-offset-5 col-xs-1">
              <button type="button" class="btn btn-success" id="login">Login</button>
            </div>
            <div class="col-sm-1">
              <button type="button" class="btn btn-danger" data-toggle="modal" data-target="#myModal">Register</button>
            </div>
          </div>
        </form>
    </div>

    <!-- 注册对话框 begin -->
    <div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <button type="button" class="close" data-dismiss="modal"><span aria-hidden="true">&times;</span><span class="sr-only">Close</span></button>
            <h4 class="modal-title" id="myModalLabel">UserRegister</h4>
          </div>
          <div class="modal-body">
            <form class="form-horizontal" role="form">
              <div class="form-group"  >
                <label for="reg_username" class="col-xs-2 control-label" >Username</label>
                <div class="col-xs-4">
                  <input type="text" class="form-control" id="reg_username"/>
                </div>
              </div>
              <div class="form-group">
                <label for="reg_password" class="col-xs-2 control-label">Password</label>
                <div class="col-xs-4">
                  <input type="password" class="form-control" id="reg_password"/>
                </div>
              </div>
              <div class="form-group">
                <label for="reg_repassword" class="col-xs-2 control-label">Reinput</label>
                <div class="col-xs-4">
                  <input type="password" class="form-control" id="reg_repassword"/>
                </div>
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-default" data-dismiss="modal">Cancel</button>
            <button type="button" class="btn btn-primary" id="register">Register</button>
          </div>
        </div>
      </div>
    </div>
    <!-- 注册对话框 end -->

    <script src="http://labfile.oss.aliyuncs.com/jquery/1.11.1/jquery.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script type="text/javascript">
        $(document).ready(function() {
            //点击登录
            $('#login').click(function() {
                //提交登录表单
                $.post('${pageContext.request.contextPath}' + '/UserAction?type=1',
                {
                    username: $('#username').val(),
                    password: $('#password').val()
                },
                function(data, status) {
                    if (data == '1') {
                        createPopOver('#username', 'right', 'Username can not be empty ', 'show');
                    } else if (data == '2') {
                        createPopOver('#password', 'right', ' password can not be empty', 'show');
                    } else if (data == '3') {
                        createPopOver('#username', 'right', ' Username does not exist', 'show');
                    } else if (data == '4') {
                        createPopOver('#password', 'right', ' wrong password', 'show');
                    } else if (data == 5) {
                        location.href = '${pageContext.request.contextPath}' + '/home.jsp';
                    }
                });
            });

            //点击注册按钮
            $('#register').click(function() {
                //提交注册表单
                $.post('${pageContext.request.contextPath}' + '/UserAction?type=2',
                {
                    username: $('#reg_username').val(),
                    password: $('#reg_password').val(),
                    repassword: $('#reg_repassword').val()
                },
                function(data, status) {
                    if (data == '1') {
                        createPopOver('#reg_username', 'right', 'not null', 'show');
                    } else if (data == '2') {
                        createPopOver('#reg_password', 'right', 'not null', 'show');
                    } else if (data == '3') {
                        createPopOver('#reg_repassword', 'right', 'not null', 'show');
                    } else if (data == '4') {
                        createPopOver('#reg_repassword', 'right', 'Passwords do not match', 'show');
                    } else if (data == '5') {
                        createPopOver('#reg_username', 'right', 'Username already exists', 'show');
                    } else if (data == '6') {
                        $('#reg_username').val('');
                        $('#reg_password').val('');
                        $('#reg_repassword').val('');
                        $('#myModal').modal('hide');
                        $('#alert1').removeClass('hide');
                        $('#form').css('margin-top', '0px');
                    }
                });
            }); 

            //显示弹出框
            function createPopOver(id, placement, content, action) {
                $(id).popover({
                    placement: placement,
                    content: content
                });
                $(id).popover(action);
            }

            //点击输入框时销毁弹出框
            $('#username').click(function() {
                $('#username').popover('destroy');
            });

            $('#password').click(function() {
                $('#password').popover('destroy');
            });

            $('#reg_username').click(function() {
                $('#reg_username').popover('destroy');
            });

            $('#reg_password').click(function() {
                $('#reg_password').popover('destroy');
            });

            $('#reg_repassword').click(function() {
                $('#reg_repassword').popover('destroy');
            });
        });
    </script>
  </body>
</html> 
```

#### 4.3.2 编写 home.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>${user.username}Photo</title>
    <link href="css/bootstrap.min.css" rel="stylesheet">
    <!--[if lt IE 9]>
      <script src="http://labfile.oss.aliyuncs.com/libs/html5shiv/3.7.2/html5shiv.min.js"></script>
      <script src="http://labfile.oss.aliyuncs.com/respond.js/1.4.2/respond.min.js"></script>
    <![endif]-->
  </head>
  <body>
    <div class="container">
        <!-- 首部 start -->
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <h3 class="page-header">
                    ${user.username}&nbsp;&nbsp;&nbsp;<small>Totals:<span class="badge">${imageList.size()}</span></small>
                    <div class="btn-group pull-right">
                      <button type="button" class="btn btn-primary dropdown-toggle" data-toggle="dropdown">
                        options<span class="caret"></span>
                      </button>
                      <ul class="dropdown-menu" role="menu">
                        <li><a href="#" data-toggle="modal" data-target="#myModa2">Upload</a></li>
                        <li><a href="#" data-toggle="modal" data-target="#myModa3">Delete</a></li>
                        <li><a href="#" data-toggle="modal" data-target="#myModa4">Exit</a></li>
                      </ul>
                    </div>
                </h3>
            </div>
        </div>
        <!-- 首部 end -->

        <!-- 显示图片列表 -->
        <c:forEach items="${imageList}" varStatus="status" var="image">
            <c:choose>
                <c:when test="${status.first or status.index % 4 eq 0}">
                    <div class="row">
                        <div class="col-xs-2 col-xs-offset-2">
                          <a href="#" class="thumbnail text-center">
                            <img name="${image.name}" date="<fmt:formatDate value='${image.date}' pattern='yyyy-MM-dd HH:mm'/>" style="width: 140px; height: 130px;" src="http:///oayqj2sj0.bkt.clouddn.com/${image.url}"><!--这里请填写自己的链接地址，详见下图-->
                            <input class="pull-left" type="checkbox" value="${image.id}" url="${image.url}"/>${image.name }
                          </a>
                        </div>
                </c:when>
                <c:when test="${status.index % 4 eq 3 and not status.last }">
                        <div class="col-xs-2">
                          <a href="#" class="thumbnail text-center">
                            <img name="${image.name}" date="<fmt:formatDate value='${image.date}' pattern='yyyy-MM-dd HH:mm'/>" style="width: 140px; height: 130px;" src="http://oayqj2sj0.bkt.clouddn.com/${image.url}"><!--这里请填写自己的链接地址，详见下图-->
                            <input class="pull-left" type="checkbox" value="${image.id}" url="${image.url}" />${image.name }
                          </a>
                        </div>
                    </div>
                </c:when>
                <c:otherwise>
                    <div class="col-xs-2">
                      <a href="#" class="thumbnail text-center">
                        <img name="${image.name}" date="<fmt:formatDate value='${image.date}' pattern='yyyy-MM-dd HH:mm'/>" style="width: 140px; height: 130px;" src="http:///oayqj2sj0.bkt.clouddn.com/${image.url}"><!--这里请填写自己的链接地址，详见下图-->
                        <input class="pull-left" type="checkbox" value="${image.id}" url="${image.url}"/>${image.name }
                      </a>
                    </div>
                </c:otherwise>
            </c:choose>
            <c:if test="${status.last}">
                </div>
            </c:if>
        </c:forEach>
        <!-- 显示图片列表 end -->
    </div>

    <!-- 显示图片对话框 start -->
    <div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
            <h4 class="modal-title" id="myModalLabel"></h4>
          </div>
          <div class="modal-body" id="modal-content">
          </div>
        </div>
      </div>
    </div>
    <!-- 显示图片对话框 end -->

    <!-- 上传图片对话框 start -->
    <div class="modal fade" id="myModa2" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h4 class="modal-title" id="myModalLabe2">Image Upload</h4>
          </div>
          <div class="modal-body">
            <form class="form-horizontal" role="form" id="form">
              <div class="form-group"  >
                <label for="image_name" class="col-xs-2 control-label" >Title</label>
                <div class="col-xs-4">
                  <input type="text" class="form-control" id="image_name" name="image_name"/>
                </div>
              </div>
              <div class="form-group">
                <label for="image" class="col-xs-2 control-label">Image</label>
                <div class="col-xs-4">
                    <input type="file" id="image" name="image"/>
                </div>
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-default" data-dismiss="modal">Cancel</button>
            <button type="button" class="btn btn-primary" id="upload">Upload</button>
          </div>
        </div>
      </div>
    </div>
    <!-- 上传图片对话框 end -->

    <!-- 删除图片对话框 start -->
    <div class="modal fade" id="myModa3" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h4 class="modal-title" id="myModalLabe3">confirm to delete?</h4>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-default" data-dismiss="modal">Cancel</button>
            <button type="button" class="btn btn-danger" id="delete">Confirm</button>
          </div>
        </div>
      </div>
    </div>
    <!-- 删除图片对话框 end -->

    <!-- 退出对话框 start -->
    <div class="modal fade" id="myModa4" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h4 class="modal-title" id="myModalLabe4">confirm to exit?</h4>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-default" data-dismiss="modal">Cancel</button>
            <button type="button" class="btn btn-danger" id="exit">Confirm</button>
          </div>
        </div>
      </div>
    </div>
    <!-- 退出对话框 end -->

    <script src="http://labfile.oss.aliyuncs.com/jquery/1.11.1/jquery.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script type="text/javascript">
        $(document).ready(function() {
            //点击图片
            $('img').click(function() {
                $('#myModalLabel').html($(this).attr('name') + '&nbsp;&nbsp;&nbsp;<small>' + $(this).attr('date') + '</small>');
                $('#modal-content').html('<img class=\'img-responsive\' src=\'' + $(this).attr('src') + '\'/>');
                $('#myModal').modal('show');
            });

            //点击上传
            $('#upload').click(function() {
                if ($('#image_name').val() == '' || $('#image').val() == '') {
                } else {
                    $('#form').attr('action', '${pageContext.request.contextPath}' + '/ImageAction?type=1');
                    $('#form').attr('enctype', 'multipart/form-data');
                    $('#form').attr('method', 'post');
                    $('#form').submit();
                }
            });

            //点击确定退出
            $('#exit').click(function() {
                $.get('${pageContext.request.contextPath}' + '/UserAction?type=3', function(data, status) {
                    location.href = '${pageContext.request.contextPath}' + '/index.jsp';
                });
            });

            //点击确定删除图片
            $('#delete').click(function() {
                var ids = "";
                var urls = "";
                $('input[type=checkbox]:checked').each(function() {
                    ids += $(this).val() + ',';
                    urls += $(this).attr('url') + ',';
                }); 
                $.post('${pageContext.request.contextPath}' + '/ImageAction?type=2', {
                    ids: ids,
                    urls: urls
                },function(data, status) {
                    $('#myModa3').modal('hide');
                    location.href = '${pageContext.request.contextPath}' + '/home.jsp';
                });
            });
        });
    </script>
  </body>
</html> 
```

链接地址：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469672932333.jpg)

### 4.4 编写后台代码

#### 4.4.1 创建 User 类

```java
package com.shiyanlou.photo.domain;

import java.io.Serializable;
import java.util.List;

/**
 * 用户类
 * @author www.shiyanlou.com
 *
 */
@SuppressWarnings("serial")
public class User implements Serializable {
    private int id; //用户 ID
    private String username;    //用户名
    private String password;    //密码
    private List<Image> images;   //图片列表

    public User() {
    }

    public User(int id, String username, String password, List<Image> images) {
        this.id = id;
        this.username = username;
        this.password = password;
        this.images = images;
    }

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    public User(int id) {
        this.id = id;
    }

    public List<Image> getImages() {
        return images;
    }

    public void setImages(List<Image> images) {
        this.images = images;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

} 
```

#### 4.4.2 创建 Image 类

```java
package com.shiyanlou.photo.domain;

import java.io.Serializable;
import java.util.Date;

/**
 * 图片类
 * @author www.shiyanlou.com
 *
 */
@SuppressWarnings("serial")
public class Image implements Serializable {
    private int id; //图片 ID
    private String name;    //图片名
    private String url; //图片 URL
    private Date date;  //上传时间
    private User user;  //所属用户

    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public String getUrl() {
        return url;
    }
    public void setUrl(String url) {
        this.url = url;
    }
    public Date getDate() {
        return date;
    }
    public void setDate(Date date) {
        this.date = date;
    }
    public User getUser() {
        return user;
    }
    public void setUser(User user) {
        this.user = user;
    }
} 
```

#### 4.4.3 创建数据库工具类 DBUtils

```java
package com.shiyanlou.photo.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;

/**
 * 数据库工具类
 * @author www.shiyanlou.com
 *
 */
public class DBUtils {
    private static Connection connection = null;
    private static PreparedStatement preparedStatement = null;
    private static ResultSet resultSet = null;

    //初始化
    static {
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取连接
     * @return
     */
    private static Connection getConnection() {
        try {
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/shiyanloudb?useUnicode=true&characterEncoding=UTF-8", "root", "");//将数据库名字修改为自己的数据库，root 为账户名，本实验环境 mysql 密码为空
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return connection;
    }

    /**
     * 关闭连接、预处理语句和结果集
     * @param connection
     * @param preparedStatement
     * @param resultSet
     */
    private static void close(Connection connection, PreparedStatement preparedStatement, ResultSet resultSet) {
        try {
            if (resultSet != null) {
                resultSet.close();
                resultSet = null;
            }

            if (preparedStatement != null) {
                preparedStatement.close();
                preparedStatement = null;
            }

            if (connection != null) {
                connection.close();
                connection = null;
            }           
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 查询数据库
     * @param sql SQL 语句
     * @param parameters 参数
     * @return
     */
    public static ArrayList<Object[]> query(String sql, String[] parameters) {
        ArrayList<Object[]> list = new ArrayList<Object[]>();
        try {
            connection = getConnection();
            preparedStatement = connection.prepareStatement(sql);
            for (int i = 0; i < parameters.length; i++) {
                preparedStatement.setString(i + 1, parameters[i]);
            }
            resultSet = preparedStatement.executeQuery();
            int columnCount = resultSet.getMetaData().getColumnCount();

            while (resultSet.next()) {
                Object[] objects = new Object[columnCount];
                for (int i = 0; i < columnCount; i++) {
                    objects[i] = resultSet.getObject(i + 1);
                }
                list.add(objects);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            close(connection, preparedStatement, resultSet);
        }
        return list;
    }

    /**
     * 更新数据库
     * @param sqls SQL 语句数组
     * @param parameters 参数数组
     */
    public static void updates(String[] sqls, String[][] parameters) {
        try {
            connection = getConnection();
            connection.setAutoCommit(false);
            for (int i = 0; i < sqls.length; i++) {
                preparedStatement = connection.prepareStatement(sqls[i]);
                for (int j = 0; j < parameters[i].length; j++) {
                    preparedStatement.setString(j + 1, parameters[i][j]);
                }
                preparedStatement.executeUpdate();
            }
            connection.commit();
        } catch (Exception e) {
            try {
                connection.rollback();
            } catch (SQLException e1) {
                e1.printStackTrace();
            }
            e.printStackTrace();
        } finally {
            close(connection, preparedStatement, resultSet);
        }
    }
} 
```

#### 4.4.4 创建文件工具类 FileUtils（使用了七牛云存储服务）

```java
package com.shiyanlou.photo.util;

import java.io.InputStream;

import org.json.JSONException;

import com.qiniu.api.auth.AuthException;
import com.qiniu.api.auth.digest.Mac;
import com.qiniu.api.io.IoApi;
import com.qiniu.api.rs.PutPolicy;
import com.qiniu.api.rs.RSClient;

/**
 * 图片工具类（使用七牛云存储服务）
 * @author www.shiyanlou.com
 *
 */
public class FileUtils {
    private static final String ACCESS_KEY = " your Access Key";//这里填上面我们讲到的，密钥管理里面的密钥
    private static final String SECRET_KEY = "your Secret Key";
    private static final String BUCKET_NAME = " your Bucket Name";//填我们在七牛云创建的 Bucket 名字

    /**
     * 上传图片到七牛云存储
     * @param reader
     * @param fileName
     */
    public static void upload(InputStream reader, String fileName) {
        String uptoken;
        try {
            Mac mac = new Mac(ACCESS_KEY, SECRET_KEY);
            PutPolicy putPolicy = new PutPolicy(BUCKET_NAME);
            uptoken = putPolicy.token(mac);
            IoApi.Put(uptoken, fileName, reader, null);
        } catch (AuthException e) {
            e.printStackTrace();
        } catch (JSONException e) {
            e.printStackTrace();
        }
    }

    /**
     * 删除七牛云存储上的图片
     * @param key
     */
    public static void delete( String key) {
        Mac mac = new Mac(ACCESS_KEY, SECRET_KEY);
        RSClient client = new RSClient(mac);
        client.delete(BUCKET_NAME, key);
    }
} 
```

#### 4.4.5 创建用户服务类 UserService

```java
package com.shiyanlou.photo.service;

import java.util.List;

import com.shiyanlou.photo.domain.User;
import com.shiyanlou.photo.util.DBUtils;

/**
 * 用户服务类
 * @author www.shiyanlou.com
 *
 */
public class UserService {
    /**
     * 通过用户名获取用户
     * @param username 用户名
     * @return 用户
     */
    public User getUserByUsername(String username) {
        String sql = "select id, username, password from user where username = ?";
        String[] parameters = {username};
        List<Object[]> users = DBUtils.query(sql, parameters);
        if (users.size() == 0) {
            return null;
        } else {
            Object[] objects = users.get(0);
            return objects == null ? null : new User(Integer.parseInt(objects[0].toString()), objects[1].toString(), objects[2].toString(), null);
        }
    }

    /**
     * 添加用户
     * @param user 用户
     */
    public void addUser(User user) {
        String[] sqls = {"insert into user(username, password) values(?, ?)"};
        String[][] parameters = {{user.getUsername(), user.getPassword()}};
        DBUtils.updates(sqls, parameters);
    }
} 
```

#### 4.4.6 创建图片服务类 ImageService

```java
package com.shiyanlou.photo.service;

import java.io.InputStream;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import com.shiyanlou.photo.domain.Image;
import com.shiyanlou.photo.domain.User;
import com.shiyanlou.photo.util.DBUtils;
import com.shiyanlou.photo.util.FileUtils;

/**
 * 图片服务类
 * @author www.shiyanlou.com
 *
 */
public class ImageService {
    /**
     * 通过用户 ID 获取图片列表
     * @param userId 用户 ID
     * @return 图片列表
     */
    public ArrayList<Image> getByUserId(int userId) {
        ArrayList<Image> images = new ArrayList<Image>();
        String sql = "select id, name, url, date, user_id from image where user_id = ? order by date desc";
        String[] parameters = {userId + ""};
        List<Object[]> imageList = DBUtils.query(sql, parameters);
        for (Object[] objects : imageList) {
            Image image = new Image();
            image.setId(Integer.parseInt(objects[0].toString()));
            image.setName(objects[1].toString());
            image.setUrl(objects[2].toString());
            image.setDate((Date) objects[3]);
            image.setUser(new User(Integer.parseInt(objects[4].toString())));
            images.add(image);
        }
        return images;
    }

    /**
     * 上传图片
     * @param image 图片
     * @param inputStream 输入流
     */
    public void addImage(Image image, InputStream inputStream) {
        FileUtils.upload(inputStream, image.getUrl());
        String[] sqls = {"insert into image(name, url, date, user_id) values(?, ?, ?, ?)"};
        String[][] parameters = {{image.getName(), image.getUrl(), new SimpleDateFormat("yyyy-MM-dd HH:mm").format(image.getDate()), image.getUser().getId() + ""}};
        DBUtils.updates(sqls, parameters);
    }

    /**
     * 通过图片 ID 数组和图片 URL 数组删除图片
     * @param ids 图片 ID 数组
     * @param urls 图片 URL 数组
     */
    public void delByIdsAndUrls(String ids, String urls) {
        String[] idArray = ids.split(",");
        String[] urlArray = urls.split(",");
        if (!"".equals(idArray[0]) && !"".equals(urlArray[0])) {
            String[] sqls = new String[idArray.length];
            String[][] parameters = new String[idArray.length][1];
            for (int i = 0; i < idArray.length; i++) {
                FileUtils.delete(urlArray[i]);
                sqls[i] = "delete from image where id = ?";
                parameters[i][0] = idArray[i];
            }
            DBUtils.updates(sqls, parameters);
        }
    }
} 
```

#### 4.4.7 创建用户控制器类 UserAction

```java
package com.shiyanlou.photo.action;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.shiyanlou.photo.domain.User;
import com.shiyanlou.photo.service.ImageService;
import com.shiyanlou.photo.service.UserService;

/**
 * 用户控制器
 * @author www.shiyanlou.com
 *
 */
@WebServlet(value = "/UserAction")
public class UserAction extends HttpServlet {
    private static final long serialVersionUID = 1L;

    /**
     * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        response.setCharacterEncoding("Utf-8");
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        UserService userService = new UserService();
        ImageService imageService = new ImageService();

        Integer type = Integer.valueOf(request.getParameter("type"));
        if (type == 1) {    //用户登录
            String username = request.getParameter("username");
            String password = request.getParameter("password");
            String result = null;
            User user = null;
            //验证用户是否有效
            if (username.isEmpty()) {
                result = "1";
            } else if (password.isEmpty()) {
                result = "2";
            } else if ((user = userService.getUserByUsername(username)) == null) {
                result = "3";
            } else {
                if (!user.getPassword().equals(password)) {
                    result = "4";
                } else {
                    request.getSession().setAttribute("user", user);
                    request.getSession().setAttribute("imageList", imageService.getByUserId(user.getId()));
                    result = "5";
                }
            }
            out.print(result);
        } else if (type == 2) { //用户注册
            String username = request.getParameter("username");
            String password = request.getParameter("password");
            String repassword = request.getParameter("repassword");
            String result = null;
            //验证有效性
            if (username.isEmpty()) {
                result = "1";
            } else if (password.isEmpty()) {
                result = "2";
            } else if (repassword.isEmpty()) {
                result = "3";
            } else if (!repassword.equals(password)) {
                result = "4";
            } else if (userService.getUserByUsername(username) != null) {
                result = "5";
            } else {
                User user = new User(username, password);
                //添加用户
                userService.addUser(user);
                result = "6";
            }
            out.print(result);
        } else if (type == 3) { //用户退出
            request.getSession().invalidate();
        }
    }

    /**
     * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doGet(request, response);
    }

} 
```

#### 4.4.8 创建图片控制器类 ImageAction

```java
package com.shiyanlou.photo.action;

import java.io.IOException;
import java.util.Date;
import java.util.UUID;

import javax.servlet.ServletException;
import javax.servlet.annotation.MultipartConfig;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.Part;

import com.shiyanlou.photo.domain.Image;
import com.shiyanlou.photo.domain.User;
import com.shiyanlou.photo.service.ImageService;

/**
 * 图片控制器
 * @author www.shiyanlou.com
 *
 */
@WebServlet(value = "/ImageAction")
@MultipartConfig
public class ImageAction extends HttpServlet {
    private static final long serialVersionUID = 1L;

    /**
     * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("utf-8");
        response.setCharacterEncoding("utf-8");
        response.setContentType("text/html;charset=utf-8");
        Integer type = Integer.valueOf(request.getParameter("type"));
        ImageService imageService = new ImageService();

        if (type == 1) {    //上传图片
            String imageName = request.getParameter("image_name");
            Part image = request.getPart("image");
            Image img = new Image();
            img.setDate(new Date());
            img.setName(imageName);
            img.setUser((User) request.getSession().getAttribute("user"));
            img.setUrl(img.getUser().getUsername() + "/" + UUID.randomUUID());
            imageService.addImage(img, image.getInputStream());
            request.getSession().setAttribute("imageList", imageService.getByUserId(img.getUser().getId()));
            response.sendRedirect(request.getContextPath() + "/home.jsp");
        } else if (type == 2) { //删除图片
            String ids = request.getParameter("ids");
            String urls = request.getParameter("urls");
            imageService.delByIdsAndUrls(ids, urls);
            request.getSession().setAttribute("imageList", imageService.getByUserId(((User) request.getSession().getAttribute("user")).getId()));
        }
    }

    /**
     * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doGet(request, response);
    }

} 
```

## 五、 发布

把 ShiyanlouPhoto 部署到 Tomcat 上，如图：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668874297.jpg)

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668898003.jpg)

启动服务，点击 Start ，如图：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469668996793.jpg)

启动服务器，访问 [`localhost:8080/ShiyanlouPhoto/index.jsp`](http://localhost:8080/ShiyanlouPhoto/index.jsp)

## 六、 项目运行

输入网址，进入登陆注册页面 index.jsp ,首先进行注册，如图：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469669402580.jpg)

然后登陆：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469669455183.jpg)

跳转至 home.jsp 页面，添加图片，选择右侧 Options ，点击 Upload ，如图：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469669641680.jpg)

![此处输入图片的描述](img/document-uid122889labid256timestamp1469669706970.jpg)

添加成功如下：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469669745832.jpg)

选中图片，点击 Options ，选择 Delete 删除：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469669790422.jpg)

![此处输入图片的描述](img/document-uid122889labid256timestamp1469669803847.jpg)

删除成功。

注释： 运行项目时如果报错：

```java
org.apache.jasper.JasperException: The absolute uri: http://java.sun.com/jsp/jstl/core cannot be 。 
```

可以将与 jstl 有关的 jar 包拷贝到 tomcat 的 lib 文件夹下，即以下两个文件：

![此处输入图片的描述](img/document-uid122889labid256timestamp1469685775979.jpg)

## 七、 项目总结

本课程，在本地运行 JavaWeb 项目，通过七牛云平台，存储图片数据，有很强的实际意义。希望大家能够亲自动手编写，熟练掌握这种方法。