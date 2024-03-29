# 第 7 节 SQL 语句语法

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

## 一、数据定义语句

### 1.1 `ALTER DATABASE`语法

```sql
ALTER {DATABASE | SCHEMA} [db_name]
    alter_specification [, alter_specification] ...
ALTER {DATABASE | SCHEMA} [db_name]
    UPGRADE DATA DIRECTORY NAME

alter_specification:
    [DEFAULT] CHARACTER SET charset_name
  | [DEFAULT] COLLATE collation_name 
```

上述语句第一行中`ALTER DATABASE`用于更改数据库的全局特性，这些特性储存在数据库目录中的`db.opt`文件中。要使用`ALTER DATABASE`，您需要获得数据库的`ALTER`权限。

第七行语句中`CHARACTER SET`子句用于更改默认的数据库字符集。

数据库名称可以忽略，如果没有指定数据库，语句对应操作的是默认数据库。`ALTER SCHEMA`语句等价于`ALTER DATABASE`。

**举例：**

当 MySQL 安装是从旧版本升级到 MySQL5.1 或更高版本，服务器显示的名称如`a-b-c`（这是在旧格式）将被更改为`＃mysql50＃a-b-c`，这就导致你每次查询数据库名称都需要添加前缀`#mysql150#`，在这种情况下使用语句`UPGRADE DATA DIRECTORY NAME`明确地告诉服务器数据库目录名称需要重新编码到当前的编码形式：

```sql
ALTER DATABASE `#mysql50#a-b-c` UPGRADE DATA DIRECTORY NAME; 
```

执行以上语句后，你会发现可以直接在不添加前缀`#mysql150#`的情况下直接查询到数据库`a-b-c`。

### 1.2 `ALTER TABLE`语法

```sql
ALTER [IGNORE] TABLE tbl_name
    alter_specification [, alter_specification] ...

alter_specification:
    ADD [COLUMN] column_definition [FIRST | AFTER col_name ]
  | ADD [COLUMN] (column_definition,...)
  | ADD INDEX [index_name] [index_type] (index_col_name,...)
  | ADD [CONSTRAINT [symbol]]
        PRIMARY KEY [index_type] (index_col_name,...)
  | ADD [CONSTRAINT [symbol]]
        UNIQUE [index_name] [index_type] (index_col_name,...)
  | ADD [FULLTEXT|SPATIAL] [index_name] (index_col_name,...)
  | ADD [CONSTRAINT [symbol]]
        FOREIGN KEY [index_name] (index_col_name,...)
        [reference_definition]
  | ALTER [COLUMN] col_name {SET DEFAULT literal | DROP DEFAULT}
  | CHANGE [COLUMN] old_col_name column_definition
        [FIRST|AFTER col_name]
  | MODIFY [COLUMN] column_definition [FIRST | AFTER col_name]
  | DROP [COLUMN] col_name
  | DROP PRIMARY KEY
  | DROP INDEX index_name
  | DROP FOREIGN KEY fk_symbol
  | DISABLE KEYS
  | ENABLE KEYS
  | RENAME [TO] new_tbl_name
  | ORDER BY col_name
  | CONVERT TO CHARACTER SET charset_name [COLLATE collation_name]
  | [DEFAULT] CHARACTER SET charset_name [COLLATE collation_name]
  | DISCARD TABLESPACE
  | IMPORT TABLESPACE
  | table_options
  | partition_options
  | ADD PARTITION partition_definition
  | DROP PARTITION partition_names
  | COALESCE PARTITION number
  | REORGANIZE PARTITION partition_names INTO (partition_definitions)
  | ANALYZE PARTITION partition_names
  | CHECK PARTITION partition_names
  | OPTIMIZE PARTITION partition_names
  | REBUILD PARTITION partition_names
  | REPAIR PARTITION partition_names 
```

`ALTER TABLE`用于更改表的结构。例如，您可以增加或删减列，创建或取消索引，更改原有列的类型，或重新命名列或者表。您还可以更改表的评注和表的类型。

允许进行的变更中，许多子句的语法与`CREATE TABLE`中的子句的语法相近。其中包括`table_options`修改，选项有`ENGINE`，`AUTO_INCREMENT 和 AVG_ROW_LENGTH`等。

**举例：**

比如说，你需要将表格`t1`转换为`InnoDB`表，可以使用如下语句：

```sql
ALTER TABLE t1 ENGINE = InnoDB; 
```

当你指定一个`ENGINE`子句，使用`ALTER TABLE`重建表，即使在该表`t1`中原本已经制定了存储引擎，以上操作仍然生效。

### 1.3 `CREATE DATABASE`语法

```sql
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name
    [create_specification [, create_specification] ...]

create_specification:
    [DEFAULT] CHARACTER SET charset_name
  | [DEFAULT] COLLATE collation_name 
```

`CREATE DATABASE`用于创建数据库，并进行命名。如果要使用`CREATE DATABASE`，您需要获得数据库 CREATE 权限。

### 1.4 `CREATE INDEX`语法

```sql
CREATE [UNIQUE|FULLTEXT|SPATIAL] INDEX index_name
    [USING index_type]
    ON tbl_name (index_col_name,...)

index_col_name:
    col_name [(length)] [ASC | DESC]
CREATE INDEX 被映射到一个 ALTER TABLE 语句上，用于创建索引。 
```

`FULLTEXT`索引只能对`CHAR`，`VARCHAR`和`TEXT`类型数据编制索引，并且只能在 MyISAM 表中编制。`SPATIAL`索引只能对 MyISAM 表中的空间数据编制索引。

**举例：**

以下语句创建了`customer`表中`name`列的前十个字符的索引`part_of_name`：

```sql
CREATE INDEX part_of_name ON customer (name(10)); 
```

如果对列中的前 10 个字符进行索引，索引速度应该不会慢于整个`name`列的索引速度。此外，使用列的前缀建立索引可以使得索引文件更小，这样可以节省大量的磁盘空间，还可以加速`INSERT`操作。

### 1.5 `CREATE TABLE`语法

```sql
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    [(create_definition,...)]
    [table_options] [select_statement]

or：

CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    [(] LIKE old_tbl_name [)];

create_definition:
    column_definition
  | [CONSTRAINT [symbol]] PRIMARY KEY [index_type] (index_col_name,...)
  | KEY [index_name] [index_type] (index_col_name,...)
  | INDEX [index_name] [index_type] (index_col_name,...)
  | [CONSTRAINT [symbol]] UNIQUE [INDEX]
        [index_name] [index_type] (index_col_name,...)
  | [FULLTEXT|SPATIAL] [INDEX] [index_name] (index_col_name,...)
  | [CONSTRAINT [symbol]] FOREIGN KEY
        [index_name] (index_col_name,...) [reference_definition]
  | CHECK (expr)

column_definition:
    col_name type [NOT NULL | NULL] [DEFAULT default_value]
        [AUTO_INCREMENT] [UNIQUE [KEY] | [PRIMARY] KEY]
        [COMMENT 'string'] [reference_definition]

type:
    TINYINT[(length)] [UNSIGNED] [ZEROFILL]
  | SMALLINT[(length)] [UNSIGNED] [ZEROFILL]
  | MEDIUMINT[(length)] [UNSIGNED] [ZEROFILL]
  | INT[(length)] [UNSIGNED] [ZEROFILL]
  | INTEGER[(length)] [UNSIGNED] [ZEROFILL]
  | BIGINT[(length)] [UNSIGNED] [ZEROFILL]
  | REAL[(length,decimals)] [UNSIGNED] [ZEROFILL]
  | DOUBLE[(length,decimals)] [UNSIGNED] [ZEROFILL]
  | FLOAT[(length,decimals)] [UNSIGNED] [ZEROFILL]
  | DECIMAL(length,decimals) [UNSIGNED] [ZEROFILL]
  | NUMERIC(length,decimals) [UNSIGNED] [ZEROFILL]
  | DATE
  | TIME
  | TIMESTAMP
  | DATETIME
  | CHAR(length) [BINARY | ASCII | UNICODE]
  | VARCHAR(length) [BINARY]
  | TINYBLOB
  | BLOB
  | MEDIUMBLOB
  | LONGBLOB
  | TINYTEXT [BINARY]
  | TEXT [BINARY]
  | MEDIUMTEXT [BINARY]
  | LONGTEXT [BINARY]
  | ENUM(value1,value2,value3,...)
  | SET(value1,value2,value3,...)
  | spatial_type

index_col_name:
    col_name [(length)] [ASC | DESC]

reference_definition:
    REFERENCES tbl_name [(index_col_name,...)]
               [MATCH FULL | MATCH PARTIAL | MATCH SIMPLE]
               [ON DELETE reference_option]
               [ON UPDATE reference_option]

reference_option:
    RESTRICT | CASCADE | SET NULL | NO ACTION

table_options: table_option [table_option] ...

table_option:
    {ENGINE|TYPE} = engine_name
  | AUTO_INCREMENT = value
  | AVG_ROW_LENGTH = value
  | [DEFAULT] CHARACTER SET charset_name [COLLATE collation_name]
  | CHECKSUM = {0 | 1}
  | COMMENT = 'string'
  | CONNECTION = 'connect_string'
  | MAX_ROWS = value
  | MIN_ROWS = value
  | PACK_KEYS = {0 | 1 | DEFAULT}
  | PASSWORD = 'string'
  | DELAY_KEY_WRITE = {0 | 1}
  | ROW_FORMAT = {DEFAULT|DYNAMIC|FIXED|COMPRESSED|REDUNDANT|COMPACT}
  | UNION = (tbl_name[,tbl_name]...)
  | INSERT_METHOD = { NO | FIRST | LAST }
  | DATA DIRECTORY = 'absolute path to directory'
  | INDEX DIRECTORY = 'absolute path to directory'

partition_options:
    PARTITION BY
           [LINEAR] HASH(expr)
        |  [LINEAR] KEY(column_list)
        |  RANGE(expr)
        |  LIST(column_list)
    [PARTITIONS num]
    [  SUBPARTITION BY
           [LINEAR] HASH(expr)
         | [LINEAR] KEY(column_list)
      [SUBPARTITIONS(num)]
    ]
    [(partition_definition), [(partition_definition)], ...]

partition_definition:
    PARTITION partition_name
        [VALUES {
                  LESS THAN (expr) | MAXVALUE
                | IN (value_list) }]
        [[STORAGE] ENGINE [=] engine-name]
        [COMMENT [=] 'comment_text' ]
        [DATA DIRECTORY [=] 'data_dir']
        [INDEX DIRECTORY [=] 'index_dir']
        [MAX_ROWS [=] max_number_of_rows]
        [MIN_ROWS [=] min_number_of_rows]
        [TABLESPACE [=] (tablespace_name)]
        [NODEGROUP [=] node_group_id]
        [(subpartition_definition), [(subpartition_definition)], ...]

subpartition_definition:
    SUBPARTITION logical_name
        [[STORAGE] ENGINE [=] engine-name]
        [COMMENT [=] 'comment_text' ]
        [DATA DIRECTORY [=] 'data_dir']
        [INDEX DIRECTORY [=] 'index_dir']
        [MAX_ROWS [=] max_number_of_rows]
        [MIN_ROWS [=] min_number_of_rows]
        [TABLESPACE [=] (tablespace_name)]
        [NODEGROUP [=] node_group_id]

select_statement:
    [IGNORE | REPLACE] [AS] SELECT ...   (Some legal select statement) 
```

`CREATE TABLE`用于创建带给定名称的表。前提是您必须拥有表`CREATE`的权限。

**举个复制表的例子：**

在一个表`orig_tb1`已经被定义好的基础上使用语句`CREATE TABLE ... LIKE`去创建一个含有相应列属性和索引相同的另外一个空表`new_tb1`。

```sql
CREATE TABLE new_tbl LIKE orig_tbl; 
```

那么，完全克隆一个表的内容以及结构就可以使用如下语句：

```sql
CREATE TABLE new_tbl SELECT * FROM orig_tbl; 
```

### 1.6 `DROP DATABASE`语法

```sql
DROP {DATABASE | SCHEMA} [IF EXISTS] db_name 
```

`DROP DATABASE`用于丢弃数据库中的所有表格并删除数据库。使用此语句时要非常小心！如果要使用`DROP DATABASE`，您需要获得数据库`DROP`权限。

`IF EXISTS`用于防止当数据库不存在时发生错误。

如果您对一个带有符号链接的数据库使用`DROP DATABASE`，则链接和原数据库都被丢弃。

`DROP DATABASE`会返回已被丢弃表的数目。此数目相当于被丢弃的`.frm`文件的数目。

### 1.7 `DROP INDEX`语法

```sql
DROP INDEX index_name ON tbl_name 
```

`DROP INDEX`用于从表`tbl_name`中取消名称为`index_name`的索引。本语句如果被映射到一个`ALTER TABLE`语句中，就是用于取消索引。

**举例：**

要删除表`t`上的主键，刚好索引名就是`PRIMARY`，这时应该给索引名指定引号标识符，因为`PRIMARY`是一个保留字：

```sql
DROP INDEX `PRIMARY` ON t; 
```

### 1.8 `DROP TABLE`语法

```sql
DROP [TEMPORARY] TABLE [IF EXISTS]
    tbl_name [, tbl_name] ...
    [RESTRICT | CASCADE] 
```

`DROP TABLE`用于移除一个或多个表。您必须有每个表的`DROP`权限。所有的表数据和表定义会被移除，所以使用本语句要小心！

**注意：**对于一个带分区的表，`DROP TABLE`会永久性地移除表定义，移除各分区，并移除储存在这些分区中的所有数据。`DROP TABLE`还会移除与被移除表有关联的分区定义（`.par`）文件。

对于不存在的表，使用`IF EXISTS`用于防止错误发生。当使用`IF EXISTS`时，对于每个不存在的表，会生成一个`NOTE`。

### 1.9 `RENAME TABLE`语法

```sql
RENAME TABLE tbl_name TO new_tbl_name
    [, tbl_name2 TO new_tbl_name2] ... 
```

本语句用于对一个或多个表进行重命名。

**注意：**你不能直接使用`RENAME`给一个`TEMPORARY`类型的表重命名。但是你可以添加`ALTER TABLE`语句实现`orig_name`的重命名：

```sql
mysql> ALTER TABLE orig_name RENAME new_name; 
```

## 二、数据操作语句

### 2.1 `DELETE`语法

单表语法：

```sql
DELETE [LOW_PRIORITY] [QUICK] [IGNORE] FROM tbl_name
    [WHERE where_definition]
    [ORDER BY ...]
    [LIMIT row_count] 
```

多表语法：

```sql
DELETE [LOW_PRIORITY] [QUICK] [IGNORE]
    tbl_name[.*] [, tbl_name[.*] ...]
    FROM table_references
    [WHERE where_definition]

or：

DELETE [LOW_PRIORITY] [QUICK] [IGNORE]
    FROM tbl_name[.*] [, tbl_name[.*] ...]
    USING table_references
    [WHERE where_definition] 
```

如果`tbl_name`中有些行满足由`where_definition`给定的条件，那么`DELETE`就会删除这些行，并返回被删除的记录的数目。

如果您编写的`DELETE`语句中没有`WHERE`子句，则所有的行都被删除。当您不想知道被删除的行的数目时，有一个更快的方法，即使用`TRUNCATE TABLE`。

**举例：**

如果`DELETE`语句中含有`ORDER BY`子句，则按照子句中指定的顺序删除行，再结合`LIMIT`语句会显得非常有优势。

比如说，下面的语句就是找出表`somelog`中匹配`WHERE`子句条件的所有行，并根据`timestamp_column`这一列数据进行排序，然后删除第一（最老的）行：

```sql
mysql> DELETE FROM somelog WHERE user = 'jcole' ORDER BY timestamp_column LIMIT 1; 
```

### 2.2 `DO`语法

```sql
DO expr [, expr] ... 
```

`DO`用于执行表达式，但是不返回任何结果。`DO`是`SELECT expr`的简化表达方式。`DO`有一个优势，就是如果您不太关心结果的话，`DO`的速度稍快。 `DO`主要用于执行有副作用的函数，比如`RELEASE_LOCK()`。

**举例：**

这个`SELECT`有停止的作用，但是也产生了一个结果集：

```sql
mysql> SELECT SLEEP(5); 
```

相反，尝试一下`DO`语句的神奇：

```sql
mysql> DO SLEEP(5); 
```

以上例子适用于禁止产生结果集的存储函数或触发器。

### 2.3 `HANDLER`语法

```sql
HANDLER tbl_name OPEN [ AS alias ]
HANDLER tbl_name READ index_name { = | >= | <= | < } (value1,value2,...)
    [ WHERE where_condition ] [LIMIT ... ]
HANDLER tbl_name READ index_name { FIRST | NEXT | PREV | LAST }
    [ WHERE where_condition ] [LIMIT ... ]
HANDLER tbl_name READ { FIRST | NEXT }
    [ WHERE where_condition ] [LIMIT ... ]
HANDLER tbl_name CLOSE 
```

`HANDLER`语句提供通往表存储引擎接口的直接通道。`HANDLER`可以用于`MyISAM`和`InnoDB`表。

`HANDLER...OPEN`语句用于打开一个表，通过后续的`HANDLER...READ`语句建立读取表的通道。本表目标不会被其它线程共享，也不会关闭，直到线程调用`HANDLER...CLOSE`或线程中止时为止。如果您使用一个别名打开表，如果要使用其它`HANDLER`语句进一步参阅表则必须使用此别名，而不能使用表名。

### 2.4 `INSERT`语法

```sql
INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name [(col_name,...)]
    VALUES ({expr | DEFAULT},...),(...),...
    [ ON DUPLICATE KEY UPDATE col_name=expr, ... ]

or：

INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name
    SET col_name={expr | DEFAULT}, ...
    [ ON DUPLICATE KEY UPDATE col_name=expr, ... ]

or：

INSERT [LOW_PRIORITY | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name [(col_name,...)]
    SELECT ...
    [ ON DUPLICATE KEY UPDATE col_name=expr, ... ] 
```

`INSERT`用于向一个已有的表中插入新行。`INSERT...VALUES`和`INSERT...SET`形式的语句根据明确指定的值插入行。`INSERT...SELECT`形式的语句插入从其它表中选出的行。

**举例：**

`INSERT`语句可以使用`VALUES`语法插入多行数据。要想实现这一点，每一列的值列表必须用括号括起来，并使用逗号隔开。例如：

```sql
INSERT INTO tbl_name (a,b,c) VALUES(1,2,3),(4,5,6),(7,8,9); 
```

### 2.5 `LOAD DATA INFILE`语法

```sql
LOAD DATA [LOW_PRIORITY | CONCURRENT] [LOCAL] INFILE 'file_name.txt'
    [REPLACE | IGNORE]
    INTO TABLE tbl_name
    [FIELDS
        [TERMINATED BY 'string']
        [[OPTIONALLY] ENCLOSED BY 'char']
        [ESCAPED BY 'char' ]
    ]
    [LINES
        [STARTING BY 'string']
        [TERMINATED BY 'string']
    ]
    [IGNORE number LINES]
    [(col_name_or_user_var,...)]
    [SET col_name = expr,...)] 
```

`LOAD DATA INFILE`语句用于高速地从一个文本文件中读取行，并装入一个表中。文件名称必须为文字字符串类型。

**举例：**

下面例子是从文件`persondata.txt`中加载数据到表`persondata`中：

```sql
LOAD DATA INFILE 'persondata.txt' INTO TABLE persondata; 
```

### 2.6 `REPLACE`语法

```sql
REPLACE [LOW_PRIORITY | DELAYED]
    [INTO] tbl_name [(col_name,...)]
    VALUES ({expr | DEFAULT},...),(...),...

或：

REPLACE [LOW_PRIORITY | DELAYED]
    [INTO] tbl_name
    SET col_name={expr | DEFAULT}, ...

或：

REPLACE [LOW_PRIORITY | DELAYED]
    [INTO] tbl_name [(col_name,...)]
    SELECT ... 
```

`REPLACE`的运行模式与`INSERT`很相像。只有一点除外，如果表中的一个旧记录与一个用于`PRIMARY KEY`或一个`UNIQUE`索引的新记录具有相同的值，则在新记录被插入之前，旧记录被删除。

### 2.7 `SELECT`语法

```sql
SELECT
    [ALL | DISTINCT | DISTINCTROW ]
      [HIGH_PRIORITY]
      [STRAIGHT_JOIN]
      [SQL_SMALL_RESULT] [SQL_BIG_RESULT] [SQL_BUFFER_RESULT]
      [SQL_CACHE | SQL_NO_CACHE] [SQL_CALC_FOUND_ROWS]
    select_expr, ...
    [INTO OUTFILE 'file_name' export_options
      | INTO DUMPFILE 'file_name']
    [FROM table_references
    [WHERE where_definition]
    [GROUP BY {col_name | expr | position}
      [ASC | DESC], ... [WITH ROLLUP]]
    [HAVING where_definition]
    [ORDER BY {col_name | expr | position}
      [ASC | DESC] , ...]
    [LIMIT {[offset,] row_count | row_count OFFSET offset}]
    [PROCEDURE procedure_name(argument_list)]
    [FOR UPDATE | LOCK IN SHARE MODE]] 
```

`SELECT`用于从一个或多个表中恢复选择的行，也可以用于在不检索任何表的情况下计算。

**例如：**

```sql
mysql> SELECT 1 + 1; 
```

也可以在没有表可以参考的情况下指定`DUAL`为一个虚拟的表名：

```sql
mysql> SELECT 1 + 1 FROM DUAL; 
```

#### 2.7.1 JOIN 语法

MySQL 支持以下`JOIN`语法。这些语法用于`SELECT`语句的`table_references`部分和多表`DELETE`和`UPDATE`语句：

```sql
table_references:
    table_reference [, table_reference] …

table_reference:
    table_factor
  | join_table

table_factor:
    tbl_name [[AS] alias]
        [{USE|IGNORE|FORCE} INDEX (key_list)]
  | ( table_references )
  | { OJ table_reference LEFT OUTER JOIN table_reference
        ON conditional_expr }

join_table:
    table_reference [INNER | CROSS] JOIN table_factor [join_condition]
  | table_reference STRAIGHT_JOIN table_factor
  | table_reference STRAIGHT_JOIN table_factor ON condition
  | table_reference LEFT [OUTER] JOIN table_reference join_condition
  | table_reference NATURAL [LEFT [OUTER]] JOIN table_factor
  | table_reference RIGHT [OUTER] JOIN table_reference join_condition
  | table_reference NATURAL [RIGHT [OUTER]] JOIN table_factor

join_condition:
    ON conditional_expr
  | USING (column_list) 
```

一个表的引用也被称为一个联合表达式。与 SQL 标准相比，`table_factor`的语法被扩展了，前者只接受`table_reference`，不接受括号内的`table_reference`列表。

#### 2.7.2 `UNION`语法

```sql
SELECT ...
UNION [ALL | DISTINCT]
SELECT ...
[UNION [ALL | DISTINCT]
SELECT ...] 
```

`UNION`用于把来自多个`SELECT`语句的结果组合到一个结果集中。

**举例：**

如果相应的`SELECT`列的数据类型不匹配，那么在`UNION`中列的类型和长度应该考虑所有的`SELECT`语句的返回值。比如：

```sql
mysql> SELECT REPEAT('a',1) UNION SELECT REPEAT('b',10); 
```

你会发现：只有最后一个`SELECT`语句被重复写入输出集；

思考一下，为什么？并把答案写入到实验报告。

### 2.8 `UPDATE`语法

单表语法：

```sql
UPDATE [LOW_PRIORITY] [IGNORE] tbl_name
    SET col_name1=expr1 [, col_name2=expr2 ...]
    [WHERE where_definition]
    [ORDER BY ...]
    [LIMIT row_count] 
```

多表语法：

```sql
UPDATE [LOW_PRIORITY] [IGNORE] table_references
    SET col_name1=expr1 [, col_name2=expr2 ...]
    [WHERE where_definition] 
```

`UPDATE`语法可以将新值更新到原有表中的行和列。`SET`子句指示要修改哪些列和要给予哪些值。`WHERE`子句指定应更新哪些行。如果没有`WHERE`子句，则更新所有的行。如果指定了`ORDER BY`子句，则按照被指定的顺序对行进行更新。`LIMIT`子句用于给定一个限值，限制可以被更新的行的数目。

**例子：**

例如，表`t`中的`id`列有`1`和`2`这两个值，并且在`2`被更新到`3`之前，`1`会被更新为`2`，那么就会出错。为了避免这个问题，添加一个`ORDER BY`子句让拥有较大`id`值的行更早更新：

```sql
UPDATE t SET id = id + 1 ORDER BY id DESC; 
```

## 三、MySQL 实用工具语法

### 3.1 `DESCRIBE`语法（获取有关列的信息）

```sql
{DESCRIBE | DESC} tbl_name [col_name | wild] 
```

`DESCRIBE`可以提供有关表中各列的信息。它是`SHOW COLUMNS FROM`的快捷方式。

### 3.2 `USE`语法

```sql
USE db_name 
```

`USE db_name`语句可以通告 MySQL 把`db_name`数据库作为默认（当前）数据库使用，用于后续的语句。

## 四、MySQL 事务处理和锁定语句

### 4.1 `START TRANSACTION`，`COMMIT`和`ROLLBACK`语法

```sql
START TRANSACTION | BEGIN [WORK]
COMMIT [WORK] [AND [NO] CHAIN] [[NO] RELEASE]
ROLLBACK [WORK] [AND [NO] CHAIN] [[NO] RELEASE]
SET AUTOCOMMIT = {0 | 1} 
```

以下语句提供事务的使用控制：

*   `START TRANSACTION`或`BEGIN`语句可以开始一项新的事务。
*   `COMMIT`可以提交当前事务，使其永久更改。
*   `ROLLBACK`可以回滚当前事务，取消变更。
*   `SET AUTOCOMMIT`语句可以禁用或启用当前连接中默认的`autocommit`模式，。

默认情况下，MySQL 采用`autocommit`模式运行。这意味着，当您执行一个用于更新（修改）表的语句之后，MySQL 立刻把更新存储到磁盘中。

如果您正在使用一个事务安全型的存储引擎（如`InnoDB`，`BDB`或`NDB`簇），则您可以使用以下语句禁用`autocommit`模式：

```sql
SET AUTOCOMMIT=0; 
```

通过把`AUTOCOMMIT`变量设置为零，禁用`autocommit`模式之后，您必须使用`COMMIT`把变更存储到磁盘中，或者如果您想要忽略从事务开始进行以来做出的变更，请使用`ROLLBACK`。

如果您想要对于一个单一系列的语句禁用`autocommit`模式，则您可以使用`START TRANSACTION`语句：

```sql
START TRANSACTION;
SELECT @A:=SUM(salary) FROM table1 WHERE type=1;
UPDATE table2 SET summary=@A WHERE type=1;
COMMIT; 
```

使用`START TRANSACTION`，`autocommit`仍然被禁用，直到您使用`COMMIT`或`ROLLBACK`结束事务为止。然后`autocommit`模式恢复到原来的状态。

`BEGIN`和`BEGIN WORK`被作为`START TRANSACTION`的别名受到支持，用于对事务进行初始化。`START TRANSACTION`是标准的 SQL 语法，并且是启动一个`ad-hoc`事务的推荐方法。`BEGIN`语句与`BEGIN`关键词的使用不同。`BEGIN`关键词可以启动一个`BEGIN...END`复合语句。后者不会开始一项事务。

您也可以按照如下方法开始一项事务：

```sql
START TRANSACTION WITH CONSISTENT SNAPSHOT; 
```

`WITH CONSISTENT SNAPSHOT`子句用于启动一个一致的读取，用于具有此类功能的存储引擎。目前，该子句只适用于`InnoDB`，该子句的效果与发布一个`START TRANSACTION`，后面跟一个来自任何`InnoDB`表的`SELECT`的效果一样。

开始一项事务会造成一个隐含的`UNLOCK TABLES`被执行。

为了获得最好的结果，事务应只使用由单一事务存储引擎管理的表执行。否则，会出现以下问题：

*   如果您使用的表来自多个事务安全型存储引擎（例如`InnoDB`），并且事务隔离等级不是`SERIALIZABLE`，则有可能当一个事务提交时，其它正在进行中的、使用同样的表的事务将只会发生由第一个事务产生的变更。也就是，用混合引擎不能保证事务的原子性，并会造成不一致。（如果混合引擎事务不经常有，则您可以根据需要使用`SET TRANSACTION ISOLATION LEVEL`把隔离等级设置到`SERIALIZABLE`。）

*   如果您在事务中使用非事务安全型表，则对这些表的任何变更被立刻存储，不论`autocommit`模式的状态如何。

*   如果您在更新了事务中一个事务表之后，发布一个`ROLLBACK`语句，则会出现一个`ER_WARNING_NOT_COMPLETE_ROLLBACK`警告。对事务安全型表的变更会被回滚，但是非事务安全型表没有发生任何改变。

每个事务被存储在一个组块中的二进制日志中，在`COMMIT`之上。其中被回滚的事务不被计入日志。（例外情况：对非事务表的更改不会被回滚。如果一个被回滚的事务包括对非事务表的更改，则整个事务使用一个`ROLLBACK`语句计入日志末端，以确保对这些表的更改进行复制。）

您可以使用`SET TRANSACTION ISOLATION LEVEL`更改事务的隔离等级。

### 4.2 `SAVEPOINT`和`ROLLBACK TO SAVEPOINT`语法

```sql
SAVEPOINT identifier
ROLLBACK [WORK] TO SAVEPOINT identifier
RELEASE SAVEPOINT identifier 
```

`InnoDB`支持 SQL 语句`SAVEPOINT, ROLLBACK TO SAVEPOINT, RELEASE SAVEPOINT`和可选的用于`ROLLBACK`的`WORK`关键词。

`SAVEPOINT`语句用于用标识符名称设置命名一个事务保存点。如果当前事务有一个同样名称的保存点，则旧的保存点被删除，新的保存点就会被设置。

`ROLLBACK TO SAVEPOINT`语句会向以命名的保存点回滚一个事务。如果在保存点被设置后，当前事务对行进行了更改，则这些更改会在回滚中被撤销。但是，`InnoDB`不会释放被存储在保存点之后的存储器中的行锁定。（注意，对于新插入的行，锁定信息被存储在行中的事务`ID`承载；锁定没有被分开存储在存储器中。在这种情况下，行锁定在撤销中被释放。）在被命名的保存点之后设置的保存点被删除。

如果语句返回以下错误，则意味着不存在带有指定名称的保存点：

```sql
ERROR 1181: Got error 153 during ROLLBACK 
```

`RELEASE SAVEPOINT`语句会从当前事务的一组保存点中删除已命名的保存点，不出现提交或回滚。如果保存点不存在，会出现错误。

如果您执行`COMMIT`或执行不能命名保存点的`ROLLBACK`，则当前事务的所有保存点被删除。

## 4.3 `LOCK TABLES`和`UNLOCK TABLES`语法

```sql
LOCK TABLES
    tbl_name [AS alias] {READ [LOCAL] | [LOW_PRIORITY] WRITE}
    [, tbl_name [AS alias] {READ [LOCAL] | [LOW_PRIORITY] WRITE}] ... 
```

`LOCK TABLES`可以锁定用于当前线程的表。如果表被其它线程锁定，则造成堵塞，直到可以获取所有锁定为止。`UNLOCK TABLES`可以释放被当前线程保持的任何锁定。当线程发布另一个`LOCK TABLES`时，或当与服务器的连接被关闭时，所有由当前线程锁定的表被隐含地解锁。

表锁定只用于防止其它客户端进行不正当地读取和写入。保持锁定（即使是读取锁定）的客户端可以进行表层级的操作，比如`DROP TABLE`。

## 4.4 `SET TRANSACTION`语法

```sql
SET [GLOBAL | SESSION] TRANSACTION ISOLATION LEVEL
{ READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE } 
```

本语句用于设置事务隔离等级，用于下一个事务或者当前会话。

在默认情况下，`SET TRANSACTION`会为下一个（还未开始）事务设置隔离等级。如果您使用`GLOBAL`关键词，则语句会设置全局性的默认事务等级，用于从该点以后创建的所有新连接，且原有的连接不受影响。要进行此操作，您需要`SUPER`权限。使用`SESSION`关键词可以设置默认事务等级，用于对当前连接执行所有将来事务。

## 4.5 `XA`事务 SQL 语法

要在 MySQL 中执行`XA`事务，应使用以下语句：

```sql
XA {START|BEGIN} xid [JOIN|RESUME]

XA END xid [SUSPEND [FOR MIGRATE]]

XA PREPARE xid

XA COMMIT xid [ONE PHASE]

XA ROLLBACK xid

XA RECOVER 
```

对于`XA START`，`JOIN`和`RESUME`子句不被支持。

对于`XA END`，`SUSPEND [FOR MIGRATE]`子句不被支持。

每个`XA`语句以`XA`关键词为开头，多数语句要求一个`xid`值。`xid`是`XA`事务的标识符，它指示该语句适用于哪个事务。`xid`值由客户端提供，或由 MySQL 服务器生成。`xid`值包含一到三个部分：

```sql
xid: gtrid [, bqual [, formatID ]] 
```

`gtrid`是一个全局事务标识符，`bqual`是一个分支限定符，`formatID`是一个用于标识由`gtrid`和`bqual`值使用的格式的数字。根据语法，`bqual`和`formatID`是可选的。如果没有给定，默认的`bqual`值是`''`，默认的`fromatID`值是`1`。

`gtrid`和`bqual`必须为字符串文字，每个的长度最多为 64 字节（不是字符）。`gtrid`和`bqual`可以用多种方法指定。您可以使用带引号的字符串`('ab')`，十六进制字符串`(0x6162, X'ab')`，或位值`(b'nnnn')`。

`formatID`是一个无符号的整数。

## 五、数据库管理语句

### 5.1 账户管理语句

#### 5.1.1 `CREATE USER`语法

```sql
CREATE USER user [IDENTIFIED BY [PASSWORD] 'password']
    [, user [IDENTIFIED BY [PASSWORD] 'password']] ... 
```

`CREATE USER`用于创建新的 MySQL 账户。要使用`CREATE USER`，您必须拥有 mysql 数据库的全局`CREATE USER`权限，或拥有`INSERT`权限。对于每个账户，`CREATE USER`会在`mysql.user`表中创建一个新记录。如果试图建立一个已经存在的账户，则会报错。

使用可选的`IDENTIFIED BY`子句，可以为账户给定一个密码。举例如下：

创建一个已经使用`sha256_password`认证的插件和密码设置为`'new-password'`的账户`'jeffrey'@'localhost'`，要求每隔 180 天建立一个新的密码：

```sql
CREATE USER 'jeffrey'@'localhost'
  IDENTIFIED WITH sha256_password BY 'new-password'
  PASSWORD EXPIRE INTERVAL 180 DAY; 
```

#### 5.1.2 `DROP USER`语法

```sql
DROP USER user [, user] ... 
```

`DROP USER`语句用于删除一个或多个 MySQL 账户。要使用`DROP USER`，您必须拥有 mysql 数据库的全局`CREATE USER`权限或`DELETE`权限。

#### 5.1.3 `GRANT`和`REVOKE`语法

```sql
GRANT priv_type [(column_list)] [, priv_type [(column_list)]] ...
    ON [object_type] {tbl_name | * | *.* | db_name.*}
    TO user [IDENTIFIED BY [PASSWORD] 'password']
        [, user [IDENTIFIED BY [PASSWORD] 'password']] ...
    [REQUIRE
        NONE |
        [{SSL| X509}]
        [CIPHER 'cipher' [AND]]
        [ISSUER 'issuer' [AND]]
        [SUBJECT 'subject']]
    [WITH with_option [with_option] ...]

object_type =
    TABLE
  | FUNCTION
  | PROCEDURE

with_option =
    GRANT OPTION
  | MAX_QUERIES_PER_HOUR count
  | MAX_UPDATES_PER_HOUR count
  | MAX_CONNECTIONS_PER_HOUR count
  | MAX_USER_CONNECTIONS count

REVOKE priv_type [(column_list)] [, priv_type [(column_list)]] ...
    ON [object_type] {tbl_name | * | *.* | db_name.*}
    FROM user [, user] ...

REVOKE ALL PRIVILEGES, GRANT OPTION FROM user [, user] ... 
```

`GRANT`和`REVOKE`语句允许系统管理员创建 MySQL 用户账户，授予权限和撤销权限。

#### 5.1.4 `RENAME USER`语法

```sql
RENAME USER old_user TO new_user
    [, old_user TO new_user] ... 
```

`RENAME USER`语句用于对原有 MySQL 账户进行重命名。要使用`RENAME USER`，您必须拥有全局`CREATE USER`权限或 mysql 数据库`UPDATE`权限。如果旧 账户不存在或者新账户已存在，则会出现错误。`old_user`和`new_user`值的给定方法与`GRANT`语句一样。

**例如：**

```sql
RENAME USER 'jeffrey'@'localhost' TO 'jeff'@'127.0.0.1'; 
```

#### 5.1.5 `SET PASSWORD`语法

```sql
SET PASSWORD = PASSWORD('some password')
SET PASSWORD FOR user = PASSWORD('some password') 
```

`SET PASSWORD`语句用于向一个原有 MySQL 用户账户赋予密码。

第一个语法是为当前用户设置密码，其中使用一个非匿名账户连接到服务器上的任何客户即都可以更改该账户的密码。

第二个语法是为当前服务器主机上的一个特定账户设置密码。只有拥有 mysql 数据库`UPDATE`权限的客户端可以这么做。`user`值应以为固定格式`user_name@host_name`，此处`user_name`和`host_name`与`mysql.user`表登录项的`User`和`Host`列中列出的完全一样。

### 5.2 表维护语句

#### 5.2.1 `ANALYZE TABLE`语法

```sql
ANALYZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ... 
```

本语句用于分析和存储表的关键字分布。在分析期间，使用读取锁定对表`MyISAM`， `BDB`和`InnoDB`表进行锁定。对于`MyISAM`表，本语句与使用`myisamchk --analyze`相当。

当您对除常数以外的对象执行联合时，MySQL 使用已存储的关键字分布来决定，表按什么顺序进行联合。

`ANALYZE TABLE`语句会返回一个含有下列的结果集：

![此处输入图片的描述](img/08079f07b009d4c641f4a4d4f6ecb4fd.jpg)

#### 5.2.3 `CHECK TABLE`语法

```sql
CHECK TABLE tbl_name [, tbl_name] ... [option] ...

option = {QUICK | FAST | MEDIUM | EXTENDED | CHANGED} 
```

`CHECK TABLE`对`MyISAM`和`InnoDB`表执行检查。对于`MyISAM`表，关键字统计数据在检查的同时也被更新。

`CHECK TABLE`也可以检查视图是否有错误，比如在视图定义中被引用的表已不存在。

`CHECK TABLE`语句会返回一个含有以下列的结果集：

![此处输入图片的描述](img/d74b12c03b5fd12036f8427f3c7e3cdf.jpg)

#### 5.2.4 `CHECKSUM TABLE`语法

```sql
CHECKSUM TABLE tbl_name [, tbl_name] ... [ QUICK | EXTENDED ] 
```

报告表的[校验和](http://baike.baidu.com/view/1969592.htm)。

#### 5.2.5 `OPTIMIZE TABLE`语法

```sql
OPTIMIZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ... 
```

如果您已经删除了表的一大部分，或者如果您已经对含有可变长度行的表（含有`VARCHAR`，`BLOB`或`TEXT`列的表）进行了很多更改，则应使用`OPTIMIZE TABLE`。被删除的记录被保存在链接清单中，后续的`INSERT`操作会重新使用旧的记录位置。您可以使用`OPTIMIZE TABLE`来重新利用未使用的空间，并整理数据文件碎片。

在多数的设置中，您根本不需要运行`OPTIMIZE TABLE`。即使您对可变长度的行进行了大量的更新，您也不需要经常运行，只对特定的表每周一次或每月一次执行该语句即可。

#### 5.2.6 `REPAIR TABLE`语法

```sql
REPAIR [LOCAL | NO_WRITE_TO_BINLOG] TABLE
    tbl_name [, tbl_name] ... [QUICK] [EXTENDED] [USE_FRM] 
```

`REPAIR TABLE`用于修复被破坏的表。默认情况下，`REPAIR TABLE`与`myisamchk --recover tbl_name`具有相同的效果。`REPAIR TABLE`对`MyISAM`和`ARCHIVE`表起作用。

通常，您基本上不必运行此语句。但是，如果'灾难'真的发生，`REPAIR TABLE`很有可能从 MyISAM 表中找回所有数据。如果您的表经常被破坏，您应该尽力找到原因，以避免使用`REPAIR TALBE`。

本语句会返回一个含有以下列的结果集：

![此处输入图片的描述](img/ae5427ca7ce1ed84f80754c25766d95e.jpg)

### 5.3 `SET`语法

```sql
SET variable_assignment [, variable_assignment] ...

variable_assignment:
      user_var_name = expr
    | [GLOBAL | SESSION] system_var_name = expr
    | @@[global. | session.]system_var_name = expr 
```

`SET`用于设置不同类型的变量，这些变量会影响服务器或客户端的操作。SET 可以用于向用户变量或系统变量赋值。

### 5.4 `SHOW`语法

#### 5.4.1 `SHOW CHARACTER SET`语法

```sql
SHOW CHARACTER SET [LIKE 'pattern'] 
```

`SHOW CHARACTER SET`语句用于显示所有可用的字符集。如果选定`LIKE`子句，该子句用于指定哪些字符集名称可以匹配。

#### 5.4.2 `SHOW COLLATION`语法

```sql
SHOW COLLATION [LIKE 'pattern'] 
```

默认情况下，`SHOW COLLATION`语句输出所有可用的字符集。该语句如果设置可选`LIKE`子句，那么`pattern`将指定哪些归类名可以匹配。

#### 5.4.3 `SHOW COLUMNS`语法

```sql
SHOW [FULL] COLUMNS FROM tbl_name [FROM db_name] [LIKE 'pattern'] 
```

`SHOW COLUMNS`显示在一个给定表中的各列的信息。对于视图，本语句也起作用。

#### 5.4.4 `SHOW CREATE DATABASE`语法

```sql
SHOW CREATE {DATABASE | SCHEMA} db_name 
```

显示`CREATE DATABASE`语句创建的给定数据库。也可以使用`SHOW CREATE SCHEMA`。

#### 5.4.5 `SHOW CREATE TABLE`语法

```sql
SHOW CREATE TABLE tbl_name 
```

显示`CREATE TABLE`语句创建的给定表格。本语句对视图也起作用。

#### 5.4.6 `SHOW DATABASES`语法

```sql
SHOW {DATABASES | SCHEMAS} [LIKE 'pattern'] 
```

`SHOW DATABASES`可以列举在 MySQL 服务器主机上的所有数据库。您也可以使用`mysqlshow`命令得到此数据库清单。您只能看到您拥有的权限可以允许看到的的数据库，除非您拥有全局`SHOW DATABASES`权限。

如果服务器以`--skip-show-database`选项方式启动，那么您根本不能使用本语句，除非您拥有`SHOW DATABASES`权限。

#### 5.4.7 `SHOW ENGINE`语法

```sql
SHOW ENGINE engine_name {LOGS | STATUS } 
```

`SHOW ENGINE`显示存储引擎的日志或状态信息。

#### 5.4.8 `SHOW ENGINES`语法

```sql
SHOW [STORAGE] ENGINES 
```

`SHOW ENGINES`显示存储引擎的状态信息。对于检查一个存储引擎是否被支持，或者对于查看默认引擎是什么，本语句十分有用。

#### 5.4.9 `SHOW ERRORS`语法

```sql
SHOW ERRORS [LIMIT [offset,] row_count]
SHOW COUNT(*) ERRORS 
```

本语句与`SHOW WARNINGS`接近，不过该语句只显示错误，并不同时显示错误、警告和注意信息。

#### 5.4.10 `SHOW GRANTS`语法

```sql
SHOW GRANTS FOR user 
```

本语句列出了在为 MySQL 用户账户复制权限时必须发布的`GRANT`语句。

#### 5.4.11 `SHOW INDEX`语法

```sql
SHOW INDEX FROM tbl_name [FROM db_name] 
```

`SHOW INDEX`会返回表索引信息。其格式与 ODBC 中的`SQLStatistics`调用相似。

#### 5.4.12 `SHOW INNODB STATUS`语法

```sql
SHOW INNODB STATUS 
```

在 MySQL 5.1 中，等价于`SHOW ENGINE INNODB STATUS`，但不赞成使用后者。

#### 5.4.13 `SHOW LOGS`语法

```sql
SHOW [BDB] LOGS 
```

#### 5.4.14 `SHOW OPEN TABLES`语法

```sql
SHOW OPEN TABLES [FROM db_name] [LIKE 'pattern'] 
```

`SHOW OPEN TABLES`列举当前在表缓存中被打开的非`TEMPORARY`表。

#### 5.4.15 `SHOW PRIVILEGES`语法

```sql
SHOW PRIVILEGES 
```

`SHOW PRIVILEGES`显示 MySQL 服务器支持的系统权限列表。确切的输出内容需要根据您的服务器的版本而定。

#### 5.4.16 `SHOW PROCESSLIST`语法

```sql
SHOW [FULL] PROCESSLIST 
```

`SHOW PROCESSLIST`显示正在运行的线程。您也可以使用`mysqladmin processlist`语句得到此信息。如果您有`SUPER`权限，您可以看到所有线程，否则，您只能看到您自己的线程（也就是，与您正在使用的 MySQL 账户相关的线程）。

#### 5.4.17 `SHOW STATUS`语法

```sql
SHOW [GLOBAL | SESSION] STATUS [LIKE 'pattern'] 
```

`SHOW STATUS`提供服务器状态信息。此信息也可以使用`mysqladmin extended-status`命令获得。

#### 5.4.18 `SHOW TABLE STATUS`语法

```sql
SHOW TABLE STATUS [FROM db_name] [LIKE 'pattern'] 
```

`SHOW TABLE STATUS`的性质与`SHOW TABLE`的类似；不过，可以提供每个表的大量信息。您也可以使用`mysqlshow --status db_name`命令得到此信息列表。本语句也显示视图信息。

#### 5.4.19 `SHOW TABLES`语法

```sql
SHOW [FULL] TABLES [FROM db_name] [LIKE 'pattern'] 
```

`SHOW TABLES`列举了给定数据库中的非`TEMPORARY`表。您也可以使用`mysqlshow db_name`命令得到此列表。

本命令也列举数据库中的视图。支持`FULL`修改符，这样`SHOW FULL TABLES`就可以显示到第二个输出列。对于一个表，第二列的值为`BASE TABLE`；对于一个视图，第二列的值为`VIEW`。

**注释：**如果您对一个表没有权限，则该表不会在来自`SHOW TABLES`或`mysqlshow db_name`命令的输出中显示。

### 5.4.20 `SHOW TRIGGERS`语法

```sql
SHOW TRIGGERS [FROM db_name] [LIKE expr] 
```

`SHOW TRIGGERS`列出了目前被 MySQL 服务器定义的触发程序。

### 5.5 其他管理语句

#### 5.5.1 `CACHE INDEX`语法

```sql
CACHE INDEX
  tbl_index_list [, tbl_index_list] ...
  IN key_cache_name

tbl_index_list:
  tbl_name [[INDEX|KEY] (index_name[, index_name] ...)] 
```

`CACHE INDEX`语句把表索引分配给某个关键缓存。该语句只用于`MyISAM`表。

#### 5.5.2 `FLUSH`语法

```sql
FLUSH [LOCAL | NO_WRITE_TO_BINLOG] flush_option [, flush_option] ... 
```

如果您想要清除 MySQL 使用部分的内部缓存，您应该使用`FLUSH`语句。要执行`FLUSH`，您必须拥有`RELOAD`权限。

#### 5.5.3 `KILL`语法

```sql
KILL [CONNECTION | QUERY] thread_id 
```

每个与`mysqld`的连接都在一个独立的线程里运行，您可以使用`SHOW PROCESSLIST`语句查看哪些线程正在运行，并使用`KILL thread_id`语句终止其中一个线程。

`KILL`允许可选参数`CONNECTION`或`QUERY`修改符：

*   `KILL CONNECTION`与不含修改符的`KILL`一样：它会终止与给定的`thread_id`有关的连接。

*   `KILL QUERY`会终止当前与正在执行的语句的连接，但是会保持连接的原状。

如果您拥有`PROCESS`权限，则您可以查看所有线程。如果您拥有`SUPER`权限，您可以终止所有线程和语句。否则，您只能查看和终止您自己的线程和语句。

您也可以使用`mysqladmin processlist`和`mysqladmin kill`命令来检查和终止线程。

#### 5.5.4 `LOAD INDEX INTO CACHE`语法

```sql
LOAD INDEX INTO CACHE
  tbl_index_list [, tbl_index_list] ...

tbl_index_list:
  tbl_name
    [[INDEX|KEY] (index_name[, index_name] ...)]
    [IGNORE LEAVES] 
```

`LOAD INDEX INTO CACHE`语句会把一个表索引预载入到某个关键缓存中。

#### 5.5.5 `RESET`语法

```sql
RESET reset_option [, reset_option] ... 
```

`RESET`语句被用于清除不同的服务器操作的状态。它也作为`FLUSH`语句的更强大的版本。

为了执行`RESET`，您必须拥有`RELOAD`权限。

`reset_option`可以为以下的任何一项：

*   `MASTER`可以删除列于索引文件中的所有二进制日志，把二进制日志索引文件重新设置为空，并创建一个新的二进制日志文件。（在以前版本的 MySQL 中，被称为`FLUSH MASTER`。）

*   `QUERY CACHE` 从查询缓存中取消所有的查询结果。

*   `SLAVE`可以使从属服务器忘记其在主服务器二进制日志中的复制位置，另外，也可以通过删除原有的中继日志文件和开始一个新文件来重新设置中继日志。

## 六、复制语句

### 6.1 用于控制主服务器的 SQL 语句

#### 6.1.1 `PURGE MASTER LOGS`语法

```sql
PURGE {MASTER | BINARY} LOGS TO 'log_name'
PURGE {MASTER | BINARY} LOGS BEFORE 'date' 
```

首先介绍什么是二进制日志。二进制日志是一组记录 MySQL 服务器进行数据修改时的信息文件；该日志由一组二进制文件和索引文件组成。

那么`PURGE MASTER LOGS`语法就是用于删除列于在指定的日志或日期之前的日志索引中的所有二进制日志。这些被删除的日志文件也会被移出索引文件的记录列表，以致指定的日志文件被排在日志列表首部。

#### 6.1.2 `RESET MASTER`语法

```sql
RESET MASTER 
```

可以删除列于索引文件中的所有二进制日志，把二进制日志索引文件重新设置为空，并创建一个新的二进制日志文件。

#### 6.1.3 `SET SQL_LOG_BIN`语法

```sql
SET SQL_LOG_BIN = {0|1} 
```

如果客户端使用一个有`SUPER`权限的账户连接，则可以禁用或启用当前连接的二进制日志记录。如果客户端没有此权限，则语句被拒绝，并伴随错误提示。

#### 6.1.4 `SHOW BINLOG EVENTS`语法

```sql
SHOW BINLOG EVENTS
   [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count] 
```

用于显示二进制日志中的事件。如果您不指定`'log_name'`，则显示第一个二进制日志。

#### 6.1.5 `SHOW MASTER LOGS`语法

```sql
SHOW MASTER LOGS
SHOW BINARY LOGS 
```

用于列出服务器中的二进制日志文件。

#### 6.1.6 `SHOW MASTER STATUS`语法

```sql
SHOW MASTER STATUS 
```

用于提供主服务器上二进制日志文件的状态信息。需要有`SUPER`或者`REPLICATION CLIENT`权限才能执行该语句。

#### 6.1.7 `SHOW SLAVE HOSTS`语法

```sql
SHOW SLAVE HOSTS 
```

用于显示当前使用主服务器注册的克隆从属服务器的清单。不以`--report-host=slave_name`选项为开头的从属服务器不会显示在本清单中。

### 6.2 用于控制从服务器的 SQL 语句

6.2.1 `CHANGE MASTER TO`语法

```sql
CHANGE MASTER TO master_def [, master_def] ...

master_def:
      MASTER_HOST = 'host_name'
    | MASTER_USER = 'user_name'
    | MASTER_PASSWORD = 'password'
    | MASTER_PORT = port_num
    | MASTER_CONNECT_RETRY = count
    | MASTER_LOG_FILE = 'master_log_name'
    | MASTER_LOG_POS = master_log_pos
    | RELAY_LOG_FILE = 'relay_log_name'
    | RELAY_LOG_POS = relay_log_pos
    | MASTER_SSL = {0|1}
    | MASTER_SSL_CA = 'ca_file_name'
    | MASTER_SSL_CAPATH = 'ca_directory_name'
    | MASTER_SSL_CERT = 'cert_file_name'
    | MASTER_SSL_KEY = 'key_file_name'
    | MASTER_SSL_CIPHER = 'cipher_list' 
```

可以更改从属服务器用于与主服务器进行连接和通讯的参数。

`MASTER_USER, MASTER_PASSWORD,MASTER_SSL,MASTER_SSL_CA,MASTER_SSL_CAPATH, MASTER_SSL_CERT,MASTER_SSL_KEY 和 MASTER_SSL_CIPHER`这些参数用于向从属服务器提供有关如何与主服务器连接的信息。

即使对于在编译时没有[SSL](https://zh.wikipedia.org/wiki/%E5%82%B3%E8%BC%B8%E5%B1%A4%E5%AE%89%E5%85%A8%E5%8D%94%E8%AD%B0)支持的从属服务器，`SSL`选项`(MASTER_SSL, MASTER_SSL_CA, MASTER_SSL_CAPATH, MASTER_SSL_CERT, MASTER_SSL_KEY 和 MASTER_SSL_CIPHER)`也可以被更改。它们被保存到`master.info`文件中，但是会被忽略，直到您使用`SSL`支持已启用的服务器。

#### 6.2.2 `MASTER_POS_WAIT()`语法

```sql
SELECT MASTER_POS_WAIT('master_log_file', master_log_pos) 
```

该语法实际上是一个函数，而不是一个语句。它被用于确认从属服务器已读取并执行最后到达主服务器上二进制日志的给定位置。

#### 6.2.3 `RESET SLAVE`语法

```sql
RESET SLAVE 
```

用于让从属服务器忘记其在主服务器的二进制日志中的复制位置。本语句被用于进行一个明确的启动：它会删除`master.info`和`relay-log.info`文件，以及所有的中继日志，并启动一个新的中继日志。

**注释：**所有的中继日志被删除，即使它们没有被从属服务器 SQL 线程完全的执行。（如果您已经发布了一个`STOP SLAVE`语句或如果从属服务器的载入量很大，那么以上情况将会发生在从属服务器上。）

存储在`master.info`文件中的连接信息通过使用在对应的启动选项中指定的值，被立即重新设置了。此信息包括主服务器主机、主服务器接口、主服务器用户和主服务器密码等值。当从属服务器 SQL 线程被中止时，它位于正在复制的临时表的中，当发布`RESET SLAVE`语句时，已被复制的临时表在从属服务器中被删除。

#### 6.2.4 `SET GLOBAL SQL_SLAVE_SKIP_COUNTER`语法

```sql
SET GLOBAL SQL_SLAVE_SKIP_COUNTER = n 
```

该语句作用是跳过来自主服务器上的`n`个事件，一般有效作用于当复制被某个语句终止的时候还原。

仅当从属线程没有正在运行时，本语句是有效的，否则会产生错误。

#### 6.2.5 `START SLAVE`语法

```sql
START SLAVE [thread_type [, thread_type] ... ]

START SLAVE [SQL_THREAD] UNTIL
    MASTER_LOG_FILE = 'log_name', MASTER_LOG_POS = log_pos

START SLAVE [SQL_THREAD] UNTIL
    RELAY_LOG_FILE = 'log_name', RELAY_LOG_POS = log_pos

thread_type: IO_THREAD | SQL_THREAD 
```

不含任何可选参数的`START SLAVE`会同时启动两个从属服务器线程。`I/O`线程从主服务器中读取查询事件，并把它们存储在中继日志中，SQL 线程读取中继日志并执行查询。`START SLAVE`语句要求`SUPER`权限。

#### 6.2.6 `STOP SLAVE`语法

```sql
STOP SLAVE [thread_type [, thread_type] ... ]

thread_type: IO_THREAD | SQL_THREAD 
```

用于中止从属服务器线程。`STOP SLAVE`的执行要求具有`SUPER`权限。

和`START SLAVE`相似，本语句在使用时可以添加`IO_THREAD`和`SQL_THREAD`选项，指明将被中止的线程。

在以前版本的 MySQL 中，本语句被称为`SLAVE STOP`，在 MySQL 5.1 中仍然接受这种用法，以便与以前版本兼容，但是现在不赞成使用该语句。

## 七、用于预处理语句的 SQL 语法

预处理语句的 SQL 语法基于以下三个 SQL 语句：

```sql
PREPARE stmt_name FROM preparable_stmt;
EXECUTE stmt_name [USING @var_name [, @var_name] ...];
{DEALLOCATE | DROP} PREPARE stmt_name; 
```

`PREPARE`语句用于准备一个语句，并赋予它一个名称`stmt_name`，借此在以后引用该语句。

语句名称不区分大小写。`preparable_stmt`可以是一个文字字符串，也可以是一个包含了语句文本的用户变量。如果是文本，那么必须展现一个单一的 SQL 语句，而不是多个语句。使用本语句，`?`字符可以被用于标记参数，以指示当您执行查询时，数据值在哪里与查询结合在一起。`?`字符不应加引号，即使您想要把它们与字符串值结合在一起，也不要加引号。参数标记符只能被用于数据值应该出现的地方，不用于 SQL 关键词和标识符等。

如果带有此名称的预备语句已经存在，则在新的语言被预备以前，它会被隐含地解除分配。这意味着，如果新语句包含一个错误并且不能被预备，则会返回一个错误，并且不存在带有给定名称语句。

预备语句用于客户端内部会话：

*   在此会话内，如果语句被创建，那么其它客户端看不到它。

*   在预备了一个语句后，您可使用一个`EXECUTE`语句（该语句引用了预制语句名称）来执行它。如果预制语句包含任何参数标记符，则您必须提供一个列举了用户变量（其中包含要与参数结合的值）的`USING`子句。参数值只能由用户变量提供，`USING`子句必须准确地指明用户变量。用户变量的数目与语句中的参数标记符的数量一样多。

*   您可以多次执行一个给定的预制语句，在每次执行前，把不同的变量传递给它，或把变量设置为不同的值。

要对一个预制语句解除分配，需使用`DEALLOCATE PREPARE`语句。尝试在解除分配后执行一个预制语句会导致错误。

如果您终止了一个客户端会话，同时没有对以前已预制的语句解除分配，则服务器会自动解除分配。

以下 SQL 语句可以被用在预制语句中：`CREATE TABLE, DELETE, DO, INSERT, REPLACE, SELECT, SET, UPDATE`和多数的`SHOW`语句。目前不支持其它语句。