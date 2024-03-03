# 第 2 节 创建一个数据库并插入语句

## 实验简介

通过本节实验，将创建一个名为 mysql_shiyan 的数据库，其中有两张表 employee 和 department。

## 一、实验说明

### 1、环境登录

无需密码自动登录，系统用户名 shiyanlou

### 2、实验准备

在使用实验楼 Linux 环境进行本次实验之前，先用以下两条命令打开 MySQL 服务并使用 root 用户登录：

```sql
sudo service mysql start        #打开 MySQL 服务

mysql -u root                   #使用 root 用户登录 
```

## 二、实验内容

### 1、新建数据库

首先，我们创建一个数据库，给它一个名字，比如“mysql_shiyan”，以后的几次实验也是对 mysql_shiyan 这个数据库进行操作。 语句格式为“CREATE DATABASE <数据库名字>;”，（注意不要漏掉“;”）因此具体命令为：

```sql
CREATE DATABASE mysql_shiyan; 
```

创建成功后输入命令“show databases;”（注意不要漏掉“;”）检查一下：

![01](img/bbeec6d3749c0e18e13ade3c395e5bca.jpg)

在大多数系统中，SQL 语句都是不区分大小写的，因此以下语句是等价的：

```sql
CREATE DATABASE name1;
create database name2;
CREATE database name3;
create DAtabaSE name4; 
```

但是出于严谨，而且便于区分保留字（***保留字(reserved word)：指在高级语言中已经定义过的字，使用者不能再将这些字作为变量名或过程名使用。***）和变量名，我们把保留字大写，把变量和数据小写。

### 2、连接数据库

接下来的操作，就在刚才创建的 mysql_shiyan 中进行，首先要连接数据库，使用语句 “use <数据库名字>”：

```sql
use mysql_shiyan 
```

如图显示，则连接成功：

![02](img/61a3986d3bb0521b36ab1d89b09b938e.jpg)

输入命令“show tables;”可以查看当前数据库里有几张表，现在 mysql_shiyan 里还是空的：

![03](img/0e386195c2d575ec62fa7d024707615b.jpg)

### 3、数据表

数据表（table）简称表，它是数据库最重要的组成部分之一。数据库只是一个框架，表才是实质内容。 而一个数据库中一般会有多张表，这些各自独立的表通过建立关系被联接起来，才成为可以交叉查阅、一目了然的数据库。如下便是一张表：

```sql
| ID  |    name  | phone      |
|:----|:---------|:-----------|
| 01  | Tom      | 110110110  |
| 02  | Jack     | 119119119  |
| 03  | Rose     | 114114114  |
```

### 4、新建数据表

在数据库中新建一张表的语句格式为：

```sql
CREATE TABLE 表的名字
(
列名 a 数据类型(数据长度),
列名 b 数据类型(数据长度)，
列名 c 数据类型(数据长度)
); 
```

我们尝试在 mysql_shiyan 中新建一张表 employee，所以语句为：

```sql
CREATE TABLE employee (id int(10),name char(20),phone int(12)); 
```

想让命令看起来更整洁，你可以这样输入命令：

![04](img/3853e561e30ed48565d72629aabb5214.jpg)

这时候再“show tables;”一下，可以看到刚才添加的两张表：

![05](img/3bc927cd0fdadd5c64b06835ac22088c.jpg)

### 5、数据类型

在刚才新建表的过程中，我们提到了数据类型，MySQL 的数据类型和其他编程语言大同小异，下表是一些 MySQL 常用数据类型：

```sql
|数据类型   |大小(字节)| 用途            |格式             |
|:--------|:-------:|:--------------:|:--------------:|
| INT     |   4     | 整数            |                |
| FLOAT   |   4     | 单精度浮点数     |                 |
| DOUBLE  |   4     | 双精度浮点数     |                 |
|         |         |                |                |
| ENUM    |         | 单选,比如性别    |ENUM('a','b','c')|
| SET     |         | 多选           |SET('1','2','3') |
|         |         |               |                 |
| DATE    |   3     | 日期           | YYYY-MM-DD      |
| TIME    |   3     | 时间点或持续时间 | HH:MM:SS        |
| YEAR    |   1     | 年份值         | YYYY            |
|         |         |               |                 |
| CHAR    | 0~255   | 定长字符串      |                 |
| VARCHAR | 0~255   | 变长字符串      |                 |
| TEXT    | 0~65535 | 长文本数据      |                 |
```

**CHAR 和 VARCHAR 的区别:** CHAR 的长度是固定的，而 VARCHAR 的长度是可以变化的，比如，存储字符串“abc"，对于 CHAR (10)，表示存储的字符将占 10 个字节(包括 7 个空字符)，而同样的 VARCHAR(12)则只占用 3 个字节的长度，12 只是最大值，当你存储的字符小于 12 时，按实际长度存储。

**ENUM 和 SET 的区别:** ENUM 类型的数据的值，必须是定义时枚举的值的其中之一，即单选，而 SET 类型的值则可以多选。

### 6、插入数据

刚才我们新建了两张表，使用语句“SELECT * FROM employee;”可以看到 employee 表中，现在还是空的：

![06](img/b9edb3a3737645419ff0971f68a401b7.jpg)

***刚才使用的 SELECT 语句将在下一节课详细介绍***

我们通过 INSERT 语句向表中插入数据，语句格式为：

```sql
INSERT INTO 表的名字(列名 a,列名 b,列名 c) VALUES(值 1,值 2,值 3); 
```

我们尝试加入 Tom、Jack 和 Rose：

```sql
INSERT INTO employee(id,name,phone) VALUES(01,'Tom',110110110);

INSERT INTO employee VALUES(02,'Jack',119119119);

INSERT INTO employee(id,name) VALUES(03,'Rose'); 
```

你已经注意到了，有的数据需要用单引号括起来，比如 Tom、Jack、Rose 的名字，这是由于它们的数据类型是 CHAR 型。此外**VARCHAR,TEXT,DATE,TIME,ENUM**等类型的数据也需要单引号修饰，而**INT,FLOAT,DOUBLE**等则不需要。

第一条语句比第二条语句多了一部分：(id,name,phone) 这个括号里列出的，是将要添加的数据(01,'Tom',110110110)其中每个值在表中对应的列。而第三条语句只添加了(id,name)两列的数据，**所以在表中 Rose 的 phone 为 NULL**。

现在我们再次使用语句“SELECT * FROM employee;”查看 employee 表，可见 Tom 和 Jack 的相关数据已经保存在其中了：

![07](img/4102fdb4d3f12e39fab909ad432e24e8.jpg)

## 三、小结

经过本次实验，我们已经有了一个名为 mysql_shiyan 的数据库，其中有两张表，我们已经向其中插入了一些数据。务必完整地操作一遍，以熟练 SQL 语句的使用。

## 四、作业

1、根据实验内容完整地练习一遍，并保存截图；

2、新建一个名为 library 的数据库，包含 book、reader 两张表，根据自己的理解安排表的内容并插入数据。保存截图。