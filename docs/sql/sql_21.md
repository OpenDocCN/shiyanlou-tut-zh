# 第 10 节 MySQL 存储程序的语法

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

## 4\. 实验内容

### 4.1 `CREATE PROCEDURE`和`CREATE FUNCTION`

```sql
CREATE PROCEDURE sp_name ([proc_parameter[,...]])
    [characteristic ...] routine_body

CREATE FUNCTION sp_name ([func_parameter[,...]])
    RETURNS type
    [characteristic ...] routine_body

    proc_parameter:
    [ IN | OUT | INOUT ] param_name type

    func_parameter:
    param_name type

type:
    Any valid MySQL data type

characteristic:
    LANGUAGE SQL
  | [NOT] DETERMINISTIC
  | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
  | SQL SECURITY { DEFINER | INVOKER }
  | COMMENT 'string'

routine_body:
    Valid SQL procedure statement or statements 
```

这些语句用于创建并存储子程序。要在 MySQL 5.1 中创建子程序，必须具有`CREATE ROUTINE`权限，并且`ALTER ROUTINE`和`EXECUTE`权限被自动授予它的创建者。

默认情况下，子程序与当前数据库关联。要明确地把子程序与一个给定数据库关联起来，可以在创建子程序的时候指定其名字为`db_name.sp_name`。

如果子程序名和 SQL 内部函数名一样，在定义子程序时，你需要在这个名字和随后括号中间插入一个空格，否则发生语法错误。在你之后调用子程序的时候也要插入。为此，即使有可能出现这种情况，我们还是建议最好避免给你的存储子程序取与已经存在的 SQL 函数一样的名字。

由括号包围的参数列必须总是存在。如果没有参数，也该使用一个空参数列()。每个参数默认都是一个`IN`（内部）参数。要指定为其它参数，可在参数名之前使用关键词 `OUT`或`INOUT`。

**注意：** 指定参数为`IN`， `OUT`， 或`INOUT` 只对`PROCEDURE`是合法的。（`FUNCTION`参数总是被认为是`IN`）

下面是一个使用`OUT`参数的简单的存储程序的例子。例子为：在程序被定义的时候，用 mysql 客户端`delimiter`命令来把语句定界符从`;`变为`//`。这就允许用在程序体中的`;`定界符被传递到服务器而不是被 mysql 自己来解释。

```sql
mysql> delimiter //
mysql> CREATE PROCEDURE simpleproc (OUT param1 INT)
    -> BEGIN
    ->   SELECT COUNT(*) INTO param1 FROM t;
    -> END
    -> //
mysql> delimiter ;
mysql> CALL simpleproc(@a);
mysql> SELECT @a; 
```

当使用`delimiter`命令时，你应该避免使用反斜杠(‘\’)字符，因为那是 MySQL 的转义字符。

下列是一个例子，一个采用参数的函数使用一个 SQL 函数执行一个操作，并返回结果：

```sql
mysql> delimiter //
mysql> CREATE FUNCTION hello (s CHAR(20)) RETURNS CHAR(50)
    -> RETURN CONCAT('Hello, ',s,'!');
    -> //
mysql> delimiter ;
mysql> SELECT hello('world'); 
```

![此处输入图片的描述](img/ee2c302f525fbf9f4eee7665a5d8ff74.jpg)

如果在存储函数中的`RETURN`语句中返回一个类型不同于在函数的`RETURNS`子句中指定类型的值，返回值被强制转换为相对恰当的类型。比如，如果一个函数返回一个`ENUM`或`SET`值，但是`RETURN`语句返回一个整数，对于`SET`成员集合的相应的`ENUM`成员，从函数返回的值是字符串类型数据。

### 4.2 `ALTER PROCEDURE`和`ALTER FUNCTION`

```sql
ALTER {PROCEDURE | FUNCTION} sp_name [characteristic ...]

characteristic:
    { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
  | SQL SECURITY { DEFINER | INVOKER }
  | COMMENT 'string' 
```

这个语句可以被用来改变存储程序或函数的特征。

在`ALTER PROCEDURE`和`ALTER FUNCTION`语句中，可以指定一个以上的改变。

### 4.3 `DROP PROCEDURE`和`DROP FUNCTION`

```sql
DROP {PROCEDURE | FUNCTION} [IF EXISTS] sp_name 
```

这个语句被用来移除一个存储程序或函数。即从服务器上移除一个制定的子程序。

`IF EXISTS`子句是一个 MySQL 的扩展。它防止如果程序或函数不存在产生的错误，并产生一个可以用`SHOW WARNINGS`查看的警告。

### 4.4 `SHOW CREATE PROCEDURE`和`SHOW CREATE FUNCTION`

```sql
SHOW CREATE {PROCEDURE | FUNCTION} sp_name 
```

这个语句是一个 MySQL 的扩展。类似于`SHOW CREATE TABLE`，它返回一个可用来重新创建并且已命名的子程序的字符串。

例如以下语句返回`test`数据库内的`hello`程序信息：

```sql
mysql> SHOW CREATE FUNCTION test.hello\G 
```

![此处输入图片的描述](img/d2070d7b097f3959d318c8cb8167f5e6.jpg)

### 4.5 `SHOW PROCEDURE STATUS`和`SHOW FUNCTION STATUS`

```sql
SHOW {PROCEDURE | FUNCTION} STATUS [LIKE 'pattern'] 
```

这个语句是一个 MySQL 的扩展。它返回子程序的特征，如数据库，名字，类型，创建者及创建和修改日期。如果没有指定样式，根据你使用的语句，所有存储程序和所有存储函数的信息都被列出。

例如：

```sql
mysql> SHOW FUNCTION STATUS LIKE 'hello'\G 
```

![此处输入图片的描述](img/3db207f8648c807ad591148bcc86ddb2.jpg)

### 4.6 `CALL`语句

```sql
CALL sp_name([parameter[,...]]) 
```

`CALL`语句调用一个先前使用`CREATE PROCEDURE`创建的程序。

`CALL`语句可以用声明为`OUT`或`INOUT`参数的参数给它的调用者传回返回值。它也`“返回”`受影响的行数，客户端程序可以在 SQL 级别上通过调用`ROW_COUNT()`函数获得这个数，在`C`中是调用`the mysql_affected_rows()`这个 `C API`函数来获得。

### 4.7 `BEGIN ... END`复合语句

```sql
begin_label:] BEGIN
    [statement_list]
END [end_label] 
```

存储子程序可以使用`BEGIN ... END`复合语句来包含多个语句。`statement_list` 代表一个或多个语句的列表。`statement_list`内每个语句都必须用分号（`；`）来结尾。

复合语句可以被标记。除非`begin_label`存在,否则`end_label`不能被给出，并且如果二者都存在,他们必须是同样的。

请注意，可选的`[NOT] ATOMIC`子句现在还不被支持。这意味着在指令块的开始没有交互的存储点被设置，并且在上下文中用到的`BEGIN`子句对当前交互动作没有影响。

使用多重语句需要客户端能发送包含语句定界符`;`的查询字符串。这个符号在命令行客户端被用`delimiter`命令来改变查询结尾定界符`;`（比如改为`//`）使得`;` 可被用在子程序体中。

### 4.8 `DECLARE`语句

`DECLARE`语句被用来把不同项目涵盖到一个子程序：局部变量，条件和处理程序 及光标。`SIGNAL`和`RESIGNAL`语句当前还不被支持。

`DECLARE`仅被用在`BEGIN ... END`复合语句里，并且必须在复合语句的开头，在任何其它语句之前。

光标必须在声明处理程序之前被声明，并且变量和条件必须在声明光标或处理程序之前被声明。

### 4.9 存储过程中的变量

#### 4.9.1 `DECLARE`局部变量

```sql
DECLARE var_name[,...] type [DEFAULT value] 
```

这个语句被用来声明局部变量。要给变量提供一个默认值，请包含一个`DEFAULT`子句。值可以被指定为一个表达式，不需要为一个常数。如果没有`DEFAULT`子句，初始值为`NULL`。

局部变量的作用范围在它被声明的`BEGIN ... END`块内。它可以被用在嵌套的块中，除了那些用相同名字声明变量的块。

#### 4.9.2 变量`SET`语句

```sql
SET var_name = expr [, var_name = expr] ... 
```

在存储程序中的`SET`语句是一般`SET`语句的扩展版本。被参考变量可能是子程序内声明的变量，或者是全局服务器变量。

在存储程序中的`SET`语句作为预先存在的`SET`语法的一部分来实现。这允许`SET a=x, b=y, ...`这样的扩展语法。其中不同的变量类型（局域声明变量及全局和集体变量）可以被混合使用。这也允许把局部变量和一些只对系统变量有意义的选项合并起来。在那种情况下，此选项会被识别，但是也会被忽略。

#### 4.9.3 `SELECT ... INTO`语句

```sql
SELECT col_name[,...] INTO var_name[,...] table_expr 
```

这个`SELECT`语法把选定的列直接存储到变量中。因此，只有单一的行可以被取回。

例如：

```sql
SELECT id,data INTO x,y FROM test.t1 LIMIT 1; 
```

**注意：** SQL 变量名不能和列名一样。如果`SELECT ... INTO`这样的 SQL 语句包含一个对列的引用，并包含一个与列相同名字的局部变量，MySQL 会把引用解释为一个变量的名字。例如，在下面的语句中，`xname` 被解释为到`xname` 变量的引用而不是到`xname`列的引用：

```sql
CREATE PROCEDURE sp1 (x VARCHAR(5))
  BEGIN
    DECLARE xname VARCHAR(5) DEFAULT 'bob';
    DECLARE newname VARCHAR(5);
    DECLARE xid INT;

    SELECT xname,id INTO newname,xid 
      FROM table1 WHERE xname = xname;
    SELECT newname;
  END; 
```

当这个程序被调用的时候，无论`table.xname`列的值是什么，变量`newname`将返回值`‘bob’`。

### 4.10 条件和处理程序

#### 4.10.1 `DECLARE`条件

```sql
DECLARE condition_name CONDITION FOR condition_value

condition_value:
    SQLSTATE [VALUE] sqlstate_value
  | mysql_error_code 
```

这个语句用于指定需要特殊处理的条件。它将一个名字和指定的错误条件关联起来。这个名字可以之后被用在`DECLARE HANDLER`语句中。

#### 4.10.2 `DECLARE`处理程序

```sql
DECLARE handler_type HANDLER FOR condition_value[,...] sp_statement

handler_type:
    CONTINUE
  | EXIT
  | UNDO

condition_value:
    SQLSTATE [VALUE] sqlstate_value
  | condition_name
  | SQLWARNING
  | NOT FOUND
  | SQLEXCEPTION
  | mysql_error_code 
```

这个语句用于指定每一个可以处理一个或多个条件的处理程序。如果产生一个或多个条件，指定的语句将被执行。

对一个`CONTINUE`处理程序，当前子程序的执行在执行处理程序语句之后继续。对于`EXIT`处理程序，当前`BEGIN...END`复合语句的执行被终止。`UNDO` 处理程序类型语句还不被支持。 `SQLWARNING`是对所有以`01`开头的`SQLSTATE`代码的速记。

`NOT FOUND`是对所有以`02`开头的`SQLSTATE`代码的速记。

`SQLEXCEPTION`是对所有没有被`SQLWARNING`或`NOT FOUND`捕获的`SQLSTATE`代码的速记。

除了`SQLSTATE`值，MySQL 错误代码也不被支持。

例如:

```sql
mysql> CREATE TABLE test.t (s1 int,primary key (s1));
mysql> delimiter //
mysql> CREATE PROCEDURE handlerdemo ()
    -> BEGIN
    ->   DECLARE CONTINUE HANDLER FOR SQLSTATE '23000' SET @x2 = 1;
    ->   SET @x = 1;
    ->   INSERT INTO test.t VALUES (1);
    ->   SET @x = 2;
    ->   INSERT INTO test.t VALUES (1);
    ->   SET @x = 3;
    -> END;
    -> //
mysql> CALL handlerdemo()//
mysql> SELECT @x// 
```

![此处输入图片的描述](img/765197b119a485d96952f9ba521048d1.jpg)

注意到，`@x`是`3`，这表明 MySQL 被执行到程序的末尾。如果`DECLARE CONTINUE HANDLER FOR SQLSTATE '23000' SET @x2 = 1;`这一行不存在，第二个`INSERT`因`PRIMARY KEY`强制而失败之后，MySQL 可能已经采取默认(`EXIT`)路径，导致`SELECT @x`可能返回`2`。

### 4.11 光标

简单光标在存储程序和函数内被支持。语法如同在嵌入的 SQL 中。当前的光标是不敏感的，分为只读的以及不滚动的。光标必须在声明处理程序之前被声明，并且变量和条件必须在声明光标或处理程序之前被声明。

例如：

```sql
CREATE PROCEDURE curdemo()
BEGIN
  DECLARE done INT DEFAULT 0;
  DECLARE a CHAR(16);
  DECLARE b,c INT;
  DECLARE cur1 CURSOR FOR SELECT id,data FROM test.t1;
  DECLARE cur2 CURSOR FOR SELECT i FROM test.t2;
  DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;

  OPEN cur1;
  OPEN cur2;

  REPEAT
    FETCH cur1 INTO a, b;
    FETCH cur2 INTO c;
    IF NOT done THEN
       IF b < c THEN
          INSERT INTO test.t3 VALUES (a,b);
       ELSE
          INSERT INTO test.t3 VALUES (a,c);
       END IF;
    END IF;
  UNTIL done END REPEAT;

  CLOSE cur1;
  CLOSE cur2;
END 
```

#### 4.11.1 声明光标

```sql
DECLARE cursor_name CURSOR FOR select_statement 
```

这个语句声明一个光标。也可以在子程序中定义多个光标，但是一个块中的每一个光标必须有唯一的名字。

其中`select_statement`中的`SELECT`语句不能有`INTO`子句。

#### 4.11.2 光标`OPEN`语句

```sql
OPEN cursor_name 
```

这个语句用于打开先前声明的光标。

#### 4.11.3 光标`FETCH`语句

```sql
FETCH cursor_name INTO var_name [, var_name] ... 
```

这个语句用指定的打开光标读取下一行（如果有下一行的话），并且向前移动光标指针。

#### 4.11.4 光标`CLOSE`语句

```sql
CLOSE cursor_name 
```

这个语句关闭先前打开的光标。

如果未被明确地关闭，光标在它被声明的复合语句的末尾被关闭。

### 4.12 流程控制构造

#### 4.12.1 `IF`语句

```sql
IF search_condition THEN statement_list
    [ELSEIF search_condition THEN statement_list] ...
    [ELSE statement_list]
END IF 
```

`IF`实现了一个基本的条件构造。如果`search_condition`求值为真，相应的 SQL 语句列表被执行。如果没有`search_condition`匹配，在`ELSE`子句里的语句列表被执行。`statement_list`可以包括一个或多个语句。

**请注意，**还有有一个`IF()` 函数，它不同于这里描述的`IF`语句。

#### 4.12.2 `CASE`语句

```sql
CASE case_value
    WHEN when_value THEN statement_list
    [WHEN when_value THEN statement_list] ...
    [ELSE statement_list]
END CASE
Or: 

CASE
    WHEN search_condition THEN statement_list
    [WHEN search_condition THEN statement_list] ...
    [ELSE statement_list]
END CASE 
```

存储程序的`CASE`语句实现一个复杂的条件构造。如果`search_condition`求值为真，相应的 SQL 被执行。如果没有搜索条件匹配，在`ELSE`子句里的语句被执行。

#### 4.12.3 `LOOP`语句

```sql
[begin_label:] LOOP
    statement_list
END LOOP [end_label] 
```

`LOOP`允许某特定语句或语句群的重复执行，实现一个简单的循环构造。在循环内的语句一直重复直循环被退出，退出循环通常伴随着一个`LEAVE`语句。

`LOOP`语句可以被标注。除非`begin_label`存在，否则`end_label`不能被给出，并且如果两者都出现，它们必须是同样的。

#### 4.12.4 `LEAVE`语句

```sql
LEAVE label 
```

这个语句被用来退出任何被标注的流程控制构造。它和`BEGIN ... END`或循环一起被使用。

#### 4.12.5 `ITERATE`语句

```sql
ITERATE label 
```

`ITERATE`只可以出现在`LOOP`，`REPEAT`， 和`WHILE`语句内。`ITERATE`意思为：`“再次循环”`。

例如：

```sql
CREATE PROCEDURE doiterate(p1 INT)
BEGIN
  label1: LOOP
    SET p1 = p1 + 1;
    IF p1 < 10 THEN ITERATE label1; END IF;
    LEAVE label1;
  END LOOP label1;
  SET @x = p1;
END 
```

#### 4.12.6 `REPEAT`语句

```sql
[begin_label:] REPEAT
    statement_list
UNTIL search_condition
END REPEAT [end_label] 
```

`REPEAT`语句内的语句或语句群被重复，直至`search_condition`为真。

`REPEAT` 语句可以被标注。除非`begin_label`也存在，`end_label`才能被用，如果两者都存在，它们必须是一样的。

例如：

```sql
mysql> delimiter //
mysql> CREATE PROCEDURE dorepeat(p1 INT)
    -> BEGIN
    ->   SET @x = 0;
    ->   REPEAT SET @x = @x + 1; UNTIL @x > p1 END REPEAT;
    -> END
    -> //
mysql> CALL dorepeat(1000)//
mysql> SELECT @x// 
```

#### 1.12.7 `WHILE`语句

```sql
[begin_label:] WHILE search_condition DO
    statement_list
END WHILE [end_label] 
```

`WHILE`语句内的语句或语句群被重复，直至`search_condition`为真。

`WHILE`语句可以被标注。 除非`begin_label`也存在，`end_label`才能被用，如果两者都存在，它们必须是一样的。

例如：

```sql
CREATE PROCEDURE dowhile()
BEGIN
  DECLARE v1 INT DEFAULT 5;

  WHILE v1 > 0 DO
    ...
    SET v1 = v1 - 1;
  END WHILE;
END 
```

## 5\. 课后作业

填写代码的时候挖了一个坑，因为有些代码需要完整的实现前提是建立好相关的数据库和表，因此建议同学们把以上语法和代码完整的实现并截图，因为这样有利于理解相对枯燥的语法。