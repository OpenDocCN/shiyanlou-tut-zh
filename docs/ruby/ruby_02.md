# 第 1 节 Ruby 基础语法

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

本次课程你会学习到使用 Ruby 来实现经典的 `Hello World`，以及 Ruby 的一些基础语法。

## 二、第一个 Ruby 程序

### 1.Hello World

Ruby 程序的扩展名是 `.rb` 。

```rb
$ vim hello.rb 
```

输入代码：

```rb
 #!/usr/bin/ruby -w

 puts "Hello World!"; 
```

运行代码：

```rb
$ ruby hello.rb 
```

## 三、基本语法

### 1.文档终止符（`<<`）

Ruby 中的多行文本需要使用 `<<`（文档终止符）符号定义的字面量来终止，`<<` 符号和其后定义的字面量之间不能有空格，用法如下：

```rb
$ vim test.rb 
```

输入代码：

```rb
 #!/usr/bin/ruby -w

 print <<EOF
     This is the first way of creating
     here document ie. multiple line string.
 EOF

 print <<"EOF";                # same as above
     This is the second way of creating
     here document ie. multiple line string.
 EOF

 print <<`EOC`                 #  execute commands
     echo hi there
     echo lo there
 EOC

 print <<"foo", <<"bar"  # you can stack them
     I said foo.
 foo
     I said bar.
 bar 
```

运行代码：

```rb
$ ruby test.rb 
```

### 2.BEGIN 语句

BEGIN 语句中包含的代码会在程序运行前运行。

```rb
$ vim test.rb 
```

输入代码：

```rb
 #!/usr/bin/ruby

 puts "This is main Ruby Program"

 BEGIN {
    puts "Initializing Ruby Program"
 } 
```

运行代码：

```rb
$ ruby test.rb 
```

### 3.END 语句

END 语句中包含的代码会在程序运行后运行。

```rb
$ vim test.rb 
```

输入代码：

```rb
 #!/usr/bin/ruby

 puts "This is main Ruby Program"

 END {
    puts "Terminating Ruby Program"
 }
 BEGIN {
     puts "Initializing Ruby Program"
     # This is a comment
 } 
```

> `#` 符号后面的是注释。

### 4\. 注释

单行注释以 # 字符开始，直到该行结束，如上面那段代码中的注释。

你可以使用 `=begin` 和 `=end` 进行多行注释：

```rb
#!/usr/bin/ruby

=begin
puts "this is line 1"
puts "this is line 2"
=end

puts "this is line 3" 
```

执行结果是这样：

```rb
this is line 3 
```

![图片描述信息](img/ccd5943db0e60c54844e9d14ce9b895f.jpg)

## 四、作业思考

> * 比较 `#` 和 `=begin`、`=end` 两种注释方式。

## 五、参考文档

> * 本实验课程基于 [Ruby 官方教程](https://www.ruby-lang.org/zh_cn/documentation/)