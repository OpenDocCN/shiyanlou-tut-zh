# 第 11 节 MySQL 数据库管理

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

## 一、MySQL 权限管理

账户权限信息被存储在 mysql 数据库中的`user`、`db`、`host`、`tables_priv`、`columns_priv`和`procs_priv`表中。

`GRANT`和`REVOKE`语句所授予的权限的名称显示在下表，还有在授权表中每个权限的表列名称和每个权限应用的领域（上下文）。

![此处输入图片的描述](img/4f23b1a4c471931ffba1839c1f8d7be6.jpg)

当从早期的没有`CREATE VIEW`、`SHOW VIEW`、`CREATE ROUTINE`、`ALTER ROUTINE``和 EXECUTE`权限的版本的 MySQL 中升级到较高版本时，要想使用这些权限，你必须运行 MySQL 提供的`mysql_fix_privilege_tables`脚本来[升级授权表](http://doc.mysql.cn/mysql5/refman-5.1-zh.html-chapter/installing.html#upgrading-grant-tables)。

如果启用了二进制日志，要想创建或修改保存的程序，你还需要具有`SUPER`权限。

通过`CREATE`和`DROP`权限，你可以创建新数据库和表，或删除(移掉)已有数据库和表。如果你将 mysql 数据库中的`DROP`权限授予某用户，该用户就可以删掉 MySQL 访问权限保存的数据库。

`SELECT`、`INSERT`、`UPDATE`和`DELETE`权限允许你对数据库中已经存在的表实施操作。

`SELECT`语句只有在真正从一个表中检索行时才需要`SELECT`权限。一些`SELECT`语句可以不需要具有访问表的权限，甚至没有任何到服务器上的数据库里的存取任何东西的许可。例如，你可使用 mysql 客户端作为一个简单的计算器来评估未引用表的表达式：

```sql
mysql> SELECT 1+1;
mysql> SELECT PI()*2; 
```

![此处输入图片的描述](img/1fe010606ad291fbb995cb246f67c6cf.jpg)

`INDEX`权限允许你创建或删除索引，`INDEX`适用已有表。如果你具有某个表的`CREATE`权限，你可以在`CREATE TABLE`语句中加入索引定义。

通过`ALTER`权限，你可以使用`ALTER TABLE`来更改表的结构或者重新命名表。

需要`CREATE ROUTINE`权限来创建需要被储存的程序（函数和程序），`ALTER ROUTINE`权限用来更改和删除保存好的程序，`EXECUTE`执行保存好的程序。

`GRANT`权限允许你把你自己拥有的那些权限授给其他的用户。可以应用于数据库、表和储存程序。

`FILE`权限给予你使用`LOAD DATA INFILE 和 SELECT ... INTO OUTFILE`语句对服务器上的文件进行读写，任何被授予`FILE`权限的用户都能对 MySQL 服务器能读或写的任何文件进行读写操作。(说明用户可以读任何数据库目录下的文件，因为服务器可以访问这些文件）。`FILE`权限允许用户在 MySQL 服务器具有写权限的目录下创建新文件，但是不能覆盖已有文件。

其余的权限用于管理性操作，它使用`mysqladmin`程序或 SQL 语句实现。下表显示每个管理性权限允许你执行的`mysqladmin`命令：

![此处输入图片的描述](img/6d6fcf911b694412943e6f423ae4c17a.jpg)

`reload`命令告诉服务器将授权表重新读入内存。`flush-privileges`等价于`reload`，`refresh`命令清空所有打开的表并关闭记录文件，其它`flush-xxx`命令执行类似`refresh`的功能，但是范围更有限，因此在某些情况下可能更好用；例如，如果你只是想清空记录文件，`flush-logs`相比`refresh`是更好的选择。

`shutdown`命令关掉服务器。只能从`mysqladmin`发出命令，而且没有相应的 SQL 语句。

`processlist`命令显示在服务器内执行的线程信息（即其它账户相关的客户端执行的语句）。`kill`命令用于杀死服务器线程。你总是能显示或杀死你自己的线程，但是你需要`PROCESS`权限来显示或杀死其他用户和`SUPER`权限启动的线程。

拥有`CREATE TEMPORARY TABLES`权限便可以使用`CREATE TABLE`语句中的关键字`TEMPORARY`。 拥有`LOCK TABLES`权限便可以直接使用`LOCK TABLES`语句来锁定你拥有`SELECT`权限的表。包括使用写锁定，可以防止他人读锁定的表。

拥有`REPLICATION CLIENT`权限便可以使用`SHOW MASTER STATUS`和`SHOW SLAVE STATUS`。

`REPLICATION SLAVE`权限授予子服务器以该账户连接主服务器后可以执行`replicate`操作。没有这个权限，子服务器不能对主服务器上的数据库发出更新请求。

拥有`SHOW DATABASES`权限便允许账户使用`SHOW DATABASE`语句来查看数据库名。没有该权限的账户只能看到他们所具有权限的部分数据库， 如果数据库用`--skip-show-database`选项启动，则根本不能使用这些语句。请注意全局权限指数据库的权限。

总的说来，授予权限给需要他们的那些用户是好主意，但是你应该在授予`FILE`和管理权限时给定特定的警告：

*   `FILE`权限可以任意的用于将服务器主机上 MySQL 能读取的任何文件读入到数据库表中。包括任何人可读的文件和服务器数据目录中的文件。使用`SELECT`访问数据库表，然后将其内容传输到客户端上。
*   `GRANT`权限允许用户将他们的权限给其他用户。拥有不同的权限但有`GRANT`权限的两个用户可以合并权限。 `ALTER`权限可以用于通过重新命名表来推翻权限系统。
*   `SHUTDOWN`权限通过终止服务器可以随意的完全拒绝为其他用户服务。
*   `PROCESS`权限能被用来察看当前执行的查询的明文文本，包括设定或改变密码的查询。 `SUPER`权限能用来终止其它用户或更改服务器的操作方式。
*   授给 mysql 数据库本身的权限能用来改变密码和其他访问权限信息。密码会被加密后存储，所以恶意的用户不能简单地读取他们以知道明文密码。然而，具有`user`表`Password`列写访问权限的用户可以更改账户的密码，并可以用该账户连接 MySQL 服务器。

你不能用 MySQL 权限系统做到的一些事情：

*   你不能确切地指定某个给定的用户应该被拒绝访问。即，你不能确切地匹配用户然后拒绝连接。
*   你不能指定用户它有权创建立或删除数据库中的表，但不能创建或删除数据库本身。

## 二、MySQL 用户账户管理

### 2.1 向 MySQL 增加新用户账户

可以用两种方式创建 MySQL 账户：

1.  使用`GRANT`语句
2.  直接操作 MySQL 授权表

最好的方法是使用`GRANT`语句，因为这样更精确，错误少。

创建账户的其它方法是使用 MySQL 账户管理功能的第三方程序。`phpMyAdmin`即是一个第三方程序。

下面的示例说明如何使用 MySQL 客户端程序来设置新用户。

首先，在启动 mysql 服务后，使用 MySQL 程序以 MySQL 的`root`用户来连接服务器：

```sql
shell> mysql -u root 
```

如果你为`root`账户指定了密码，还需要为该 MySQL 命令和本节中的其它命令提供--password 或-p 选项（实验楼环境是免密码登陆）。

![此处输入图片的描述](img/9ac96a3fd4ca0acaacb065059a7ac3d2.jpg)

以`root`连接到服务器上后，可以添加新账户。下面的语句表示使用`GRANT`来设置四个新账户：

```sql
mysql> GRANT ALL PRIVILEGES ON *.* TO 'monty'@'localhost'
    ->     IDENTIFIED BY 'some_pass' WITH GRANT OPTION;

mysql> GRANT ALL PRIVILEGES ON *.* TO 'monty'@'%'
    ->     IDENTIFIED BY 'some_pass' WITH GRANT OPTION;

mysql> GRANT RELOAD,PROCESS ON *.* TO 'admin'@'localhost';

mysql> GRANT USAGE ON *.* TO 'dummy'@'localhost'; 
```

![此处输入图片的描述](img/b6d99e15cb764484cd447edd69d587cd.jpg)

`GRANT`命令说明：

`ALL PRIVILEGES` 是表示所有权限，你也可以使用`select`、`update`等权限。

`ON` 用来指定权限针对哪些库和表。

`*.*` 中前面的`*`号用来指定数据库名，后面的`*`号用来指定表名。

`TO` 表示将权限赋予某个用户。

`'monty'@'localhost'` 表示`monty`用户，`@`后面接限制的主机，可以是`IP`、`IP`段、域名以及`%`，`%`表示任何地方。（**注意：**这里`%`有的版本不包括本地，以前碰到过给某个用户设置了`%`允许任何地方登录，但是在本地登录不了，这个和版本有关系，遇到这个问题再加一个`localhost`的用户就可以了。）

`IDENTIFIED BY`指定用户的登录密码。

`WITH GRANT OPTION` 这个选项表示该用户可以将自己拥有的权限授权给别人。（**注意：**经常有人在创建操作用户的时候不指定`WITH GRANT OPTION`选项导致后来该用户不能使用`GRANT`命令创建用户或者给其他用户授权。）

**备注：**可以使用`GRANT`重复给用户添加权限，权限叠加，比如你先给用户添加了一个`SELECT`权限，然后又给用户添加了一个`INSERT`权限，那么该用户就同时拥有了`SELECT`和`INSERT`权限。

上述用`GRANT`语句创建的账户具有以下属性：

其中两个账户有相同的用户名`monty`和密码`some_pass`。两个账户均为超级用户账户，具有完全的权限可以做任何事情。一个账户 (`'monty'@'localhost'`)只用于从本机连接。另一个账户`('monty'@'%')`可用于从其它主机连接。请注意`monty`的两个账户必须能从任何主机以`monty`连接。当不具有`localhost`账户的用户`monty`从本机连接时，`mysql_install_db`创建的`localhost`匿名用户账户将具有优先权限。结果是，`monty`将被视为匿名用户。原因是在`user`表中匿名用户账户的`Host`列值比`monty'@'%`账户更具体（`%`相当于空`HOST`），这样在`user`表中排序是排在前面。

第三个账户有用户名`admin`，但没有密码。该账户只能用于本机连接。上面第三条语句中授予`admin`用户`RELOAD`和`PROCESS`管理权限；这些权限允许`admin`用户执行`mysqladmin reload`、`mysqladmin refresh`和`mysqladmin flush-xxx`以及`mysqladmin processlist`命令；但是它未被授予访问数据库的权限；你可以通过`GRANT`语句添加此类权限。

第四个账户有用户名`dummy`，但是也没有密码，该账户只用于本机连接，通过`GRANT`语句中赋予的`USAGE`权限，你可以创建账户而不授予任何权限；它可以将所有全局权限设为`'N'`。假定你将在以后将具体权限授予该账户。

除了`GRANT`语句，你可以直接用`INSERT`语句创建相同的账户，然后使用`FLUSH PRIVILEGES`刷新权限来告诉服务器重载授权表：

```sql
# 以 root 账户连接到服务器上的 mysql 数据库
shell> mysql -u root mysql

mysql> INSERT INTO user
    -> VALUES('localhost','monty',PASSWORD('some_pass'),
    -> 'Y','Y','Y','Y','Y','Y','Y','Y','Y','Y','Y','Y','Y','Y');

mysql> INSERT INTO user
    -> VALUES('%','monty',PASSWORD('some_pass'),
    -> 'Y','Y','Y','Y','Y','Y','Y','Y','Y','Y','Y','Y','Y','Y'); 
```

**注意：**在实验楼环境里操作`INSERT INTO`语句插入用户会报错：

`ERROR 1136 (21S01): Column count doesn't match value count at row 1`

意思是给表`user`中插入的数据列数跟表的列数不匹配。由于本教程的版本是 MySQL5.1 而实验楼环境安装的版本是 MySQL5.1，可能`user`表的字段随着版本的更新而更新。那么查看该表中字段信息：

```sql
mysql> SHOW FULL COLUMNS FROM user; 
```

获取字段的以下信息：

*   `Field` ：字段名
*   `Type`：字段类型
*   `Collation`：字符集（mysql 5.0 以上有）
*   `Null` ：是否可以为`NULL`
*   `Key`：索引（`PRI,unique,index`)
*   `Default`：缺省值
*   `Extra`：额外（是否 `auto_increment`)
*   `Privileges`：权限
*   `Comment`：备注（mysql 5.0 以上有)

（下面的图由于表`user`的字段过多，所有只截取了开头和结尾部分。） ![此处输入图片的描述](img/d9d251d2bed577170384ed44c146688f.jpg) ![此处输入图片的描述](img/8bec2b20ec2c044c6b0667c31913453a.jpg)

我们可以看到`user`表一共有 42 个字段，那么字段如果是缺省（`Default`值存在）就意味插入数据的时候不需要考虑该字段，可以看到字段名为`Host`，`User`，`Password`，`ssl_type`以及`plugin`的字段是没有缺省以外，其他字段都允许有缺省，因此我们在插入`user`表的时候注意给不缺省的字段插入值即可。（后来证明只要`INSERT`语句在指明字段名的情况下无论怎么插入数据都行。）

综上所述，上面插入表操作出错的原因就是在没有指定变量名的时候插入数据必须得全部 42 个字段依次赋值，那么爱动脑经的人一般不会重复输入并插入某个数，因此我们不鼓励上述插入语句中的前两种`INSERT`方法。

尝试执行第三种`INSERT`方法：

```sql
mysql> INSERT INTO user SET Host='localhost',User='admin',
    -> Reload_priv='Y', Process_priv='Y';

mysql> INSERT INTO user (Host,User,Password)
    -> VALUES('localhost','dummy','');

mysql> FLUSH PRIVILEGES; 
```

完美插入数据！ ![此处输入图片的描述](img/f6eb2b42bb3fafc76694b6ea6882d326.jpg)

继续学习~

当你用`INSERT`创建账户时使用`FLUSH PRIVILEGES`的原因是告诉服务器重读授权表。否则，只有在重启服务器后更新才会被注意到。使用 `GRANT`，则不需要使用`FLUSH PRIVILEGES`。

前面第一个`INSERT`操作中，使用`PASSWORD()`函数是为了加密密码。`GRANT`语句已经为你加密密码，因此不需要`PASSWORD()`。

`'Y'`值启用账户权限。对于 admin 账户，还可以使用更加可读的`INSERT`扩充的语法（使用`SET`）。

在为添加`dummy`账户的`INSERT`语句中，只给该用户插入`Host`、`User`和`Password`数据。那些没有被指定插入的字段就会被默认值`'N'`填充。

请注意要设置超级用户账户，只需要创建一个权限列设置为'Y'的`user`表记录。`user`表权限为全局权限，因此其它授权表不再需要记录。

下面的例子创建 3 个账户，允许它们访问专用数据库。每个账户的用户名为`custom`，密码为`obscure`。

要想用`GRANT`创建账户，使用下面的语句：

```sql
mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP
    -> ON bankaccount.*
    -> TO 'custom'@'localhost'
    -> IDENTIFIED BY 'obscure';

mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP
    -> ON expenses.*
    -> TO 'custom'@'whitehouse.gov'
    -> IDENTIFIED BY 'obscure';

mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP
    -> ON customer.*
    -> TO 'custom'@'server.domain'
    -> IDENTIFIED BY 'obscure'; 
```

![此处输入图片的描述](img/2824fe0ec3149d842f5105420f0d3a14.jpg)

这 3 个账户分别可以用于：

*   第 1 个账户可以访问`bankaccount`数据库，但只能本机访问。

*   第 2 个账户可以访问`expenses`数据库，但只能从主机访问`whitehouse.gov`。

*   第 3 个账户可以访问`customer`数据库，但只能从主机访问`server.domain`。

要想不用`GRANT`设置`custom`账户，使用`INSERT`语句直接修改授权表：

```sql
# 插入用户信息到`user`表
mysql> INSERT INTO user (Host,User,Password)
    -> VALUES('localhost','custom',PASSWORD('obscure'));

mysql> INSERT INTO user (Host,User,Password)
    -> VALUES('whitehouse.gov','custom',PASSWORD('obscure'));

mysql> INSERT INTO user (Host,User,Password)
    -> VALUES('server.domain','custom',PASSWORD('obscure'));

# 插入用户信息到`db`表  
mysql> INSERT INTO db
    -> (Host,Db,User,Select_priv,Insert_priv,
    -> Update_priv,Delete_priv,Create_priv,Drop_priv)
    -> VALUES('localhost','bankaccount','custom',
    -> 'Y','Y','Y','Y','Y','Y');

mysql> INSERT INTO db
    -> (Host,Db,User,Select_priv,Insert_priv,
    -> Update_priv,Delete_priv,Create_priv,Drop_priv)
    -> VALUES('whitehouse.gov','expenses','custom',
    -> 'Y','Y','Y','Y','Y','Y');

mysql> INSERT INTO db
    -> (Host,Db,User,Select_priv,Insert_priv,
    -> Update_priv,Delete_priv,Create_priv,Drop_priv)
    -> VALUES('server.domain','customer','custom',
    -> 'Y','Y','Y','Y','Y','Y');

# 刷新授权表    
mysql> FLUSH PRIVILEGES; 
```

前 3 个`INSERT`语句在`user`表中加入用户信息记录，允许用户`custom`从各种主机用给定的密码进行连接，但不授予全局权限(所有权限设置为 默认值`'N'`)。后面 3 个`INSERT`语句在`db`表中加入记录，为`custom`授予`bankaccount`、`expenses`和`customer`数据库权限，但只能从指定的主机发出访问。通常若直接修改授权表，则应告诉服务器用`FLUSH PRIVILEGES`重载授权表，使权限更改生效。

如果你想要让某个用户从给定域内的所有机器访问(例如，`mydomain.com`)，你可以在账户名的主机部分使用含`‘%’`通配符的`GRANT`语句：

```sql
mysql> GRANT ...
    -> ON *.*
    -> TO 'myname'@'%.mydomain.com'
    -> IDENTIFIED BY 'mypass'; 
```

也可以通过直接修改授权表来实现：

```sql
mysql> INSERT INTO user (Host,User,Password,...)
    -> VALUES('%.mydomain.com','myname',PASSWORD('mypass'),...);
mysql> FLUSH PRIVILEGES; 
```

### 2.2 从 MySQL 删除用户账户

要想移除账户，应使用`DROP USER`语句。

### 2.3 限制账户资源

限制 MySQL 服务器资源使用的一个方法是将`max_user_connections`系统变量设置为非零值。但是，该方法严格限于全局，不允许管理具体的账户。并且，它只限制使用单一账户同时连接的数量，而不是客户端连接后的操作。许多 MySQL 管理员对两种类型的控制均感兴趣，特别是`Internet`服务提供者。

在 MySQL 5.1 中,你可以为具体账户限制下面的服务器资源：

*   账户每小时可以发出的查询数

*   账户每小时可以发出的更新数

*   账户每小时可以连接服务器的次数

客户端可以执行的语句根据查询限制来记数。只有修改数据库或表的语句根据更新限制来记数。

还可以限制每个账户的同时连接服务器的连接数。

本文中的账户为`user`表中的单个记录。根据`User`和`Host`列值唯一识别每个账户。

作为使用该特性的先决条件，mysql 数据库的`user`表必须包含资源相关的列。资源限制保存在`max_questions`、`max_updates`、`max_connections`和`max_user_connections`列内。如果`user`表没有这些列，则必须对它进行升级。

要想用`GRANT`语句设置资源限制，使`WITH`子句来命名每个要限制的资源和根据每小时记数的限制值。例如，要想只以限制方式创建可以访问`customer`数据库的新账户，执行该语句：

```sql
mysql> GRANT ALL ON customer.* TO 'francis'@'localhost'
    -> IDENTIFIED BY 'frank'
    -> WITH MAX_QUERIES_PER_HOUR 20
    -> MAX_UPDATES_PER_HOUR 10
    -> MAX_CONNECTIONS_PER_HOUR 5
    -> MAX_USER_CONNECTIONS 2; 
```

限制类型不需要全部在`WITH`子句中命名，但已经命名的可以按任何顺序。每个每小时限制值均应为整数，代表每小时的记数。如果`GRANT`语句没有`WITH`子句，则每个限制值设置为默认值零（即没有限制）。对于`MAX_USER_CONNECTIONS`，限制为整数，表示账户一次可以同时连接的最大连接数。如果限制设置为 默认值零，则根据`MAX_USER_CONNECTIONS`系统变量确定该账户可以同时连接的数量。

要想设置或更改已有账户的限制，在全局级别使用`GRANT USAGE`语句（`ON .`）。下面的语句可以将`francis`的查询限制更改为`100`：

```sql
mysql> GRANT USAGE ON *.* TO 'francis'@'localhost'
    -> WITH MAX_QUERIES_PER_HOUR 100; 
```

该语句没有改变账户的已有权限，只修改了指定的限制值。

要想取消已有限制，将该值设置为零。例如，要想取消 francis 每小时可以连接的次数的限制，使用该语句：

```sql
mysql> GRANT USAGE ON *.* TO 'francis'@'localhost'
    -> WITH MAX_CONNECTIONS_PER_HOUR 0; 
```

在账户使用资源时如果有非零限制，则对资源使用进行记数。

服务器运行时，它统计每个账户使用资源的次数。如果账户在最后一个小时的连接次数达到限制，该账户的进一步的连接被拒绝。类似地，如果账户达到查询或更新次数的限制，进一步的查询或更新被拒绝。在这种情况下，会给出相关错误消息。

根据每个账户进行资源计算，而不是根据每个客户端。例如，如果你的账户的查询次数限制为`50`，你不能通过两个客户端同时连接服务器将连接次数限制增加到`100`。两个连接的查询会被一起计算。

可以为所有账户从全局重设当前的每小时资源使用记数，或单独重设给定的账户：

*   要想将所有账户当前的记数重设为零，可以执行`FLUSH USER_RESOURCES`语句。还可以通过重载授权表来重设记数(例如，使用`FLUSH PRIVILEGES`语句或`mysqladmin reload`命令)。

*   将具体账户的限制重新授予任何值，可以将它设置为零。要想实现，按照前面语句所述使用`GRANT USAGE`，并将限制值指定为该账户当前的限制值。

计数器重设不影响`MAX_USER_CONNECTIONS`限制。

当服务器启动时所有记数从零开始。

### 2.4 设置账户密码

可以用`mysqladmin`命令在命令行指定密码：

```sql
shell> mysqladmin -u user_name -h host_name password "newpwd" 
```

该命令为`Host`为`host_name`，`User`为`user_name`账户添加密码`newpwd`。

为账户赋予密码的另一种方法是执行`SET PASSWORD`语句：

```sql
mysql> SET PASSWORD FOR 'jeffrey'@'%' = PASSWORD('biscuit'); 
```

只有`root`等可以更新 mysql 数据库的用户可以更改其它用户的密码。如果你没有以匿名用户连接，省略`FOR`子句便可以更改自己的密码：

```sql
mysql> SET PASSWORD = PASSWORD('biscuit'); 
```

你还可以在全局级别使用`GRANT USAGE`语句(`ON .`)来指定某个账户的密码而不影响账户当前的权限：

```sql
mysql> GRANT USAGE ON *.* TO 'jeffrey'@'%' IDENTIFIED BY 'biscuit'; 
```

一般情况下最好使用上述方法来指定密码，你还可以直接修改 user 表：

要想在创建新账户时建立密码，在 Password 列提供一个值：

```sql
shell> mysql -u root mysql
mysql> INSERT INTO user (Host,User,Password)
  -> VALUES('%','jeffrey',PASSWORD('biscuit'));
mysql> FLUSH PRIVILEGES; 
```

要想更改已有账户的密码，使用`UPDATE`来设置`Password`列值：

```sql
mysql> UPDATE user SET Password = PASSWORD('bagel')
  -> WHERE Host = '%' AND User = 'francis';
mysql> FLUSH PRIVILEGES; 
```

当你使用`SET PASSWORD`、`INSERT`或`UPDATE`指定账户的密码时，必须用`PASSWORD()`函数对它进行加密。（唯一的特例是如果密码为空，你不需要使用`PASSWORD()`）。需要使用`PASSWORD()`是因为`user`表以加密方式保存密码，而不是明文。如果你忘记了，你可能会这样设置密码：

```sql
mysql> INSERT INTO user (Host,User,Password)
  -> VALUES('%','jeffrey','biscuit');
mysql> FLUSH PRIVILEGES; 
```

结果是密码`'biscuit'`保存到`user`表后没有被加密。当`jeffrey`使用该密码连接服务器时，值被加密并同保存在`user`表中的进行比较。但是，由于已经保存的`Password`值为字符串`'biscuit'`，因此比较将以失败告终，服务器拒绝连接：

```sql
shell> mysql -u jeffrey -p biscuit test 
```

![此处输入图片的描述](img/6b15949b91022aa850888ac83c7bb325.jpg)

如果你使用`GRANT ... IDENTIFIED BY`语句或`mysqladmin password`命令设置密码，它们均会加密密码。在这种情况下，不需要使用 `PASSWORD()`函数。

**注释：**`PASSWORD()`加密不同于`Unix`密码加密。

## 三、备份与恢复

### 3.1 数据库备份

由于 MySQL 表保存为文件方式会很容易备份。要想保持备份的一致性，需要对相关表执行`LOCK TABLES`操作，然后对表执行`FLUSH TABLES`。你只需要读锁定；这样当你复制数据库目录中的文件时，允许其它客户继续查询表。需要`FLUSH TABLES`语句来确保开始备份前将所有激活的索引页写入硬盘。

如果你想要进行 SQL 级别的表备份，你可以使用`SELECT INTO ...OUTFILE`或`BACKUP TABLE`。对于`SELECT INTO ...OUTFILE`， 输出的文件不能先存在。对于`BACKUP TABLE`也如此，因为覆盖完整的文件会有安全风险。

对于`InnoDB`表，可以进行在线备份，不需要对表进行锁定。

MySQL 支持增量备份：需要用`--log-bin`选项来启动服务器以便启用二进制日志。当想要进行增量备份时(包含上一次完全备份或增量备份之后的所有更改)，应使用`FLUSH LOGS`回滚二进制日志。然后，你需要将从最后的完全或增量备份的某个时刻到最后某个点的所有二进制日志复制到备份位置。这些二进制日志为增量备份；恢复时，按照下面的解释应用。下次进行完全备份时，还应使用`FLUSH LOGS`或`mysqlhotcopy --flushlogs`回滚二进制日志。

如果 MySQL 服务器为复制子服务器时，则无论选择什么备份方法，当备份子机数据时，还应备份`master.info`和`relay-log.info`文件。恢复了子机数据后，需要这些文件来继续复制。如果子机执行复制`LOAD DATA INFILE`命令，你应用`--slave-load-tmpdir`选项备份指定的目录中的`SQL_LOAD-*`文件。（如果未指定，该位置默认为`tmpdir`变量值）。子机需要这些文件来继续复制中断的`LOAD DATA INFILE`操作。

如果必须恢复`MyISAM`表，先使用`REPAIR TABLE`或`myisamchk -r`来恢复，99.9%的情况下该方法可以生效。如果`myisamchk`恢复失败，试试下面的方法：

请注意只有添加`--log-bin`选项启动 MySQL 服务器从而启用二进制日志它才生效。

如果 MySQL 服务器启用了二进制日志，你可以使用`mysqlbinlog`工具来恢复从指定的时间点开始(例如，从你最后一次备份)直到现在或另一个指定的时间点的数据。

1.  恢复原`mysqldump`备份，或二进制备份。

2.  执行下面的命令重新更新二进制日志： `shell> mysqlbinlog hostname-bin.[0-9]* | mysql`

在某些情况下，你可能只想要从某个位置重新运行某些二进制日志。（通常你想要根据恢复备份的日期重新运行所有二进制日志）。

还可以对具体文件进行选择备份：

*   要想复制表，使用`SELECT * INTO OUTFILE 'file_name' FROM tbl_name`语句。

*   要想重载表，使用`LOAD DATA INFILE 'file_name' REPLACE ...`载入并恢复表。要避免复制记录，表必须有`PRIMARY KEY`或一个`UNIQUE`索引。当新记录复制唯一键值的旧记录时，`REPLACE`关键字可以将旧记录替换为新记录。

如果备份时遇到服务器性能问题，有用的一个策略是在子服务器而不是主服务器上建立复制并执行备份。

如果使用`Veritas`文件系统，可以这样备份：

1.  从客户端程序执行`FLUSH TABLES WITH READ LOCK`语句。

2.  从另一个`shell`执行`mount vxfs snapshot`命令。

3.  从第一个客户端执行`UNLOCK TABLES`。

4.  从快照复制文件。

5.  卸载快照。

### 3.2 备份与恢复策略示例

#### 3.2.1 备份策略

我们都知道必须按计划定期进行备份。可以用一些工具(某个时间点的数据快照)完全备份 MySQL。例如，`InnoDB Hot Backup`为`InnoDB`数据文件提供在线非数据块物理备份，`mysqldump`提供在线逻辑备份。

假定我们在星期日下午 1 点进行了备份，此时负荷较低。下面的命令可以完全备份所有数据库中的所有`InnoDB`表：

```sql
shell> mysqldump --single-transaction --all-databases backup_sunday_1_PM.sql 
```

以上方法是在线非数据块备份，不会干扰对表的读写。我们假定我们以前的表为`InnoDB`表，因此`--single-transaction`一致性地表，并且保证`mysqldump`所看见的数据不会更改。(其它客户端对 InnoDB 表进行的更改不会被 mysqldump 进程看见）。如果我们还有其它类型的表，我们必须假定在备份过程中它们不会更改。例如，对于 mysql 数据库中的 MyISAM 表，我们必须假定在备份过程中没有对 MySQL 账户进行管理更改。

`mysqldump`命令产生的`.sql`文件包含一系列`SQL INSERT`语句，可以用来重载转储的表。

进行完全备份的时候有时不方便，因为会产生大的备份文件并需要花时间来生成。从某个角度来看，完全备份并不理想，因为每个成功的完全备份都包括所有数据，甚至包括自从上一次完全备份以来没有被更改的部分。完成了初始完全备份后，进行增量备份会更有效。这样备份文件要小得多，备份时间也较短。缺点是，恢复时不能只重载完全备份来恢复数据。还必须要用增量备份来恢复增量更改。

要想进行增量备份，我们需要保存增量更改。应使用`--log-bin`选项启动 MySQL 服务器，以便更新数据时将这些更改保存到文件中。该选项启用二进制日志，因此服务器会将每个更新数据的 SQL 语句写入到 MySQL 二进制日志。让我们看看用`--log-bin`选项启动的已经运行多日的 MySQL 服务器的数据目录。找到以下 MySQL 二进制日志文件：

![此处输入图片的描述](img/4fb5ce4d0f7f81fbed9d7b6bb73b1311.jpg)

每次重启，MySQL 服务器都会使用以上序列中的下一个编号创建一个新的二进制日志文件。当服务器运行时，你还可以通过执行`FLUSH LOGS SQL`语句或`mysqladmin flush-logs`命令，告诉服务器关闭当前的二进制日志文件并创建一个新文件。`mysqldump`也有一个选项来清空日志。数据目录中的`.index`文件包含该目录下所有 MySQL 二进制日志的清单，该文件用于复制。

恢复时 MySQL 二进制日志很重要，因为它们是增量备份。如果进行完全备份时确保清空了日志，则后面创建的二进制日志文件包含了备份后的所有数据更改。让我们稍稍修改前面的`mysqldump`命令，让它在完全备份时能够清空 MySQL 二进制日志，以便转储文件包含包含新的当前二进制日志：

```sql
shell> mysqldump --single-transaction --flush-logs --master-data=2
           --all-databases > backup_sunday_1_PM.sql 
```

执行该命令后，数据目录则包含新的二进制日志文件。产生的`.sql`文件包含下列行：

```sql
-- Position to start replication or point-in-time recovery from
-- CHANGE MASTER TO MASTER_LOG_FILE='gbichot2-bin.000007',MASTER_LOG_POS=4; 
```

因为`mysqldump`命令可以执行完全备份，以上行代表两件事情：

1.  `.sql`文件包含所有写入`gbichot2-bin.000007`二进制日志文件或最新的文件之前的更改。

2.  备份后所记录的所有数据更改不出现在`.sql`中，但会出现在`gbichot2-bin.000007`二进制日志文件或最新的文件中。

在星期一下午 1 点，我们可以清空日志并开始根据新的二进制日志文件来创建增量备份。例如，执行`mysqladmin flush-logs`命令创建`gbichot2-bin.000008`。星期日下午 1 点的完全备份和星期一下午 1 点之间的所有更改为文件`gbichot2-bin.000007`。该增量备份很重要，因此最好将它复制到安全的地方。（例如，备份到磁带或 DVD 上，或复制到另一台机器上）。在星期二下午 1 点，执行另一个`mysqladmin flush-logs`命令，这样星期一下午 1 点和星期二下午 1 点之间的所有更改为文件`gbichot2-bin.000008`(也应复制到某个安全的地方)。

MySQL 二进制日志占据硬盘空间。要想释放空间，应随时清空。操作方法是删掉不再使用的二进制日志，例如进行完全备份时输入以下命令：

```sql
shell> mysqldump --single-transaction --flush-logs --master-data=2
       --all-databases --delete-master-logs > backup_sunday_1_PM.sql 
```

**注释：**如果你的服务器为复制主服务器，用`mysqldump`方法中的 `--delete-master-logs`选项删掉 MySQL 二进制日志很危险，因为从服务器可能还没有完全处理该二进制日志的内容。关于这一点，`PURGE MASTER LOGS`语句的描述中解释了为什么在删掉 MySQL 二进制日志之前应进行确认一下。

#### 3.2.2 为恢复进行备份

现在假设在星期三上午 8 点出现了灾难性崩溃，需要使用备份文件进行恢复。恢复时，我们首先恢复最后的完全备份(从星期日下午 1 点开始)。完全备份文件是一系列 SQL 语句，因此恢复它很容易：

```sql
shell> mysql < backup_sunday_1_PM.sql 
```

接下来使得数据恢复到星期日下午 1 点的状态。要想恢复从那时起的更改，我们必须使用增量备份，也就是`gbichot2-bin.000007`和`gbichot2-bin.000008`这两个二进制日志文件。根据需要从备份处取得这些文件，然后按下述方式处理：

```sql
shell> mysqlbinlog gbichot2-bin.000007 gbichot2-bin.000008 | mysql 
```

我们现在将数据恢复到星期二下午 1 点的状态，但是从该时刻到崩溃之间的数据仍然有丢失；要实现恢复，我们需要 MySQL 服务器将 MySQL 二进制日志保存到安全的位置(`RAID disks, SAN, ...`)，应为与数据文件的保存位置不同的地方，保证这些日志不在被毁坏的硬盘上。(也就是，我们可以用`--log-bin`选项启动服务器，指定一个其它物理设备上的与数据目录不同的位置。这样，即使包含该目录的设备丢失，日志也不会丢失）。如果我们执行了这些操作，我们手头上会有`gbichot2-bin.000009`文件，我们可以用它来恢复大部分最新的数据更改，而不会丢失星期二下午 1 点到崩溃时刻之间的数据。

#### 3.2.3 备份策略摘要

出现操作系统崩溃或电源故障时，`InnoDB`自己可以完成所有数据恢复工作。但为了确保你可以睡好觉，应遵从下面的指导：

*   一定用`--log-bin`或甚至`--log-bin=log_name`选项启动 MySQL 服务器，其中日志文件名位于某个安全媒介上，不同于数据目录所在驱动器。如果你有这样的安全媒介，最好进行硬盘负载均衡(这样能够提高性能)。

*   定期进行完全备份，使用`mysqldump`命令进行在线非数据块备份。

*   用`FLUSH LOGS`或`mysqladmin flush-logs`清空日志进行定期增量备份。

### 3.3 自动恢复

**注意：**由于实验楼环境中默认的 mysql 配置文件中把`log_bin`等日志设置选项给注释了，因此需要更改文件`my.cnf`：

```sql
# 登陆到 root 账户
shell> sudo -s
shell> vi /etc/mysql/my.cnf 
```

![此处输入图片的描述](img/1fbba48ec816cbe62d03c1cc3748d8c1.jpg)

修改后然后保存`my.cnf`文件，重启 mysql 服务器，并查看日志是否启动：

```sql
shell> service mysql restart --log-bin
shell> mysql -u root
sql> show variables like 'log_%'; 
```

![此处输入图片的描述](img/f5b6062cc839793aa9e3163881bc4738.jpg)

继续~

要想确定当前的二进制日志文件的文件名，在命令行中加入下面的 MySQL 语句：

```sql
shell> mysql -u root -e 'SHOW BINLOG EVENTS \G' 
```

![此处输入图片的描述](img/f92c44e22bba7c9b0e776ddb368f0336.jpg)

#### 3.3.1 指定恢复时间

对于 MySQL 5，可以在`mysqlbinlog`语句中通过`--start-date`和`--stop-date`选项指定`DATETIME`格式的起止时间。举例说明，假设在今天上午 10:00(今天是 2015 年 8 月 6 日)，执行 SQL 语句来删除一个大表。要想恢复表和数据，你可以恢复前一晚上的备份，并从命令行输入以下命令：

```sql
shell> mysqlbinlog --stop-date="2015-8-6 10:01:00" /var/log/mysql/bin.123456 \
     | mysql -u root -p mypwd 
```

该命令将恢复截止到在`--stop-date`选项中以`DATETIME`格式给出的日期和时间的所有数据。

在以上行中，从上午 10:01 登录的 SQL 语句将运行。结合执行前夜的转储文件和`mysqlbinlog`的两行命令可以将所有数据恢复到上午 10:00 前一秒钟。你应检查日志以确保时间确切。下一节介绍如何实现。

#### 3.3.2 指定恢复位置

也可以不指定日期和时间，而使用`mysqlbinlog`的选项`--start-position`和`--stop-position`来指定日志位置。它们的作用与起止日选项相同，不同的是给出了从日志起的位置号。使用日志位置是更准确的恢复方法，特别是当由于破坏性 SQL 语句同时发生许多事务的时候。要想确定位置号，可以运行`mysqlbinlog`寻找执行了不期望的事务的时间范围，但应将结果重新指向文本文件以便进行检查。操作方法为：

```sql
shell> mysqlbinlog --start-date="2014-10-29 9:55:00" --stop-date="2014-10-29 10:05:00" \
      /var/log/mysql/bin.123456 > /tmp/mysql_restore.sql 
```

该命令将在`/tmp`目录创建小的文本文件，将显示执行了错误的 SQL 语句时的 SQL 语句。你可以用文本编辑器打开该文件，寻找你不要想重复的语句。如果二进制日志中的位置号用于停止和继续恢复操作，应进行注释。用`log_pos 加一个数字来标记位置。使用位置号恢复了以前的备份文件后，你应从命令行输入下面内容：

```sql
shell> mysqlbinlog --stop-position="368312" /var/log/mysql/bin.123456 \
    | mysql -u root -pmypwd 

shell> mysqlbinlog --start-position="368315" /var/log/mysql/bin.123456 \
    | mysql -u root -pmypwd \ 
```

上面的第 1 行将恢复到停止位置为止的所有事务。第二行将恢复从给定的起始位置直到二进制日志结束的所有事务。因为`mysqlbinlog`的输出包括每个 SQL 语句记录之前的`SET TIMESTAMP`语句，恢复的数据和相关 MySQL 日志将反映事务执行的原时间。

## 四、日志文件

### 4.1 错误日志

错误日志文件包含了当`mysqld`启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息。

如果`mysqld`莫名其妙地死掉并且需要`mysqld_safe`重新启动它，那么`mysqld_safe`在错误日志中写入一条`restarted mysqld`消息。如果`mysqld`注意到需要自动检查或着修复一个表，则错误日志中将写入这条消息。

在一些操作系统中，如果 mysqld 死掉，错误日志会包含堆栈跟踪信息。跟踪信息可以用来确定`mysqld`死掉的地方。

可以用`--log-error[=file_name]`选项来指定`mysqld`保存错误日志文件的位置。如果没有给定`file_name`值，`mysqld`会在数据目录中使用日志名`host_name.err` 写入日志文件，如果你执行`FLUSH LOGS`，日志会使用`-old`重新命名后缀并且`mysqld`创建一个新的空日志文件。(如果未给出`--log-error`选项，则不会重新命名）。

如果不指定`--log-error`，或者(在`Windows`中)如果你使用`--console`选项，错误被写入标准错误输出`stderr`。通常标准输出为你的终端。

在`Windows`中，如果未给出`--console`选项，错误输出总是写入`.err`文件。

### 4.2 通用查询日志

如果你想要知道`mysqld`内部发生了什么，你应该用`--log[=file_name]`或`-l [file_name]`选项启动服务器。如果没有给定`file_name`的值， 默认名是`host_name.log`。所有连接和语句都会被记录到日志文件。当你怀疑在客户端发生了错误并想确切地知道该客户端发送给`mysqld`的语句时，该日志可能非常有用。 `mysqld`按照它接收的语句顺序记录查询日志，这可能与执行的顺序不同。这与更新日志和二进制日志不同，它们在执行后但是是在任何一个锁释放之前记录日志。(查询日志还包含所有语句，而二进制日志不包含只查询数据的语句）。

服务器重新启动和日志刷新不会产生新的通用查询日志文件(尽管刷新会关闭并重新打开一般查询日志文件)。在`Unix`中，你可以通过下面的命令重新命名文件并创建一个新的日志文件：

```sql
shell> mv hostname.log hostname-old.log
shell> mysqladmin flush-logs
shell> cp hostname-old.log to-backup-directory
shell> rm hostname-old.log 
```

在`Windows`中，服务器打开日志文件期间你不能重新命名日志文件，你必须先停止服务器然后重新命名日志文件，然后重启服务器来创建新的日志文件。

### 4.3 二进制日志

二进制日志以一种更有效的格式，并且是事务安全的方式包含更新日志中可用的所有信息。

二进制日志包含了所有更新了数据或者已经潜在更新了数据（例如，没有匹配任何行的一个`DELETE`）的所有语句。语句以`“事件”`的形式保存，它描述数据更改。

**注释：**二进制日志已经代替了老的更新日志，更新日志在 MySQL 5.1 中不再使用。

二进制日志还包含关于每个更新数据库的语句的执行时间信息。它不包含没有修改任何数据的语句。如果你想要记录所有语句（例如，为了识别有问题的查询），你应使用一般查询日志。

二进制日志的主要目的是在恢复使能够最大可能地更新数据库，因为二进制日志包含备份后进行的所有更新。

二进制日志还用于在主复制服务器上记录所有将发送给从服务器的语句。

运行服务器时若启用二进制日志则性能大约慢`1%`。但是，二进制日志的好处即用于恢复并允许设置复制超过了这个小小的性能损失。

当用`--log-bin[=file_name]`选项启动服务器时，`mysqld`写入包含所有更新数据的 SQL 命令的日志文件。如果未给出`file_name`值， 默认名为`-bin`后面所跟的主机名。如果给出了文件名，但没有包含路径，则文件被写入数据目录。

如果你在日志名中提供了扩展名(例如，`--log-bin=file_name.extension`)，则扩展名被悄悄除掉并忽略。

`mysqld`在每个二进制日志名后面添加一个数字扩展名。每次你启动服务器或刷新日志时该数字则增加。如果当前的日志大小达到`max_binlog_size`时，还会自动创建新的二进制日志。如果你正使用大的事务，二进制日志大小还会超过`max_binlog_size`。（事务要全写入一个二进制日志中，绝对不要写入不同的二进制日志中。）

为了能够知道还使用了哪个不同的二进制日志文件，`mysqld`还创建一个二进制日志索引文件，包含所有使用的二进制日志文件的文件名。默认情况下与二进制日志文件的文件名相同，扩展名为`'.index'`。你可以用`--log-bin-index[=file_name]`选项更改二进制日志索引文件的文件名。当`mysqld`在运行时，不应手动编辑该文件；如果这样做将会使`mysqld`变得混乱。

可以用`RESET MASTER`语句删除所有二进制日志文件，或用`PURGE MASTER LOGS`只删除部分二进制文件。

二进制日志格式有一些已知限制，会影响从备份恢复。

可以使用下面的`mysqld`选项来影响记录到二进制日志内的内容：

*   `--binlog-do-db=db_name`

    告诉主服务器，如果当前的数据库(即`USE`选定的数据库)是`db_name`，应将更新记录到二进制日志中。其它所有没有明显指定的数据库 被忽略。如果使用该选项，你应确保只对当前的数据库进行更新。

    对于`CREATE DATABASE`、`ALTER DATABASE`和`DROP DATABASE`语句，有一个例外，即通过操作的数据库来决定是否应记录语句，而不是用当前的数据库。

    一个不能按照期望执行的例子：如果用`binlog-do-db=sales`启动服务器，并且执行`USE prices; UPDATE sales.january SET amount=amount+1000；`，该语句不写入二进制日志。

*   `--binlog-ignore-db=db_name`

    告诉主服务器，如果当前的数据库(即`USE`选定的数据库)是`db_name`，不应将更新保存到二进制日志中。如果你使用该选项，你应确保只对当前的数据库进行更新。

    一个不能按照你期望的执行的例子：如果服务器用`binlog-ignore-db=sales`选项启动，并且执行`USE prices; UPDATE sales.january SET amount=amount+1000；`，该语句不被写入二进制日志。

    类似于`--binlog-do-db`，对于`CREATE DATABASE、ALTER DATABASE 和 DROP DATABASE`语句，有一个例外，即通过操作的数据库来决定是否应记录语句，而不是用当前的数据库。

要想记录或忽视多个数据库，可以在启动服务器的时候使用多个选项，为每个数据库指定相应的选项。

服务器根据下面的规则对选项进行评估，以便将更新记录到二进制日志中或忽视。请注意对于`CREATE/ALTER/DROP DATABASE`语句有一个例外。在这些情况下，根据以下列出的不同情况，所创建、修改或删除的数据库将代替当前的数据库。

1.  是否有`binlog-do-db`或`binlog-ignore-db`规则?

    没有：将语句写入二进制日志并退出。

    有：执行下一步。

2.  有一些规则(`binlog-do-db`或`binlog-ignore-db`或二者都有)。当前有一个数据库(是否使用`USE`语句选择了数据库？)?

    没有：不要写入语句，并退出。

    有：执行下一步。

3.  有一些`binlog-ignore-db`规则。当前的数据库是否匹配`binlog-ignore-db`规则?

    有：不要写入语句，并退出。

    没有：写入查询并退出。

例如，只用`binlog-do-db=sales`运行的服务器只将当前数据库为`sales`的语句写入二进制日志(换句话说，`binlog-do-db`有时可以表示`“忽视其它数据库”`)。

如果你正进行复制，应确保没有子服务器在使用旧的二进制日志文件时，方可删除它们。一种方法是每天一次执行`mysqladmin flush-logs`并删除三天前的所有日志。可以手动删除，或最好使用`PURGE MASTER LOGS`语句删除日志。

具有`SUPER`权限的客户端可以通过`SET SQL_LOG_BIN=0`语句禁止将自己的语句记入二进制记录。

你可以用`mysqlbinlog`实用工具检查二进制日志文件。如果你想要重新处理日志止的语句，这很有用。例如，可以从二进制日志更新 MySQL 服务器，方法如下：

```sql
shell> mysqlbinlog log-file | mysql -h server_name 
```

如果你正使用事务，必须使用 MySQL 二进制日志进行备份，而不能使用旧的更新日志。

查询结束后、锁定被释放前或提交完成后则立即记入二进制日志。这样可以确保按执行顺序记入日志。

对非事务表的更新执行完毕后立即保存到二进制日志中。对于事务表，例如`BDB`或`InnoDB`表，所有更改表的更新(`UPDATE`、`DELETE`或`INSERT`)都会 被缓存起来，直到服务器接收到`COMMIT`语句。在执行完`COMMIT`之前，`mysqld`将整个事务写入二进制日志。当处理事务的线程启动时，它为缓冲查询分配`binlog_cache_size`大小的内存。如果语句大于该值，线程则打开临时文件来保存事务。线程结束后临时文件被删除。

`Binlog_cache_use`状态变量显示了使用该缓冲区(也可能是临时文件)保存语句的事务的数量。`Binlog_cache_disk_use`状态变量显示了这些事务中实际上有多少必须使用临时文件。这两个变量可以用于将`binlog_cache_size`调节到足够大的值，以避免使用临时文件。

`max_binlog_cache_size`(默认`4GB`)可以用来限制用来缓存多语句事务的缓冲区总大小。如果某个事务大于该值，将会失败并回滚。

如果你正使用更新日志或二进制日志，当使用`CREATE ... SELECT or INSERT ... SELECT`时，并行插入被转换为普通插入。这样通过在备份时使用日志可以确保重新创建表的备份。

默认情况下，并不是每次写入时都将二进制日志与硬盘同步。因此如果操作系统或机器(不仅仅是 MySQL 服务器)崩溃，有可能二进制日志中最后的语句丢失了。要想防止这种情况，你可以使用`sync_binlog`全局变量(设置该变量值为`1`是最安全的值，但也是最慢的)，使二进制日志在每`N`次二进制日志写入后与硬盘同步。

该选项可以提供更大程度的安全，还应对 MySQL 服务器进行配置，使每个事务的二进制日志(`sync_binlog =1`)和(默认情况为真)`InnoDB`日志与硬盘同步。该选项的效果是崩溃后重启时，在滚回事务后，MySQL 服务器从二进制日志剪切 回滚的`InnoDB`事务。这样可以确保二进制日志反馈`InnoDB`表的确切数据等，并使从服务器保持与主服务器保持同步(不接收回滚的语句)。

请注意即使 MySQL 服务器更新其它存储引擎而不是`InnoDB`，也可以使用`--innodb-safe-binlog`选项启动服务。在`InnoDB`崩溃恢复时，只能从二进制日志中删除影响`InnoDB`表的语句或事务。如果崩溃恢复时 MySQL 服务器发现二进制日志变短了(即至少缺少一个成功提交的`InnoDB`事务)，如果`sync_binlog =1`并且硬盘或文件系统的确能根据需要进行同步(有些不需要)则不会发生，则输出错误消息 ("二进制日志<名>比期望的要小")。在这种情况下，二进制日志不准确，复制应从主服务器的数据快照开始。

写入二进制日志文件和二进制日志索引文件的方法与写入`MyISAM`表的相同。

### 4.4 慢速查询日志

用`--log-slow-queries[=file_name]`选项启动服务时，`mysqld`会写入一个包含所有执行时间超过`long_query_time`秒的 SQL 语句的日志文件。其中，获得初使表锁定的时间不算作执行时间。

如果没有给出`file_name`值，默认为主机名，后缀为`-slow.log`。如果给出了文件名，但不是绝对路径名，文件则写入数据目录。

语句执行完并且所有锁释放后记入慢查询日志。记录顺序可以与执行顺序不相同。

慢查询日志可以用来找到执行时间长的查询，可以用于优化。但是，检查又长又慢的查询日志会很困难。要想容易些，你可以使用`mysqldumpslow`命令获得日志中显示的查询摘要来处理慢查询日志。

在 MySQL 5.1 的慢查询日志中，不使用索引的慢查询同使用索引的查询一样记录。要想防止不使用索引的慢查询记入慢查询日志，使用`--log-short-format`选项。

在 MySQL 5.1 中,通过`--log-slow-admin-statements`服务器选项，你可以请求将慢管理语句，例如将`OPTIMIZE TABLE`、`ANALYZE TABLE`和`ALTER TABLE`语句写入慢查询日志。

用查询缓存处理的查询不加到慢查询日志中，因为表有零行或一行而不能从索引中受益的查询也不写入慢查询日志。

### 4.5 日志文件维护

MySQL 服务器可以创建各种不同的日志文件，从而可以很容易地看见所进行的操作。

当启用日志使用 MySQL 时，你可能想要不时地备份并删除旧的日志文件，并告诉 MySQL 开始记入新文件。

在 `Linux (Redhat)`的安装上，你可为此使用`mysql-log-rotate`脚本。如果你在`RPM`上分发安装 MySQL，脚本应该已经自动被安装。

在其它系统上，你必须自己安装短脚本，你可从镜像网站获得处理日志文件。

你可以通过`mysqladmin flush-logs`或 SQL 语句`FLUSH LOGS`来强制 MySQL 开始使用新的日志文件。

日志清空操作主要完成下列事情：

*   如果使用标准日志(`--log)`或慢查询日志(`--log-slow-queries`)，关闭并重新打开日志文件。(默认为`mysql.log`和``hostname`-slow.log`)。

*   如果使用更新日志(`--log-update`)或二进制日志(`--log-bin`)，关闭日志并且打开有更高序列号的新日志文件。

*   如果你只使用更新日志，你只需要重新命名日志文件，然后在备份前清空日志。例如，你可以这样做：

    ```sql
    shell> cd mysql-data-directory
    shell> mv mysql.log mysql.old
    shell> mysqladmin flush-logs 
    ```

    然后备份并删除`“mysql.old”`。