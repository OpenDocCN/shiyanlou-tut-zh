# 第 9 节 MySQL 视图

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

## 一、`CREATE VIEW`语法

```sql
CREATE [OR REPLACE] [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    VIEW view_name [(column_list)]
    AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION] 
```

该语句能创建新的视图，如果给定了`OR REPLACE`子句，该语句还能替换已有的视图。`select_statement`是一种`SELECT`语句，它给出了视图的定义。该语句可从基表或其他视图进行选择。

`CREATE VIEW`语句要求具有针对视图的`CREATE VIEW`权限，以及针对由`SELECT`语句选择的每一列上的某些权限。对于在`SELECT`语句中其他地方使用的列，必须具有`SELECT`权限。如果还有`OR REPLACE`子句，必须在视图上具有`DROP`权限。

视图属于数据库。在默认情况下，将在当前数据库创建新视图。要想在给定数据库中明确创建视图，创建时，应将名称指定为`db_name.view_name`。

下面的例子中就是创建一个视图`v`，该视图包含从另一个表`t`中选择两列并用一个表达式计算这两列的数据：

```sql
mysql> CREATE DATABASE test;
mysql> USE test;
mysql> CREATE TABLE t (qty INT, price INT);
mysql> INSERT INTO t VALUES(3, 50), (5, 60);
mysql> CREATE VIEW v AS SELECT qty, price, qty*price AS value FROM t;
mysql> SELECT * FROM v; 
```

![此处输入图片的描述](img/ef6358df25e5300a7761a393087af89b.jpg)

视图定义服从下述限制：

*   `SELECT`语句不能包含`FROM`子句中的子查询。

*   `SELECT`语句不能引用系统或用户变量。

*   `SELECT`语句不能引用预处理语句参数。

*   在存储子程序内，定义不能引用子程序参数或局部变量。

*   在定义中引用的表或视图必须存在。但是，创建了视图后，能够舍弃定义引用的表或视图。要想检查视图定义是否存在这类问题，可使用`CHECK TABLE`语句。

*   在定义中不能引用`TEMPORARY`表，不能创建`TEMPORARY`视图。

*   在视图定义中命名的表必须已存在。

*   不能将触发程序与视图关联在一起。

在视图定义中允许使用`ORDER BY`，但是，如果从特定视图进行了选择，而该视图使用了具有自己`ORDER BY`的语句，它将被忽略。

对于定义中的其他选项或子句，它们将被增加到引用视图的语句的选项或子句中，但效果未定义。例如，如果在视图定义中包含`LIMIT`子句，而且从特定视图进行了选择，而该视图使用了具有自己`LIMIT`子句的语句，那么对使用哪个`LIMIT`未作定义。相同的原理也适用于其他选项，如跟在`SELECT`关键字后的`ALL`、`DISTINCT`或`SQL_SMALL_RESULT`，并适用于其他子句，如`INTO`、`FOR UPDATE`、`LOCK IN SHARE MOD`、以及`PROCEDURE`。

如果应用系统变量创建视图，并通过更改系统变量更改了查询处理环境，会影响从视图获得的结果：

```sql
mysql> CREATE VIEW v_1 AS SELECT CHARSET(CHAR(65)), COLLATION(CHAR(65));
mysql> SET NAMES 'latin1';
mysql> SELECT * FROM v_1;
mysql> SET NAMES 'utf8';
mysql> SELECT * FROM v_1; 
```

![此处输入图片的描述](img/02b4331ad19e815ce696a5bd996c48ae.jpg)

## 二、视图处理算法

可选的`ALGORITHM`子句是对标准 SQL 的 MySQL 扩展。`ALGORITHM`可取三个值：`MERGE`、`TEMPTABLE`或`UNDEFINED`。如果没有`ALGORITHM`子句，默认算法是`UNDEFINED`（未定义的）。算法会影响 MySQL 处理视图的方式。

对于`MERGE`，会将引用视图的语句的文本与视图定义合并起来，使得视图定义的某一部分取代语句的对应部分。

对于`TEMPTABLE`，视图的结果将被置于临时表中，然后使用它执行语句。

对于`UNDEFINED`，MySQL 需要自己选择所要使用的算法。如果可能，它倾向于`MERGE`而不是`TEMPTABLE`，这是因为`MERGE`通常更有效，而且如果使用了临时表，视图是不可更新的。

明确选择`TEMPTABLE`的 1 个原因在于，创建临时表之后、并在完成语句处理之前，能够释放基表上的锁定。与`MERGE`算法相比，锁定释放的速度更快，这样，使用视图的其他客户端不会被屏蔽过长时间。

视图算法在以下三种情况下可以是`UNDEFINED`：

*   在`CREATE VIEW`语句中没有`ALGORITHM`子句。

*   `CREATE VIEW`语句有 1 个显式`ALGORITHM = UNDEFINED`子句。

*   为仅能用临时表处理的视图指定`ALGORITHM = MERGE`。在这种情况下，MySQL 将生成告警，并将算法设置为`UNDEFINED`。

正如前面所介绍的那样，通过将视图定义中的对应部分合并到引用视图的语句中，对`MERGE`进行处理。在下面的示例中，简要介绍了`MERGE`的工作方式。在该示例中，假定有 1 个具有下述定义的视图`v_merge`：

```sql
CREATE ALGORITHM = MERGE VIEW v_merge (vc1, vc2) AS
SELECT c1, c2 FROM t WHERE c3 > 100; 
```

**示例 1：**假定执行下述语句：

```sql
SELECT * FROM v_merge; 
```

MySQL 以下述方式处理语句：

*   `v_merge`成为`t`

*   `*`成为`vc1`、`vc2`，与`c1`、`c2`对应

*   给视图添加`WHERE`子句

最后被执行的语句为：

```sql
SELECT c1, c2 FROM t WHERE c3 > 100; 
```

**示例 2：**假定输入下述语句：

```sql
SELECT * FROM v_merge WHERE vc1 < 100; 
```

该语句的处理方式与前面介绍的类似，除了`vc1 < 100`变为`c1 < 100`以外，并使用`AND`连接词将视图的`WHERE`子句添加到原语句的`WHERE`子句中（增加了圆括号以确保以正确的优先顺序执行子句部分）。所得的将要执行的语句变为：

```sql
SELECT c1, c2 FROM t WHERE (c3 > 100) AND (c1 < 100); 
```

事实上，将要执行的语句是具有下述形式的 WHERE 子句：

```sql
WHERE (select WHERE) AND (view WHERE) 
```

`MERGE`算法要求视图中的行和基表中的行具有一对一的关系。如果不具有该关系，必须使用临时表取而代之。如果视图包含下述结构中的任何一种，将失去一对一的关系：

*   聚合函数（`SUM()`，`MIN()`，`MAX()`， `COUNT()`等）。

*   `DISTINCT`

*   `GROUP BY`

*   `HAVING`

*   `UNION`或`UNION ALL`

*   在选择的列表中使用子查询

*   仅引用文字值（在该情况下，没有基表）

## 三、可更新和插入的视图

### 3.1 可更新视图

某些视图是可更新的。也就是说，可以在诸如`UPDATE`、`DELETE`或`INSERT`等语句中使用它们，以更新基表的内容。对于可更新的视图，在视图中的行和基表中的行之间必须具有一对一的关系。还有一些特定的其他结构，这类结构会使得视图不可更新。更具体地讲，如果视图包含下述结构中的任何一种，那么它就是不可更新的：

*   聚合函数（`SUM()`， `MIN()`， `MAX()`， `OUNT()`等）。

*   `DISTINCT`

*   `GROUP BY`

*   `HAVING`

*   `UNION`或`UNION ALL`

*   位于选择列表中的子查询

*   `Join`

*   `FROM`子句中引用不可更新视图

*   `WHERE`子句中的子查询引用的是`FROM`子句中的表。

*   仅引用文字值（在该情况下，没有要更新的基表）。

*   `ALGORITHM = TEMPTABLE`（使用临时表总会使视图成为不可更新的）。

### 3.2 可插入视图

关于可插入性（可用 INSERT 语句更新），如果它也满足关于视图列的下述额外要求，可更新的视图也是可插入的：

*   不得有重复的视图列名称。

*   视图必须包含没有默认值的基表中的所有列。

*   视图列必须是简单的列引用而不是导出列。导出列不是简单的列引用，而是从表达式导出的。下面给出了一些导出列示例：

    ```sql
    3.14159
    col1 + 3
    UPPER(col2)
    col3 / col4
    (subquery) 
    ```

混合了简单列引用和导出列的视图是不可插入的，但是，如果仅更新非导出列，视图是可更新的。考虑下述视图：

```sql
CREATE VIEW v AS SELECT col1, 1 AS col2 FROM t; 
```

该视图是不可插入的，这是因为`col2`是从表达式`1`导出的。但是，如果更新时不更新`col2`，它是可更新的。这类更新是允许的：

```sql
UPDATE v SET col1 = 0; 
```

下述更新是不允许的，原因在于，它试图更新导出列：

```sql
UPDATE v SET col2 = 0; 
```

在某些情况下，能够更新多表视图，假定它能使用`MERGE`算法进行处理。为此，视图必须使用内部联合（而不是外部联合或`UNION`）。此外，仅能更新视图定义中的单个表，因此，`SET`子句必须仅命名视图中某一表的列。即使从理论上讲也是可更新的，不允许使用`UNION ALL`的视图，这是因为，在实施中将使用临时表来处理它们。

对于多表可更新视图，如果是将其插入单个表中，`INSERT`能够工作，不支持`DELETE`。

### 3.3 使用`WITH CHECK OPTION`子句的视图

对于可更新视图，可给定`WITH CHECK OPTION`子句来防止插入或更新行，除非作用在行上的`select_statement`中的`WHERE`子句为`“真”`。

在关于可更新视图的`WITH CHECK OPTION`子句中，当视图是根据另一个视图定义的时，`LOCAL`和`CASCADED`关键字决定了检查测试的范围。`LOCAL`关键字对`CHECK OPTION`进行了限制，使其仅作用在定义的视图上，`CASCADED`会对将进行评估的基表进行检查。如果未给定任一关键字，默认为`CASCADED`。请考虑下述表和视图集合的定义：

```sql
mysql> CREATE TABLE t1 (a INT);
mysql> CREATE VIEW v1 AS SELECT * FROM t1 WHERE a < 2
    -> WITH CHECK OPTION;
mysql> CREATE VIEW v2 AS SELECT * FROM v1 WHERE a > 0
    -> WITH LOCAL CHECK OPTION;
mysql> CREATE VIEW v3 AS SELECT * FROM v1 WHERE a > 0
    -> WITH CASCADED CHECK OPTION; 
```

这里，视图`v2`和`v3`是根据另一视图`v1`定义的。`v2`具有`LOCAL`检查选项，因此，仅会针对`v2`检查对插入项进行测试。`v3`具有`CASCADED`检查选项，因此，不仅会针对它自己的检查对插入项进行测试，也会针对基本视图的检查对插入项进行测试。在下面的语句中，介绍了这些差异：

```sql
mysql> INSERT INTO v2 VALUES (2);

mysql> INSERT INTO v3 VALUES (2); 
```

## 四、`ALTER VIEW`语法

```sql
ALTER [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    VIEW view_name [(column_list)]
    AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION] 
```

该语句用于更改已有视图的定义。其语法与`CREATE VIEW`类似。

## 五、`DROP VIEW`语法

```sql
DROP VIEW [IF EXISTS]
    view_name [, view_name] ...
    [RESTRICT | CASCADE] 
```

`DROP VIEW`能够删除 1 个或多个视图。必须在每个视图上拥有`DROP`权限。

可以使用关键字`IF EXISTS`来防止因不存在的视图而出错，给定了该子句时，将为每个不存在的视图生成`NOTE`。

如果给定了`RESTRICT`和`CASCADE`，将解析并忽略它们。

四、`SHOW CREATE VIEW`语法

```sql
SHOW CREATE VIEW view_name 
```