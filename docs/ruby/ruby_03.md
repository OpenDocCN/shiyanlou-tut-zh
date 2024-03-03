# 第 2 节 Ruby 类和对象

## 一、实验说明介绍

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou

### 2\. 环境介绍

本实验环境采用 Ubuntu Linux 桌面环境，实验中会用到桌面上的程序：

1.  命令行终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令
2.  Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可
3.  gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程 [Vim 编辑器](http://www.shiyanlou.com/courses/2)
4.  gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

### 3\. 环境使用

使用编辑器输入实验所需的代码及文件，使用命令行终端运行所需命令进行操作。

“实验记录”页面可以在“我的主页”中查看，每次实验的截图及笔记，以及有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您在实验楼学习的真实性证明。

### 4\. 实验介绍

本次课程学习 Ruby 类和对象的相关知识。包括如何定义一个类，以及如何创建类的对象。

## 二、类的定义

### 1.定义一个类

```rb
$ vim test.rb 
```

输入代码：

```rb
 #!/usr/bin/ruby -w

 class Customer
     @@no_of_customer=0
     def hello(id, name, addr)
         @cust_id=id
         @cust_name=name
         @cust_addr=addr
     end
 end 
```

*   以 `@@` 开始的是类变量

*   以 `@` 开始的是实例变量

*   以 `$` 开始的是全局变量

*   以小写或者是 `_` 开头的是局部变量

## 三、创建类的对象

### 1.对象

类中定义的方法称为类的成员函数。使用 new 关键字创建类的对象。

```rb
$ vim test.rb 
```

输入代码：

```rb
 #!/usr/bin/ruby -w

 class Customer
     def hello
         puts "Hello, World."
     end
 end

 cust1 = Customer.new
 cust2 = Customer. new # new 前面也可以有一个空格
 cust1.hello
 cust2.hello 
```

运行代码：

```rb
$ ruby test.rb 
```

![图片描述信息](img/01f61fc00d3083b549e299e053706e2b.jpg)

在上面的代码中定义了类 Customer，其中定义了类的成员函数 hello（方法），其功能语句是 puts "Hello, World."

### 2.initialize 函数

initialize 函数是类初始化函数，创建对象时，会自动调用，并可通过 new 传递参数给 initialize 函数。

```rb
$ vim test.rb 
```

输入代码：

```rb
 #!/usr/bin/ruby -w

 class Customer
     @@no_of_customer=0
     def initialize(name)
         @cust_name=name
     end

     def hello
         puts "Hello, " + @cust_name
     end
 end

 cust = Customer.new("Jack")
 cust.hello 
```

运行代码：

```rb
$ ruby test.rb 
```

![图片描述信息](img/81c98d3b10f01dbe1ce99bd51fe1e6b1.jpg)

## 四、作业思考

> * 试着自己分析上面的示例程序。

## 五、参考文档

> * 本实验课程基于：[Ruby 官方教程](https://www.ruby-lang.org/zh_cn/documentation/)