# 第 7 节 Ruby 模块

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

本课讲解 Ruby 模块（module）相关知识。

## 二、模块 - module

模块在 Ruby 里面有两个作用：

*   第一：把功能相似的函数放在同一个名称之下；

*   第二：方便调用方法。

### 1\. 模块示例

创建一个 Hello 模块的范例：

```rb
#!/usr/bin/ruby                #文件名为 module.rb

module Hello                    #模块名的首写字母与类一样都必须大写
    def self.hello_python       #self 为关键字
        print "Hello Python!"
    end
    def self.hello_ruby
        print "Hello Ruby!"
    end
end

Hello.hello_python
Hello.hello_ruby                #调用模块中的方法 
```

### 2\. require 语句

require 语句与 C 语言中的 include 类似，是用来加载模块文件的。

范例：

```rb
#!/usr/bin/ruby

$LOAD_PATH  <<  ‘.’    #在当前目录搜索被引用文件
require  'module'        #或者是 require  'module.rb' ，这里填的是文件名

Hello.hello_python
Hello.hello_ruby        #调用 Hello 模块中的方法 
```

### 3\. 在类中引用模块 -include

范例： 现有模块文件

```rb
#!/usr/bin/ruby        #文件名为 support.rb

module Week
   FIRST_DAY = "Sunday"
   def Week.weeks_in_month
      puts "You have four weeks in a month"
   end
   def Week.weeks_in_year
      puts "You have 52 weeks in a year"
   end
end 
```

现在在类中来引用这个模块

```rb
#!/usr/bin/ruby        #文件名为 include.rb
$LOAD_PATH << '.'
require "support"

class Decade
include Week
   no_of_yrs=10
   def no_of_months
      puts Week::FIRST_DAY
      number=10*12
      puts number
   end
end
puts Week::FIRST_DAY
Week.weeks_in_month
Week.weeks_in_year 
```

运行：

```rb
$ ruby include.rb
Sunday
You have four weeks in a year    .
You have 52 weeks in a year. 
```

### 4\. mixins 装置

在面向对象的语言中继承是非常重要的，但是 Ruby 不直接支持继承，幸运的是 Ruby 的模块提供了 mixins 装置，而这几乎消除了对多重继承的需要。

举例：

```rb
module A
   def a1
   end
   def a2
   end
end
module B
   def b1
   end
   def b2
   end
end

class Sample
include A
include B
   def s1
   end
end
samp=Sample.new
samp.a1
samp.a2
samp.b1
samp.b2
samp.s1 
```

在模块 A 中包含了方法 a1 和 a2，模块 B 包含了 b1 和 b2。同时类 Sample 中包含了模块 A 和 B，因此类 Sample 继承了两个模块，可以访问模块 A 和 B 包含的四个方法。

## 三、作业思考

> * 模块（module）和之前学习的 “代码块” 有什么区别？

## 四、参考文档

> * 本实验课程基于：[Ruby 官方教程](https://www.ruby-lang.org/zh_cn/documentation/)