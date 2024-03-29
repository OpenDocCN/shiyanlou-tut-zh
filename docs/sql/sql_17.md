# 第 6 节 MySQL 函数和操作符

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

## 一、[操作符](http://baike.baidu.com/view/2099153.htm)

### 1.1 操作符优先级

以下列表显示了操作符优先级的由低到高的顺序。排列在同一行的操作符具有相同的优先级。

![此处输入图片的描述](img/8de4ac14216cb2869df41f651f0ea216.jpg)

其中，部分操作符的优先级取决于 SQL 的模式：

*   默认情况下，[||](https://www.shiyanlou.com/register?inviter=NTY0MzE5MDg1OTM0)是逻辑运算符`OR`。当启用[PIPES*AS*CONCAT](http://dev.mysql.com/doc/refman/5.1/en/sql-mode.html#sqlmode_pipes_as_concat)模式时，`||`就是一个字符串连接符，优先级处于[^](http://dev.mysql.com/doc/refman/5.1/en/bit-functions.html#operator_bitwise-xor)与一元运算符之间。
*   默认情况下，[!](http://dev.mysql.com/doc/refman/5.1/en/logical-operators.html#operator_not)的优先级高于`NOT`。但当启用[HIGH*NOT*PRECEDENCE](http://dev.mysql.com/doc/refman/5.1/en/sql-mode.html#sqlmode_high_not_precedence)模式时，`!`和`NOT`拥有相同的优先级。

### 1.2 圆括号`( ... )`

使用括弧来规定表达式的运算顺序，例如：

```sql
mysql> SELECT 1+2*3;

mysql> SELECT (1+2)*3; 
```

![此处输入图片的描述](img/8c0c021080147018e00633758e9cad85.jpg)

### 1.3 比较函数和操作符

比较运算产生的结果为`1(TRUE)`、`0 (FALSE)`或`NULL`。这些运算可用于数字和字符串。如果必要的话，字符串可自动转换为数字，而数字也可自动转换为字符串。

本节中的一些函数（如[LEAST()](http://dev.mysql.com/doc/refman/5.1/en/comparison-operators.html#function_least)和[GREATEST()](http://dev.mysql.com/doc/refman/5.1/en/comparison-operators.html#function_greatest)）的返回值并不会返回`1(TRUE)`、 `0 (FALSE)`和`NULL`这样的结果。但是按照下述规则函数进行比较运算后其返回值可以为以上结果：

MySQL 按照以下规则进行数值比较：

*   若函数中有一个或两个参数都是`NULL`，则比较运算的结果为`NULL`，除非是等号比较运算符`<=>`。
*   若同一个比较运算中的两个参数都是字符串类型，则作为字符串进行比较。
*   若两个参数均为整数，则按照整数进行比较。
*   十六进制值在不作为数字进行比较时，则按照二进制字符串处理。
*   假如参数中的一个为`TIMESTAMP`或`DATETIME`数据类型，而其它参数均为常数，则在进行比较前应该将常数转为`timestamp`类型。这样做的目的是为了使`ODBC`的进行更加顺利。**注意：**这不用于[IN()](http://dev.mysql.com/doc/refman/5.1/en/comparison-operators.html#function_in)中的参数!为了更加可靠，在进行对比时通常使用完整的`datetime/date/time`字符串。
*   在其它情况下，参数作为浮点数（实数）进行比较。

在默认状态下，字符串比较不区分大小写，并使用现有字符集(默认为`cp1252 Latin1`，同时也适用于英语)。

为了达到比较的目的，可使用[CAST()](http://dev.mysql.com/doc/refman/5.1/en/comparison-operators.html#function_in)函数将某个值转为另外一种类型。使用[CONVERT()](http://dev.mysql.com/doc/refman/5.1/en/cast-functions.html#function_convert)可以将字符串值转为不同的字符集。

下面对各类操作符的使用进行示例：

*   `=` 等于：

    ```sql
    mysql> SELECT 1 = 0;

    mysql> SELECT '0' = 0;

    mysql> SELECT '0.01' = 0; 
    ```

    ![此处输入图片的描述](img/ef37ffb99b9348b632105c24616a04b8.jpg)

    对于行比较，(a, b) = (x, y)相当于：(a = x) AND (b = y)。

*   `<=>` 空值安全的等号：

    这个操作符与`=`操作符执行相同的比较操作，不过在两个操作码均为`NULL`时，其返回至为`1`而不为`NULL`，而当一个操作码为`NULL`时，其所得值为`0`而不为`NULL`。

    ```sql
    mysql> SELECT 1 <=> 1, NULL <=> NULL, 1 <=> NULL;

    mysql> SELECT 1 = 1, NULL = NULL, 1 = NULL; 
    ```

    ![此处输入图片的描述](img/40220857322d124223c959e5e2fff981.jpg)

    对于行比较，(a, b) <=> (x, y)相当于：(a <=> x) AND (b <=> y)。

*   `<>` 或 `!=` 不等于：

    ```sql
    mysql> SELECT '.01' <> '0.01';

    mysql> SELECT .01 <> '0.01';

    mysql> SELECT 'zapp' <> 'zappp'; 
    ```

    ![此处输入图片的描述](img/4c2dcdef62a3c9c27b045976dd5457f7.jpg)

    对于行比较，(a, b) <> (x, y)相当于：(a <> x) OR (b <> y)。

*   `<=` 小于等于：

    ```sql
    mysql> SELECT 0.1 <= 2; 
    ```

    对于行比较，(a, b) <= (x, y)相当于：(a <= x) AND (b <= y)。

*   `>` 大于：

    ```sql
    mysql> SELECT 2 > 2; 
    ```

    对于行比较，(a, b) > (x, y)相当于：(a > x) AND (b > y)。

*   `IS boolean_value`和`IS NOT boolean_value`：根据一个布尔值来检验一个值，在这里，布尔值可以是`TRUE`、`FALSE`或`UNKNOWN`。

    ```sql
    mysql> SELECT 1 IS TRUE, 0 IS FALSE, NULL IS UNKNOWN;

    mysql> SELECT 1 IS NOT UNKNOWN, 0 IS NOT UNKNOWN, NULL IS NOT UNKNOWN;

    # IS NULL 和 IS NOT NULL 检验一个值是否为 NULL。

    mysql> SELECT 1 IS NULL, 0 IS NULL, NULL IS NULL;

    mysql> SELECT 1 IS NOT NULL, 0 IS NOT NULL, NULL IS NOT NULL; 
    ```

    ![此处输入图片的描述](img/7f155ed531745905d50d63afad5fcb4b.jpg)

*   `expr BETWEEN min AND max` 假如`expr`大于或等于`min`且`expr`小于或等于`max`, 则`BETWEEN`的返回值为`1`，否则是`0`。若所有参数都是同一类型，则上述关系相当于表达式 ：`min <= expr AND expr <= max`。其它类型的转换 根据本章开篇所述规律进行，且适用于 3 种参数中任意一种。

    ```sql
    mysql> SELECT 1 BETWEEN 2 AND 3;

    mysql> SELECT 'b' BETWEEN 'a' AND 'c';

    mysql> SELECT 2 BETWEEN 2 AND '3'; 
    ```

    ![此处输入图片的描述](img/b7eabeee2db2a99f295f220e98c5442b.jpg)

    `expr NOT BETWEEN min AND max`这相当于`NOT(expr BETWEEN min AND max)`。

*   `COALESCE(value,...)` 返回参数列表当中的第一个非`NULL`值，在没有非`NULL`值的情况下返回值为`NULL`。

    ```sql
    mysql> SELECT COALESCE(NULL,1);

    mysql> SELECT COALESCE(NULL,NULL,NULL); 
    ```

    ![此处输入图片的描述](img/01081d80ce2bd4ee188fc64f9b5dc3dc.jpg)

*   `GREATEST(value1,value2,...)`当有 2 个或 2 个以上参数时，返回值为最大(最大值的)参数。比较参数所依据的规律同[LEAST()](http://dev.mysql.com/doc/refman/5.1/en/comparison-operators.html#function_least)相同。

    ```sql
    mysql> SELECT GREATEST(2,0);

    mysql> SELECT GREATEST('B','A','C'); 
    ```

    ![此处输入图片的描述](img/702d32b7f52b33f90c535c9110c95fda.jpg)

    在所有参数为`NULL`的情况下，`GREATEST()`的返回值为`NULL`。

*   `expr IN (value,...)`若`expr`为`IN`列表中的任意一个值，则其返回值为`1`, 否则返回值为`0`。假如所有的值都是常数，则其计算和分类根据 `expr`的类型进行。这时，使用二分搜索来搜索信息。如果`IN`值列表全部由常数组成，则意味着`IN`的速度非常快。如果`expr`是一个区分大小写的字符串表达式，则字符串比较也按照区分大小写的方式进行。

    ```sql
    mysql> SELECT 2 IN (0,3,5,'wefwf');

    mysql> SELECT 'wefwf' IN (0,3,5,'wefwf'); 
    ```

    尝试输入上述语句并分析结果，思考第二条语句的可行性。

    `IN`列表中所列值的个数仅受限于`max_allowed_packet`值。

    为了同`SQL`标准相一致，在左侧表达式为`NULL`的情况下，或是表中找不到匹配项或是表中一个表达式为`NULL`的情况下，`IN`的返回值均为`NULL`。

    `IN()`语法也可用于书写某些类型的子查询。

*   `expr NOT IN (value,...)`这与`NOT (expr IN (value,...))`相同。

    `ISNULL(expr)`如果`expr`为`NULL`，那么`ISNULL()`的返回值为`1`，否则返回值为`0`。

    ```sql
    mysql> SELECT ISNULL(1+1);

    mysql> SELECT ISNULL(1/0); 
    ```

    通常使用[ISNULL()](http://dev.mysql.com/doc/refman/5.1/en/comparison-operators.html#function_isnull)来判断一个值是否为`NULL`。（使用`=`比较符对比一个值与`NULL`值是错误的）。

*   `INTERVAL(N,N1,N2,N3,...)`假如`N < N1`，则返回值为`0`；假如`N < N2` 等，则返回值为`1`；假如`N`为 NULL，则返回值为`-1`。所有的参数均按照整数处理。为了这个函数的正确运行，必须满足`N1 < N2 < N3 < ……< Nn` 。其原因是使用了二分查找(极快速)。

    ```sql
    mysql> SELECT INTERVAL(23, 1, 15, 17, 30, 44, 200);

    mysql> SELECT INTERVAL(10, 1, 10, 100, 1000);

    mysql> SELECT INTERVAL(22, 23, 30, 44, 200); 
    ```

### 1.4 逻辑操作符

在 SQL 中，所有逻辑操作符的计算所得结果均为`TRUE`、`FALSE`或`NULL`(`UNKNOWN`)。在 MySQL 中，它们的表达形式为`1 (TRUE)`、 `0 (FALSE)`和`NULL`，这在不同 SQL 数据库服务器上都是通用的，然而有一些服务器对`TRUE`的返回值可能是任意一个非零值。

*   `NOT`，`!` ：逻辑`NOT`。

    当操作数为`0`时，所得值为`1` ；当操作数为非零值时，所得值为`0`，而当操作数为`NOT NULL`时，所得的返回值为`NULL`。

    ```sql
    mysql> SELECT NOT 10;

    mysql> SELECT NOT 0;

    mysql> SELECT NOT NULL;

    mysql> SELECT ! (1+1);

    mysql> SELECT ! 1+1; 
    ```

    尝试运行上面的语句，发现最后一个例子产生的结果为 `1`，原因是表达式的计算方式和`(!1)+1`相同。

*   `AND`，`&&`：逻辑`AND`。

    当所有操作数均为非零值、并且不为`NULL`时，计算所得结果为`1`，当一个或多个操作数为`0`时，所得结果为`0`，其余情况返回值为`NULL`。

    ```sql
    mysql> SELECT 1 && 1;

    mysql> SELECT 1 && 0;

    mysql> SELECT 1 && NULL;

    mysql> SELECT 0 && NULL; 
    ```

*   `OR`，`||`：逻辑`OR`。

    当两个操作数均为非`NULL`值时，如有任意一个操作数为非零值，则结果为`1`，否则结果为`0`。当有一个操作数为`NULL`时，如另一个操作数为非零值，则结果为`1`，否则结果为`NULL`。假如两个操作数均为`NULL`，则所得结果为`NULL`。

    ```sql
    mysql> SELECT 1 || 1;

    mysql> SELECT 1 || 0;

    mysql> SELECT 0 || 0;

    mysql> SELECT 0 || NULL;

    mysql> SELECT 1 || NULL; 
    ```

*   `XOR`：逻辑`XOR`。

    当任意一个操作数为`NULL`时，返回值为`NULL`。对于非`NULL`的操作数，假如有奇数个操作数为非零值，则计算所得结果为 1 ，否则为 0 。

    ```sql
    mysql> SELECT 1 XOR 1;

    mysql> SELECT 1 XOR 0;

    mysql> SELECT 1 XOR NULL;

    mysql> SELECT 1 XOR 1 XOR 1; 
    ```

    `a XOR b`的计算等同于`(a AND (NOT b)) OR ((NOT a)和 b)`。

## 二、控制流程函数

*   `CASE value WHEN [compare-value] THEN result [WHEN [compare-value] THEN result ...] [ELSE result] END`

    `CASE WHEN [condition] THEN result [WHEN [condition] THEN result ...] [ELSE result] END`

    在上面第一条语句返回的是`value=compare-value`的结果。而第二条语句的返回结果是第一条语句的真正的结果。如果没有匹配的结果值，则返回结果为`ELSE`语句后的结果，如果没有`ELSE`部分，则返回值为`NULL`。

    ```sql
    mysql> SELECT CASE 1 WHEN 1 THEN 'one'
     -> WHEN 2 THEN 'two' ELSE 'more' END;

    mysql> SELECT CASE WHEN 1>0 THEN 'true' ELSE 'false' END;

    mysql> SELECT CASE BINARY 'B'
     -> WHEN 'a' THEN 1 WHEN 'b' THEN 2 END; 
    ```

    一个`CASE`表达式的默认返回值类型是任何返回值的兼容类型，但具体情况视其所在语境而定。如果用在字符串语境中，则返回结果为字符串类型。如果用在数字语境中，则返回结果为十进制值、实数值或整数值。

*   `IF(expr1,expr2,expr3)`

    如果`expr1`是`TRUE(expr1 <> 0 and expr1 <> NULL)`，则`IF()`的返回值为`expr2`; 否则返回值则为`expr3`。`IF()`的返回值是否为数字值或字符串值，具体情况视其所在语境而定。

    ```sql
    mysql> SELECT IF(1>2,2,3);

    mysql> SELECT IF(1<2,'yes ','no');

    mysql> SELECT IF(STRCMP('test','test1'),'no','yes'); 
    ```

    如果`expr2`或`expr3`中只有一个表达式是`NULL`值，则`IF()`函数的结果类型 为非`NULL`表达式的结果类型。

    `expr1`必须作为一个整数值进行评估，也就是说，假如你正在验证浮点值或字符串值，那么应该使用比较运算进行检验。

    ```sql
    mysql> SELECT IF(0.1,1,0);
    -> 1
    mysql> SELECT IF(0.1<>0,1,0);
    -> 1 
    ```

    观察并对比上述语句的返回结果，发现在上述的第一个例子中，`IF(0.1)`的返回值为`1`，原因是`IF(0.1)`检验为真。在第二个例子中，比较检验了原始浮点值，目的是为了了解是否其为非零值，对比的结果是`0.1`确实不等于`0`，那么第一个表达式的结果就是整数`1`，因此返回结果为`1`。

    `IF()`（这一点在其被储存到临时表时很重要）的默认返回值类型按照以下方式计算： ![此处输入图片的描述](img/ed75e7abc7ca0670504b578087b65170.jpg)

    假如`expr2`和`expr3`都是字符串类型，且其中任何一个字符串区分大小写，则返回结果都是区分大小写。

*   `IFNULL(expr1,expr2)`

    假如`expr1`不为`NULL`，则`IFNULL()`的返回值为 `expr1`；否则其返回值为`expr2`。`IFNULL()`的返回值是否为数字或是字符串，具体情况取决于其所使用的语境。

    ```sql
    mysql> SELECT IFNULL(1,0);

    mysql> SELECT IFNULL(NULL,10);

    mysql> SELECT IFNULL(1/0,10); 
    ```

    `IFNULL(expr1,expr2)`的默认结果值为两个表达式中数据类型更加“通用”的一个，顺序为`STRING`、`REAL`或`INTEGER`。假设有一个表中含有该表达式，或 MySQL 必须在内存储器中储存 IFNULL()的返回值到一个临时表中：

    ```sql
    CREATE TABLE tmp SELECT IFNULL(1,'test') AS test；
    DESCRIBE tmp; 
    ```

    在这个例子中，测试列的类型为字符串类型`CHAR(4)`。

*   `NULLIF(expr1,expr2)`

    如果`expr1 = expr2`成立，那么返回值为`NULL`，否则返回值为`expr1`。这和`CASE WHEN expr1 = expr2 THEN NULL ELSE expr1 END`语句的原理相同。

    ```sql
    mysql> SELECT NULLIF(1,1);

    mysql> SELECT NULLIF(1,2); 
    ```

    **注意：**如果参数不相等，则 MySQL 会评估`expr1`两次。

## 三、字符串函数

如果字符串函数返回结果的长度大于`max_allowed_packet`系统变量的最大值时，字符串值函数的返回值为 NULL。

对于在字符串上操作的函数，第一个位置的编号为 1。

*   `ASCII(str)`

    返回值为字符串`str`的最左字符的数值。假如`str`为空字符串，则返回值为 `0`。假如`str`为`NULL`，则返回值为`NULL`。`ASCII()`用于从`0`到`255`的 数值的字符。

    ```sql
    mysql> SELECT ASCII('2');

    mysql> SELECT ASCII(2);

    mysql> SELECT ASCII('dx'); 
    ```

    更多疑问见[ORD()](http://dev.mysql.com/doc/refman/5.1/en/string-functions.html#function_ord)函数。

    -`BIN(N)`

    返回值为`N`的二进制值的字符串表示，其中`N`为一个`longlong (BIGINT)`型数字。等同于[CONV(N,10,2)](http://dev.mysql.com/doc/refman/5.1/en/mathematical-functions.html#function_conv)。假如`N`为`NULL`，则返回值为`NULL`。

    ```sql
    mysql> SELECT BIN(12); 
    ```

*   `BIT_LENGTH(str)`

    返回值为二进制的字符串 str 长度。

    ```sql
    mysql> SELECT BIT_LENGTH('text'); 
    ```

*   `CHAR(N,... [USING charset])`

    `CHAR()`将每个参数`N`理解为一个整数，其返回值为一个由这些参数转换为字符后组成的字符串。其中`NULL`值被省略。

    ```sql
    mysql> SELECT CHAR(77,121,83,81,'76');

    mysql> SELECT CHAR(77,77.3,'77.3'); 
    ```

    大于`255`的`CHAR()`参数被转换为多个字符。 例如，`CHAR(256)`相当于 `CHAR(1,0)`, 而`CHAR(256*256)`则相当于`CHAR(1,0,0)`：

    ```sql
    mysql> SELECT HEX(CHAR(1,0)), HEX(CHAR(256));
    mysql> SELECT HEX(CHAR(1,0,0)), HEX(CHAR(256*256)); 
    ```

    `CHAR()`的返回值为一个二进制字符串。可选择使用`USING`语句产生一个给定的字符集中的字符串：

    ```sql
    mysql> SELECT CHARSET(CHAR(0x65)), CHARSET(CHAR(0x65 USING utf8));
    mysql> SELECT CHARSET(CHAR(0x65)), CHARSET(CHAR(0x65 USING utf8)); 
    ```

    如果`USING`已经被给定，而结果字符串不符合给出的字符集，则会发出警告。同样，如果严格的 SQL 模式被激活，则`CHAR()`的结果会是`NULL`。

*   `CHAR_LENGTH(str)`

    返回值为字符串`str`的长度，长度单位为字符，一个多字节字符算作一个单字符。对于一个包含五个二字节字符集,`LENGTH()`返回值为`10`,而`CHAR_LENGTH()`的返回值为 5。

*   `CHARACTER_LENGTH(str)`

    `CHARACTER_LENGTH()`等价于`CHAR_LENGTH()`。

*   `CONCAT(str1,str2,...)`

    返回结果为连接参数产生的字符串。如有任何一个参数为`NULL`，则返回值为 `NULL`。 如果所有参数均为非二进制字符串，则结果为非二进制字符串。 如果自变量中含有任一个二进制字符串，则结果为一个二进制字符串。一个数字参数将被转化为与之相等的二进制字符串格式；若要避免这种情况，可使用显式类型`cast`转换, 例如：

    ```sql
    SELECT CONCAT(CAST(int_col AS CHAR), char_col) 
    ```

    ```sql
    mysql> SELECT CONCAT('My', 'S', 'QL');

    mysql> SELECT CONCAT('My', NULL, 'QL');

    mysql> SELECT CONCAT(14.3); 
    ```

*   `CONCAT_WS(separator,str1,str2,...)`

    `CONCAT_WS()`代表`CONCAT With Separator`（使用分隔符连接），是`CONCAT()`的特殊形式。 第一个参数是其它参数的分隔符。分隔符的位置放在要连接的两个字符串之间。分隔符可以是一个字符串，也可以是其它参数。如果分隔符为 `NULL`，则结果为`NULL`。函数会忽略任何分隔符参数后的`NULL`值。

    ```sql
    mysql> SELECT CONCAT_WS(',','First name','Second name','Last Name');

    mysql> SELECT CONCAT_WS(',','First name',NULL,'Last Name'); 
    ```

    `CONCAT_WS()`不会忽略任何空字符串。 (然而会忽略所有的`NULL`）。

*   `ELT(N,str1,str2,str3,...)`

    若`N = 1`，则返回值为`str1`，若`N = 2`，则返回值为`tr2`，以此类推。 若`N`小于`1`或大于参数的数目，则返回值为`NULL`（突然觉得这个函数好神奇）。

    ```sql
    mysql> SELECT ELT(1, 'ej', 'Heja', 'hej', 'foo');

    mysql> SELECT ELT(4, 'ej', 'Heja', 'hej', 'foo'); 
    ```

*   `EXPORT_SET(bits,on,off[,separator[,number_of_bits]])`

    返回值为字符串。`bits`中的比特值按照从右到左的顺序接受检验 (低位比特到高位比特的顺序)。字符串被分隔字符串分开(默认为逗号',')，按照从左到右的顺序被添加到结果中。其中`number_of_bits`会给出被检验的二进制位数 (默认为`64`)。

    ```sql
    mysql> SELECT EXPORT_SET(5,'Y','N',',',4);

    mysql> SELECT EXPORT_SET(6,'1','0',',',10); 
    ```

*   `FIELD(str,str1,str2,str3,...)`

    返回值为`str1, str2,str3,……`列表中的`str`所在位置。在找不到`str`的情况下，返回值为`0`。如果所有`FIELD()`的参数均为字符串，则所有参数均按照字符串进行比较。如果所有的参数均为数字，则按照数字进行比较。否则，参数按照双精度类型进行比较。如果`str`为`NULL`值，则返回值为`0`，原因是`NULL`不能同任何值进行同等比较。

    ```sql
    mysql> SELECT FIELD('ej', 'Hej', 'ej', 'Heja', 'hej', 'foo');

    mysql> SELECT FIELD('fo', 'Hej', 'ej', 'Heja', 'hej', 'foo'); 
    ```

*   `FIND_IN_SET(str,strlist)`

    假如字符串`str`在由`N`子字符串组成的字符串列表`strlist`中，则返回值的范围在`1`到`N`之间。一个字符串列表就是一个由一些被‘,’符号分开的子字符串组成的字符串。如果第一个参数是一个常数字符串，而第二个是[SET](http://dev.mysql.com/doc/refman/5.1/en/set.html)类型的数据，则`FIND_IN_SET()`函数将被使用比特计算优化。如果`str`不在`strlist`或`strlist`为空字符串，则返回值为`0`。如果任意一个参数为`NULL`，则返回值为`NULL`。 该函数在第一个参数就包含逗号(‘,’)时将无法正常运行。

    ```sql
    mysql> SELECT FIND_IN_SET('b','a,b,c,d'); 
    ```

*   `FORMAT(X,D)`

    将数字`X`的格式设置为'#,###,###.##',以四舍五入的方式保留到小数点后 D 位, 返回结果为一个字符串。

*   `HEX(N_or_S)`

    如果`N_OR_S`是一个数字，则返回一个十六进制值`N`的字符串表示，其中，`N`是一个`longlong`（也就是`BIGINT`）类型的数。如果`N_OR_S`是一个字符串，则返回值为一个`N_OR_S`的十六进制字符串表示，其中字符串`N_OR_S` 里的每个字符都被转化为两个十六进制数字。

    ```sql
    mysql> SELECT HEX(255);

    mysql> SELECT 0x616263;

    mysql> SELECT HEX('abc'); 
    ```

*   `INSERT(str,pos,len,newstr)`

    返回字符串`str`中起始于`pos`位置被字符串`newstr`替换长度为`len` 后的字符串。如果`pos`不在字符串长度范围内，则返回值为原始字符串。 假如`len`的长度大于剩下的字符串的长度，则从位置`pos`开始替换。若任何一个参数为`null`，则返回值为`NULL`。

    ```sql
    mysql> SELECT INSERT('Quadratic', 3, 4, 'What');

    mysql> SELECT INSERT('Quadratic', -1, 4, 'What');

    mysql> SELECT INSERT('Quadratic', 3, 100, 'What'); 
    ```

*   `INSTR(str,substr)`

    返回字符串`str`中子字符串`substr`第一次出现的位置。

    ```sql
    mysql> SELECT INSTR('foobarbar', 'bar');

    mysql> SELECT INSTR('xbar', 'foobar'); 
    ```

*   `LEFT(str,len)`

    返回从字符串`str`左边数前`len`个字符。

    ```sql
    mysql> SELECT LEFT('foobarbar', 5); 
    ```

*   `LENGTH(str)`

    返回值为字符串`str`的长度，单位为字节。对于一个包含`5`个`2`字节字符的字符串，`LENGTH()`的返回值为`10`,而`CHAR_LENGTH()`的返回值则为`5`。

    ```sql
    mysql> SELECT LENGTH('text'); 
    ```

*   `LOAD_FILE(file_name)`

    读取文件并将这一文件按照字符串的格式返回。文件的位置必须在服务器上,你必须为文件制定路径全名，而且你还必须拥有[FILE](http://dev.mysql.com/doc/refman/5.1/en/privileges-provided.html#priv_file)权限。文件必须可读，文件容量必须小于`max_allowed_packet`字节。若文件不存在，或因不满足上述条件而不能被读取，函数返回值为 NULL。

    ```sql
    mysql> UPDATE tbl_name
     -> SET blob_column=LOAD_FILE('/tmp/picture')
     -> WHERE id=1; 
    ```

*   `LOCATE(substr,str)`

    `LOCATE(substr,str,pos)`在没有参数`pos`时，返回为字符串`str`中子字符串`substr`的第一次出现的位置。反之，返回字符串`str`中以起始位置为`pos`开始的子字符串`substr`的第一次出现的位置。如若`substr`不在`str`中，则返回值为`0`。

    ```sql
    mysql> SELECT LOCATE('bar', 'foobarbar');

    mysql> SELECT LOCATE('xbar', 'foobar');

    mysql> SELECT LOCATE('bar', 'foobarbar',5); 
    ```

*   `LOWER(str)`

    返回字符串`str`根据最新的字符集(默认为`cp1252 Latin1`)映射表转换为小写字母的字符 。

    ```sql
    mysql> SELECT LOWER('QUADRATICALLY'); 
    ```

*   `LPAD(str,len,padstr)`

    返回字符串`str`的左边由字符串`padstr`填补到`len`字符长度后的字符串。假如`str`的长度大于`len`, 则返回值从右边开始被缩短至`len`字符。

    ```sql
    mysql> SELECT LPAD('hi',4,'??');

    mysql> SELECT LPAD('hi',1,'??'); 
    ```

*   `LTRIM(str)`

    返回删除空格后的字符串`str`。

    ```sql
    mysql> SELECT LTRIM('  barbar'); 
    ```

*   `MAKE_SET(bits,str1,str2,...)`

    返回一个（一个包含被‘,’号分开的字符串）由在`bits`集合中具有相应的比特的字符串组成的设定值。`str1`对应比特`0`,`str2`对应比特`1`,以此类推。`str1, str2,...`中的`NULL`值不会被添加到返回结果中。

    ```sql
    mysql> SELECT MAKE_SET(1,'a','b','c');

    mysql> SELECT MAKE_SET(1 | 4,'hello','nice','world');

    mysql> SELECT MAKE_SET(1 | 4,'hello','nice',NULL,'world');

    mysql> SELECT MAKE_SET(0,'a','b','c'); 
    ```

*   `OCT(N)`

    返回`N`的八进制值的字符串表示，其中`N`是一个`longlong(BIGINT)`数。若`N`为`NULL`，则返回值为`NULL`。

    ```sql
    mysql> SELECT OCT(12); 
    ```

    `OCTET_LENGTH(str) OCTET_LENGTH()`等价于`LENGTH()`。

*   `ORD(str)`

    若字符串`str`的最左边的字符是一个多字节字符，则返回该字符的代码，代码的计算通过使用以下公式计算其组成字节的数值而得出：

    ```sql
    (1st byte code)+(2nd byte code × 256)+(3rd byte code × 256 × 256) ... 
    ```

    如果最左边的字符不是一个多字节字符，那么`ORD()`和函数`ASCII()`返回相同的值。

    ```sql
    mysql> SELECT ORD('2'); 
    ```

*   `QUOTE(str)`通过引用字符串`str`，产生一个在 SQL 语句中可用作完全转义数据值的结果。返回的字符串由单引号标注，每例都带有单引号(`'`)、反斜线符号(`\`)、`ASCII NUL`以及前面有反斜线符号的`Control-Z`。如果自变量的值为`NULL`,则返回不带单引号的单词`NULL`。

    ```sql
    mysql> SELECT QUOTE('Don\'t!');

    mysql> SELECT QUOTE(NULL); 
    ```

*   `REPEAT(str,count)`

    返回一个由重复的字符串`str`组成的字符串，字符串`str`重复的数目为`count`。若`count <= 0`,则返回一个空字符串。若`str`或`count`为`NULL`，则返回`NULL`。

    ```sql
    mysql> SELECT REPEAT('MySQL', 3); 
    ```

*   `REPLACE(str,from_str,to_str)`

    返回所有被字符串`to_str`替代成字符串`from_str`后的`str`。

    ```sql
    mysql> SELECT REPLACE('www.shiyanlou.com', 'w', 'Ww'); 
    ```

*   `REVERSE(str)`

    返回和字符正常顺序相反的`str`。

    ```sql
    mysql> SELECT REVERSE('abc'); 
    ```

*   `RIGHT(str,len)`

    返回`str`中从最右开始数 len 个字符。

    ```sql
    mysql> SELECT RIGHT('foobarbar', 4); 
    ```

*   `SOUNDEX(str)`

    `str`返回一个[soundex](http://baike.baidu.com/item/SOUNDEX)字符串。 两个具有几乎同样发音的字符串应该具有同样的`soundex`字符串。一个标准的`soundex`字符串的长度为`4`个字符，然而`SOUNDEX()`函数会返回一个任意长度的字符串。可使用`SUBSTRING()`来得到一个标准`soundex` 字符串结果。在`str`中，会忽略所有未按照字母顺序排列的字符。所有不在`A-Z`范围之内的国际字母符号被视为元音字母。

    ```sql
    mysql> SELECT SOUNDEX('Hello');

    mysql> SELECT SOUNDEX('Quadratically'); 
    ```

    **注意：**这个函数执行原始的`Soundex`算法，而非更加流行的加强版本算法。其区别在于原始版本首先会删去元音，其次是去除重复字符，而加强版则首先删去重复字符，而后删去元音字符。

*   `SUBSTRING(str,pos) , SUBSTRING(str FROM pos) SUBSTRING(str,pos,len) , SUBSTRING(str FROM pos FOR len)`

    不带有`len`参数的情况，返回一个起始于位置`pos`的子字符串；带有 len 参数的情况，返回一个起始于位置 `pos`长度同`len`相同的子字符串；使用 `FROM`的格式为标准 SQL 语法；也可能对`pos`使用一个负值，假若这样，则子字符串的位置起始于字符串结尾的第`pos`个字符，而不是字符串的开头位置。请输入以下语句检验该函数的结果：

    ```sql
    mysql> SELECT SUBSTRING('Quadratically',5);

    mysql> SELECT SUBSTRING('foobarbar' FROM 4);

    mysql> SELECT SUBSTRING('Quadratically',5,6);

    mysql> SELECT SUBSTRING('Sakila', -3);

    mysql> SELECT SUBSTRING('Sakila', -5, 3);

    mysql> SELECT SUBSTRING('Sakila' FROM -4 FOR 2); 
    ```

**注意：**如果`len`使用的是一个小于`1`的值，则结果始终为空字符串。

*   `SUBSTRING_INDEX(str,delim,count)`

    若`count`为正值，则返回`str`中第`count`个定界符`delim`(从左边开始)左边的一切内容。若`count`为负值，则返回定界符（从右边开始）右边的一切内容。

    ```sql
    mysql> SELECT SUBSTRING_INDEX('www.shiyanlou.com', '.', 2);

    mysql> SELECT SUBSTRING_INDEX('www.shiyanlou.com', '.', -2); 
    ```

*   `TRIM([{BOTH | LEADING | TRAILING} [remstr] FROM] str) TRIM(remstr FROM] str)`

    返回字符串`str`，其中所有`remstr`前缀或后缀都已被删除。若分类符`BOTH`、`LEADING`或`TRAILING`中没有一个被指定，则假设为`BOTH`。`remstr`为可选项，在未指定情况下，删除空格。

    ```sql
    mysql> SELECT TRIM('  bar   ');

    mysql> SELECT TRIM(LEADING 'x' FROM 'xxxbarxxx');

    mysql> SELECT TRIM(BOTH 'x' FROM 'xxxbarxxx');

    mysql> SELECT TRIM(TRAILING 'xyz' FROM 'barxxyz'); 
    ```

## 四、数值函数

### 4.1 算数操作符

*   `+` 加号: `mysql> SELECT 3+5;`

*   `-` 减号: `mysql> SELECT 3-5;`

*   `-` 负号：

    ```sql
    mysql> SELECT - 2; 
    ```

    **注意：**若该操作符同一个`BIGINT`同时使用，则返回值也是一个`BIGINT`。

*   `*` 乘号:

    ```sql
    mysql> SELECT 3*5;

    mysql> SELECT 18014398509481984*18014398509481984.0;

    mysql> SELECT 18014398509481984*18014398509481984; 
    ```

    观察最后一个表达式的结果。原因是整数相乘的结果超过了`BIGINT`计算的 64 位范围。

*   `/` 除号:

    ```sql
    mysql> SELECT 3/5; 
    ```

    被零除的结果为`NULL`：

    ```sql
    mysql> SELECT 102/(1-1); 
    ```

*   `DIV` 整数除法。

    类似于`FLOOR()`。

    ```sql
    mysql> SELECT 5 DIV 2; 
    ```

### 4.2 数学函数

若发生错误，所有数学函数会返回`NULL`。

*   `ABS(X)`

    返回`X`的绝对值。

    ```sql
    mysql> SELECT ABS(2);

    mysql> SELECT ABS(-32); 
    ```

    该函数支持使用`BIGINT`值。

*   `ACOS(X)`

    返回`X`的反余弦, 即余弦是`X`的值。若`X`不在`-1`到`1`的范围之内，则返回`NULL`。

    ```sql
    mysql> SELECT ACOS(1);

    mysql> SELECT ACOS(1.0001);

    mysql> SELECT ACOS(0); 
    ```

*   `ATAN(Y,X) , ATAN2(Y,X)`

    返回两个变量`X`及`Y`的反正切。

    ```sql
    mysql> SELECT ATAN(-2,2);

    mysql> SELECT ATAN2(PI(),0); 
    ```

*   `CEILING(X),CEIL(X)`

    返回不小于`X`的最小整数值。

    ```sql
    mysql> SELECT CEILING(1.23);

    mysql> SELECT CEIL(-1.23); 
    ```

*   `CRC32(expr)`

    计算循环冗余码校验值并返回一个`32`位无符号值。若参数为`NULL`，则结果为`NULL`。该参数应为一个字符串，而且在不是字符串的情况下会被作为字符串处理（如果必要的话）。

    ```sql
    mysql> SELECT CRC32('MySQL');

    mysql> SELECT CRC32('mysql'); 
    ```

*   `DEGREES(X)`

    返回参数`X`由弧度被转化为度以后的值。

    ```sql
    mysql> SELECT DEGREES(PI());

    mysql> SELECT DEGREES(PI() / 2); 
    ```

*   `EXP(X)` 返回`e`（自然对数的底）的`X`乘方后的值。

    ```sql
    mysql> SELECT EXP(2);

    mysql> SELECT EXP(-2);

    mysql> SELECT EXP(0); 
    ```

*   `FLOOR(X)`

    返回不大于`X`的最大整数值 。

    ```sql
    mysql> SELECT FLOOR(1.23);

    mysql> SELECT FLOOR(-1.23); 
    ```

*   `FORMAT(X,D)`

    将数字`X`的格式写成'#,###,###.##'格式，且保留小数点后`D`位，而第`D`位的保留方式为四舍五入，然后将结果以字符串的形式返回。

*   `LN(X)`

    返回`X`的自然对数，即`X`相对于基数`e`的对数。

    ```sql
    mysql> SELECT LN(2);

    mysql> SELECT LN(-2); 
    ```

    该函数同`LOG(X)`具有相同意义。

*   `LOG(X),LOG(B,X)`

    若只用一个参数调用，该函数就会返回`X`的自然对数。

    ```sql
    mysql> SELECT LOG(2);

    mysql> SELECT LOG(-2); 
    ```

    若用两个参数进行调用，该函数会返回`X`对于任意基数`B`的对数。

    ```sql
    mysql> SELECT LOG(2,65536);

    mysql> SELECT LOG(10,100); 
    ```

    `LOG(B,X)`就相当于`LOG(X) / LOG(B)`。

*   `LOG2(X)`

    返回`X`的基数为 2 的对数。

    ```sql
    mysql> SELECT LOG2(65536);

    mysql> SELECT LOG2(-100); 
    ```

    要想查出存储一个数字需要多少个比特，`LOG2()`函数会非常有效。这个函数相当于表达式`LOG(X) / LOG(2)`。

*   `MOD(N,M) , N % M N MOD M`

    模操作。返回`N`被 `M`除后的余数。

    ```sql
    mysql> SELECT MOD(234, 10);

    mysql> SELECT 253 % 7;

    mysql> SELECT MOD(29,9);

    mysql> SELECT 29 MOD 9; 
    ```

    `MOD()`对于带有小数部分的数值也起作用，它返回除法运算后的精确余数：

    ```sql
    mysql> SELECT MOD(34.5,3); 
    ```

*   `PI()`

    返回`ϖ(pi)`的值。默认的显示小数位数是`7`位，但是 MySQL 内部可以使用完全双精度值。

    ```sql
    mysql> SELECT PI();

    mysql> SELECT PI()+0.000000000000000000; 
    ```

*   `POW(X,Y) , POWER(X,Y)`

    返回`X`的`Y`乘方的结果值。

    ```sql
    mysql> SELECT POW(2,2);

    mysql> SELECT POW(2,-2); 
    ```

*   `RADIANS(X)`

    返回由度转化为弧度的参数`X`, (注意`ϖ`弧度等于`180`度）。

    ```sql
    mysql> SELECT RADIANS(90); 
    ```

*   `RAND()`

    `RAND(N)`返回一个范围在`0`到`1`之间（即范围为 0 ≤ v ≤1.0）的随机浮点值`v`。若已指定一个整数参数`N`，则该参数将被用作种子值，用来产生重复序列。

    ```sql
    mysql> SELECT RAND();

    mysql> SELECT RAND(20);

    mysql> SELECT RAND(20);

    mysql> SELECT RAND(); 
    ```

    若要在`i ≤ R ≤ j`这个范围得到一个随机整数`R`，需要用到表达式`FLOOR(i + RAND() * (j – i + 1))`。例如， 若要在`7`到 `12` 的范围（包括`7`和`12`）内得到一个随机整数, 可使用以下语句：

    ```sql
    SELECT FLOOR(7 + (RAND() * 6)); 
    ```

    在`ORDER BY`语句中，不能使用一个带有`RAND()`值的列，原因是 `ORDER BY`会计算列中的重复数值。但是也可按照如下的随机顺序检索数据行：

    ```sql
    mysql> SELECT * FROM tbl_name ORDER BY RAND(); 
    ```

    `ORDER BY RAND()`同`LIMIT`的结合可以有效的从一组列中选择随机样本：

    ```sql
    mysql> SELECT * FROM table1, table2 WHERE a=b AND c<d
     -> ORDER BY RAND() LIMIT 1000; 
    ```

    **注意：**在`WHERE`语句中，`WHERE`每执行一次，`RAND()`就会被再执行一次。

    `RAND()`的作用不是作为一个精确的随机发生器，而是一种用来发生在同样的 MySQL 版本的平台之间的可移动`ad hoc`随机数的快速方式。

*   `ROUND(X),ROUND(X,D)`

    返回与参数`X`最接近的整数。在有两个参数的情况下，返回保留到小数点后`D`位的`X`，而第`D`位的保留方式为四舍五入。若要返回保留`X`值小数点左边的`D`位，可将`D`设为负值。

    ```sql
    mysql> SELECT ROUND(-1.23);

    mysql> SELECT ROUND(-1.58);

    mysql> SELECT ROUND(1.58);

    mysql> SELECT ROUND(1.298, 1);

    mysql> SELECT ROUND(1.298, 0);

    mysql> SELECT ROUND(23.298, -1); 
    ```

    返回值的类型同第一个参数类型相同(假设它是一个整数、双精度数或小数)。这意味着对于一个整数参数,结果也是一个整数(无小数部分)。

    `ROUND()`在以下情况下依赖于第一个参数的类型：

    *   对于准确值数字，`ROUND()`使用“四舍五入” 或“舍入成最接近的数” 的规则：对于一个分数部分为`.5`或大于 `.5`的值，正数则上舍入到邻近的整数值，负数则下舍入临近的整数值。(换言之,其舍入的方向是数轴上远离零的方向）。对于一个分数部分小于`.5`的值，正数则下舍入下一个整数值，负数则下舍入邻近的整数值，而正数则上舍入邻近的整数值。
    *   对于近似值数字，其结果根据 C 库而定。在很多系统中，这意味着`ROUND()`的使用遵循“舍入成最接近的偶数”的规则： 一个带有任何小数部分的值会被舍入成最接近的偶数。 以下举例说明舍入法对于精确值和近似值的不同之处：

    ```sql
    mysql> SELECT ROUND(2.5), ROUND(25E-1); 
    ```

*   `SIGN(X)`

    返回参数`X`的符号，该符号取决于 X 的值是否为负、零或正。

    ```sql
    mysql> SELECT SIGN(-32);

    mysql> SELECT SIGN(0);

    mysql> SELECT SIGN(234); 
    ```

*   `SQRT(X)`

    返回非负数`X`的二次方根。

    ```sql
    mysql> SELECT SQRT(4);

    mysql> SELECT SQRT(-16); 
    ```

## 五、日期和时间函数

*   `ADDDATE(date,INTERVAL expr type),ADDDATE(expr,days)`

    当被第二个参数`INTERVAL`被设置后，ADDDATE()就是等价于`DATE_ADD()`。

    ```sql
    mysql> SELECT DATE_ADD('1998-01-02', INTERVAL 31 DAY);

    mysql> SELECT ADDDATE('1998-01-02', INTERVAL 31 DAY); 
    ```

    若`days`参数只是整数值，则 MySQL 5.1 将其作为天数值添加至`expr`。

    ```sql
    mysql> SELECT ADDDATE('1998-01-02', 31); 
    ```

*   `ADDTIME(expr,expr2)` `ADDTIME()`将`expr2`添加至`expr`然后再返回结果。`expr`是一个时间或日期表达式，而`expr2`是一个时间表达式。

    ```sql
    mysql> SELECT ADDTIME('1997-12-31 23:59:59.999999',
     -> '1 1:1:1.000002');

    mysql> SELECT ADDTIME('01:00:00.999999', '02:00:00.999998'); 
    ```

*   `CONVERT_TZ(dt,from_tz,to_tz)`

    `CONVERT_TZ()`将时间日期值`dt`从`from_tz`给出的时区转到`to_tz`给出的时区，然后返回结果值。在从`from_tz` 到`UTC`的转化过程中，如果该值超出`TIMESTAMP`类型的被支持范围，那么转化不会发生。

    ```sql
    mysql> SELECT CONVERT_TZ('2004-01-01 12:00:00','GMT','MET');

    mysql> SELECT CONVERT_TZ('2004-01-01 12:00:00','+00:00','+10:00'); 
    ```

    **注释：**若要使用诸如 `MET`或`Europe/Moscow`之类指定时间区，首先要设置正确的时区表。

*   CURDATE()`

    将当前日期按照`'YYYY-MM-DD'`或`YYYYMMDD`格式返回，具体格式根据函数用在字符串或是数字语境中而定。

    ```sql
    mysql> SELECT CURDATE();

    mysql> SELECT CURDATE() + 0; 
    ```

*   `CURRENT_DATE,CURRENT_DATE()`

    `CURRENT_DATE`等价于`CURRENT_DATE()`，`CURTIME()`将当前时间以`'HH:MM:SS'`或`HHMMSS`的格式返回， 具体格式根据函数用在字符串或是数字语境中而定。

    ```sql
    mysql> SELECT CURTIME();

    mysql> SELECT CURTIME() + 0; 
    ```

*   `DATEDIFF(expr,expr2)`

    `DATEDIFF()`返回起始时间`expr`和结束时间`expr2`之间的天数。`Expr`和`expr2`为日期或`date-and-time` 表达式。计算中只用到这些值的日期部分。

    ```sql
    mysql> SELECT DATEDIFF('1997-12-31 23:59:59','1997-12-30');

    mysql> SELECT DATEDIFF('1997-11-30 23:59:59','1997-12-31'); 
    ```

*   `DATE_ADD(date,INTERVAL expr type),DATE_SUB(date,INTERVAL expr type)`

    这些函数执行日期运算。 `date`是一个`DATETIME`或`DATE`值，用来指定起始时间。`expr`是一个表达式，用来指定从起始日期添加或减去的时间间隔值。 `Expr`是一个字符串;对于负值的时间间隔，它可以以一个`'-'`开头。 `type`为关键词，它指示了表达式被解释的方式。

    关键词`INTERVA`及`type`分类符均不区分大小写。

    下表显示了`type`和`expr`参数的关系：

    ![此处输入图片的描述](img/e08afe49fb7d496db3ed3c60359cad27.jpg)

    MySQL 允许任何`expr`格式中的标点分隔符，表中所显示的是建议的分隔符。若`date`参数是一个`DATE`类型的 值，那么计算只会包括`YEAR`、`MONTH`和`DAY`部分（即没有时间部分），其结果也是一个`DATE`类型的 值。否则，结果将是一个`DATETIME`类型值。

    若位于另一端的表达式是一个日期或日期时间值 ， 则`INTERVAL expr type`只允许出现在`+`操作符的两端。对于 `–`操作符， `INTERVAL expr type`只允许在其右端，原因是从一个时间间隔中提取一个日期或日期时间值是毫无意义的(见下面的例子）。

    ```sql
    mysql> SELECT '1997-12-31 23:59:59' + INTERVAL 1 SECOND;

    mysql> SELECT INTERVAL 1 DAY + '1997-12-31';

    mysql> SELECT '1998-01-01' - INTERVAL 1 SECOND;

    mysql> SELECT DATE_ADD('1997-12-31 23:59:59',
     -> INTERVAL 1 SECOND);

    mysql> SELECT DATE_ADD('1997-12-31 23:59:59',
     -> INTERVAL 1 DAY);

    mysql> SELECT DATE_ADD('1997-12-31 23:59:59',
     -> INTERVAL '1:1' MINUTE_SECOND);

    mysql> SELECT DATE_SUB('1998-01-01 00:00:00',
     -> INTERVAL '1 1:1:1' DAY_SECOND);

    mysql> SELECT DATE_ADD('1998-01-01 00:00:00',
     -> INTERVAL '-1 10' DAY_HOUR);

    mysql> SELECT DATE_SUB('1998-01-02', INTERVAL 31 DAY);

    mysql> SELECT DATE_ADD('1992-12-31 23:59:59.000002',
     -> INTERVAL '1.999999' SECOND_MICROSECOND); 
    ```

    若你指定了一个过于短的时间间隔值（不包括`type`关键词所预期的所有时间间隔部分）， MySQL 会假定你已经省去了时间间隔值的最左部分。 例如，你指定了一种类型的`DAY_SECOND`，`expr`的值应当具有天、 小时、分钟和秒部分。若你指定了一个类似`'1:10'`的值, MySQL 会假定天和小时部分不存在，那么这个值代表分和秒。

    假如你对一个日期值添加或减去一些含有时间部分的内容，则结果自动转化为一个日期时间值：

    ```sql
    mysql> SELECT DATE_ADD('1999-01-01', INTERVAL 1 DAY);

    mysql> SELECT DATE_ADD('1999-01-01', INTERVAL 1 HOUR); 
    ```

    假如你使用了格式严重错误的日期，则结果为`NULL`。假如你添加了`MONTH`、`YEAR_MONTH`或`YEAR`，而结果日期中有一天的日期大于添加的月份的日期最大限度，则这个日期自动被调整为添加该月份的最大日期：

    ```sql
    mysql> SELECT DATE_ADD('1998-01-30', INTERVAL 1 MONTH); 
    ```

*   `DATE_FORMAT(date,format)`

    根据 format 字符串安排`date`值的格式。

    **注意：** 字符`%`要求在格式指定符之前。

    月份和日期说明符的范围从零开始，原因是 MySQL 允许存储诸如 '2004-00-00'这样的的不完全日期.

    ```sql
    mysql> SELECT DATE_FORMAT('1997-10-04 22:23:00', '%W %M %Y');

    mysql> SELECT DATE_FORMAT('1997-10-04 22:23:00', '%H:%i:%s');

    mysql> SELECT DATE_FORMAT('1997-10-04 22:23:00',
     ->'%D %y %a %d %m %b %j');

    mysql> SELECT DATE_FORMAT('1997-10-04 22:23:00',
     -> '%H %k %I %r %T %S %w');

    mysql> SELECT DATE_FORMAT('1999-01-01', '%X %V'); 
    ```

*   `DAYNAME(date)`

    返回`date`对应的工作日名称。

    ```sql
    mysql> SELECT DAYNAME('1998-02-05'); 
    ```

*   `EXTRACT(type FROM date)`

    `EXTRACT()`函数所使用的时间间隔类型说明符同`DATE_ADD()`或`ATE_SUB()`的相同，但它从日期中提取其部分，而不是执行日期运算。

    ```sql
    mysql> SELECT EXTRACT(YEAR FROM '1999-07-02');

    mysql> SELECT EXTRACT(YEAR_MONTH FROM '1999-07-02 01:02:03');

    mysql> SELECT EXTRACT(MICROSECOND
     -> FROM '2003-01-02 10:30:00.00123'); 
    ```

*   `FROM_DAYS(N)`

    给定一个天数`N`，返回一个`DATE`类型的值。

    ```sql
    mysql> SELECT FROM_DAYS(729669); 
    ```

    **注意：**使用`FROM_DAYS()`处理古老日期时，务必谨慎。它并不用于处理阳历出现前的日期(`1582`)。详情请参考请[MySQL 使用什么日历？](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/functions.html#mysql-calendar)。

*   `FROM_UNIXTIME(unix_timestamp) , FROM_UNIXTIME(unix_timestamp,format)`

    返回`'YYYY-MM-DD HH:MM:SS'`或`YYYYMMDDHHMMSS`格式值的`unix_timestamp`参数表示，具体格式取决于该函数是否用在字符串中或是数字语境中。 若`format` 已经给出，则结果的格式是根据`format` 字符串而定。 `format` 可以包含同`DATE_FORMAT()`函数输入项列表中相同的说明符。

    ```sql
    mysql> SELECT FROM_UNIXTIME(875996580);

    mysql> SELECT FROM_UNIXTIME(875996580) + 0;

    mysql> SELECT FROM_UNIXTIME(UNIX_TIMESTAMP(),
     -> '%Y %D %M %h:%i:%s %x'); 
    ```

*   `GET_FORMAT(DATE|TIME|DATETIME, 'EUR'|'USA'|'JIS'|'ISO'|'INTERNAL')`

    返回一个格式字符串。该函数在同`DATE_FORMAT()`及`STR_TO_DATE()`函数结合时很有用。 第一个参数的 3 个可能值和第二个参数的 5 个可能值产生 15 个可能格式字符串 (对于使用的说明符，请参见`DATE_FORMAT()`函数说明表 )。

    ![此处输入图片的描述](img/be125d76cb7cd22a29e52f4645513760.jpg)

    其中，`ISO` 格式为`ISO 9075`， 而非`ISO 8601`。

    `DATE_FORMAT()`函数的第一个参数也可以使用`TIMESTAMP`, 这时`GET_FORMAT()`的返回值和`DATETIME`相同。

    ```sql
    mysql> SELECT DATE_FORMAT('2003-10-03',GET_FORMAT(DATE,'EUR'));

    mysql> SELECT STR_TO_DATE('10.31.2003',GET_FORMAT(DATE,'USA')); 
    ```

*   `HOUR(time)`

    返回`time`对应的小时数。对于日时值的返回值范围是从`0` 到`23`。

    ```sql
    mysql> SELECT HOUR('10:05:03'); 
    ```

    然而，`TIME` 值的范围实际上非常大, 所以`HOUR`可以返回大于`23`的值。如：

    ```sql
    mysql> SELECT HOUR('272:59:59'); 
    ```

*   `LAST_DAY(date)`

    获取一个日期或日期时间值，返回该月最后一天对应的值。若参数无效，则返回`NULL`。

    ```sql
    mysql> SELECT LAST_DAY('2003-02-05');

    mysql> SELECT LAST_DAY('2004-02-05');

    mysql> SELECT LAST_DAY('2004-01-01 01:01:01');

    mysql> SELECT LAST_DAY('2003-03-32'); 
    ```

*   `MAKEDATE(year,dayofyear)`

    给出年份值和一年中的天数，最后返回一个日期。`dayofyear`必须大于`0`，否则结果为`NULL`。

    ```sql
    mysql> SELECT MAKEDATE(2001,31), MAKEDATE(2001,32);

    mysql> SELECT MAKEDATE(2001,365), MAKEDATE(2004,365);

    mysql> SELECT MAKEDATE(2001,0); 
    ```

*   `MAKETIME(hour,minute,second)`

    返回由`hour`、 `minute`和`second`参数计算得出的时间值。

    ```sql
    mysql> SELECT MAKETIME(12,15,30); 
    ```

*   `NOW()`

    返回当前日期和时间值，其格式为`'YYYY-MM-DD HH:MM:SS'`或`YYYYMMDDHHMMSS` ， 具体格式取决于该函数所用处于的字符串或数字类型语境中。

    ```sql
    mysql> SELECT NOW();

    mysql> SELECT NOW() + 0; 
    ```

    在一个存储程序或触发器内, `NOW()`返回一个常数时间，该常数指示了该程序或触发语句开始执行的时间。这同`SYSDATE()`的运行有所不同。

*   `PERIOD_ADD(P,N)`

    添加 `N`个月至周期`P`(格式为`YYMM` 或`YYYYMM`)，返回值的格式为 `YYYYMM`。注意周期参数`P`不是日期值。

    ```sql
    mysql> SELECT PERIOD_ADD(9801,2); 
    ```

*   PERIOD_DIFF(P1,P2)

    返回周期`P1`和`P2`之间的月份数。`P1`和`P2`的格式应该为`YYMM`或`YYYYMM`。注意周期参数`P1`和`P2` 不是日期值。

    ```sql
    mysql> SELECT PERIOD_DIFF(9802,199703); 
    ```

*   `QUARTER(date)`

    返回`date` 对应的一年中的季度值，范围是从 1 到 4。

    ```sql
    mysql> SELECT QUARTER('98-04-01'); 
    ```

*   SEC*TO*TIME(seconds)

    返回被转化为小时、 分钟和秒数的 seconds 参数值, 其格式为 `'HH:MM:SS'`或`HHMMSS`，具体格式根据该函数是否用在字符串或数字语境中而定。

    ```sql
    mysql> SELECT SEC_TO_TIME(2378);

    mysql> SELECT SEC_TO_TIME(2378) + 0; 
    ```

*   `UNIX_TIMESTAMP(), UNIX_TIMESTAMP(date)`

    若无参数调用，则返回一个`Unix timestamp`('1970-01-01 00:00:00' `GMT`时间之后的秒数) 作为无符号整数。若用`date`参数来调用`UNIX_TIMESTAMP()`，它会将参数值以`'1970-01-01 00:00:00'` GMT 后的秒数的形式返回。`date` 可以是一个`DATE`类型的 字符串、一个 `DATETIME`类型的字符串、一个 `TIMESTAMP`或一个当地时间的`YYMMDD`或`YYYMMDD`格式的数字。

    ```sql
    mysql> SELECT UNIX_TIMESTAMP();

    mysql> SELECT UNIX_TIMESTAMP('1997-10-04 22:23:00'); 
    ```

    当 `UNIX_TIMESTAMP`被用在 `TIMESTAMP`列时, 函数直接返回内部时戳值， 而不进行任何隐含的 `“string-to-Unix-timestamp”`转化。假如你向`UNIX_TIMESTAMP()`传递一个溢出日期，它会返回 `0`,但请注意只有一般的时间范围生效（年份从`1970` 到`2037`， 月份从`01`到`12`,日期从`01`到`31`）。

*   `UTC_DATE, UTC_DATE()`

    返回当前`UTC`日期值，其格式为`'YYYY-MM-DD'`或 `YYYYMMDD`，具体格式取决于函数是否用在字符串或数字语境中。

    ```sql
    mysql> SELECT UTC_DATE(), UTC_DATE() + 0; 
    ```

*   `UTC_TIME, UTC_TIME()`

    返回当前 `UTC` 值，其格式为 `'HH:MM:SS'` 或`HHMMSS`，具体格式根据该函数是否用在字符串或数字语境而定。

    ```sql
    mysql> SELECT UTC_TIME(), UTC_TIME() + 0; 
    ```

*   `WEEK(date[,mode])`

    该函数返回`date` 对应的星期数。`WEEK()` 的双参数形式允许你指定该星期是否起始于周日或周一， 以及返回值的范围是否为从`0` 到`53`或从`1`到`53`。若 `mode`参数被省略，则使用`default_week_format`系统值。请参见[服务器系统变量](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/database-administration.html#server-system-variables)。

    下表说明了`mode`参数的工作过程：

    ![此处输入图片的描述](img/e3dd6feacb28e5624936dd97b9f47c4f.jpg)

    ```sql
    mysql> SELECT WEEK('1998-02-20');

    mysql> SELECT WEEK('1998-02-20',0);

    mysql> SELECT WEEK('1998-02-20',1);

    mysql> SELECT WEEK('1998-12-31',1); 
    ```

    **注意：**假如有一个日期位于前一年的最后一周， 若你不使用`2`、`3`、`6`或`7`作为`mode` 参数选择，则 MySQL 返回 `0`：

    ```sql
    mysql> SELECT YEAR('2000-01-01'), WEEK('2000-01-01',0); 
    ```

    有人或许会提出意见，认为 MySQL 对于`WEEK()`函数应该返回 `52` ，原因是给定的日期实际上发生在`1999`年的第`52`周。我们决定返回`0`作为代替的原因是我们希望该函数能返回“给定年份的星期数”。这使得`WEEK()`函数在同其它从日期中抽取日期部分的函数结合时的使用更加可靠。

    假如你更希望所计算的关于年份的结果包括给定日期所在周的第一天，则应使用`0`、`2`、`5`或`7` 作为`mode`参数选择。

    ```sql
    mysql> SELECT WEEK('2000-01-01',2); 
    ```

    作为选择，也可使用 `YEARWEEK()`函数:

    ```sql
    mysql> SELECT YEARWEEK('2000-01-01');

    mysql> SELECT MID(YEARWEEK('2000-01-01'),5,2); 
    ```

## 六、全文搜索功能

`MATCH (col1,col2,...) AGAINST (expr [IN BOOLEAN MODE | WITH QUERY EXPANSION])`

MySQL 支持全文索引和搜索功能。

*   MySQL 中的全文索引是针对`FULLTEXT`类型的索引。
*   `FULLTEXT`索引仅可用于 `MyISAM`表（在 MySQL5.6 以及以上的版本也可用于`InnoDB`表）；可以从`CHAR`、 `VARCHAR`或`TEXT`列中作为`CREATE TABLE`语句的一部分被创建，或是随后使用`ALTER TABLE` 或 `CREATE INDEX`添加。对于较大的数据集，将你的资料输入一个没有`FULLTEXT`索引的表中，然后创建索引， 其速度比把资料输入现有`FULLTEXT`索引的速度更为快。尝试输入以下代码： `mysql> CREATE TABLE articles ( -> id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY, -> title VARCHAR(200), -> body TEXT, -> FULLTEXT (title,body) -> ); mysql> INSERT INTO articles (title,body) VALUES -> ('MySQL Tutorial','DBMS stands for DataBase ...'), -> ('How To Use MySQL Well','After you went through a ...'), -> ('Optimizing MySQL','In this tutorial we will show ...'), -> ('1001 MySQL Tricks','1\. Never run mysqld as root. 2\. ...'), -> ('MySQL vs. YourSQL','In the following database comparison ...'), -> ('MySQL Security','When configured properly, MySQL ...'); mysql> SELECT * FROM articles -> WHERE MATCH (title,body) AGAINST ('database'); mysql> SELECT id, MATCH (title,body) AGAINST ('Tutorial') -> FROM articles; mysql> SELECT id, body, MATCH (title,body) AGAINST -> ('Security implications of running MySQL as root') AS score -> FROM articles WHERE MATCH (title,body) AGAINST -> ('Security implications of running MySQL as root'); mysql> SELECT * FROM articles -> WHERE MATCH (title,body) AGAINST ('MySQL');`

## 七、`Cast`函数和操作符

*   `BINARY`

    `BINARY`操作符将后面的字符串转换成一个二进制字符串。这是一种简单的方式来促使逐字节而不是逐字符的进行列比较。这使得比较区分大小写，即使该列不被定义为`BINARY`或 `BLOB`类型。

    ```sql
    mysql> SELECT 'a' = 'A';

    mysql> SELECT BINARY 'a' = 'A';

    mysql> SELECT 'a' = 'a ';

    mysql> SELECT BINARY 'a' = 'a '; 
    ```

    在比较运算中，`BINARY`会影响整个操作；它可以在任何操作数前被给定，而产生相同的结果。

*   `CAST(expr AS type), CONVERT(expr,type) , CONVERT(expr USING transcoding_name)`

    `CAST()`和`CONVERT()`函数通过获取一个类型的值，转化为另一个被指定类型的值。

    这里的类型可以是以下列表中的任意一个：

    *   `BINARY[(N)]`
    *   `CHAR[(N)]`
    *   `DATE`
    *   `DATETIME`
    *   `DECIMAL`
    *   `SIGNED[INTEGER]`
    *   `TIME`
    *   `UNSIGNED [INTEGER]`

    其中`BINARY`产生一个二进制字符串。

    假如给定了随意长度`N`，则 `BINARY[N]`使 `cast`使用不多于`N`个字节的参数。同样， `CHAR[N]`会使 `cast` 使用不多于 N 个字符的参数。

    `CAST() and CONVERT(... USING ...)` 是标准 SQL 语法。`CONVERT()`的非`USING`格式是`ofis ODBC`语法。

    带有`USING`的`CONVERT()`被用来在不同的字符集之间转化数据。在 MySQL 中, 自动译码名和相应的字符集名称相同。例如，以下语句将服务器的默认字符集中的字符串 `'abc'`转化为`utf8`字符集中相应的字符串：

    ```sql
    SELECT CONVERT('abc' USING utf8); 
    ```

    当你想要在一个`CREATE ... SELECT`语句中创建一个特殊类型的列时，`cast`函数会很有用：

    ```sql
    CREATE TABLE new_table SELECT CAST('2000-01-01' AS DATE); 
    ```

    该函数也用于`ENUM`列按词法顺序的排序。通常`ENUM`列的排序在使用内部数值时发生，将这些值按照词法顺序派给 `CHAR`的结果：

    ```sql
    SELECT enum_col FROM tbl_name ORDER BY CAST(enum_col AS CHAR); 
    ```

    `CAST(str AS BINARY)`和`BINARY str`的意义相同。 `CAST(expr AS CHAR)`将表达式视为一个带有默认字符集的字符串。

    你不应在不同的格式中使用 `CAST()`来析取数据，但可以使用诸如`LEFT()` 或 `EXTRACT()` 这样的字符串函数来代替。

    若要在数值语境中将一个字符串派给一个数值, 通常情况下，除了将字符串值作为数字使用外，你不需要做任何事：

    ```sql
    mysql> SELECT 1+'1'; 
    ```

    若要在一个字符串语境中使用一个数字，该数字会被自动转化为一个`BINARY`字符串。

    ```sql
    mysql> SELECT CONCAT('hello you ',2); 
    ```

## 八、其他函数

### 位函数

对于位运算，MySQL 使用 `BIGINT (64 位)`算法，因此这些操作符的最大范围是 64 位。

*   `|` 按位`OR`:

    ```sql
    mysql> SELECT 29 | 15; 
    ```

*   `&` 按位 `AND`:

    ```sql
    mysql> SELECT 29 & 15; 
    ```

*   `^` 按位`XOR`:

    ```sql
    mysql> SELECT 1 ^ 1;

    mysql> SELECT 1 ^ 0;

    mysql> SELECT 11 ^ 3; 
    ```

*   `<<` 把一个`longlong (BIGINT)`数左移两位。

    ```sql
    mysql> SELECT 1 << 2; 
    ```

*   `>` 把一个`longlong (BIGINT)`数右移两位。

    ```sql
    mysql> SELECT 4 >> 2; 
    ```

*   `~` 反转所有位。

    ```sql
    mysql> SELECT 5 & ~1; 
    ```

*   `BIT_COUNT(N)` 返回参数`N`中所设置的位的数量。 `mysql> SELECT BIT_COUNT(29);`

## 九、思考与练习

当你看到这一节的时候，恭喜你，你已经完成了一个艰巨的任务！因为我在翻译的时候就知道涉及到的知识很多，当然还有很多函数没有全部写上，既然是参考手册，不要求你们全部掌握，只要在需要的时候查阅即可，但是前提是得掌握一些基本的函数和操作符的运用；因此，希望你们能在实验楼操作以上函数或者操作符中感兴趣的语句，记得写入实验报告让我瞧瞧你的学习状态哦~