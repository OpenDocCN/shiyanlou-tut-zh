# 第 5 节 JDBC 数据类型和事务

* * *

## 一、实验简介

　　Java 数据类型转换为相应的 JDBC 类型的知识，以及 JDBC 事务管理的学习 　　

## 二、JDBC 数据类型

　　我们知道 Java 语言中的数据类型和 SQL 语言的数据类型有一定的差别，我们编写的 Java 程序在与数据库交互的时候，往往需要利用 JDBC 驱动程序将 Java 数据类型转换为相应的 JDBC 类型。这种转换过程默认情况下采用哪个映射的方式。比如一个 Java 整型转换为 SQL INTEGER 类型。

　　下表总结了 Java 数据类型转换为调用 PreparedStatement 中的 setXXX() 方法或 CallableStatement 对象或 ResultSet.updateXXX() 方法的默认 JDBC 数据类型。同时我们在上一节实验课中知道，ResultSet 对象为每个数据类型来检索列值对应了一个 getter 方法，我们将它一并列在下面

```java
| SQL | JDBC/Java | setter | updater | getter |
|-----|-----------|--------|---------|-------------|
| VARCHAR | java.lang.String | setString | updateString | getString |
| CHAR | java.lang.String | setString | updateString | getString |
| LONGVARCHAR | java.lang.String | setString | updateString | getString |
| BIT | boolean | setBoolean | updateBoolean | getBoolean |
| NUMERIC | java.math.BigDecimal | setBigDecimal | updateBigDecimal | getBigDecimal |
| TINYINT | byte | setByte | updateByte | getByte |
| SMALLINT | short | setShort | updateShort | getShort |
| INTEGER | int | setInt | updateInt | getInt |
| BIGINT | long | setLong | updateLong | getLong |
| REAL | float | setFloat | updateFloat | getFloat |
| FLOAT | float | setFloat | updateFloat | getFloat |
| DOUBLE | double | setDouble | updateDouble | getDouble |
| VARBINARY | byte[] | setBytes | updateBytes | getBytes |
| BINARY | byte[] | setBytes | updateBytes | getBytes |
| DATE | java.sql.Date | setDate | updateDate | getDate |
| TIME | java.sql.Time | setTime | updateTime | getTime |
| TIMESTAMP | java.sql.Timestamp | setTimestamp  | updateTimestamp | getTimestamp |
| CLOB | java.sql.Clob | setClob | updateClob | getClob |
| BLOB | java.sql.Blob | setBlob | updateBlob | getBlob |
| ARRAY | java.sql.Array | setARRAY | updateARRAY | getARRAY |
| REF | java.sql.Ref | setRef | updateRef | getRef |
| STRUCT | java.sql.Struct | setStruct | updateStruct | getStruct |
```

java.sql.Date，以及 java.sql.Time 和 java.sql.Timestamp 类映射分别到 SQL DATE、SQL TIME 和 SQL TIMESTAMP 数据类型：

```java
public class test {
   public static void main(String[] args) {
       //获取日期和时间格式
       //为了和下面 SQL 的日期做对比所以直接写明是 java.util.Date 类
       //我们也可以引入 java.util.Date 包，然后声明为 Date 类
      java.util.Date javaDate = new java.util.Date();
      long javaTime = javaDate.getTime();
      System.out.println("The Java Date is:" + 
             javaDate.toString());

      //获取 SQL 的日期
      java.sql.Date sqlDate = new java.sql.Date(javaTime);
      System.out.println("The SQL DATE is: " + 
             sqlDate.toString());

      //获取 SQL 的时间
      java.sql.Time sqlTime = new java.sql.Time(javaTime);
      System.out.println("The SQL TIME is: " + 
             sqlTime.toString());
      //获取 SQL 的时间戳
      java.sql.Timestamp sqlTimestamp =
      new java.sql.Timestamp(javaTime);
      System.out.println("The SQL TIMESTAMP is: " + 
             sqlTimestamp.toString());
     }
} 
```

　　上面表格中的方法同学们可以参看我们之前的所有实例，理解数据库中的数据类型与 java 中数据类型的异同。

## 三、JDBC 事务

　　我们在编写 java 程序的时候，在默认情况下，JDBC 连接是在自动提交模式下，即每个 SQL 语句都是在其完成时提交到数据库。但有时候我们为了提高程序运行的性能或者保持业务流程的完整性，以及使用了分布式事务管理方式，这个时候我们可能想关闭自动提交而自己管理和控制自己的事务。 　　 　　让多条 SQL 在一个事务中执行，并且保证这些语句是在同一时间共同执行的时，我们就应该为这多条语句定义一个事务。一个事务是把单个 SQL 语句或一组 SQL 语句作为一个逻辑单元，并且如果事务中任何语句失败，则整个事务失败。

　　如果我们要启动一个事务，而不是 JDBC 驱动程序默认使用 auto-commit 模式支持。这个时候我们就要使用 Connection 对象的 setAutoCommit() 方法。我们传递一个布尔值 false 到 setAutoCommit() 中，就可以关闭自动提交。反之我们传入一个 true 便将其重新打开。

例如:

```java
Connection conn = null;
conn = DriverManager.getConnection(URL);
//关闭自动提交
conn.setAutoCommit(false); 
```

　　我们关闭了自动提交后，如果我们要提交数据库更改怎么办呢？这时候就要用到我们的提交和回滚了。我们要提交更改，可以调用 commit() 方法:

```java
conn.commit(); 
```

　　尤其不要忘记，在 catch 块内添加回滚事务，表示操作出现异常，撤销事务：

```java
conn.rollback(); 
```

## 四、作业

　　将我们第一次写 JDBC 程序示例关闭自动提交。测试一下吧！

```java
import java.sql.*;

public class test {
   // JDBC 驱动器名称 和数据库地址
   static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";  
   //数据库的名称为 EXAMPLE
   static final String DB_URL = "jdbc:mysql://localhost/EXAMPLE";

   //  数据库用户和密码
   static final String USER = "root";
   //因为 mumu 是在自己的电脑上做测试的，所以是有密码的
   static final String PASS = "0909";  

   public static void main(String[] args) {
       Connection conn = null;
       Statement stmt = null;
       try{
           //注册 JDBC 驱动程序
           Class.forName("com.mysql.jdbc.Driver");

           //打开连接
           System.out.println("Connecting to database...");
           conn = DriverManager.getConnection(DB_URL,USER,PASS);
           conn.setAutoCommit(false);  

           //执行查询
           System.out.println("Creating statement...");
           stmt = conn.createStatement();
           //插入
           String sql = "INSERT INTO Students  " +
                    "VALUES (5, 20, 'Rose', 'Java')";
           stmt.executeUpdate(sql);
           //查找
           sql = "SELECT id, name, age FROM Students";
           ResultSet rs = stmt.executeQuery(sql);

           //提交事务
           conn.commit();

           //得到和处理结果集
           while(rs.next()){
               //检索
               int id  = rs.getInt("id");
               int age = rs.getInt("age");
               String name = rs.getString("name");

               //显示
               System.out.print("ID: " + id);
               System.out.print(", Age: " + age);
               System.out.print(", Name: " + name);
               System.out.println();
           }
           //清理环境
           rs.close();
           stmt.close();
           conn.close();
       }catch(SQLException se){
           // JDBC 操作错误
           se.printStackTrace();
           // conn.rollback();
           try{
                 if(conn!=null)
                    conn.rollback();
              }catch(SQLException se2){
                 se2.printStackTrace();
              }
       }catch(Exception e){
           // Class.forName 错误
           e.printStackTrace();
       }finally{
           //这里一般用来关闭资源的
           try{
               if(stmt!=null)
                   stmt.close();
           }catch(SQLException se2){
           }
           try{
               if(conn!=null)
                   conn.close();
           }catch(SQLException se){
               se.printStackTrace();
           }
       }
       System.out.println("Goodbye!");
   }
} 
```