# 第 4 节 MySQL 语言结构

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

### 一、[文字值](http://resource.innovati.com.tw/innobasic-kai-fa-gong-ju-wan-quan-jie-xi/innobasic-cheng-shi-yu-yan-jie-shao/8-wen-zi-zhi-literals)

#### 1.1 字符串

字符串指用单引号(‘'’)或双引号(‘"’)引用起来的字符序列。例如：

```sql
'a string'
"another string" 
```

如果 SQL 服务器模式启用了`NSI_QUOTES`，可以只用单引号引用字符串，此时用双引号引用的字符串被解释为一个识别符。

字符串可以有一个可选的介绍字符`_charset_name`和`COLLATE`子句：

```sql
[_charset_name]'string' [COLLATE collation_name] 
```

例如：

```sql
SELECT _latin1'string';
SELECT _latin1'string' COLLATE latin1_danish_ci; 
```

在字符串中，某些序列具有特殊含义。这些序列均以反斜线(`\`)开始，即所谓的[转义字符](http://baike.baidu.com/view/73.htm)。MySQL 识别以下转义序列：

![此处输入图片的描述](img/cff0ad2fa5eaebb42e35e549126ec23a.jpg)

这些序列对大小写敏感。例如，`\b`为退格，但`\B`为字符`B`。

`\%`和`\_`序列如果用于通配符的模式匹配环境中匹配`%`和`_`。参见[字符串比较函数](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/functions.html#string-comparison-functions)。请注意如果你在其它环境中使用`\%`或`\_`，它们返回字符串`\%`和`\_`，而不是`%`和`_`。

在其它转义序列中，反斜线被忽略。也就是说，转义字符就仿佛没有转义。

转义比较好理解，引用就比较麻烦了，此处省略 30 字，因为官网给出的解释比较难以被理解，因此直接举例。

下面的 SELECT 语句显示了引用和转义如何工作：

```sql
mysql> SELECT 'hello', '"hello"', '""hello""', 'hel''lo', '\'hello';
mysql> SELECT "hello", "'hello'", "''hello''", "hel""lo", "\"hello";
mysql> SELECT 'This\nIs\nFour\nLines';
mysql> SELECT 'disappearing\ backslash'; 
```

查看输出结果，有没有理解一点？

如果你想要在字符串列内插入二进制数据(例如[BLOB](http://dev.mysql.com/doc/refman/5.5/en/blob.html))，必须通过转义序列表示下面的字符：

![此处输入图片的描述](img/e41ad47c6fbdb8ecd415e3a0300a675d.jpg)

当编写应用程序时，在将包含这些特殊字符的字符串发送到 MySQL 服务器的 SQL 语句中的数据值之前，必须对它们正确进行转义。可以用两种方法来完成：

1.  用转义特殊字符的函数处理字符串。例如，在 C 程序中，可以使用 C 语言的 API 函数[mysql*real*escape_string()](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/apis.html#mysql-real-escape-string) 来转义字符。
2.  显式转义特殊字符，许多 MySQL API 提供了占位符功能，允许你在查询字符串中插入特殊标记，然后当你发出查询时将数据值同这些标记绑定起来。在这种情况下，API 关注转义值中的特殊字符。

#### 1.2 数值型

整数用一系列阿拉伯数字表示，浮点数使用`.`作为十进制间隔符，以上两种类型的数值均可以在前面加一个`-`来表示负值。

合法整数的例子：

**1221 0 -32 **

合法浮点数的例子：

**294.42 -32032.6809e+10 148.00**

整数可以用在浮点环境中；它被认为与浮点数等效。

#### 1.3 [日期类型](http://dev.mysql.com/doc/refman/5.5/en/hexadecimal-literals.html)

#### 1.4 十六进制

MySQL 支持十六进制。在数值文本中，十六进制数如同整数(64 位精度)。在字符串文本中，如同二进制字符串，每对十六进制数字被转换为一个字符：

```sql
mysql> SELECT x'4D7953514C'；
    -> 'MySQL'
mysql> SELECT 0xa+0；
    -> 10
mysql> SELECT 0x5061756c；
    -> 'Paul' 
```

十六进制值的默认类型是字符串。如果想要确保该值能作为数字处理，可以使用`CAST(...AS UNSIGNED)`：

```sql
mysql> SELECT 0x41，CAST(0x41 AS UNSIGNED)；
    -> 'A'，65 
```

`0x`语法基于`ODBC`。十六进制字符串通常用于 ODBC 以便为`BLOB`列提供值。`x’hexstring’`语法基于标准`SQL`。

可以用`HEX()`函数将一个字符串或数字转换为十六进制格式的字符串：

```sql
mysql> SELECT HEX('cat')；
    -> '636174'
mysql> SELECT 0x636174；
    -> 'cat' 
```

#### 1.5 布尔值

常量`TRUE`等于`1`，常量`FALSE`等于`0`。常量名的大小写随意。

```sql
mysql> SELECT TRUE，true，FALSE，false；
    -> 1，1，0，0 
```

#### 1.6 [位域(bit-field)](http://baike.baidu.com/view/1256879.htm)类型

可以使用`b'value'`符号表达位域型。`value`是一个用`0`和`1`写成的二进制值。

位域型数值可以方便指定分配给`BIT`列的值：

```sql
mysql> CREATE TABLE t (b BIT(8));
mysql> INSERT INTO t SET b = b'11111111';
mysql> INSERT INTO t SET b = b'1010'; 
```

#### 1.7 `NULL`值

`NULL`值表示“没有数据”。`NULL`可以任意大小写。

请注意`NULL`值不同于数字类型的`0`或字符串类型的空字符串。

对于用`LOAD DATA INFILE`或`SELECT ...INTO OUTFILE`执行的文本文件导入或导出操作，`NULL`用序列`\N`表示。

### 二、数据库、表、索引、列和别名

数据库、表、索引、列和别名是识别符。该节描述了在 MySQL 中识别符的允许的语法。

下面的表描述了每类识别符的最大长度和允许的字符:

![此处输入图片的描述](img/aa1ac934f8a8b2e5cc145ef4af98d3de.jpg)

除了表内注明的限制，识别符不可以包含`ASCII 0`或值为`255`的字节。数据库、表和列名不应以空格结尾。在识别符中可以使用引号识别符，但是应尽可能避免这样使用。

识别符用`Unicode(UTF8)`保存。在`.frm`文件中保存的对表进行定义的识别符和在 mysql 数据库中的授权表保存的识别符也用`Unicode(UTF8)`保存。在 MySQL 5.1 中授权表（和其它表）的字符串列的大小等于字符个数；这说明（不象以前版本的 MySQL）你可以在这些列保存的值中使用多字节字符而不需要降低字符个数。

识别符可以使用引号引起来也可以不引起来。如果识别符是一个保留字或包含特殊字符，无论何时使用，必须将它引起来。

识别符的引用符是反勾号(‘`’)：

```sql
mysql> SELECT * FROM `select` WHERE `select`.id > 100; 
```

如果 SQL 服务器模式包括`ANSI_QUOTES`模式选项，还可以用双引号将识别符引起来：

```sql
mysql> CREATE TABLE "test" (col INT);
mysql> SET sql_mode='ANSI_QUOTES';
mysql> CREATE TABLE "test" (col INT); 
```

请尝试比较上面语句中两种创建表形式的输出结果。

如果你引用识别符，可以在识别符内包含识别符引用符。如果识别符内包括的字符与引用识别符的字符相同，则需要用双字符。下面的语句创建一个名为 a`b 包含列 c"d 的表：

```sql
mysql> CREATE TABLE `a``b` (`c"d` INT)； 
```

建议不要使用`XeX`模式的名，例如`1e`或`2e2`，因为与类似`1e+1`这样的数字混淆。

#### 2.1 识别符限制条件

MySQL 允许使用由单个识别符或多个识别符组成的名字。多部分名各组件之间应以句点(‘.’)间隔开。多部分名的开头部分作为限定词，后面的识别符才具有对该名字的解释意义。

在 MySQL 中可以引用下面形式的列：

![此处输入图片的描述](img/bcda618e4f8da81e9d1958f7565cc94a.jpg)

如果多部分名的组件需要被引用，应分别将它们引起来而不要将整个名字引起来。例如，`my-tables`.`my-column` 有效，而`my-tables.my-column`无效。

不需要在语句中为列指定`tbl_name`或`db_name.tbl_name`前缀，除非列本身很模糊。举个例子：假定表`t1`和`t2`各包含一个列`c`，你使用`SELECT`语句在`t1`和`t2`中搜索`c`。在这种情况下，`c`很模糊，因为它在语句中使用的表内不唯一。你必须用表名`t1.c`或`t2.c`限定它，表示指哪个表。同样，要想用同一语句搜索数据库`db1`中的表`t`和数据库`db2`中的表`t`，你必须将那些表中的列指为`db1.t.col_name`和`db2.t.col_name`。

限定名中句点后面的字必须为一个识别符，因此不需要将它引起来，即使是一个保留字。

语法`.tbl_name`表示当前数据库中的`tbl_name`。该语法与`ODBC`兼容，因为某些`ODBC`程序在表名前面加前缀‘.’字符。

#### 2.2 识别符的大小写敏感性

在 MySQL 中，所有的数据库分别存放在数据目录中的不同路径。数据库中的每个表至少对应数据库目录中的一个文件(也可能是多个，取决于存储引擎)。因此，操作系统的大小写敏感性决定了数据库名和表名的大小写敏感性。前面已经说明在大多数`Unix`中数据库名和表名对大小写敏感，而在`Windows`中对大小写不敏感。一个显著的例外情况是`Mac OS X`，它基于`Unix`但使用默认文件系统类型(`HFS+`)，对大小写不敏感。然而，`Mac OS X`也支持`UFS`卷，该卷对大小写敏感，就像`Unix`一样。

**注：**尽管在某些平台中数据库名和表名对大小写不敏感，但是也不应在同一查询中使用不同的大小写来引用给定的数据库或表。下面的查询不会生效，因为它使用两种命名方式引用了同一个表`my_tables`和`MY_tables`：

```sql
mysql> SELECT * FROM my_table WHERE MY_TABLE.col=1; 
```

列、索引、存储子程序和触发器名在任何平台上对大小写不敏感，列的别名也不敏感。

默认情况，表别名在`Unix`中对大小写敏感，但在`Windows`或`Mac OS X`中对大小写不敏感。下面的查询在`Unix`中不会生效，因为它同时引用了别名 a 和 A：

```sql
mysql> SELECT col_name FROM tbl_name AS a
    -> WHERE a.col_name = 1 OR A.col_name = 2; 
```

然而，该查询在`Windows`中是可以的。要想避免出现差错，最好采用一致的命名方式，例如总是用小写创建并引用数据库名和表名。

在 MySQL 中如何在硬盘上保存和使用表名和数据库名由`lower_case_tables_name`这个系统变量确定，可以在启动 mysqld 时设置。`lower_case_tables_name`可以采用下面的任一值：

![此处输入图片的描述](img/ae9ce2f23be4a51164664019e5ed9367.jpg)

在`Unix`上的`lower_case_tables_name`默认值是 0，在`Windows`上的`lower_case_tables_name`默认值是 1，`Mac OS X`上的默认值是 2。

如果只在一个平台上使用 MySQL，通常不需要更改`lower_case_tables_name`变量。然而，如果你想要在对大小写敏感不同的文件系统的平台之间使用同一个表会遇到困难。例如，在`Unix`中，`my_tables`和`MY_tables`是两个不同的表，但在 Windows 中，这两个表名相同，属于同一张表。要想避免由于数据库或表名的大小写造成的数据转移问题，你拥有以下两个选择：

*   在任何系统中设置`lower_case_tables_name=1`。使用该选项的缺点是当使用`SHOW TABLES`或`SHOW DATABASES`时，看不出原来的名字是用大写还是小写。

*   在`Unix`中使`lower_case_tables_name=0`，在`Windows`中使`lower_case_tables_name=2`。这样了可以保留原数据库名和表名的大小写。缺点是如果将查询转移到`Unix`中，必须确保在`Windows`中查询的时候总是用正确大小写引用数据库名和表名，原因是在`Unix`中大小写很重要，如果大小写不正确，它们将不会生效。

**此外：**如果你正使用`InnoDB`表，在任何平台上均应将`lower_case_tables_name`设置为 1，以强制将名转换为小写。

请注意在`Unix`中将`lower_case_tables_name`设置为 1 之前，每次重启 mysqld 服务之前必须先将旧的数据库名和表名转换为小写，这样避免重启 mysqld 服务的时候有新的变量名出现。

#### 2.3 关键词和[保留字](http://baike.baidu.com/view/421743.htm)

关键词在 SQL 中指代的是具有意义的一些单词。某些特定的关键字例如：`SELECT`，`DELETE`或者`BIGNT`需要与表名和列名这些标识符一起使用。

非保留关键词在没有被引用的情况下不允许作为标识符，保留字符合要求的情况下（具体见[文档](http://dev.mysql.com/doc/refman/5.7/en/identifiers.html)）可以被引用为标识符。

由于 MySQL 中可以使用的关键词和保留字比较多，因此更多关于关键词和保留字的信息请查看[文档](http://dev.mysql.com/doc/refman/5.7/en/keywords.html)。

### 三、用户自定义变量

可以先在用户自己定义的变量中存储一个值然后在后面引用它；这样可以将该值从一个语句传递到另一个语句。用户自定义变量具有会话专一性，也就是说，一个客户端定义的变量不能被其它客户端看到或使用，当客户端退出时，该客户端连接的所有变量将自动释放。

用户变量的形式为`@varname`，其中变量名`var_name`可以由当前字符集中的`字母数字字符`、`.`、`_`和`$`组成。 默认字符集是`cp1252 (Latin1)`。可以用`mysqld`的`--default-character-set`选项更改字符集。

设置用户变量的一个途径是执行 SET 语句：

```sql
SET @var_name = expr [, @var_name = expr] ... 
```

对于`SET`语句，可以使用`=`或`:=`作为分配符，分配给每个变量的`expr`可以为整数、实数、字符串或者`NULL`值。

除了使用`SET`语句也可以用为用户变量分配一个值。在这种情况下，分配符必须为`:=`而不能用`=`，因为在非`SET`语句中`=`被视为比较操作符：

```sql
# 尝试输入以下语句观察结果
mysql> SET @t1=0, @t2=0, @t3=0;
mysql> SELECT @t1:=(@t2:=1)+@t3:=4,@t1,@t2,@t3; 
```

用户变量可以用于表达式中。目前不包括明显需要文字值的语句，例如`SELECT`语句的`LIMIT`子句，或者`LOAD DATA`语句的`IGNORE number LINES`子句。

如果使用没有初始化的变量，其值是`NULL`。

**注：**在`SELECT`语句中，表达式是在被发送到客户端后才进行计算。这说明在`HAVING`、`GROUP BY`或者`ORDER BY`子句中，在`SELECT`列表中不能使用将值赋值到变量的表达式。例如，下面的语句不能按期望进行：

```sql
mysql> SELECT (@aa:=id) AS a，(@aa+3) AS b FROM tbl_name HAVING b=5； 
```

上面`HAVING`子句中使用`@aa`这个变量引用了`b`，但是这并不能按期望生效：`@aa`并不是当前行的值，而是前面所选定的行的 id 值。因此一般情况下是不建议在语句的一个部分为用户变量分配一个值而在同一语句的其它部分又使用该变量，这可能会得到期望的结果，当然也可能不会。

另一种情况是，先使用`SET`语句将值赋值到一个用户自定义变量，再在另一个非`SET`语句中，重新读取该用户自定义变量并在该语句中对原来的变量的值进行更改，此时该自定义用户的变量的值取决于表达式本身出现的先后顺序。下面的例子说明了该点：

```sql
mysql> SET @a='test';
mysql> SELECT @a,(@a:=20) FROM tbl_name; 
```

对于该`SELECT`语句，MySQL 在第一行向客户端报告`@a`是一个字符串'test'，并且将`@a`的所有访问结果转换为字符串，即使`@a`在第 2 行中设置为一个数字。只有在执行完`SELECT`语句后，`@a`的值在下一语句中才是数字 20。

要想避免这种问题，要么不在同一个语句中设置并使用相同的变量，要么在使用前将变量设置为`0`、`0.0`或者''来定义其类型。

### 五、注释语句

MySQL 服务器支持 3 种注释风格：

*   以`#`字符开始到行尾。

*   以`--`序列开始到行尾。请注意`--`(双破折号)注释风格要求第 2 个破折号后面至少跟一个空格符(例如空格、tab、换行符等等)。该语法与标准 SQL 注释语法稍有不同。

*   以`/*`序列开始到`*/`序列结束。结束序列不一定在同一行中，因此该语句允许注释跨越多行。

下面的例子显示了 3 种风格的注释：

```sql
mysql> SELECT 1+1;     # This comment continues to the end of line
mysql> SELECT 1+1;     -- This comment continues to the end of line
mysql> SELECT 1 /* this is an in-line comment */ + 1;
mysql> SELECT 1+
/*
this is a
multiple-line comment
*/
1; 
```

上述的注释语句适用于 mysqld 服务器中对 SQL 语句的分析与解释。发送到服务器之前，mysql 客户程序也执行部分语句解析。(例如，它通过解析来确定在多语句行中的语句边界）。