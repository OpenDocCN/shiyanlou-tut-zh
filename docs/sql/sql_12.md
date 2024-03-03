# 第 1 节 一、实验说明

## 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou

## 2\. 环境介绍

本实验环境采用带桌面的 Ubuntu Linux 环境，实验中会用到桌面上的程序：

1.  命令行终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令

2.  Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可

3.  gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)

4.  gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

## 3\. 环境使用

使用 GVim 编辑器输入实验所需的代码及文件，使用 Xfce 终端（XfceTerminal）运行所需命令进行操作。

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

## 4\. 课程来源

本实验课程基于 MySQL[简体中文官方手册](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/)制作，关于 MySQL 参考手册，请访问：[官网文档](http://dev.mysql.com/doc/refman/5.7/en/index.html)。 原始参考手册为英文版，与英文版参考手册相比，翻译版可能不是最新的。

译者说明：这是 MySQL 参考手册 它涉及 MySQL 5.1 至 5.1.2-alpha 版本。

## 二、MySQL 介绍

> - MySQL 是一个关系型数据库管理系统，由瑞典 MySQLAB 公司开发，目前属于 Oracle 公司。MySQL 是最流行的关系型数据库管理系统，在 WEB 应用方面 MySQL 是最好的 RDBMS(Relational Database Management System：关系数据库管理系统)应用软件之一。

*   MySQL 是一种关联数据库管理系统，关联数据库将数据保存在不同的表中，而不是将所有数据放在一个大仓库内，这样就增加了速度并提高了灵活性。
*   MySQL 所使用的 SQL 语言是用于访问数据库的最常用标准化语言。由于其体积小、速度快、总体拥有成本低，尤其是开放源码这一特点，一般中小型网站的开发都选择 MySQL 作为网站数据库。由于其社区版的性能卓越，搭配 PHP 和 Apache 可组成良好的开发环境。

## 二、安装 MySQL

实验楼已经为大家安装好了 mysql，无需大家再次安装，以下安装仅用于大家学习使用

## 1.Windows 下安装

可以从[官网](http://dev.mysql.com/downloads)下载 MySQL 安装软件包。在 Windows 中安装 MySQL 时，新用户可以使用 MySQL 安装帮助和`MySQL Configuration Wizard`（配置向导）。

在 Windows 中安装 MySQL 时，有 3 种 MySQL 5.1 安装软件包可供选择：

*   基本安装：该安装软件包的文件名类似于`mysql-essential-5.1.2-alpha-win32.msi`，包含在 Windows 中安装 MySQL 所需要的最少的文件，包括配置向导。该安装软件包不包括可选组件，例如嵌入式服务器和基准套件。

*   完全安装：该安装软件包的文件名类似于`mysql-5.1.2-alpha-win32.zip`，包含在 Windows 中安装 MySQL 所需要的全部文件，包括配置向导。该安装软件包包括可选组件，例如嵌入式服务器和基准套件。

*   非自动安装文件：该安装软件包的文件名类似于`mysql-noinstall-5.1.2-alpha-win32.zip`，包含完整安装包中的全部文件，只是不包括配置向导。该安装软件包不包括自动安装器，必须手动安装和配置。

对于大多数用户，建议选择基本安装。

如果你下载的安装软件包在`Zip`文件中，你需要先解压文件。如果有`setup.exe`文件，双击启动安装过程。如果有`.msi`文件，双击启动安装过程。

## 2.Linux 下安装

建议在 Linux 中使用`RPM`（`RedHat`系列）或者`DEB`（`Debian/Ubuntu`系列）包来安装 MySQL，在大多数情况，你只需要通过命令行的方式`apy-get`安装`MySQL-server`和`MySQL-client`软件包来安装 MySQL。在标准安装中不需要其它的包。如果你想要运行含更多功能的`MySQL-Max`服务器，还应当安装`MySQL-Max RPM`。必须先安装`M MySQL-server RPM`再安装该模块。

## 三、实验内容

## 1.启动 mysql 服务器

实验楼配置的环境中，默认 mysql 是没有启动的，我们首先在命令行启动 mysql 服务器：

```sql
$ sudo service mysql start 
```

## 2.连接与断开服务器

为了连接服务器，当调用 mysql 时，通常需要提供一个 MySQL 用户名并且很可能需要一个密码。在这里我们使用`root`用户连接服务器（密码环境设置为空，无需我们输入密码）,输入以下命令连接服务器：

```sql
$ mysql -u root 
```

如果有效，你应该看见一些介绍信息：

![此处输入图片的描述](img/52177d2dac454aad61b5b94ec67b6ce5.jpg)

`mysql>`提示符告诉你 mysql 准备为你输入命令。

成功地连接后，可以在`mysql>`提示下输入`QUIT` (或`\q`)随时退出：

## 2.输入查询

这是一个简单的命令，要求服务器告诉 MySQL 的版本号和当前日期。在`mysql>`提示输入如下命令并按回车键：

```sql
mysql> SELECT VERSION(), CURRENT_DATE; 
```

![此处输入图片的描述](img/75855e984af22a9baaf9856ffccf4ff8.jpg)

能够以大小写输入关键词（建议关键词大写，以后你会感谢我的~）。下列查询是等价的：

```sql
mysql> SELECT VERSION(), CURRENT_DATE;
mysql> select version(), current_date;
mysql> SeLeCt vErSiOn(), current_DATE; 
```

这是另外一个查询，它说明你能将 mysql 用作一个简单的计算器：

```sql
mysql> SELECT SIN(PI()/4), (4+1)*5; 
```

![此处输入图片的描述](img/5663fc0d1c1b4a880e75a37aa05e2755.jpg)

至此显示的命令是相当短的单行语句。你可以在一行上输入多条语句，只需要以一个分号间隔开各语句：

```sql
mysql> SELECT VERSION(); SELECT NOW(); 
```

不必全在一个行内给出一个命令，较长命令可以输入到多个行中。mysql 通过寻找终止分号而不是输入行的结束来决定语句在哪儿结束。（换句话说，mysql 接受自由格式的输入：它收集输入行但直到看见分号才执行。）

这里是一个简单的多行语句的例子：

```sql
mysql> SELECT
    -> USER()
    -> ,
    -> CURRENT_DATE; 
```

在这个例子中，在输入多行查询的第一行后，要注意提示符如何从`mysql>`变为`->`，这正是 mysql 如何指出它没见到完整的语句并且正在等待剩余的部分。提示符是你的朋友，因为它提供有价值的反馈，如果使用该反馈，将总是知道 mysql 正在等待什么。

如果你决定不想执行正在输入过程中的一个命令，输入`\c`取消它：

```sql
mysql> SELECT
    -> USER()
    -> \c
mysql> 
```

这里也要注意提示符，在你输入`\c`以后，它切换回到`mysql>`，提供反馈以表明 mysql 准备接受一个新命令。

下表显示出可以看见的各个提示符并简述它们所表示的 mysql 的状态：

![此处输入图片的描述](img/7086965735b80f5dfdcfd61c23bbd636.jpg)

当你打算在一个单行上发出一个命令时，通常会“偶然”出现多行语句，但是没有终止分号。在这种情况中，mysql 等待进一步输入：

```sql
mysql> SELECT USER()
    -> 
```

如果出现这种情况（你认为输完了语句，但是只有一个`->`提示符响应），很可能 mysql 正在等待分号。如果你没有注意到提示符的提示，在意识到你需要做什么之前，你可能会呆坐一会儿。输入一个分号完成语句，mysql 将执行：

```sql
mysql> SELECT USER()
    -> ; 
```

在字符串收集期间将出现 `'>` 和`">` 提示符（提示 MySQL 正等待字符串的结束）。在 MySQL 中，可以写由`'`或`"`字符括起来的字符串 (例如，`'hello'`或`"goodbye"`)，并且 mysql 允许输入跨越多行的字符串。当看到一个 `'>`或 `">` 提示符时，这意味着已经输入了包含以`'`或`"`括号字符开始的字符串的一行，但是还没有输入终止字符串的匹配引号。这显示你粗心地省掉了一个引号字符。例如：

```sql
mysql> SELECT * FROM my_table WHERE name = 'Smith AND age < 30;
    '> 
```

如果你输入`SELECT`语句，然后按 Enter（回车）键并等待结果，什么都没有出现。不要惊讶，“为什么该查询这么长呢？”，注意`">`提示符提供的线索。它告诉你 mysql 期望见到一个未终止字符串的余下部分。（你看见语句中的错误吗？字符串`Smith`丢掉了第二个引号。）

走到这一步，你该做什么？最简单的是取消命令。然而，在这种情况下，你不能只是输入`\c`，因为 mysql 作为它正在收集的字符串的一部分来解释它！相反，应输入关闭的引号字符(这样 mysql 知道你完成了字符串)，然后输入`\c`：

```sql
mysql> SELECT * FROM my_table WHERE name = 'Smith AND age < 30;
    '> '\c
mysql> 
```

提示符回到`mysql>`，显示 mysql 准备好接受一个新命令了。

知道`'>`和`">`提示符的含义很重要，因为如果你错误地输入一个未终止的字符串，任何后面输入的行将要被 mysql 忽略——包括包含`QUIT`的行！这可能令人相当困惑，特别是如果取消当前命令前还不知道你需要提供终止引号。