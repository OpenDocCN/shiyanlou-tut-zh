# 第 8 节 触发器

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

## 预备知识：MySQL 程序

### 调用 MySQL 程序

要想从命令行调用 MySQL 程序，应输入程序名，并随后输入指导操作的选项或其它参量。下面的命令显示了一些程序调用的例子。`“shell>”`表示命令解释符提示；并不是输入的部分。你所看见的具体提示取决于命令解释符。典型提示符：`sh`或`bash`为`$`，`csh`或`tcsh`为`%`，Windows 系统上的`cmd.exe`为`C:\>`。

```sql
shell> mysql test
shell> mysqladmin extended-status variables
shell> mysqlshow --help
shell> mysqldump --user=root personnel 
```

以破折号开始的参数为选项参量。它们通常指定程序与服务器的连接类型或影响其操作模式。关于选项语法的描述参见[指定程序选项](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/using-mysql-programs.html#program-options)。

非选项参数(不以破折号开始的参量)可以为程序提供更详细的信息。例如，mysql 程序将第一个非选项参量解释为数据库名，因此命令 `mysql test`表示你想要使用`test`数据库。

部分选项对部分程序是通用的。最常用的是指定连接参数的`--host`、`--user`和`--password`选项。它们指示 MySQL 服务器运行的主机和 MySQL 账户的用户名和 密码。所有 MySQL 客户程序可以理解这些选项；它们允许你指定连接哪个服务器，以及在该服务器上使用的 账户。

你也许会发现需要使用 MySQL 程序安装的`bin`目录的路径名来调用 MySQL 程序。如果你试图不在`bin`目录下运行 MySQL 程序，可能会遇到`“程序未找到”`错误。为了更方便地使用 MySQL，可以将`bin`目录的路径名添加到`PATH`环境变量中。然后运行程序时只需要输入程序名，而不需要输入整个路径名。

关于设置 PATH 的指令的命令解释符请查阅相关文档。设置环境变量的语法与解释符有关。

### 批量执行 SQL 语句

如果你想批量处理某些 SQL 语句或者某些语句内容比较多，您可以考虑将 SQL 语句放到一个文件中然后告诉 mysql 从该文件读取输入。实现过程为：创建一个文本文件`text_file`，并包含你想要执行的语句。然后在命令行终端调用 mysql：

```sql
shell> mysql db_name < text_file 
```

如果`text_file`文件中已经使用了`USE db_name`指定了数据库，那么可以直接在命令行输入：

```sql
shell> mysql < text_file 
```

如果正运行 mysql，可以使用`source`或`\.`命令执行 SQL 脚本文件`text_file.sql`：

```sql
mysql> source text_file.sql

mysql> \. text_file.sql 
```

**注意：**如果脚本文件不存在于 mysql 的工作目录下，则需要指定脚本文件的绝对路径名称，例如：

```sql
mysql> source /home/shiyanlou/text_file.sql 
```

## 一、`CREATE TRIGGER`语法

```sql
CREATE TRIGGER trigger_name trigger_time trigger_event
    ON tbl_name FOR EACH ROW trigger_stmt 
```

触发器（触发程序）是与表有关的固定的数据库对象，当表上出现特定事件时，将激活该对象。一般用于检查给表插入新的值或者进行表内的数值计算之类的更新。

触发程序与`bl_name`表相关。`tbl_name`必须是永久性表被引用，不能将触发程序与`TEMPORARY`表或视图关联起来。

`trigger_time`是触发程序的动作时间。它可以是`BEFORE`或`AFTER`，以指明触发程序是在激活它的语句之前或之后触发。

`trigger_event`指明了激活触发程序的语句的类型。`trigger_event`可以是下述值之一：

*   `INSERT`：将新行插入表时激活触发程序，例如，通过`INSERT`、`LOAD DATA`和`REPLACE`语句实现插入数据。

*   `UPDATE`：更改某一行时激活触发程序，例如，通过`UPDATE`语句更新数据。

*   `DELETE`：从表中删除某一行时激活触发程序，例如，通过`DELETE`和`REPLACE`语句删除数据。

**请注意：**`trigger_event`与以表操作方式激活触发程序的 SQL 语句并不很类似，这点很重要。例如，关于`INSERT`的`BEFORE`触发程序不仅能被`INSERT`语句激活，也能被`LOAD DATA`语句激活。

对于具有相同触发程序动作时间和事件的某个表，不能有两个触发程序。例如，对于某一个表，不能有两个`BEFORE UPDATE`触发程序。但可以有 1 个`BEFORE UPDATE`触发程序和 1 个`BEFORE INSERT`触发程序，或 1 个`BEFORE UPDATE`触发程序和 1 个`AFTER UPDATE`触发程序。

`trigger_stmt`是当触发程序激活时执行的语句。如果你打算执行多个语句，可使用`BEGIN ... END`复合语句结构。这样，就能使用存储子程序中允许的相同语句。

在 MySQL 中，可以编写包含按名称对表进行直接引用的触发程序，如下例中创建了一个名为`testref`的触发程序（首先是先在命令行创建一个`test.sql`的脚本文件，将下面语句写入到该脚本中）：

```sql
CREATE DATABASE test_db;
USE test_db;
CREATE TABLE test1(a1 INT);
CREATE TABLE test2(a2 INT);
CREATE TABLE test3(a3 INT NOT NULL AUTO_INCREMENT PRIMARY KEY);
CREATE TABLE test4(
  a4 INT NOT NULL AUTO_INCREMENT PRIMARY KEY, 
  b4 INT DEFAULT 0
);

DELIMITER |

CREATE TRIGGER testref BEFORE INSERT ON test1
  FOR EACH ROW BEGIN
    INSERT INTO test2 SET a2 = NEW.a1;
    DELETE FROM test3 WHERE a3 = NEW.a1;  
    UPDATE test4 SET b4 = b4 + 1 WHERE a4 = NEW.a1;
  END
|

DELIMITER ;

INSERT INTO test3 (a3) VALUES 
  (NULL), (NULL), (NULL), (NULL), (NULL), 
  (NULL), (NULL), (NULL), (NULL), (NULL);

INSERT INTO test4 (a4) VALUES 
  (0), (0), (0), (0), (0), (0), (0), (0), (0), (0); 
```

然后使用`root`账户登录到 MySQL 服务器（记得先启动服务器），并运行 SQL 脚本：

```sql
mysql> source test.sql;
# 检验脚本是否成功运行
mysql> SHOW tables; 
```

![此处输入图片的描述](img/d9eed96ddc73374bad9d3cb5f1828474.jpg)

如果您在实验楼已经得到以上结果，则可以继续执行以下命令。如果不成功，请检查一下您的操作。

下面对表`test1`执行插入操作，并通过观察各个表中的数据情况了解触发器的动作：

```sql
mysql> INSERT INTO test1 VALUES 
    -> (1), (3), (1), (7), (1), (8), (4), (4); 
```

如下所示：

![此处输入图片的描述](img/51dd789ac718fe7d186c6a4d9d24558e.jpg)

查询 4 个表中的数据：

```sql
mysql> SELECT * FROM test1;
mysql> SELECT * FROM test2;
mysql> SELECT * FROM test3;
mysql> SELECT * FROM test4; 
```

使用别名`OLD`和`NEW`，能够引用与触发程序相关的表中的列。`OLD.col_name`在更新或删除它之前，作为引用已有行中的列。`NEW.col_name`在更新它之后用来引用将要插入的新行的 1 列或已有行的 1 列。

激活触发程序时，对于触发程序引用的所有`OLD`和`NEW`列，需要具有`SELECT`权限，对于作为`SET`赋值目标的所有`NEW`列，需要具有`UPDATE`权限。

**注释：**目前，触发程序不会被级联的外键动作激活。该限制将会被尽早放宽。`CREATE TRIGGER`语句的执行者需要具有`SUPER`权限。

## 二、`DROP TRIGGER`语法

```sql
DROP TRIGGER [schema_name.]trigger_name 
```

舍弃触发程序。方案名称（`schema_name`）是可选的。如果省略了`schema`（方案），将从当前方案中舍弃触发程序。`DROP TRIGGER`语句需要`SUPER`权限。

## 三、 使用触发器

下面给出了 1 个简单的示例，在该示例中，针对`INSERT`语句，将触发程序和表关联了起来。其作用相当于累加器，能够将插入表中某一列的值加起来。

在下面的语句中，创建了 1 个表`account`，并为表创建了 1 个触发程序`ins_sum`：

```sql
mysql> CREATE TABLE account (acct_num INT, amount DECIMAL(10,2));
mysql> CREATE TRIGGER ins_sum BEFORE INSERT ON account
    -> FOR EACH ROW SET @sum = @sum + NEW.amount; 
```

![此处输入图片的描述](img/7e30f5366c257b0aab70fde27500ce8a.jpg)

`CREATE TRIGGER`语句创建了与账户表相关的、名为`ins_sum`的触发程序。它还包括一些子句，这些子句指定了触发程序激活时间、触发程序事件、以及激活触发程序时做些什么：

*   关键字`BEFORE`指明了触发程序的动作时间。在本例中，应在将每一行插入表之前激活触发程序。与之相对应的关键字是`AFTER`。

*   关键字`INSERT`指明了激活触发程序的事件。在本例中，`INSERT`语句将导致触发程序的激活。你也可以为`DELETE`和`UPDATE`语句创建触发程序。

*   跟在`FOR EACH ROW`后面的语句定义了每次激活触发程序时将执行的程序，对于受触发语句影响是每一行都要执行一次。在本例中，触发的语句是简单的`SET`语句，负责将插入`amount`列的值加起来。该语句将列引用为`NEW.amount`，意思是`“将要插入到新行的 amount 列的值”`。

要想使用触发程序，还要将累加器变量初始化设置为 0，执行`INSERT`语句，然后查看变量的值：

```sql
mysql> SET @sum = 0;
mysql> INSERT INTO account VALUES(137,14.98),(141,1937.50),(97,-100.00);
mysql> SELECT @sum AS 'Total amount inserted'; 
```

![此处输入图片的描述](img/4cf009bdfa1523115a47264a14e144fc.jpg)

在本例中，执行了`INSERT`语句后，`@sum`的值是`14.98 + 1937.50 – 100`，为`1852.48`。

要想销毁触发程序，可使用`DROP TRIGGER`语句。如果触发程序不在默认的方案中，必须指定方案名称：

```sql
mysql> DROP TRIGGER test.ins_sum; 
```

触发程序名称存在于方案的名称空间内，这意味着，在 1 个方案中，所有的触发程序必须具有唯一的名称。位于不同方案中的触发程序可以具有相同的名称。

在 1 个方案中，所有的触发程序名称必须是唯一的，除了该要求外，对于能够创建的触发程序的类型还存在其他限制。尤其是，对于具有相同触发时间和触发事件的表，不能有 2 个触发程序。例如，不能为某一表定义 2 个`BEFORE INSERT`触发程序或 2 个`AFTER UPDATE`触发程序。这几乎不是有意义的限制，这是因为，通过在`FOR EACH ROW`之后使用`BEGIN ... END`复合语句结构，能够定义执行多条语句的触发程序。请参见本节后面给出的示例。

此外，激活触发程序时，对触发程序执行的语句也存在一些限制：

*   触发程序不能调用将数据返回客户端的存储程序，也不能使用采用`CALL`语句的动态 SQL（允许存储程序通过参数将数据返回触发程序）。

*   触发程序不能使用以显式或隐式方式开始或结束事务的语句，如`START TRANSACTION`、`COMMIT`或`ROLLBACK`。

使用`OLD`和`NEW`关键字，能够访问受触发程序影响的行中的列（`OLD`和`NEW`不区分大小写）。在`INSERT`触发程序中，仅能使用`NEW.col_name`，没有`OLD.col_name`。在`DELETE`触发程序中，仅能使用`OLD.col_name`，没有`NEW.col_name`。在`UPDATE`触发程序中，可以使用`OLD.col_name`来引用更新前的某一行的列，也能使用`NEW.col_name`来引用更新后的行中的列。

用`OLD`命名的列是只读的。你可以引用它，但不能更改它。对于用`NEW`命名的列，如果具有`SELECT`权限，可引用它。在`BEFORE`触发程序中，如果你具有`UPDATE`权限，可使用`SET NEW.col_name = value`更改它的值。这意味着，你可以使用触发程序来更改将要插入到新行中的值，或用于更新行的值。

在`BEFORE`触发程序中，`AUTO_INCREMENT`列的`NEW`值为 0，不是实际插入新记录时将自动生成的序列号。

`OLD`和`NEW`是对触发程序的 MySQL 扩展。

通过使用`BEGIN ... END`结构，能够定义执行多条语句的触发程序。在`BEGIN`块中，还能使用存储子程序中允许的其他语法，如条件和循环等。但是，正如存储子程序那样，定义执行多条语句的触发程序时，如果使用 mysql 程序来输入触发程序，需要重新定义语句分隔符，以便能够在触发程序定义中使用字符`;`。

在下面的示例中，演示了这些要点。在该示例中，定义了 1 个`UPDATE`触发程序，用于检查更新每一行时将使用的新值，并更改值，使之位于`0～100`的范围内。它必须是`BEFORE`触发程序，这是因为需要在将值用于更新行之前对其进行检查：

```sql
mysql> delimiter //
mysql> CREATE TRIGGER upd_check BEFORE UPDATE ON account
    -> FOR EACH ROW
    -> BEGIN
    ->     IF NEW.amount < 0 THEN
    ->         SET NEW.amount = 0;
    ->     ELSEIF NEW.amount > 100 THEN
    ->         SET NEW.amount = 100;
    ->     END IF;
    -> END;//
mysql> delimiter ; 
```

较为简单的方法是，单独定义存储程序，然后使用简单的`CALL`语句从触发程序中调用存储程序。如果你打算从数个触发程序内部调用相同的子程序，该方法也很有帮助。

在触发程序的执行过程中，MySQL 处理错误的方式如下：

*   如果`BEFORE`触发程序失败，不执行相应行的操作。

*   仅当`BEFORE`触发程序（如果有的话）和行操作均已成功执行，才执行`AFTER`触发程序。

*   如果在`BEFORE`或`AFTER`触发程序的执行过程中出现错误，将导致调用触发程序的整个语句的失败。

对于事务性表，如果触发程序失败（以及由此导致的整个语句的失败），该语句所执行的所有更改将回滚。对于非事务性表，不能执行这类回滚，因而，即使语句失败，失败之前所作的任何更改依然有效。