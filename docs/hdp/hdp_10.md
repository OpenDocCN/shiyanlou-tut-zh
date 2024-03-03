# 第 3 节 HBase 基本操作

## 一、实验简介

本章介绍 HBase Shell 的一些常用命令。

### 1.1 连接 HBase

使用`hbase shell`命令来连接正在运行的 Hbase 实例，该命令位于 HBase 安装包下的*bin/*目录。HBase Shell 提示符以>符号结束。

```java
$ ./bin/hbase shell
hbase(main):001:0> 
```

### 1.2 显示 HBase Shell 帮助文档

输入`help`并按 Enter 键，可以显示 HBase Shell 的基本使用信息，和我们接下来会列举的一些命令类似。需要注意的是，表名，行，列都必须包含在引号内。

### 1.3 退出 HBase Shell

使用`quit`命令，退出 HBase Shell 并且断开和集群的连接，但此时 HBase 仍然在后台运行。

### 1.4 查看 HBase 状态

```java
hbase(main):024:0>status
3 servers, 0 dead,1.0000 average load 
```

### 1.5 关闭 HBase

和*bin/start-hbase.sh*开启所有的 HBase 进程相同，*bin/stop-hbase.sh*用于关闭所有的 HBase 进程。

```java
$ ./bin/stop-hbase.sh
stopping hbase....................
$ 
```

## 二、数据定义（DDL）操作

### 2.1 创建新表

使用`create`命令来创建一个新的表。在创建的时候，必须指定表名和列族名。

```java
hbase(main):001:0> create 'test', 'cf'
0 row(s) in 0.4170 seconds

=> Hbase::Table - test 
```

### 2.2 列举表信息

使用`list`命令：

```java
hbase(main):002:0> list 'test'
TABLE
test
1 row(s) in 0.0180 seconds

=> ["test"] 
```

### 2.3 获取表描述

使用`describe`命令：

```java
hbase(main):003:0> describe 't'
DESCRIPTION ENABLED
 't', {NAME => 'f', DATA_BLOCK_ENCODING => 'NONE', BLOOMFILTER => 'ROW', REPLICATION_ true
 SCOPE => '0', VERSIONS => '1', COMPRESSION => 'NONE', MIN_VERSIONS => '0', TTL => '2
 147483647', KEEP_DELETED_CELLS => 'false', BLOCKSIZE => '65536', IN_MEMORY => 'false
 ', BLOCKCACHE => 'true'}
1 row(s) in 1.4430 seconds 
```

### 2.4 删除表

使用`drop`命令实现删除表的功能：

```java
hbase(main):011:0> drop 'test'
0 row(s) in 0.1370 seconds 
```

### 2.5 检查表是否存在

```java
hbase(main):021:0>exists 'member'
Table member doesexist                                                                                                                                     
0 row(s) in 0.1610seconds 
```

## 三、数据管理（DML）操作

### 3.1 向表中插入数据

使用`put`命令，将数据插入表中：

```java
hbase(main):003:0> put 'test', 'row1', 'cf:a', 'value1'
0 row(s) in 0.0850 seconds

hbase(main):004:0> put 'test', 'row2', 'cf:b', 'value2'
0 row(s) in 0.0110 seconds

hbase(main):005:0> put 'test', 'row3', 'cf:c', 'value3'
0 row(s) in 0.0100 seconds 
```

可以看到，在本例中，一共插入了三条数据，一次一条。第一次插入到*row1*行，*cf/:*列，插入值为*value1*。所有列在 HBase 中有一个列族前缀。本例中的*cf*，后面跟着一个冒号还有一个列限定后缀，本例中是*a*。

### 3.2 一次性扫描全表数据

一种获取 HBase 数据的方法是扫描，使用`scan`命令来扫描表的数据。可以限制限制扫描的范围，在本例中，获取的是所有的数据。

```java
hbase(main):006:0> scan 'test'
ROW                                      COLUMN+CELL
 row1                                    column=cf:a, timestamp=1421762485768, value=value1
 row2                                    column=cf:b, timestamp=1421762491785, value=value2
 row3                                    column=cf:c, timestamp=1421762496210, value=value3
3 row(s) in 0.0230 seconds 
```

### 3.3 获取一个行数据

使用`get`命令来获得某一行的数据：

```java
hbase(main):007:0> get 'test', 'row1'
COLUMN                                   CELL
 cf:a                                    timestamp=1421762485768, value=value1
1 row(s) in 0.0350 seconds 
```

### 3.4 更新一条数据

使用`put`命令，本例中，将*shiyanlou*地址改为*E*

```java
hbase(main):004:0>put 'company','shiyanlou','info:address' ,'E'
0 row(s) in 0.0210seconds

hbase(main):005:0>get 'company','shiyanlou','info:address' 
COLUMN                                   CELL                                                                                                               
 info:address                               timestamp=1321586571843, value=E                                                                                  
1 row(s) in 0.0180seconds 
```

### 3.5 禁用一个表

如果你想要删除一个表或是修改它的设置，或者是其它的情况，都需要首先禁用该表。使用`disable`命令禁用表，`enable`命令重新启用表。

```java
hbase(main):008:0> disable 'test'
0 row(s) in 1.1820 seconds

hbase(main):009:0> enable 'test'
0 row(s) in 0.1770 seconds 
```

## 四、实验总结

本章介绍了 HBase Shell 的一些基本的常用命令，主要包括数据定义（DLL）的数据管理（DML）的一些操作命令。只能满足基本的 HBase 操作，更进一步的了解，请通过阅读 HBase 官方教程。

## 参考资料

*   http://hbase.apache.org/book.html