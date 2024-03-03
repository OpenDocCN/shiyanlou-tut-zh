# 第 3 节 Ruby 变量

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

本次课程讲解 Ruby 的变量。包括 全局变量、局部变量、实例变量以及类变量。

## 二、全局变量

### 1.什么是全局变量？

全局变量以 `$` 开头，并且未初始化的全局变量值为空；全局变量能够跨类使用。

范例：

```rb
#!/usr/bin/ruby        #文件名为 global.rb

$global_var = 10        #定义全局变量
class Class1
  def print_global
     puts "Global variable in Class1 is #$global_var."
     #通过在变量或常量的前面放置”#”，可以实现对任何变量或常量的访问
  end
end
class Class2
  def print_global
     puts "Global variable in Class2 is #$global_var."
  end
end

class1obj = Class1.new    #创建一个对象
class1obj.print_global    #对象调用成员函数，成员函数调用全局变量
class2obj = Class2.new
class2obj.print_global 
```

运行：

```rb
$ ruby global.rb
Global variable in Class1 is 10.
Global variable in Class2 is 10. 
```

![图片描述信息](img/c3208526241f26abd3b36b1732c6f8e3.jpg)

## 三、实例变量

### 1.什么是实例变量？

实例变量以 `@` 开头；实例变量可以跨任何特定的实例或对象中的方法。

范例：

```rb
#!/usr/bin/ruby            #文件名为 cust.rb

class Customer
   def initialize(id, name, addr)
      @cust_id=id        #定义一个 cust_id 实例变量
      @cust_name=name
      @cust_addr=addr
   end
   def display_details()
      puts "Customer id #@cust_id"
      puts "Customer name #@cust_name"
      puts "Customer address #@cust_addr"
                        #访问实例变量
    end
end

cust1=Customer.new("1", "John", "Wisdom Apartments, Ludhiya")
cust2=Customer.new("2", "Poul", "New Empire road, Khandala")
                        #创建两个对象
cust1.display_details()        #调用成员函数，成员函数调用实例变量
cust2.display_details() 
```

运行：

```rb
$ ruby cust.rb 
```

![图片描述信息](img/48fa65c0036b200d22379102f0439ea5.jpg)

## 四、类变量

### 1.什么是类变量？

类变量以 `@@` 开头，而且需要初始化之后才能在方法定义中使用； 类变量属于类，是类的一个属性，同时类变量可以跨不同的对象使用。

范例：

```rb
#!/usr/bin/ruby        #文件名为 class_var.rb

class Customer
   @@no_of_customers=0
   def initialize(id, name, addr)
      @cust_id=id
      @cust_name=name
      @cust_addr=addr
   end
   def display_details()
      puts "Customer id #@cust_id"
      puts "Customer name #@cust_name"
      puts "Customer address #@cust_addr"
    end
    def total_no_of_customers()
       @@no_of_customers += 1
       puts "Total number of customers: #@@no_of_customers"
    end
end
cust1=Customer.new("1", "John", "Wisdom Apartments, Ludhiya")
cust2=Customer.new("2", "Poul", "New Empire road, Khandala")
cust1.total_no_of_customers()
cust2.total_no_of_customers() 
```

运行：

```rb
$ ruby class_var.rb 
```

![图片描述信息](img/efa6619ff4009781a6faa968629e4eff.jpg)

## 四、局部变量

### 1.什么是局部变量？

局部变量是在方法中定义的变量，在方法外不可定义； 局部变量是以小写字母或者下划线 `_` 开头的。

## 五、作业思考

> * 找出上面的例子中，哪些是局部变量？

> * 参考：(id, name, addr)

## 六、参考文档

> * 本实验课程基于：[Ruby 官方教程](https://www.ruby-lang.org/zh_cn/documentation/)