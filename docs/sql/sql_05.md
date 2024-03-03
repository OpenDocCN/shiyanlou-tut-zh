# 第 5 节 修改和删除

## 实验简介

对数据库的内容做修改，删除，重命名等操作。

## 一、实验说明

### 1、环境登录

无需密码自动登录，系统用户名 shiyanlou

### 2、环境介绍

本实验环境采用 Ubuntu Linux 桌面环境，实验中会用到的程序：

1、MySQL：流行的数据库管理系统，实验楼环境已经安装

2、Gedit：简单、快捷的文档编辑器

## 二、实验准备

在正式开始本实验内容之前，需要先从 github 下载相关代码。该代码可以新建两个数据库，分别名为**test_01**和**mysql_shiyan** ，并在**mysql_shiyan**数据库中建 4 个表（department，employee，project，table_1），然后向其中插入数据。

具体操作如下,首先输入命令进入 Desktop：

```sql
cd Desktop 
```

然后再输入命令，下载代码：

```sql
git clone http://git.shiyanlou.com/shiyanlou/SQL5 
```

下载完成后，输入“cd ~”（注意有空格）退回到原先目录，然后输入命令开启 MySQL 服务并使用 root 用户登录：

```sql
sudo service mysql start        #打开 MySQL 服务

mysql -u root                   #使用 root 用户登录 
```

刚才从 github 下载的 SQL5 目录下,有 1 个文件**MySQL-05.sql** （**SQL5 目录在桌面上，你可以用 Gedit 查看、编辑里面的文件。**）

输入命令运行这个文件，完成实验准备：

```sql
source /home/shiyanlou/Desktop/SQL5/MySQL-05.sql 
```

## 三、实验内容

### 1、对数据库的修改

使用命令 **SHOW DATABASES;** 可以看到刚才运行**MySQL-05.sql**文件生成的两个数据库：

![01](img/c57b9f8420accffff49cf56a7996f073.jpg)

现在我们运行命令删除名为**test_01**的数据库：

```sql
DROP DATABASE test_01; 
```

现在再次使用命令 **SHOW DATABASES;** 可以发现，**test_01**数据库已经被成功删除：

![02](img/a01d423768992e37f993a4ac549d2675.jpg)

### 2、对一张表的修改

#### (1)重命名一张表

重命名一张表的语句有多种形式，以下 3 种格式效果是一样的：

```sql
RENAME TABLE 原名 TO 新名字;

ALTER TABLE 原名 RENAME 新名;

ALTER TABLE 原名 RENAME TO 新名; 
```

使用命令尝试修改 **table_1** 的名字为 **table_2** ：

![03](img/0db5dd9cc7d1ec7fd610dcac87545e0b.jpg)

#### (2)删除一张表

删除一张表的语句，类似于刚才用过的删除数据库的语句，格式是这样的：

```sql
DROP TABLE 表名字; 
```

比如我们把 **table_2** 表删除：

![04](img/ee63a5a7497e2fbdb5ccabb63dabffe3.jpg)

### 3、对一列的修改(即对表结构的修改)

对表结构的修改，是本节实验的难点，有时候一些小的错误会造成不可挽回的后果，所以请细心操作。

#### (1)增加一列

在表中增加一列的语句格式为：

```sql
 ALTER TABLE 表名字 ADD COLUMN 列名字 数据类型 约束;

或： ALTER TABLE 表名字 ADD 列名字 数据类型 约束; 
```

现在**employee**表中有 id、name、age、salary、phone、in_dpt 这 6 个列，我们尝试加入 **height** (身高)一个列并指定 DEFAULT 约束：

![05](img/bdfa6aceff3f5fe027b42d2d5b34c7e5.jpg)

可以发现：新增加的列，被默认放置在这张表的最右边。如果要把增加的列插入在指定位置，则需要在语句的最后使用 AFTER 关键词(**“AFTER 列 1” 表示新增的列被放置在 “列 1” 的后面**)。

比如我们新增一列 **weight** (体重)放置在 **age** (年龄)的后面：

![06](img/d32b1fe4cfefb589ce03ded7b0be9980.jpg)

上面的效果是把新增的列加在某位置的后面，如果想放在第一列的位置，则使用 **FIRST** 关键词，如语句：

```sql
ALTER TABLE employee ADD test INT(10) DEFAULT 11 FIRST; 
```

效果如下：

![07](img/2902f3e8525c5f696ef5666958e9d9d7.jpg)

#### (2)删除一列

删除表中的一列和刚才使用的新增一列的语句格式十分相似，只是把关键词**ADD** 改为 **DROP** ，语句后面不需要有数据类型、约束或位置信息。具体语句格式：

```sql
 ALTER TABLE 表名字 DROP COLUMN 列名字;

或： ALTER TABLE 表名字 DROP 列名字; 
```

我们把刚才新增的 **test** 删除：

![08](img/c48490e52919a58accf561a338a22b27.jpg)

#### (3)重命名一列

这条语句其实不只可用于重命名一列，准确地说，它是对一个列做修改(CHANGE) ：

```sql
ALTER TABLE 表名字 CHANGE 原列名 新列名 数据类型 约束; 
```

>**注意：这条重命名语句后面的 “数据类型” 不能省略，否则重命名失败。**

当**原列名**和**新列名**相同的时候，指定新的**数据类型**或**约束**，就可以用于修改数据类型或约束。需要注意的是，修改数据类型可能会导致数据丢失，所以要慎重使用。

我们用这条语句将 “height” 一列重命名为汉语拼音 “shengao” ，效果如下：

![09](img/30c86316ca7a7bae4fa2e2f7b4f04769.jpg)

#### (4)改变数据类型

要修改一列的数据类型，除了使用刚才的**CHANGE**语句外，还可以用这样的**MODIFY**语句：

```sql
ALTER TABLE 表名字 MODIFY 列名字 新数据类型; 
```

再次提醒，修改数据类型必须小心，因为这可能会导致数据丢失。在尝试修改数据类型之前，请慎重考虑。

### 4、对表的内容修改

#### (1)修改表中某个值

大多数时候我们需要做修改的不会是整个数据库或整张表，而是表中的某一个或几个数据，这就需要我们用下面这条命令达到精确的修改：

```sql
UPDATE 表名字 SET 列 1=值 1,列 2=值 2 WHERE 条件; 
```

比如，我们要把 Tom 的 age 改为 21，salary 改为 3000：

![10](img/730e89c33a2a016a8547431822e2be59.jpg)

>**注意：一定要有 WHERE 条件，否则会出现你不想看到的后果**

#### (2)删除一行记录

删除表中的一行数据，也必须加上 WHERE 条件，否则整列的数据都会被删除。删除语句：

```sql
DELETE FROM 表名字 WHERE 条件; 
```

我们尝试把 Tom 的数据删除：

![11](img/e0526d9464bcd67f247e5b706bd49b13.jpg)

## 四、作业

1、按照实验过程完整操作一遍：先用 github 中的代码建立数据库并插入数据，然后试用各种修改语句。实验过程截图。

2、使用没有 WHERE 条件的 UPDATE 语句，查看有什么不良后果(截图并说明)。