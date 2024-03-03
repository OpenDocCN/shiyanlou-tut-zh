# 第 1 节 HBase 简介

## 实验简介

我们本节课程将要讲述以下内容：

*   HBase 的概述及历史
*   HBase 的数据模型
*   HBase 的系统架构

## 一、HBase 概述

HBase 是一个开源的非关系型分布式数据库（NoSQL），它参考了谷歌的 BigTable 建模，实现的编程语言为 Java。它是 Apache 软件基金会的 Hadoop 项目的一部分，运行于 HDFS 文件系统之上，为 Hadoop 提供类似于 BigTable 规模的服务，可以存储海量稀疏的数据，并具备一定的容错性、高可靠性及伸缩性。主要应用场景是实时随机读写超大规模的数据。

HBase 在列上实现了 BigTable 论文提到的压缩算法、内存操作和布隆过滤器。HBase 的表能够作为 MapReduce 任务的输入和输出，可以通过 Java API 来存取数据，也可以通过 REST、Avro 或者 Thrift 的 API 来访问。

HBase 不能取代 RDBMS，因为二者的应用场景不同。HBase 为了解决海量数据的扩展性，支持简单的增加节点来实现线性扩展，从而在集群上管理海量的非结构化或半结构化的稀疏数据。HBase 仅能通过主键（raw key）或主键的 range 检索数据，支持单行事务。

![Alt text](img/1fda7f35801ba33433d856c82d6e3036.jpg)

上图描述 Hadoop EcoSystem 中的各层系统。其中,HBase 位于结构化存储层，Hadoop HDFS 为 HBase 提供了高可靠性的底层存储支持，Hadoop MapReduce 为 HBase 提供了高性能的计算能力，Zookeeper 为 HBase 提供了稳定服务和 failover 机制。

此外，Pig 和 Hive 还为 HBase 提供了高层语言支持，使得在 HBase 上进行数据统计处理变的非常简单。 Sqoop 则为 HBase 提供了方便的 RDBMS 数据导入功能，使得传统数据库数据向 HBase 中迁移变的非常方便。

## 二、HBase 历史

Apache HBase 最初是 Powerset 公司为了处理自然语言搜索产生的海量数据而开展的项目。下图展示了 HBase 的发展历程。

![Alt text](img/18081323e911463be328968d9e7a77c9.jpg)

## 三、HBase 数据模型

![Alt text](img/e6d4a2465aeddc0aab4ec482c39747b6.jpg)

*   **行健（Row Key）**：表的主键，表中的记录默认按照行健升序排序
*   **时间戳（Timestamp）**：每次数据操作对应的时间戳，可以看作是数据的版本号
*   **列族（Column Family）**：表在水平方向有一个或者多个列族组成，一个列族中可以由任意多个列组成，列族支持动态扩展，无需预先定义列的数量以及类型，所有列均以二进制格式存储，用户需要自行进行类型转换。所有的列族成员的前缀是相同的，例如“abc:a1”和“abc:a2”两个列都属于 abc 这个列族。
*   **表和区域（Table&Region）**：当表随着记录数不断增加而变大后，会逐渐分裂成多份，成为区域，一个区域是对表的水平划分，不同的区域会被 Master 分配给相应的 RegionServer 进行管理
*   **单元格（Cell）**：表存储数据的单元。由{行健，列（列族:标签），时间戳}唯一确定，其中的数据是没有类型的，以二进制的形式存储。

## 四、HBase 架构

![Alt text](img/cbd09e977c093d53842eeb47dde10deb.jpg)

如上图所示，HBase 架构中只有一个 Master 节点，称 HMaster，还有多台 RegionServer 成为 HRegionServer，每个 RegionServer 包含多个 Region。

1.  HBase 访问接口：Java，REST，Thrift 等
2.  Master：集群的管理服务器，为 RegionServer 分配 Region，负责 RegionServer 的负载均衡，处理 schema 更新请求
3.  RegionServer：管理 HBase 的数据存储，维护 Region，处理 IO 请求。
4.  Zookeeper：保证集群的高可用性、存储 Region 的寻址入口，并实时监控 RegionServer 的状态，存储 HBase 的 Schema。

可以看到，client 访问 hbase 上数据的过程并不需要 Master 参与（寻址访问 Zookeeper 和 RegionServer，数据读写访问 RegionServer），Master 仅仅维护 Table 和 Region 的元数据信息，负载很低。

## 五、HBase 访问接口

1.  Native Java API，最常规和高效的访问方式，适合 Hadoop MapReduce Job 并行批处理 HBase 表数据
2.  HBase Shell，HBase 的命令行工具，最简单的接口，适合 HBase 管理使用
3.  Thrift Gateway，利用 Thrift 序列化技术，支持 C++，PHP，Python 等多种语言，适合其他异构系统在线访问 HBase 表数据
4.  REST Gateway，支持 REST 风格的 Http API 访问 HBase, 解除了语言限制
5.  Pig，可以使用 Pig Latin 流式编程语言来操作 HBase 中的数据，和 Hive 类似，本质最终也是编译成 MapReduce Job 来处理 HBase 表数据，适合做数据统计
6.  Hive，当前 Hive 的 Release 版本尚没有加入对 HBase 的支持，但在下一个版本 Hive 0.7.0 中将会支持 HBase，可以使用类似 SQL 语言来访问 HBase

## 六、HBase 存储格式

HBase 中的所有数据文件都存储在 Hadoop HDFS 文件系统上，主要包括上述提出的两种文件类型：

1.  HFile， HBase 中 KeyValue 数据的存储格式，HFile 是 Hadoop 的二进制格式文件，实际上 StoreFile 就是对 HFile 做了轻量级包装，即 StoreFile 底层就是 HFile
2.  HLogFile，HBase 中 WAL（Write Ahead Log） 的存储格式，物理上是 Hadoop 的 Sequence File

## 七、HBase 应用场景

HBase 的优势主要在以下几方面：

1.  海量数据存储
2.  快速随机访问
3.  大量写操作的应用

常见的应用场景

1.  互联网搜索引擎数据存储（BigTable 要解决的问题）
2.  审计日志系统
3.  实时系统
4.  消息中心
5.  内容服务系统

## 参考文档

*   http://www.ymc.ch/en/introduction-to-hbase
*   http://bigdatariding.blogspot.jp/2013/12/hbase-architecture.html
*   http://baike.baidu.com/view/1993870.htm
*   http://hbase.apache.org/
*   http://www.alidata.org/archives/1509
*   http://zh.wikipedia.org/zh/Apache_HBase
*   http://abloz.com/hbase/book.html