# 第 5 节 MySQL 数据类型

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

## 4\. 数据类型

### 4.1 数值类型

下面为数值类型的概述。详细信息参见[数值类型](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/column-types.html#numeric-types)。列存储需求参见[列类型存储需求](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/column-types.html#storage-requirements)。

M 指的是整型数据的最大显示宽度，且最大有效显示宽度是 255。显示宽度与存储大小或类型包含的值的范围无关，相关描述见[数值类型](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/column-types.html#numeric-types)。

如果为一个数值列指定`ZEROFILL`，MySQL 将自动为该列添加`UNSIGNED`属性，其中数值型数据允许为`UNSIGNED`属性和`SIGNED`属性，然而由于这种数据类型默认的属性是`SIGNED`，因此没有必要额外声明`SIGNED`属性；`SERIAL`是`BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE`的一个别名；在给整数类型的列定义时，`SERIAL DEFAULT VALUE`是`NOT NULL AUTO_INCREMENT UNIQUE`的一个别名。

**注意：**在进行两个整数的减法运算时，如果其中一个整数的属性为`UNSIGNED`，那么减法的结果也是无符号的，除非在是开启 SQL 模式为[NO*UNSIGNED*SUBTRACTION](http://dev.mysql.com/doc/refman/5.7/en/sql-mode.html#sqlmode_no_unsigned_subtraction)

*   `BIT[(M)]`

    **位域类型。M 表示每个值的位数，范围为从 1 到 64。如果 M 被省略，默认为 1。**

*   `TINYINT[(M)] [UNSIGNED] [ZEROFILL]`

    **很小的整数。带符号的范围是-128 到 127。无符号的范围是 0 到 255。**

*   `BOOL，BOOLEAN`

    **是 TINYINT(1)的同义词。`0`值被视为假。非`0`值视为真。**

*   `SMALLINT[(M)] [UNSIGNED] [ZEROFILL]`

    **小的整数。带符号的范围是-32768 到 32767。无符号的范围是 0 到 65535。**

*   `MEDIUMINT[(M)] [UNSIGNED] [ZEROFILL]`

    **中等大小的整数。带符号的范围是-8388608 到 8388607。无符号的范围是 0 到 16777215。**

*   `INT[(M)] [UNSIGNED] [ZEROFILL]`

    **普通大小的整数。带符号的范围是-2147483648 到 2147483647。无符号的范围是 0 到 4294967295。**

*   `INTEGER[(M)] [UNSIGNED] [ZEROFILL]`

    **这是 INT 的同义词。**

*   `BIGINT[(M)] [UNSIGNED] [ZEROFILL]`

    **大整数。带符号的范围是-9223372036854775808 到 9223372036854775807。无符号的范围是 0 到 18446744073709551615。**

    应清楚`BIGINT`类型列的下述内容：

    *   所有的算术都是使用带符号的`BIGINT`或`DOUBLE`类型的数值，因此除了位函数，不应使用大于 9223372036854775807(63 位)的无符号的大整数! 如果这样做，结果中的最后几位可能出错，这是由于将`BIGINT`值转换为`DOUBLE`进行四舍五入时造成的错误。

    MySQL 可以在以下情况下处理`BIGINT`：

    *   在`BIGINT`列中使用整数保存大的无符号的值时。

    *   在`MIN(col_name)`或`MAX(col_name)`中，其中`col_name`指`BIGINT`列。

    *   使用操作符(`+`，`-`，`*`等等)并且两边操作数均为整数时。

        *   也可以在`在 BIGINT`列中使用一个字符串保存一个固定的整数值。在这种情况下，MySQL 执行字符串到数字之间的转换，其间不存在双精度表示。
        *   当两个操作数均为整数值时，`-`、`+`和`*`操作符使用`BIGINT`算数。这说明如果乘两个大整数(函数返回的整数值)，当结果大于 9223372036854775807 时，会得到意想不到的结果。
*   `FLOAT[(M,D)] [UNSIGNED] [ZEROFILL]`

    **小(单精度)浮点数。允许的值是-3.402823466E+38 到-1.175494351E-38、0 和 1.175494351E-38 到 3.402823466E+38。这些是理论限制，基于[IEEE 标准](http://cn.ieee.org/standards.html)。实际的范围根据硬件或操作系统的不同可能稍微小些。**

    **M 是浮点数的总位数，D 是小数点后面的位数。如果 M 和 D 被省略，根据硬件允许的限制来保存值。单精度浮点数精确到大约 7 位小数位。**

    **如果指定`UNSIGNED`，不允许负值。**

    **使用浮点数可能会遇到意想不到的问题，因为在 MySQL 中的所有计算用双精度完成。参见[解决与不匹配行有关的问题](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/problems.html#no-matching-rows)。**

*   `DOUBLE[(M,D)] [UNSIGNED] [ZEROFILL]`

    **普通大小(双精度)浮点数。允许的值是-1.7976931348623157E+308 到-2.2250738585072014E-308、0 和 2.2250738585072014E-308 到 1.7976931348623157E+308。这些是理论限制，基于 IEEE 标准。实际的范围根据硬件或操作系统的不同可能稍微小些。**

    **M 是小数总位数，D 是小数点后面的位数。如果 M 和 D 被省略，根据硬件允许的限制来保存值。双精度浮点数精确到大约 15 位小数位。**

    **如果指定 UNSIGNED，不允许负值。**

*   `DOUBLE PRECISION[(M,D)] [UNSIGNED] [ZEROFILL]`, `REAL[(M,D)] [UNSIGNED] [ZEROFILL]`

    **为`DOUBLE`的同义词。除非：如果 SQL 服务器模式启动`REAL_AS_FLOAT`选项，`REAL`是`FLOAT`的同义词而不是`DOUBLE`的同义词。**

*   `FLOAT(p) [UNSIGNED] [ZEROFILL]`

    **浮点数`.p`表示精度，但 MySQL 只使用该值来确定列的数据类型是否为`FLOAT`或`DOUBLE`。如果`p`取值在 0 到 24 之间，数据类型将变为没有`M`或`D`值的`FLOAT`类型。如果`p`取值为`25`到`53`，数据类型将变为没有`M`或`D`值的`DOUBLE`型。这种情况下列范围与本节前面描述的单精度`FLOAT`或双精度`DOUBLE`数据类型相同。**

    **`FLOAT(p)`语法与 ODBC 兼容。**

### 4.2 日期和时间类型

本节综合讨论了时间数据类型。详细信息，参见[日期和时间类型](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/column-types.html#date-and-time-types)。数据存储需求参见[列类型存储需求](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/column-types.html#storage-requirements)。

*   `DATE`

    **日期。支持的范围为`1000-01-01`到`9999-12-31`。MySQL 以`YYYY-MM-DD`格式显示`DATE`值，但允许使用字符串或数字类型数据为`DATE`列赋值。**

*   `DATETIME[(fsp)]`

    **日期和时间的组合。支持的范围是`1000-01-01 00:00:00`到`9999-12-31 23:59:59`。MySQL 以`YYYY-MM-DD HH:MM:SS`格式显示`DATETIME`值，但允许使用字符串或数字为`DATETIME`列赋值。**

    **其中，可选参数`fsp`取值范围是 0-6 可以指定精确到秒的分位数，默认的是 0，指精确到秒。**

*   `TIMESTAMP[(fsp)]`

    **时间戳。范围是`1970-01-01 00:00:00`到`2038-01-19 03:14:07.999999`。**

    **`TIMESTAMP`列用于执行`INSERT`或`UPDATE`操作时记录日期和时间。如果你不分配一个值，表中的第一个`TIMESTAMP`列自动设置为最近操作的日期和时间。也可以通过分配一个`NULL`值，将`TIMESTAMP`列设置为当前的日期和时间。**

*   `TIME` **时间。范围是`-838:59:59`到`838:59:59`。MySQL 以`HH:MM:SS`格式显示`TIME`值，但允许使用字符串或数字为 TIME 列分配值。**

*   `YEAR[(2|4)]` **两位或四位格式的年。默认是四位格式。在四位格式中，允许的值是`1901`到`2155`和`0000`。在两位格式中，允许的值是`70`到`69`，表示从`1970`年到`2069`年。MySQL 以`YYYY`格式显示`YEAR`值，但允许使用字符串或数字为`YEAR`分配值。**

### 4.3 字符串类型

本节综合讨论了字符串数据类型。详细信息参见[String 类型](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/column-types.html#string-types)。

在某些情况中，MySQL 可以将一个字符串数据更改为不同于`CREATE TABLE`或`ALTER TABLE`语句中所给出的类型（参见[沉寂的列规格变更](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/sql-syntax.html#silent-column-changes)）。

MySQL 5.1 字符串数据类型包括在 MySQL 4.1 之前的版本中没有的特性部分：

*   许多字符串数据类型的列定义可以包括指定字符集的`CHARACTER SET`属性或校对规则。(`CHARSET`是`CHARACTER SET`的一个同义词）。这些属性适用于`CHAR`、`VARCHAR`、`TEXT`类型、`ENUM`和`SET`。例如：

    ```sql
    CREATE TABLE t
    (
        c1 CHAR(20) CHARACTER SET utf8,
        c2 CHAR(20) CHARACTER SET latin1 COLLATE latin1_bin
        ); 
    ```

    该表的定义创建了一个名为`c1`的列，具有一个`utf8`字符集和该字符集的默认[校对规则](http://blog.csdn.net/codolio/article/details/5613370)，和一个名为`c2`的列以及`latin1`字符集和该字符集的二元校对规则，其中二元校对规则对大小写不敏感。

    MySQL 5.1 用字符单位作为在字符列定义中的长度规范。(以前的一些 MySQL 版本以字节作为长度）。

    对于`CHAR`、`VARCHAR`和`TEXT`类型，`BINARY`属性可以为列分配该列字符集的 校对规则。字符列的排序和比较基于分配给列的字符集。在以前的版本中，排序和比较基于服务器字符集的校对规则。对于`CHAR`和`VARCHAR`列，你可以用`BINARY`属性声明列让排序和校对规则使用当前的字符代码值而不是词汇顺序。

    关于 MySQL 5.1 中字符集的支持，参见[字符集支持](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/charset.html)。

*   `[NATIONAL] CHAR(M) [BINARY| ASCII | UNICODE]`

    固定长度字符串，当保存数据时在右侧自动填充空格以达到指定的长度。`M`表示列长度。`M`的范围是`0`到`255`个字符。

    **注：**当检索`CHAR`类型值时尾部空格将被删除。

    如果想要将某个`CHAR`的长度设为大于`255`，执行`CREATE TABLE`或`ALTER TABLE`语句时将失败并提示错误，尝试输入：

    ```sql
    mysql> CREATE TABLE c1 (col1 INT, col2 CHAR(500));
    mysql> SHOW CREATE TABLE c1; 
    ```

    `CHAR`是`CHARACTER`的简写。`NATIONAL CHAR(`或其等效短形式`NCHAR`)是标准的定义`CHAR`列应使用默认字符集的 SQL 方法。`BINARY`属性是指定列字符集的二元校对规则的简写。排序和比较基于数值字符值。列类型`CHAR BYTE`是`CHAR BINARY`的一个别名，这是为了保证兼容性。通过指定`latin1`字符集，可以为`CHAR`指定`ASCII`属性。通过指定`ucs2`字符集可以为`CHAR`指定`UNICODE`属性。

    MySQL 允许创建类型为`CHAR(0)`的列。这主要用于与必须有一个列但实际上不使用它的值的旧版本中应用程序相兼容。

*   `CHAR`

    这是 CHAR(1)的同义词。

*   `[NATIONAL] VARCHAR(M) [BINARY]`

    变长字符串。`M`表示最大列长度。`M`的范围是`0`到`65,535`。(`VARCHAR`的最大实际长度由最长的行的大小和使用的字符集确定。最大有效长度是 65,532 字节）。

    **注：**MySQL 5.1 遵从标准 SQL 规范，并且不自动移除`VARCHAR`值的尾部空格。

    `VARCHAR`是字符`VARYING`的简写。`BINARY`属性是指定列的字符集的二元校对规则的简写。排序和比较基于数值字符值。`VARCHAR`保存时用一个字节或两个字节长的前缀+数据。如果`VARCHAR`列声明的长度大于`255`，长度前缀是两个字节。

*   `BINARY(M)`

    `BINARY`类型类似于`CHAR`类型，但存储二进制字节字符串而不是非二进制字符串。

*   `VARBINARY(M)`

    `VARBINARY`类型类似于`VARCHAR`类型，但存储二进制字节字符串而不是非二进制字符串。

*   `TINYBLOB`

    最大长度为`255`字节的`BLOB`列。

*   `TINYTEXT`

    最大长度为`255`字符的`TEXT`列。

*   `BLOB[(M)]`

    最大长度为`65,535`字节的`BLOB`列。

    可以给出该类型的可选长度`M`。如果给出，则 MySQL 将列创建为最小的但足以容纳`M`字节长的值的`BLOB`类型。

*   `TEXT[(M)]`

    最大长度为`65,535`字符的`TEXT`列。

    可以给出可选长度`M`。则 MySQL 将列创建为最小的但足以容纳`M`字符长的值的`TEXT`类型。

*   `MEDIUMBLOB`

    最大长度为`16,777,215`字节的`BLOB`列。

*   `MEDIUMTEXT`

    最大长度为`16,777,215`字符的`TEXT`列。

*   `LONGBLOB`

    最大长度为`4,294,967,295`或`4GB`字节的`BLOB`列。`LONGBLOB`列的最大有效(允许的)长度取决于客户端或服务器协议中配置最大包大小和可用的内存。

*   `LONGTEXT`

    最大长度为`4,294,967,295`或`4GB`字符的`TEXT`列。`LONGTEXT`列的最大有效(允许的)长度取决于客户端或服务器协议中配置最大包大小和可用的内存。

*   `ENUM('value1','value2',...)`

    枚举类型。一个字符串只能由一个值，从值列列表`'value1'，'value2'，...，NULL`中或特殊`''`错误值中选出。`ENUM`列最多可以有`65,535`个截然不同的值。`ENUM`值在内部用整数表示。

*   `SET('value1','value2',...)`

    一个[SET](http://dev.mysql.com/doc/refman/5.1/en/set.html)类型列可以 64 个不同值。字符串对象可以有零个或多个值，每个值必须来自列表值`'value1'，'value2'，...`，`SET`对应的值在内部用整数表示。