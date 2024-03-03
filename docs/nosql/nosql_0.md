# mongoDB 基础教程

mongoDB 基础教程实验课。Mongo DB 是目前在 IT 行业非常流行的一种非关系型数据库(NoSql),其灵活的数据存储方式备受当前 IT 从业人员的青睐。

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou 若不小心登出后，直接刷新页面即可

### 2\. 环境使用

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

### 3、参考文档

本实验课程参考以下文档： [Mongodb 官方教程](http://docs.mongodb.org/manual/)

## 二、mongoDB 简介

### 1、简介

MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。他支持的数据结构非常松散，是类似 json 的 bson 格式，因此可以存储比较复杂的数据类型。

### 2、面向集合的存储

MongoDB 中，一个数据库包含多个集合，类似于 MySql 中一个数据库包含多个表；一个集合包含多个文档，类似于 MySql 中一个表包含多条数据。

### 3、虚拟机开机配置

启动 MongoDB 服务，因为 mongoDB 并不随系统一起启动，可能以下命令运行后会等一小段的时间才会启动完毕。

```
$ sudo service mongodb start 
```

进入 MongoDB 命令行操作界面(可能会出现 connect failed，多试几次就行)，在命令行中敲**exit**可以退出

```
$ mongo 
```

实验中的布尔类型的 ture 用 1 代替，false 用 0 代替

## 三、基本概念

### 1、数据库

*   一个 mongoDB 可以创建多个数据库
*   使用 show dbs 可以查看所有数据库的列表
*   执行 db 命令则可以查看当前数据库对象或者集合
*   运行 use 命令可以连接到指定的数据库

```
$ mongo      #进入到 mongo 命令行
> use test            #连接到 test 数据库 
```

注意：数据库名可以是任何字符，但是不能有空格、点号和$字符

### 2、文档

文档是 mongoDB 的核心，类似于 SQLite 数据库（关系数据库）中的每一行数据。多个键及其关联的值放在一起就是文档。在 mongodb 中使用一种类 json 的 bson 存储数据，bson 数据可以理解为在 json 的基础上添加了一些 json 中没有的数据类型。

例：

```
{"company":"Chenshi keji"} 
```

### 3、文档的逻辑联系

假设有两个文档：

```
{
   "name": "Tom Hanks",
   "contact": "987654321",
   "dob": "01-01-1991"
}#user 文档

{
   "building": "22 A, Indiana Apt",
   "pincode": 123456,
   "city": "chengdu",
   "state": "sichuan"
}#address 文档 
```

关系 1：嵌入式关系：把 address 文档嵌入到 user 文档中

```
{
   "name": "Tom Hanks",
   "contact": "987654321",
   "dob": "01-01-1991",
   "address":
   [{
   "building": "22 A, Indiana Apt",
   "pincode": 123456,
   "city": "chengdu",
   "state": "sichuan"
    },
    {
    "building": "170 A, Acropolis Apt",
    "pincode": 456789,
    "city": "beijing",
    "state": "beijing"
    }]
}#这就是嵌入式的关系 
```

关系 2：引用式关系：将两个文档分开，通过引用文档的 _id 字段来建立关系

```
{
   "contact": "987654321",
   "dob": "01-01-1991",
   "name": "Tom Benzamin",
   "address_ids": [
      ObjectId("52ffc4a5d85242602e000000")    #对应 address 文档的 id 字段
   ]
}#这就是引用式关系 
```

### 4、集合

集合就是一组文档的组合，就相当于是**关系数据库中的表**，在 mongodb 中可以存储不同的文档结构的文档 例:

```
{"company":"Chenshi keji"} {"people":"man","name":"peter"} 
```

上面两个文档就可以存储在同一个集合中

### 5\. 元数据

数据库的信息存储在集合中，他们统一使用系统的命名空间：DBNAME.system.* DBNAME 可用 db 或数据库名替代

*   DBNAME.system.namespaces ：列出所有名字空间
*   DBNAME.system.indexs ：列出所有索引
*   DBNAME.system.profile ：列出数据库概要信息
*   DBNAME.system.users ：列出访问数据库的用户
*   DBNAME.system.sources ：列出服务器信息

## 四、数据库的创建和销毁

### 1、创建数据库

启动服务后，进入 MongoDB 命令行操作界面：

```
$ mongo 
```

使用 use 命令创建数据库：

```
> use mydb 
```

查看当前连接的数据库：

```
> db 
```

查看所有的数据库：

```
> show dbs 
```

列出的所有数据库中看不到 mydb 或者显示 mydb(empty) ，因为 mydb 为空，里面没有任何东西，MongoDB 不显示或显示 mydb(empty)。

### 2、销毁数据库

使用 db.dropDatabase() 销毁数据库：

```
> use local
 switched to db local
> db.dropDatabase() 
```

查看所有的数据库：

```
> show dbs 
```

## 五、集合（collection）的创建和删除

### 1、创建集合

在数据库 mydb 中创建一个集合

```
> use mydb
switched to db mydb
> db.createCollection("users") 
```

查看创建的集合：

```
> show collections 
```

### 2、删除集合

删除集合的方法如下：（删除 users 集合）

```
> show collections
> db.users.drop() 
```

查看是否删除成功：

```
> show collections 
```

## 六、向集合中插入数据

### 1、使用 insert()

插入数据时，如果 users 集合没有创建会自动创建。

```
> use mydb
switched to db mydb
> db.users.insert([
... { name : "jam",
... email : "jam@qq.com"
... },
... { name : "tom",
... email : "tom@qq.com"
... }
... ]) 
```

### 2、使用 save()

插入数据时，如果 users 集合没有创建会自动创建。

```
> use mydb2
switched to db mydb2
> db.users.save([
... { name : "jam",
... email : "jam@qq.com"
... },
... { name : "tom",
... email : "tom@qq.com"
... }
... ]) 
```

## 七、小结

本节介绍了 MongoDB 和集合的基本操作，在 mongodb 中使用一种类 json 的 bson 存储数据，可以使用 use 创建和切换数据库，show dbs 可以查看有哪些数据库，dropDatabase 可以删除数据库，createCollection 可以创建集合，show collections 可以查看集合，insert()和 save()可以插入数据。

## 八、练习

请新建一个名为 shiyanlou 的数据库，创建一个 users 的集合，插入一个`name：你的昵称`的文档，并了解 insert 和 save 的区别。

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou 若不小心登出后，直接刷新页面即可

### 2\. 环境使用

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

## 二、查询语句

### 1、find() 语句

启动 MongoDB 服务，因为 mongoDB 并不随系统一起启动，可能以下命令运行后会等一小段的时间才会启动完毕。

```
$ sudo service mongodb start 
```

进入 MongoDB 命令行操作界面，在命令行中敲**exit**可以退出

```
$ mongo 
```

find() 用法：db.COLLECTION_NAME.find()

```
> use post
> db.post.insert([
{
   title: 'MongoDB Overview', 
   description: 'MongoDB is no sql database',
   by: 'shiyanlou',
   url: 'http://www.shiyanlou.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 100
},
{
   title: 'NoSQL Database', 
   description: "NoSQL database doesn't have tables",
   by: 'shiyanlou',
   url: 'http://www.shiyanlou.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 20, 
   comments: [    
      {
         user:'user1',
         message: 'My first comment',
         dateCreated: new Date(2013,11,10,2,35),
         like: 0 
      }
   ]
}
]) 
```

查询数据，不加任何参数默认返回所有数据记录：

```
> db.post.find() 
```

### 2、pretty() 语句

pretty() 可以使查询输出的结果更美观。

```
> db.post.find().pretty() 
```

## 三、MongoDB 中的 AND

### 1、AND

当 find() 中传入多个键值对时，MongoDB 就会将其作为 AND 查询处理。用法：db.mycol.find({ key1: value1, key2: value2 }).pretty()

```
> db.post.find({"by":"shiyanlou","title": "MongoDB Overview"}).pretty() 
```

## 四、MongoDB 中的 OR

### 1、OR

MongoDB 中，OR 查询语句以 $or 作为关键词，用法如下：

```
> db.post.find(
   {
      $or: [
         {key1: value1}, {key2:value2}
      ]
   }
).pretty() 
```

操作示例：

```
> db.post.find({
    $or:[
        {"by":"shiyanlou"},
        {"title": "MongoDB Overview"}
    ]
}).pretty() 
```

## 五、同时使用 AND 和 OR

### 1、操作范例

```
> db.post.find({
    "likes": {$gt:10},
    $or: [
        {"by": "shiyanlou"},
        {"title": "MongoDB Overview"}
    ]
}).pretty() 
```

{\$gt:10} 表示大于 10，另外，\$lt 表示小于，\$lte 表示小于等于，\$gte 表示大于等于，\$ne 表示不等于。

## 六、小结

本节讲了 MongoDB 中的查询，find 可以查询集合的所有数据，find 中添加 AND 和 OR 条件查询，{\$gt:10} 表示大于 10，另外，\$lt 表示小于，\$lte 表示小于等于，\$gte 表示大于等于，\$ne 表示不等于。

## 七、练习

请新建一个学生表的集合，插入学生的信息，包括姓名、年龄、性别等，使用上述讲的查询方法查询性别为男的学生、年龄大于 20 的学生和姓张的学生。

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou 若不小心登出后，直接刷新页面即可

### 2\. 环境使用

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

### 3、虚拟机开机配置

启动 MongoDB 服务，因为 mongoDB 并不随系统一起启动，可能以下命令运行后会等一小段的时间才会启动完毕。

```
$ sudo service mongodb start 
```

进入 MongoDB 命令行操作界面，在命令行中敲**exit**可以退出

```
$ mongo 
```

实验中的布尔类型的 ture 用 1 代替，false 用 0 代替

## 二、创建数据库

范例：进入 mongoDB 命令行操作界面之后，进行如下操作：

```
> use Chenshi
switched to db Chenshi 
```

您可以使用 db 命令查看您当前选择的数据库

```
> db
Chenshi 
```

您也可以通过使用 show dbs 命令查看所有的数据库

```
> show dbs 
```

在这条命令之后您刚才创建的 Chenshi 不会显示，这只有在您像数据库中插入了数据之后才能显示

## 三、删除数据库 -db.dropDatabase()

假设刚才我们使用语句 db.computer.insert({"name":"shiyanlou"})对数据库 Chenshi 进行了数据插入操作

```
> show dbs      #显示当前系统中的所有数据库
> use Chenshi   #转到 Chenshi 数据库
switched to db Chenshi
> db.dropDatabase()
{"dropped":"Chenshi","ok":1}
> show dbs 
```

## 四、创建集合 -createCollection()

语法：

```
db.createCollection(name,options) 
```

参数描述：

*   name：创建的集合名称
*   options：是一个作为初始化的文档(可选)

范例：

```
> db.createCollection("shiyanlou")            #无参数
{ "ok" : 1 }
> show collections
shiyanlou
system.indexes
> db.createCollection("shiyanlou2", { capped : 1, autoIndexID : 1, size : 6142800, max : 10000 } )            #带参数
{ "ok ": 1 } 
```

参数描述：

*   capped：类型为 Boolean，如果为 ture 则创建一个固定大小的集合，当其条目达到最大时可以自动覆盖以前的条目。在设置其为 ture 时也要指定参数大小；
*   autoIndexID：类型为 Boolean，默认为 false，如果设置为 ture，则会在 _id field.s 上自动创建索引；
*   size：如果 capped 为 ture 需要指定，指定参数的最大值，单位为 byte；
*   max：指定最大的文档数。 在 mogodb 中也可以不用创建集合，因为在创建文档的时候也会自动的创建集合

## 五、删除集合 -db.COLLECTION.drop()

操作实例：

```
> use Chenshi
switched to db Chenshi
> show collections
shiyanlou
shiyanlou2
system.indexes
> db.shiyanlou.drop()
ture
> show collections
shiyanlou2
system.indexes 
```

删除成功

注意：当您要删除指定的集合时，用您想要删除的集合名称替代 COLLECTION 即可

## 六、插入文档 -db.COLLECTION_NAME.insert(document)

操作实例：

```
> userdoc1=({"user_id":1,"name":"cloud","state":"active","actor":"user","e-mail":"test@qq.com","VM_num":2,"time":[{"date":"2014-08-12","hour":"10:53 PM"}] })        
> userdoc2=({"user_id":2,"name":"testadmin","state":"active","actor":"admin","e-mail":"test@qq.com","VM_num":2,"time":[{"date":"2014-08-11","hour":"06:34 AM"}] })    
> doc1=({"name":"peter","position":"teacher"})        #先定义文档
> use Chenshi
switched to db Chenshi
> db.shiyanlou.insert(userdoc1)
WriteResult({"nInserted":1})
> db.shiyanlou.insert(userdoc2)
WriteResult({"nInserted":1})
> db.shiyanlou.insert(doc1)
WriteResult({"nInserted":1}) 
```

插入文档成功，当然也可以直接将文档的内容作为函数的参数直接替代 document

## 七、更新文档 -db.COLLECTION*NAME.update(SELECTION*CRITERIA,UPDATED_DATA)

操作实例：

```
> db.shiyanlou.update({"user_id":"02","e-mail":"test@qq.com"},{$set:{"e-mail":"group@qq.com"}})
WriteResult({"nMatched":1,"nUpserted":1,"nModified":1})
> db.shiyanlou.find() 
```

*   将 user_id=2 的文档的 e-mail 改为 group@qq.com
*   第一个大括号内容标示查找条件，第二个大括号内容则表示更新后的数据
*   默认的 update 函数只对一个文档更新，如果想作用所有文档，则需要加入 multi:ture

操作实例：

```
db.shiyanlou.update({"e-mail":"test@qq.com"},{$set:{"e-mail":"group@qq.com"}},{multi:true}) 
```

## 八、替换已存在的文档 -db.COLLECTION*NAME.save({*id:ObjectId(),NEW_DATA})

操作实例：

```
> db.shiyanlou.save({"_id":ObjectId("53ea174ccb4c62646d9544f4"),"name":"Bob","position":"techer"})
WriteResult({"nMatched":1,"nUpserted":1,"nModified":1}) 
```

跟 update 差不多，但是 update 更好用

## 九、删除文档 -db.COLLECTION*NAME.remove(DELECTION*CRITERIA)

操作实例：

```
> db.shiyanlou.remove({"name":"Bob"})
WriteResult({"nRemoved":1}) 
```

其实 remove 函数的参数跟 update 函数的第一个参数一样，相当于查找条件，注意，不要误删！

删除后可以用查找命令确认数据：

```
> db.shiyanlou.find() 
```

## 十、小结

本节讲解了集合和文档的基本操作：

createCollection()：创建集合

db.COLLECTION.drop()：删除集合

db.COLLECTION_NAME.insert(document)：插入文档

db.COLLECTION*NAME.update(SELECTION*CRITERIA,UPDATED_DATA)：更新文档

db.COLLECTION*NAME.save({*id:ObjectId(),NEW_DATA})：替换已存在的文档

db.COLLECTION*NAME.remove(DELECTION*CRITERIA)：删除文档

## 十一、练习

请通过查询资料更加熟练地掌握上述提到的操作，了解它们的高级用法。

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou 若不小心登出后，直接刷新页面即可

### 2\. 环境使用

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

### 3、初始化环境

启动 MongoDB 服务，因为 mongoDB 并不随系统一起启动，可能以下命令运行后会等一小段的时间才会启动完毕。

```
$ sudo service mongodb start 
```

进入 MongoDB 命令行操作界面，在命令行中敲**exit**可以退出

```
$ mongo 
```

停止 MongoDB 服务：

```
$ mongod --shutdown 
```

实验中的布尔类型的 ture 用 1 代替，false 用 0 代替

### 4、初始化 mongodb 数据库

```
> use Chenshi
switched to db Chenshi
> db.createCollection("shiyanlou")            #无参数
{"ok":1}
> show collections
shiyanlou
system.indexes
> userdoc1=({"user_id":1,"name":"cloud","state":"active","actor":"user","e-mail":"test@qq.com","VM_num":2,"time":[{"date":"2014-08-12","hour":"10:53 PM"}] })        
> userdoc2=({"user_id":2,"name":"testadmin","state":"active","actor":"admin","e-mail":"test@qq.com","VM_num":2,"time":[{"date":"2014-08-11","hour":"06:34 AM"}] })    
> doc=({"name":"peter","position":"teacher"})
> db.shiyanlou.insert(userdoc1)
WriteResult({"nInserted":1})
> db.shiyanlou.insert(userdoc2)
WriteResult({"nInserted":1})
> db.shiyanlou.insert(doc1)
WriteResult({"nInserted":1}) 
```

## 二、查询语句

db.COLLECTION_NAME.find(Parameter)范例：

```
> db.shiyanlou.find() 
```

文档信息，这条指令相当于 sqlite 中的 SELECT * FROM TABLE_NAME

### 条件操作符 1

mongodb 中的条件操作符有：

*   (>) 大于 - \$gt #greate
*   (<) 小于 - \$lt #low
*   (>=) 大于等于 - \$gte #equal
*   (<= ) 小于等于 - \$lte

范例：

```
> db.shiyanlou.find({user_id:{$gt:1}})
> db.shiyanlou.find({user_id:{$lte:2,$gt:1}}) 
```

### 条件操作符 2

语法：

```
$type 
```

type 的值：

*   双精度型-1
*   字符串-2
*   对象-3
*   数组-4
*   二进制数据-5
*   对象 ID-7
*   布尔类型-8
*   数据-9
*   空-10
*   正则表达式-11
*   JS 代码-13
*   符号-14
*   有作用域的 JS 代码-15
*   32 位整型数-16
*   时间戳-17
*   64 位整型数-18
*   Min key-255
*   Max key-127

范例：

```
> db.shiyanlou.find({"name":{$type:2}}) 
```

查找 name 是字符串的文档记录

#### limit 与 skip

读取指定数量的数据记录 -limit

范例：

```
> db.shiyanlou.find().limit(1) 
```

读取一条记录，默认是排在最前面的那一条被读取

读取时跳过指定数量的数据记录 -skip

范例：

```
> db.shiyanlou.find().limit(1).skip(1) 
```

当然，还可以添加 find 的查找条件的参数，以便进行更精确的查找

### MongoDB 排序 -sort()

与 sqlite 中的排序一样有升序和降序，其中升序用 1 表示，降序用-1 表示 语法：

```
db.COLLECTION_NAME.find().sort({KEY:1|-1}) 
```

范例：

```
> db.shiyanlou.find().sort({"time":1}) 
```

## 三、索引 - ensureIndex()

索引通常能够极大的提高查询的效率，如果没有索引，MongoDB 在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录。这种扫描全集合的查询效率是非常低的，特别在处理大量的数据时，查询可以要花费几十秒甚至几分钟，无疑对网站的性能是非常致命的。

索引是特殊的数据结构，索引存储在一个易于遍历读取的数据集合中，索引是对数据库集合中一个文档或多个文档的值进行排序的一种结构。

语法：

```
db.COLLECTION_NAME.ensureIndex({KEY:1|-1}) 
```

同样 1 代表升序，-1 代表降序

范例：

```
> db.shiyanlou.ensureIndex({"name":1}) 
```

ensureIndex()的可选参数：

```
参数|                类型|     描述
---|---|---
background|            Boolean |   建立索引要不要阻塞其他数据库操作，默认为 false
unique|                Boolean |建立的索引是否唯一，默认 false
name    |           string|     索引的名称，若未指定，系统自动生成
dropDups|                Boolean|        建立唯一索引时，是否删除重复记录，默认 flase
sparse    |           Boolean     |对文档不存在的字段数据不启用索引，默认 false
expireAfterSeconds|        integer |   设置集合的生存时间，单位为秒
v        |           index version|  索引的版本号
weights    |           document    |索引权重值，范围为 1 到 99999
default-language|        string  |   默认为英语
language_override|        string  |   默认值为 language
```

范例：

```
> db.shiyanlou.ensureIndex({"user_id":1,"name":1},{background:1}) 
```

## 四、聚合 -aggregate()

语法：

```
db.COLLECTION_NAME.aggregate({
$match:{x:1},
{limit:NUM},
$group:{_id:$age}
}) 
```

这些参数都可选

*   $match:查询，跟 find 一样；
*   $limit：限制显示结果数量；
*   $skip：忽略结果数量；
*   $sort：排序；
*   $group：按照给定表达式组合结果。

范例：

```
> db.shiyanlou.aggregate([{$group:{_id:"$name", user:{$sum:"$user_id"}}}]) 
```

$name 意为取得 name 的值

### 聚合表达式

```
名称|描述
---|---
$sum|        计算总和
$avg|            计算平均值
\$min 和$max|    计算最小和最大值
$push    |   在结果文档中插入值到一个数组
$addToSet|    在结果文档中插入值到一个数组，但不创建副本
$first    |   根据资源文档的排序获取第一个文档数据
$last    |       根据资源文档的排序获取最后一个文档数据
```

### 管道

MongoDB 的聚合管道将 MongoDB 文档在一个管道处理完毕后将结果传递给下一个管道处理。管道操作是可以重复的。

表达式：处理输入文档并输出。表达式是无状态的，只能用于计算当前聚合管道的文档，不能处理其它的文档。 聚合框架中常用的几个操作：

*   $project：修改输入文档的结构。可以用来重命名、增加或删除域，也可以用于创建计算结果以及嵌套文档。
*   \$match：用于过滤数据，只输出符合条件的文档。$match 使用 MongoDB 的标准查询操作。
*   $limit：用来限制 MongoDB 聚合管道返回的文档数。
*   $skip：在聚合管道中跳过指定数量的文档，并返回余下的文档。
*   $unwind：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值。
*   $group：将集合中的文档分组，可用于统计结果。
*   $sort：将输入文档排序后输出。
*   $geoNear：输出接近某一地理位置的有序文档。

范例：

```
> db.shiyanlou.aggregate([{$match:{user_id:{$gt:0,$lte:2}}},{$group:{_id:"user",count:{$sum:1}}}])
{"_id":"user","count":2} 
```

## 五、小结

本节讲解了 MongoDB 的查询、索引和聚合，可以使用 find 进行查询，sort 用于排序，ensureIndex 用于建立索引，aggregate 用于聚合。

## 六、练习

请再新添加一个职工集合，每个职工包括年龄，工资，住址等信息，插入多个文档，最后结合上述所讲操作一遍。

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou 若不小心登出后，直接刷新页面即可

### 2\. 环境使用

实验报告可以在个人主页中查看，其中含有每次实验的截图及笔记，以及每次实验的有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您学习的真实性证明。

#### 3、初始化环境

启动 MongoDB 服务，因为 mongoDB 并不随系统一起启动，可能以下命令运行后会等一小段的时间才会启动完毕。

```
$ sudo service mongodb start 
```

进入 MongoDB 命令行操作界面，在命令行中敲**exit**可以退出

```
$ mongo 
```

实验中的布尔类型的 ture 用 1 代替，false 用 0 代替。

## 二、覆盖索引查询

所有的查询字段都是索引的一部分；所有的查询返回字段在同一个索引中。

由于索引存在于 RAM 中，因而从索引中获取数据要比扫面文档更快

范例：创建如下 users 集合（使用前面所学的方法创建该集合）：

```
{
   "contact": "987654321",
   "dob": "01-01-1991",
   "gender": "M",
   "name": "Tom Benzamin",
   "user_name": "tombenzamin"
} 
```

在 user 中创建一个联合索引

```
> db.users.ensureIndex({gender:1,user_name:1}) 
```

该索引会覆盖下面的查询：

```
> db.users.find({gender:"M"},{user_name:1,_id:0}) 
```

对于上述查询，MongoDB 不会在数据库文件中查找，而会从索引中提取数据。因为索引中不包含*id 字段，所以*id 在查询中会默认返回，可以在查询结果中将其排除。而`db.users.find({gender:"M"},{user_name:1})`就不会被索引覆盖。

## 三、高级索引

创建如下 users 集合（使用前面所学的方法创建该集合）：

```
{
   "address": {
      "city": "chengdu",
      "province": "sichuan",
      "pincode": "123"
   },
   "tags": [
      "music",
      "cricket",
      "blogs"
   ],
   "name": "clound"
} 
```

索引数组字段，在数组中创建索引，需要对数组中的每个字段依次建立索引。所以在我们为数组 tags 创建索引时，会为 music、cricket、blogs 三个值建立单独的索引。 范例：

```
> db.users.ensureIndex({"tags":1}) 
```

创建索引后，我们可以这样检索集合的 tags 字段：

```
> db.users.find({tags:"cricket"}) 
```

为了验证我们使用使用了索引，可以使用 explain 命令：

```
> db.users.find({tags:"cricket"}).explain() 
```

以上命令执行结果中会显示 "cursor" : "BtreeCursor tags_1" ，则表示已经使用了索引。

#### 索引子文档字段

假设我们需要通过 city、state、pincode 字段来检索文档，由于这些字段是子文档的字段，所以我们需要对子文档建立索引。 范例： 为子文档的三个字段创建索引，命令如下：

```
> db.users.ensureIndex({"address.city":1,"address.province":1,"address.pincode":1}) 
```

一旦创建索引，我们可以使用子文档的字段来检索数据：

```
> db.users.find({"address.city":"chengdu"}) 
```

记住查询表达式必须遵循指定的索引的顺序。所以上面创建的索引将支持以下查询：

```
> db.users.find({"address.city":"chengdu","address.province":"sichuan"}) 
```

同样支持以下查询：

```
> db.users.find({"address.city":"chengdu","address.province":"sichuan","address.pincode":"123"}) 
```

## 四、原子操作

所谓原子操作，就是要么执行成功，要么执行失败，执行成功完成既定任务，执行失败还原执行前的状态。 常用原子操作命令：

### $set

用来指定一个键并更新键值，若键不存在并创建。

```
{ $set : { field : value } } 
```

### $unset

用来删除一个键。

```
{ $unset : { field : 1} } 
```

### $inc

$inc 可以对文档的某个值为数字型（只能为满足要求的数字）的键进行增减的操作。

```
{ $inc : { field : value } } 
```

### $push

把 value 追加到 field 里面去，field 一定要是数组类型才行，如果 field 不存在，会新增一个数组类型加进去。

```
{ $push : { field : value } } 
```

### $pushAll

同$push,只是一次可以追加多个值到一个数组字段内。

```
{ $pushAll : { field : value_array } } 
```

### $pull

从数组 field 内删除一个等于 value 值。

```
{ $pull : { field : _value } } 
```

### $addToSet

增加一个值到数组内，而且只有当这个值不在数组内才增加。

### $pop

删除数组的第一个或最后一个元素

```
{ $pop : { field : 1 } } 
```

### $rename

修改字段名称

```
{ $rename : { old_field_name : new_field_name } } 
```

### $bit

位操作，integer 类型

```
{$bit : { field : {and : 5}}} 
```

## 五、查询分析

### explain()

explain 操作提供了查询信息，使用索引及查询统计等。有利于我们对索引的优化。接下来我们在 users 集合中创建 gender 和 user_name 的索引：

```
> db.users.ensureIndex({gender:1,user_name:1})
> db.users.find({gender:"M"},{user_name:1,_id:0}).explain() 
```

结果中字段解释：

*   indexOnly:为 true ，表示我们使用了索引；
*   cursor：因为这个查询使用了索引，MongoDB 中索引存储在 B 树结构中，所以这是也使用了 BtreeCursor 类型的游标。如果没有使用索引，游标的类型是 BasicCursor。这个键还会给出你所使用的索引的名称，你通过这个名称可以查看当前数据库下的 system.indexes 集合（系统自动创建）来得到索引的详细信息；
*   n：当前查询返回的文档数量；
*   nscanned/nscannedObjects：表明当前这次查询一共扫描了集合中多少个文档，我们的目的是，让这个数值和返回文档的数量越接近越好；
*   millis：当前查询所需时间，毫秒数；
*   indexBounds：当前查询具体使用的索引。

### hint()

虽然 MongoDB 查询优化器一般工作的很不错，但是也可以使用 hints 来强迫 MongoDB 使用一个指定的索引。 通过这种方法在某些情形下会提升性能。 范例：指定使用 gender 和 user_name 索引字段来查询：

```
> db.users.find({gender:"M"},{user_name:1,_id:0}).hint({gender:1,user_name:1}) 
```

可以使用 explain() 函数来分析以上查询：

```
> db.users.find({gender:"M"},{user_name:1,_id:0}).hint({gender:1,user_name:1}).explain() 
```

## 六、小结

本节讲解了 MongoDB 的高级查询与索引，支持索引子文档字段，支持原子操作，explain 可以用于查询分析，hint 可以强制指定使用哪一个索引。

## 七、练习

请通过查阅资料把 MongoDB 与一种编程语言结合使用。