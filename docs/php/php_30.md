# 第 2 节 项目实战 1（数据库的设计与实现）

## 一、启动 apache 和 mysql

**ps：系统用户名 shiyanlou**

打开 XfceTerminal，启动 apache：

```php
sudo service apache2 start 
```

再输入以下命令启动 mysql：

```php
sudo service mysql start 
```

## 二、数据库设计

数据库名：webbookmarker
数据表名：web_marker(utf8)

## 三、创建数据库

#### 1\. XfceTerminal 打开数据库，MySQL 用户名 root，密码为空

```php
mysql -u root -p #或者 mysql -uroot 
```

#### 2\. 创建 webmarker 数据库

```php
CREATE DATABASE IF NOT EXISTS `webbookmarker` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci; 
```

```php
USE `webbookmarker`; 
```

#### 3\. 创建 web_marker 数据表

```php
CREATE TABLE IF NOT EXISTS `web_marker` (  
        `id` int(10) unsigned NOT NULL,  
        `title` mediumtext NOT NULL,
        `href` mediumtext NOT NULL,
        `icon` mediumtext NOT NULL
    ) ENGINE=MyISAM  DEFAULT CHARSET=utf8 AUTO_INCREMENT=1; 
```

```php
ALTER TABLE `web_marker` ADD PRIMARY KEY (`id`); 
```

```php
ALTER TABLE `web_marker` MODIFY `id` int(10) unsigned NOT NULL AUTO_INCREMENT,AUTO_INCREMENT=1; 
```

#### 4\. 查看数据表的结构

```php
DESC `web_marker`; 
```

```php
+-------+------------------+------+-----+---------+----------------+
| Field | Type             | Null | Key | Default | Extra          |
+-------+------------------+------+-----+---------+----------------+
| id    | int(10) unsigned | NO   | PRI | NULL    | auto_increment |
| title | mediumtext       | NO   |     | NULL    |                |
| href  | mediumtext       | NO   |     | NULL    |                |
| icon  | mediumtext       | NO   |     | NULL    |                |
+-------+------------------+------+-----+---------+----------------+ 
```

#### 5\. 手动添加一行数据

```php
INSERT INTO `web_marker` (`id`, `title`, `href`, `icon`) VALUES (null, 'lisheng blog', 'http://mirrors.aliyuncs.com', 'https://dn-anything-about-doc.qbox.me/0422/favicon.ico'); 
```

```php
SELECT * FROM `web_marker`; 
```

我们能够看到数据，如果再添加一条主键 id 字段为空的数据，发现 id 也是能够自增长的。

## `四、作业思考`

尝试自行添加另外一行数据。