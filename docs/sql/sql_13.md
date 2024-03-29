# 第 2 节 MySQL 基本操作

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

## 一、创建并使用数据库

### 1\. 创建并选择数据库

使用 SHOW 语句找出服务器上当前存在什么数据库：

```sql
mysql> SHOW DATABASES; 
```

如果管理员在设置权限时为你创建了数据库，你可以开始使用它。否则，你需要自己创建数据库：

```sql
mysql> CREATE DATABASE test; 
```

在 Unix 下，数据库名称是区分大小写的(不像 SQL 关键字)，因此你必须总是以 test 访问数据库，而不能用`Test`、`TEST`或其它一些变量。对表名也是这样的。（在 Windows 下，该限制不适用，尽管你必须在一个给定的查询中使用同样的大小写来引用数据库和表。但是，由于多种原因，作为最好的惯例，一定要使用与数据库创建时的同样的大小写。）

创建数据库并不表示选定并使用它，你必须明确地操作。为了使 test 成为当前的数据库，使用这个命令：

```sql
mysql> USE test 
```

数据库只需要创建一次，但是必须在每次启动 mysql 会话时在使用前先选择它。你可以根据上面的例子执行一个 USE 语句来实现。

### 2\. 创建表

创建数据库是很容易的部分，较难的部分是决定你的数据库结构应该是什么：你需要什么数据库表，各数据库表中有什么样的列。

假如你需要建立一个包含你的每个宠物的记录的表。它可称为`pet`表，并且它应该至少包含每个动物的名字。因为名字本身不是很有趣，表应该包含另外的信息。例如，如果在你家豢养宠物人超过一个，你可能想要列出每个动物的主人。你可能也想要记录例如宠物的种类和性别等基本的描述信息。

那么年龄呢？那听起来可能有趣，但是如果将年龄存储到数据库中并不是那么简单。年龄随着时间流逝而变化，这意味着你将要不断地更新你的记录。相反,存储一个固定值例如生日比较好，那么，无论何时你需要用到宠物的年龄，可以用当前日期和出生日期之间的差来计算它。MySQL 提供了日期运算函数，因此并不困难。存储出生日期而非年龄还有其它优点：

1.  你可以使用数据库完成这样的任务：例如生成即将到来的宠物生日的提示。（如果你认为这类查询有点蠢，注意，这与根据商务数据库来识别出不久要发给生日祝贺的客户是同一个问题。）

2.  你可以相对于日期而不止是当前日期来计算年龄。例如，如果你在数据库存储死亡日期，你能很容易地计算出一只宠物死时有多大。

你可能想到 pet 表中其它有用信息，但是到目前为止这些已经足够了：名字、主人、种类，性别、出生和死亡日期。

使用一个`CREATE TABLE`语句指定你的数据库表的布局：

```sql
mysql> CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20),
    -> species VARCHAR(20), sex CHAR(1), birth DATE, death DATE); 
```

![此处输入图片的描述](img/7cb013b4e9184ac887456dcded930a13.jpg)

`VARCHAR`适合于`name`、`owner`和`species`列，因为这些列值的长度是可以变化的，这些列的长度不必都相同，而且不必是 20。你可以选从 1 到 65535 选择一个最合理的值作为列属性值的长度。如果选择得不合适，MySQL 提供一个`ALTER TABLE`语句来修改表格（后来证明你需要一个更长的字段）。

动物性别可以由多个名称表示，例如，"m"和"f"，或"male"和"female"。使用单字符"m"和"f"是最简单的方法。

很显然，`birth`和`death`列应选用`DATE`（日期）数据类型。

创建了数据库表后，SHOW TABLES 应该产生一些输出：

```sql
mysql> SHOW TABLES; 
```

![此处输入图片的描述](img/3624ff4d06786d7a05941e19be2f8e0a.jpg)

为了验证你的表是按你期望的方式创建，使用一个`DESCRIBE`语句：

```sql
mysql> DESCRIBE pet; 
```

![此处输入图片的描述](img/ecc67f09583335351938cf6fd21cb389.jpg)

你可以随时使用`DESCRIBE`，例如：你忘记表中的列的名称或类型的时候。

### 3\. 将数据加载到表中

创建表后，需要填入内容。通过`LOAD DATA`和`INSERT`语句可以完成该任务。

假定你的宠物纪录描述如下。（假定在 MySQL 中期望的日期格式是`YYYY-MM-DD`）

![此处输入图片的描述](img/8401aef9eccae9e53832ec14d9c89fa5.jpg)

因为你是从一个空表开始的，填充它的一个简易方法是创建一个文本文件，每个动物各一行，然后用一个语句将文件的内容加载到表中。

你可以根据上面的宠物记录创建一个文本文件“pet.txt”，每行包含一个记录，用定位符(tab)把值分开，并且按照上面的`CREATE TABLE`语句中列出的次序依次填写数据。对于丢失的值(例如未知的性别，或仍然活着的动物的死亡日期)，你可以使用\N（反斜线，字母 N）表示该值属于`NULL`。例如，`Whistler`这只鸟的记录应为(这里值之间的分割线实际上是一个定位符)：

![此处输入图片的描述](img/82d95fa339bc06962d1ca87fe7ddee31.jpg)

要想将文本文件“pet.txt”装载到`pet`表中，使用这个命令：

```sql
mysql> LOAD DATA LOCAL INFILE '/path/pet.txt' INTO TABLE pet; 
```

请注意如果用 Windows 中的编辑器（使用\r\n 做为行的结束符）创建文件，应使用：

```sql
mysql> LOAD DATA LOCAL INFILE '/path/pet.txt' INTO TABLE pet
    -> LINES TERMINATED BY '\r\n'; 
```

（在运行 OS X 的苹果电脑上，应使用行结束符`\r`。）

如果你愿意，你能明确地在`LOAD DATA`语句中指出列值的分隔符和行尾标记，但是默认标记是定位符和换行符。这对读入文件“pet.txt”的语句已经足够。

如果想要一次增加一个新记录，可以使用 INSERT 语句。最简单的形式是，提供每一列的值，其顺序与`CREATE TABLE`语句中列的顺序相同。假定 Diane 把一只新仓鼠取名为`Puffball`，你可以使用下面的 INSERT 语句添加一条新记录：

```sql
mysql> INSERT INTO pet
    -> VALUES ('Puffball','Diane','hamster','f','1999-03-30',NULL); 
```

注意，这里字符串和日期值均为引号扩起来的字符串。另外，可以直接用`INSERT`语句插入`NULL`代表不存在的值。

### 4\. 从表检索信息

#### 4.1 选择所有的数据

从表中检索所有记录的最简单方式是使用`SELECT`语句：

```sql
mysql> SELECT * FROM pet; 
```

有可能你想到`Bowser`的生日看起来不太对。经过查询，你发现正确的出生年份是 1989，而不是 1979。

那么现在至少有两种修正方法：

*   编辑文件“pet.txt”改正错误，然后使用`DELETE`和`LOAD DATA`清空并重新加载表:

```sql
mysql> DELETE FROM pet;
mysql> LOAD DATA LOCAL INFILE 'pet.txt' INTO TABLE pet; 
```

然而, 在这样操作之前，需要重新输入`Puffball`记录。

*   用一个`UPDATE`语句就可以修正错误记录：

```sql
mysql> UPDATE pet SET birth = '1989-08-31' WHERE name = 'Bowser'; 
```

`UPDATE`只更改有问题的记录，并且不需要重新加载数据库表。

#### 4.2 选择特殊行

由上一节内容可知，检索整个表是容易的。只需要使用`SELECT`语句即可。但是一般情况下你不想检索整个表的内容，特别地当表变得很大时。相反，你通常对解决一个具体的问题更感兴趣，在这种情况下在你需要添加一些限制条件。让我们学习如何做到这一点吧：

可以从表中只选择特定的行。例如，如果你想要验证你对 Bowser 的生日所做的更改是否正确或者生效，按下述方法选择`Bowser`的记录：

```sql
mysql> SELECT * FROM pet WHERE name = 'Bowser'; 
```

输出证实正确的年份记录为 1989，而不是 1979。

字符串比较时通常对大小写不敏感，因此你可以将名字指定为"bowser"、"BOWSER"等，查询结果相同。

你可以在任何列上指定条件，不只仅仅是`name`。例如，如果你想要知道哪个动物在 1998 以后出生的，尝试筛选`birth`列：

```sql
mysql> SELECT * FROM pet WHERE birth > '1998-1-1'; 
```

还可以使用`AND`语句组合筛选条件，例如，找出雌性的狗：

```sql
mysql> SELECT * FROM pet WHERE species = 'dog' AND sex = 'f'; 
```

有`AND`逻辑操作符，那么就有一个`OR`操作符：

```sql
mysql> SELECT * FROM pet WHERE species = 'snake' OR species = 'bird'; 
```

`AND`和`OR`可以混用，但`AND`比`OR`具有更高的优先级。如果你使用两个操作符，最好是使用圆括号指明如何按条件分组：

```sql
mysql> SELECT * FROM pet WHERE (species = 'cat' AND sex = 'm')
    -> OR (species = 'dog' AND sex = 'f'); 
```

#### 4.3 选择特殊列

如果你不想看到表中的所有行，就需要指明你感兴趣的列名称，并用逗号将列名分开。例如，如果你想要知道你的动物什么时候出生的，选择`name`和`birth`列：

```sql
mysql> SELECT name, birth FROM pet; 
```

找出拥有宠物的所有人，可以使用以下个查询：

```sql
mysql> SELECT owner FROM pet; 
```

请注意该查询只是简单地检索每个记录的`owner`列，但是他们有些名字出现多次。为了使输出减到最少（避免重复），增加关键字`DISTINCT`检索出每条唯一的输出记录：

```sql
mysql> SELECT DISTINCT owner FROM pet; 
```

可以使用一个`WHERE`子句同时进行行选择与列选择。例如，要想查询狗和猫的出生日期，使用以下查询：

```sql
mysql> SELECT name, species, birth FROM pet
    -> WHERE species = 'dog' OR species = 'cat'; 
```

#### 4.4 行分类

你可能已经注意到前面的例子中行并没有以特定的顺序显示。然而，当行按某种方式排序时，检查查询输出的时候通常变得相对简单一点。为了给结果排序，使用`ORDER BY`子句。 这里是对动物生日按日期排序：

```sql
mysql> SELECT name, birth FROM pet ORDER BY birth; 
```

由于字符串类型的数据对大小写并不敏感。这意味着，对于大小写不同其他都一样的列，排序后的结果都不会更改。这种情况下，可以使用`BINARY`强制执行区分大小写的排序功能，如：`ORDER BY BINARY col_name`。

默认排序是升序，也就是最小的值排在第一。要想以降序排序，在你正在排序的列名旁边增加`DESC`（降序 ）关键字：

```sql
mysql> SELECT name, birth FROM pet ORDER BY birth DESC; 
```

可以对多个列进行排序，并且可以按不同的方向对不同的列进行排序。例如，按升序对动物的种类进行排序，然后按降序根据生日对各动物种类进行排序（最年轻的动物在最前面），使用下列查询：

```sql
mysql> SELECT name, species, birth FROM pet
    -> ORDER BY species, birth DESC; 
```

注意`DESC`关键字仅适用于在它前面的列名(`birth`)；不影响`pecies`列的排序顺序。

#### 4.5 日期计算

MySQL 可以用来计算日期的函数，比如计算年龄或提取日期部分。

要想确定每个宠物有多大，可以使用函数`TIMESTAMPDIFF()`计算当前日期的年和出生日期之间的差也可以按照直接使用语句`(YEAR(CURDATE())-YEAR(birth))`计算，其中函数`CURDATE()`是计算当前的日期。如果当前日期的日历年比出生日期早，则减去一年。以下代码是查询每个宠物的出生日期、当前日期和年龄（以年作为计算单位），其中关键字`age`是年龄这个计算结果的标签。

```sql
mysql> SELECT name, birth, CURDATE(),
    -> TIMESTAMPDIFF(YEAR,birth,CURDATE()) AS age
    -> FROM pet;

# 比较这两个查询语句的结果

mysql> SELECT name, birth, CURDATE(),
    -> (YEAR(CURDATE())-YEAR(birth))
    -> - (RIGHT(CURDATE(),5)<RIGHT(birth,5)) AS age  
    -> FROM pet; 
```

此处，`YEAR()`提取日期的年部分，`RIGHT()`提取日期最右面 5 个字符的`MM-DD` (月份和日期)部分。`MM-DD`值的表达式部分的值一般为`1`或`0`，如果`CURDATE()`的年比`birth`的年早，则年份应减去`1`。整个表达式看起来有些难懂，使用`age`来使输出的列标记更有意义。

尽管查询可行，但是人类的欲望是永无止尽的，如果以某个顺序排列行，那么会使得浏览结果变得更加轻松。添加`ORDER BY name`子句则能够实现按照名字进行排序输出。

```sql
mysql> SELECT name, birth, CURDATE(),
    -> (YEAR(CURDATE())-YEAR(birth))
    -> - (RIGHT(CURDATE(),5)<RIGHT(birth,5))
    -> AS age
    -> FROM pet ORDER BY name; 
```

为了按`age`而非`name`排序输出，只要再使用一个 ORDER BY 子句：

```sql
mysql> SELECT name, birth, CURDATE(),
    -> (YEAR(CURDATE())-YEAR(birth))
    -> - (RIGHT(CURDATE(),5)<RIGHT(birth,5))
    -> AS age
    -> FROM pet ORDER BY age; 
```

可以使用一个类似的查询来确定已经死亡动物的死亡年龄。你通过检查`death`值是否为`NULL`来确定是哪些动物已经死亡，然后对于那些非`NULL`值的动物，需要计算出`death`和`birth`值之间的差来知道他们在这个世界上所存在的时间：

```sql
mysql> SELECT name, birth, death,
    -> (YEAR(death)-YEAR(birth)) - (RIGHT(death,5)<RIGHT(birth,5))
    -> AS age
    -> FROM pet WHERE death IS NOT NULL ORDER BY age; 
```

查询使用`death IS NOT NULL`而非`death != NULL`，因为 NULL 是特殊的值，不能使用普通比较符来比较。

如果你想要知道哪个动物下个月过生日怎么办？对于这类计算，年和天是无关的，你只需要提取`birth`列的月份部分。MySQL 提供几个日期方面的提取函数，例如`YEAR()`、`MONTH()`和`DAYOFMONTH()`。在这里`MONTH()`是我们需要的函数。为了观察它的实现原理，可以运行以下简单的查询显示`birth`和`MONTH(birth)`的值：

```sql
mysql> SELECT name, birth, MONTH(birth) FROM pet; 
```

找出下个月生日的动物也很简单。假定当前月是 4 月，那么月值是`4`，你需要找在 5 月出生的动物，方法是：

```sql
mysql> SELECT name, birth FROM pet WHERE MONTH(birth) = 5; 
```

如果当前月份是 12 月，就有点复杂了。你不能只把`1`加到月份数(`12`)上并寻找在 13 月出生的动物，因为没有这样的月份。相反，你应寻找在 1 月出生的动物。

你甚至可以编写查询，不管当前月份是什么它都能执行。因此不必在查询中使用一个特定的月份，`DATE_ADD()`允许在一个给定的日期上加上时间间隔。如果在`NOW()`值上加上一个月，然后用`MONTH()`提取月份，产生生日所在月份：

```sql
mysql> SELECT name, birth FROM pet
    -> WHERE MONTH(birth) = MONTH(DATE_ADD(CURDATE(),INTERVAL 1 MONTH)); 
```

完成该任务的另一个方法是加 1 得出当前月份的下一个月(在使用取模函数`MOD()`后，如果月份当前值是`12`，则“返回”到值`0`)：

```sql
mysql> SELECT name, birth FROM pet
    -> WHERE MONTH(birth) = MOD(MONTH(CURDATE()), 12) + 1; 
```

注意，`MONTH`返回在`1`和`12`之间的一个数字，且`MOD(something,12)`返回在 0 和 11 之间的一个数字，因此必须在`MOD()`后加 1，否则我们将从 11 月(`11`)跳到 1 月(`1`)。

#### 4.6 `NULL`值操作

`NULL`值可能令人感到奇怪因此你需要习惯它。概念上，`NULL`意味着“没有值”或“未知值”，并且它被看作使与众不同的值。为了测试`NULL`，你不能使用算术比较操作符例如`=`、`<`或`!=`。为了了解它，试试下列查询：

```sql
mysql> SELECT 1 = NULL, 1 <> NULL, 1 < NULL, 1 > NULL; 
```

![此处输入图片的描述](img/3ea88f4acff4339f47d8168eee0bf087.jpg)

很显然你不能通过这些得到有意义的结果，因为任何使用算数比较操作符对`NULL`进行比较的结果都是`NULL`。因此使用`IS NULL`和`IS NOT NULL`操作符：

```sql
mysql> SELECT 1 IS NULL, 1 IS NOT NULL; 
```

![此处输入图片的描述](img/a25bbe9101c30eee7b1b4b1ce8591a08.jpg)

请注意在 MySQL 中，`0`或`NULL`意味着假而其它值意味着真。[布尔运算](http://baike.baidu.com/view/638530.htm)的默认真值是`1`。

为了查询出哪个动物不再是活着的，使用`death IS NOT NULL`而不使用`death != NULL`的原因。

在`GROUP BY`中，两个`NULL`值被视为相同等价的。

执行`ORDER BY`语句排序时，如果运行`ORDER BY ... ASC`，则`NULL`值出现在最前面，若运行`ORDER BY ... DESC`，则`NULL`值出现在最后面。

`NULL`操作的常见错误是认为不能在定义为`NOT NULL`的列内插入`0`或空字符串，但事实并非如此。这样的结果是在`NULL`表示"没有数值"的时候恰恰是有数值`0`存在的。因此使用`IS [NOT] NULL`则可以很容易地进行区分，如下所示：

```sql
mysql> SELECT 0 IS NULL, 0 IS NOT NULL, '' IS NULL, '' IS NOT NULL; 
```

因此完全可以在定义为`NOT NULL`的列内插入`0`或空字符串，因为它们实际是`NOT NULL`。

#### 4.7 模式匹配

MySQL 提供标准的 SQL 模式匹配，以及一种基于类`Unix`里的程序如`vi`、`grep`和`sed`里的[扩展正则表达式](http://book.51cto.com/art/201303/385961.htm)模式匹配的格式。 SQL 模式匹配允许你使用“_”匹配任何单个字符，而“%”匹配任意数目字符(包括零字符)。在 MySQL 中，SQL 的模式默认是忽略大小写的。下面给出一些例子。注意使用 SQL 模式时，不能使用`=`或`!=`；而应使用`LIKE`或`NOT LIKE`比较操作符。 要想找出以“b”开头的名字：

```sql
mysql> SELECT * FROM pet WHERE name LIKE 'b%'; 
```

要想找出以“fy”结尾的名字：

```sql
mysql> SELECT * FROM pet WHERE name LIKE '%fy'; 
```

要想找出包含“w”的名字：

```sql
mysql> SELECT * FROM pet WHERE name LIKE '%w%'; 
```

要想找出正好包含 5 个字符的名字，使用“_”模式字符：

```sql
mysql> SELECT * FROM pet WHERE name LIKE '_____'; 
```

由 MySQL 提供的模式匹配的其它类型是使用扩展正则表达式。当你对这类模式进行匹配测试时，使用`REGEXP`和`NOT REGEXP`操作符(或`RLIKE`和`NOT RLIKE`，它们是同义词)。

扩展正则表达式的一些字符是：

*   ‘.’匹配任何单个的字符。

*   字符类“[...]”匹配在方括号内的任何字符。例如，“[abc]”匹配“a”、“b”或“c”。为了命名字符的范围，使用一个“-”。“[a-z]”匹配任何字母，而“[0-9]”匹配任何数字。

*   “ ”匹配零个或多个在它前面的字符。例如，“x”匹配任何数量的“x”字符，“[0-9]”匹配任何数量的数字，而“.”匹配任何数量的任何字符。

如果`REGEXP`模式与被测试值的任何地方匹配，模式就匹配(这不同于`LIKE`模式匹配，只有与整个值匹配，模式才匹配)。 为了定位一个模式以便它必须匹配被测试值的开始或结尾，在模式开始处使用“^”或在模式的结尾用“$”。 为了说明扩展正则表达式如何工作，下面使用`REGEXP`重写上面所示的 LIKE 查询：

为了找出以“b”开头的名字，使用“^”匹配名字的开始：

```sql
mysql> SELECT * FROM pet WHERE name REGEXP '^b'; 
```

如果你想强制使`REGEXP`比较区分大小写，使用`BINARY`关键字使其中一个字符串变为二进制字符串。该查询只匹配名称首字母的小写‘b’。

```sql
mysql> SELECT * FROM pet WHERE name REGEXP BINARY '^b'; 
```

为了找出以“fy”结尾的名字，使用“$”匹配名字的结尾：

```sql
mysql> SELECT * FROM pet WHERE name REGEXP 'fy$'; 
```

为了找出包含一个“w”的名字，使用以下查询：

```sql
mysql> SELECT * FROM pet WHERE name REGEXP 'w'; 
```

既然如果一个正则表达式出现在值的任何地方，他就会被模式匹配，就不必在先前的查询中在模式的两侧放置一个通配符以使得它匹配整个值，就像你使用了一个 SQL 模式那样。

为了找出包含正好 5 个字符的名字，使用“^”和“$”匹配名字的开始和结尾，和 5 个“.”实例在两者之间：

```sql
mysql> SELECT * FROM pet WHERE name REGEXP '^.....$'; 
```

你也可以使用“{n}”重复 n 次操作符,重写前面的查询：

```sql
mysql> SELECT * FROM pet WHERE name REGEXP '^.{5}$'; 
```

#### 4\. 计算行数

数据库经常用于回答这个问题，`“查询出某个类型的数据在表中出现的频数是多少?”`

例如，你可能想要知道你有多少宠物，或每位主人有多少宠物，或你可能想要对你的动物进行各种类型的普查。

计算你拥有动物的总数目与`“在 pet 表中有多少行?”`是同样的问题，因为每个宠物都对应一条记录。`COUNT(*)`函数计算行数，所以计算动物数目的查询应为：

```sql
mysql> SELECT COUNT(*) FROM pet; 
```

在前面的章节中，你检索了拥有宠物的人的名字。如果你想要知道每个主人有多少宠物，你也可以使用`COUNT(*)`函数：

```sql
mysql> SELECT owner, COUNT(*) FROM pet GROUP BY owner; 
```

注意，使用`GROUP BY`对每个`owner`的所有记录分组，没有它，你会得到错误消息：

```sql
mysql> SELECT owner, COUNT(*) FROM pet;
ERROR 1140 (42000): Mixing of GROUP columns (MIN(),MAX(),COUNT(),...)
with no GROUP columns is illegal if there is no GROUP BY clause 
```

`COUNT(*)`和`GROUP BY`以各种形式分类你的数据。下列例子显示出以不同方式进行动物普查操作。

查看每种动物的数量：

```sql
mysql> SELECT species, COUNT(*) FROM pet GROUP BY species; 
```

查看每种性别的动物数量：

```sql
mysql> SELECT sex, COUNT(*) FROM pet GROUP BY sex; 
```

按种类和性别组合分类的动物数量：

```sql
mysql> SELECT species, sex, COUNT(*) FROM pet GROUP BY species, sex; 
```

若使用`COUNT(*)`，你不必检索整个表。例如, 当只对狗和猫进行查询时，应为：

```sql
mysql> SELECT species, sex, COUNT(*) FROM pet
    -> WHERE species = 'dog' OR species = 'cat'
    -> GROUP BY species, sex; 
```

或，如果你仅需要知道已知性别的按性别分组的动物数目：

```sql
mysql> SELECT species, sex, COUNT(*) FROM pet
    -> WHERE sex IS NOT NULL
    -> GROUP BY species, sex; 
```

#### 4.9 使用 1 个以上的表

`pet`表追踪你拥有的宠物。如果你想要记录其它相关信息，例如在他们看兽医的情况或后代出生的情况，那么你需要另外的表。这张表应该拥有些什么呢？它需要：

*   需要包含宠物名字以便你知道每个发生的事件属于哪个动物。
*   需要一个日期以便你知道事件是什么时候发生的。
*   需要一个描述事件的字段。
*   如果你想要对事件进行分类，则需要一个事件类型字段。

综上所述，`event`表的 CREATE TABLE 语句应为：

```sql
mysql> CREATE TABLE event (name VARCHAR(20), date DATE,
    -> type VARCHAR(15), remark VARCHAR(255)); 
```

类似于于`pet`表，最简单的方法是创建一个用定位符分隔的文本文件来加载载初始记录：

![此处输入图片的描述](img/2695f8dad4a66bac5f879a8d412eaf98.jpg)

采用如下方式加载记录：

```sql
mysql> LOAD DATA LOCAL INFILE 'event.txt' INTO TABLE event; 
```

由于你已经在`pet`表上的查询中学到了一定的知识，你应该能执行对`event`表中记录的检索；原理是一样的。但是有没有`event`表本身不能回答你可能问的问题呢?

当宠物们生了了一窝小动物时，假定你想要找出这时候每只宠物的年龄。我们前面看到了如何通过两个日期计算年龄。`event`表中有母亲的生产日期，但是为了计算母亲的年龄，你需要她的出生日期，存储在`pet`表中。说明查询需要两个表：

```sql
mysql> SELECT pet.name,
    -> (YEAR(date)-YEAR(birth)) - (RIGHT(date,5)<RIGHT(birth,5)) AS age,
    -> remark
    -> FROM pet, event
    -> WHERE pet.name = event.name AND event.type = 'litter'; 
```

关于该查询要注意以下几件事：

*   `FROM`子句连接两个表，因为查询需要从两个表中提取信息。

*   当从多个表组合(联结)信息时，你需要指定其中一个表中的列明以期匹配其它表的列名。这很简单，因为它们都有一个`name`列，查询可以通过使用`WHERE`子句基于`name`值来匹配两个表中的记录。

*   因为`name`列都存在两个表中，因此当引用该列时，一定要指定是哪个表，把表名附在列名前即可以实现。 如果你想要将一个表的记录与该表的其它记录进行比较，可以将该表联结到自身。例如，为了在你的宠物之中选择繁殖中的配偶，你可以用`pet`表联结自身来进行相同种类的雄雌配对：

```sql
mysql> SELECT p1.name, p1.sex, p2.name, p2.sex, p1.species
    -> FROM pet AS p1, pet AS p2
    -> WHERE p1.species = p2.species AND p1.sex = 'f' AND p2.sex = 'm'; 
```

在这个查询中，我们为表名指定别名`p1`和`p2`以便能引用它们的列并且使得每一个列的引用更直观。

## 二、获得数据库和表的信息

如果你忘记数据库或表的名字，或给定的表的结构是什么(例如，它的列叫什么)，怎么办？MySQL 提供一些语句解决这个问题。 你已经知道`SHOW DATABASES`可以列出由服务器管理的所有数据库。为了找出当前选择了哪个数据库，使用`DATABASE()`函数：

```sql
mysql> SELECT DATABASE(); 
```

如果你还没选择任何数据库，结果是`NULL`。

为了找出当前的数据库包含什么表(例如，当你不能确定一个表的名字)，使用这个命令：

```sql
mysql> SHOW TABLES; 
```

如果你想要知道一个表的结构，可以使用`DESCRIBE`命令；它显示表中每个列的信息：

```sql
mysql> DESCRIBE pet; 
```

`Field`显示列名字，`Type`是列的数据类型，`Null`表示列是否能包含`NULL`值，`key`显示列是否被索引而`Default`指定列的默认值。

如果表有索引，`SHOW INDEX FROM tbl_name`生成有关索引的信息。

## 三、作业

请将以上的操作在实验楼的环境下执行，并把结果截图，把你们在这个过程中遇到的问题以及解决方案写入到实验报告中，更多细节上的问题可以尝试百度，你会发现搜索引擎给你带来的不一样的惊喜！