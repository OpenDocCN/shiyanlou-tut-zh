# 第 6 节 JDBC 的处理

* * *

## 一、实验介绍

　　本实验将学习 JDBC 异常处理、批量处理的相关知识

## 二、JDBC 异常处理

　　我们写了这么多的代码了同学们对异常处理是不是已经很熟悉了呢？ 　　异常处理，顾名思义可以处理在受控制的方式下的异常情况。当程序在运行的过程中并没有按照我们的预期情况执行，将会引发异常。当程序异常的时候，当前执行的程序就会立即停止，并且重定向到最近的适用的 catch 子句中，如果没有适用的 catch 子句存在，那么程序结束。 　　 　　JDBC 的异常处理非常类似于 Java Excpetion 处理。JDBC 最常见的异常处理的是 java.sql.SQLException，所以今天我们就来学习 SQLException 方法

```java
| 方法 | 描述 |
|------|------|
| getError() | 获取与异常关联的错误号 |
| getMessage() | 获取 JDBC 驱动程序的错误消息由驱动程序处理错误或获取数据库错误号和消息 |
| getSQLState() | 获取 XOPEN SQLSTATE 字符串。对于 JDBC 驱动程序的错误，没有有用的信息从该方法返回。对于一个数据库错误，则返回五位 XOPEN SQLSTATE 代码。这种方法可以返回 null |
| getNextException() | 获取异常链的下一个 Exception 对象 |
| printStackTrace(PrintStream s) | 打印此抛出，回溯到指定的打印流 |
| printStackTrace(PrintWriter w) | 打印次抛出，其回溯到指定的打印写入 |
```

　　通过利用可从 Exception 对象捕获异常的信息，并适当地继续运行程序。示例这里就不给出了，同学们可以去参看以前的代码。

## 三、JDBC 批量处理

　　批处理允许将相关的 SQL 语句组合成一个批处理和一个调用数据库提交。还记得我们学习 JDBC 事物的知识吗？今天的课程实质上和事务是一致的。

　　当一次发送多个 SQL 语句到数据库，可以减少通信开销的数额，从而提高了性能。不过 JDBC 驱动程序不需要支持此功能。应该使用 DatabaseMetaData.supportsBatchUpdates() 方法来确定目标数据库支持批量更新处理。如果你的 JDBC 驱动程序支持此功能的方法返回 true。

　　接下来我们来看看如何进行批处理操作： 　　1\. 使用 createStatement()方法创建一个 Statement 对象 　　2\. 设置使用自动提交为 false 　　3\. 添加任意多个 SQL 语句到批量处理，使用 addBatch()方法 　　4\. 使用 executeBatch()方法，将返回一个整数数组，数组中的每个元素代表了各自的更新语句的更新计数 　　5\. 最后，提交使用 commit()方法的所有更改

我们来看看示例：

```java
// 创建 statement 对象
Statement stmt = conn.createStatement();

// 关闭自动提交
conn.setAutoCommit(false);

// 创建 SQL 语句
String SQL = "INSERT INTO Students (id, name, age) VALUES(6,'Mike', 21)";
// 将 SQL 语句添加到批处理中
stmt.addBatch(SQL);

// 创建更多的 SQL 语句
String SQL = "INSERT INTO Students (id, name, age) VALUES(7, 'Angle', 23)";
// 将 SQL 语句添加到 批处理中
stmt.addBatch(SQL);

// 创建整数数组记录更新情况
int[] count = stmt.executeBatch();

//提交更改
conn.commit(); 
```

　　当然我们也可以使用 prepareStatement 对象进行批处理操作，SQL 语句依然可以使用占位符，示例如下：

```java
// 创建 SQL 语句
String SQL = "INSERT INTO Employees (id, name, age) VALUES(?, ?, ?)";

// 创建 PrepareStatement 对象
PreparedStatemen pstmt = conn.prepareStatement(SQL);

//关闭自动连接
conn.setAutoCommit(false);

// 绑定参数
pstmt.setInt( 1, 8 );
pstmt.setString( 2, "Cindy" );
pstmt.setInt( 3, 17 );

// 添入批处理
pstmt.addBatch();

// 绑定参数
pstmt.setInt( 1, 9 );
pstmt.setString( 2, "Jeff" );
pstmt.setInt( 3, 22 );

// 添入批处理
pstmt.addBatch();

//创建数组记录更改
int[] count = stmt.executeBatch();

//提交更改
conn.commit(); 
```

## 四、作业

　　好了，同学们整个 JDBC 的学习就全部结束了，同学们结合前面所学的 java 课程和数据库的相关知识，赶快参与实践吧，进一步系统地整理和完善这些知识，加油！