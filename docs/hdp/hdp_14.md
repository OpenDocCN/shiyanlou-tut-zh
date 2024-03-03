# 第 2 节 Mahout 安装配置

* * *

## 实验简介

本节实验，介绍 Mahout 的安装和配置。

* * *

## 一、实验环境说明

**1\. 环境登录**

无需密码自动登录，系统用户名 shiyanlou

**2\. 环境介绍**

本实验环境采用带桌面的 Ubuntu Linux 环境，实验中会用到桌面上的程序：

> * XfceTerminal: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令；

*   Firefox：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可；
*   GVim：非常好用的编辑器，最简单的用法可以参考课程 [Vim 编辑器](http://www.shiyanlou.com/courses/2)。
*   Eclipse：Eclipse 是著名的跨平台的自由集成开发环境（IDE）。主要用来 Java 语言开发，但是目前亦有人通过插件使其作为 C++ 和 Python 等语言的开发工具。

**3\. 环境使用**

使用 GVim 编辑器输入实验所需的代码，然后使用 XfceTerminal 命令行环境进行编译运行，查看运行结果，运行后可以截图并分享自己的实验成果，实验楼提供的截图是后台截图，无法作弊，可以真实有效证明您已经完成了实验。

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

## 二、Mahout 与 Hadoop

> * 说明：Mahout 官网目前最新的已编译版本为 0.9，此版本只支持 Hadoop V1.x.y 版本；如果要使用 Hadoop V2.0.0 及其以上版本，需下载最新的 Mahout 源码自行编译。这里为了大家实验的方便，我们已经为大家重新安装了 Hadoop V1.2.1 版本，你只需要下载 Mahout 0.9 解压安装即可。

从 Mahout 0.9 源码中的 pom.xml 文件中，可以发现其支持的 Hadoop 版本：

![图片描述信息](img/84fe368559636b04ce053236d1f72aa5.jpg)

最新的 Mahout 源码地址，请点击 [这里](https://github.com/apache/mahout)。同样可以看到，已经支持 Hadoop V2.0.0 以上版本了。

![图片描述信息](img/edec0d277950c32a6a28d7504a5fec74.jpg)

## 三、下载 Mahout

为了方便大家实验，本实验环境集成 Hadoop V1.2.1 （单机模式），且将其设为了普通权限，无需切换到 Hadoop 用户目录，你只需输入命令来下载（路径自定义，例如 /usr/local）：

```java
$ sudo wget http://labfile.oss.aliyuncs.com/mahout-distribution-0.9.tar.gz 
```

解压下载的文件：

```java
$ sudo tar zxvf mahout-distribution-0.9.tar.gz 
```

建议修改文件夹名：

```java
$ sudo mv mahout-distribution-0.9 mahout-0.9 
```

*PS：（如果提示权限不够，请 sudo）*

## 四、配置环境变量

修改 /etc/profile 文件，这个文件我们在前面的实验中已经修改过多次了，相信大家已经很熟悉了。如果你不熟悉 Vim 的使用，可以点击[Vim 编辑器](http://www.shiyanlou.com/courses/2) 课程回顾。

```java
$ sudo vim /etc/profile 
```

添加如下环境变量（HADOOP*HOME 是实验环境集成的，大家路径都一致；但 MAHOUT*HOME 需要根据你自己实际解压之后存放的路径来填写）：

```java
# Hadoop Environment
export HADOOP_HOME=/usr/local/hadoop-1.2.1
export HADOOP_CONF_DIR=$HADOOP_HOME/conf

# Mahout Environment
export MAHOUT_HOME=/usr/local/mahout-0.9
export MAHOUT_CONF_DIR=$MAHOUT_HOME/conf
export PATH=$PATH:$MAHOUT_HOME/conf:$MAHOUT_HOME/bin 
```

保存刚刚的修改，并使之生效：

```java
$ source /etc/profile 
```

## 五、启动 Mahout

确认上面的步骤我们都没有问题后，就可以验证 Mahout 是否安装成功，在 `$MAHOUT_HOME/bin` 路径下输入以下代码，就会输出 Mahout 所支持的命令：

```java
$ mahout -help 
```

![图片描述信息](img/3c8d0dcc870f82125c37ea0ee70cbcc1.jpg)

看到类似上图的信息，就说明 Mahout 安装成功了。

## 作业

根据我们提供的信息和地址，同学们可以试试自行下载 Mahout 最新的源码来编译安装。

## 参考文档

> * 《Hadoop 实战 第 2 版》陆嘉恒，机械工业出版社；

*   [Mahout 运行在 Hadoop 2.x 上兼容性问题的解决](http://f.dataguru.cn/thread-381702-1-1.html)；