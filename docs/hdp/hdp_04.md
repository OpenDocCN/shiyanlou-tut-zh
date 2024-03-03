# 第 4 节 Hive 数据导入/导出

## 一、实验简介

EXPORT 和 IMPORT 命令是在 Hive 0.8.0 中加入的，用于 Hive 和 HDFS 进行数据迁移或者数据备份。

### 1.1 导入/导出规则

*   EXPORT 命令导出数据表或分区，与元数据一起输出到指定位置。又可以从这个输出位置移动到不同的 Hadoop 或 Hive 实例中，并且使用 IMPORT 命令导入。
*   当导出一个分区表时，原始数据可能位于不同的 HDFS 位置，同时还支持导出/导入分区的子集。
*   导出的元数据存储在目标目录中，数据文件则存储在子目录中。
*   EXPORT 和 IMPORT 命令独立于所用的数据源和目标元数据数据管理系统；例如，它们可以在 Derby 和 MYSQL 数据库之间使用。

## 二、导入/导出语法

*   数据导出（EXPORT）

```java
EXPORT TABLE tablename [PARTITION (part_column="value"[, ...])] 
  TO 'export_target_path' [ FOR replication('eventid') 
```

*   数据导入（IMPORT）

```java
IMPORT [[EXTERNAL] TABLE new_or_original_tablename [PARTITION (part_column="value"[, ...])]] 
  FROM 'source_path'
  [LOCATION 'import_target_path'] 
```

## 三、导入/导出实例

*   简单导入/导出

```java
export table department to 'hdfs_exports_location/department';
import from 'hdfs_exports_location/department'; 
```

*   导入重命名

```java
export table department to 'hdfs_exports_location/department';
import table imported_dept from 'hdfs_exports_location/department'; 
```

*   导出分区

```java
export table employee partition (emp_country="in", emp_state="ka") to 'hdfs_exports_location/employee';
import from 'hdfs_exports_location/employee'; 
```

*   导入分区

```java
export table employee to 'hdfs_exports_location/employee';
import table employee partition (emp_country="us", emp_state="tn") from 'hdfs_exports_location 
```

*   指定导入位置

```java
export table department to 'hdfs_exports_location/department';
import table department from 'hdfs_exports_location/department' 
       location 'import_target_location/department'; 
```

*   作为外部表导入

```java
export table department to 'hdfs_exports_location/department';
import external table department from 'hdfs_exports_location/department'; 
```

## 参考资料

*   https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport

## 版权声明（可选）

在这里写课程内容的版权声明