# 第 10 节 Ruby 输入输出

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

本次课程介绍 Ruby 的输入输出（I/O）。包括 puts、gets、putc、print 以及对文件（File）和目录（Dir）的操作等等知识点。

## 二、字符的输入输出

之前的实验中，我们已经使用过一些字符输入输出的方法，比如 puts、gets 等。本次实验，我们将介绍 Ruby 更多的输入输出方法。

### 1\. puts & gets

我们已经多次使用过（比如上一实验的作业）。gets 用于从键盘输入字符，puts 用于输出字符在显示器。我们还是举例：

```rb
#!/usr/bin/ruby
puts "please input your words: "

words = gets
puts words 
```

gets 语句把用户的输入存储在变量中，puts 语句在屏幕上显示出用户输入的内容。

### 2\. putc

putc 与 puts 不同，puts 语句输出一整个字符串到屏幕上，而 putc 只是输出一个字符：

```rb
str = "hello"

puts str
putc str 
```

可见输出结果是：

```rb
hello
h 
```

### 3\. print

print 语句也和 puts 语句类似，不同的是，puts 语句在完成输出后会跳到下一行，而使用 print 语句则不会。

```rb
puts "Tom"
print "Mary"
puts "John" 
```

输出结果将会是：

```rb
Tom       # puts 语句输出后，跳到下一行
MaryJohn  # print 语句输出后，没有跳到下一行 
```

## 三、对文件（File）的操作

刚才都只是标准输入输出，现在我们看看对文件的操作。

### 1\. 打开和关闭文件

#### （1） File.new 方法

使用 **File.new** 方法创建一个 File 对象做读写操作，读写权限（mode）可以指定。格式是这样的：

```rb
file_1 = File.new("文件名","模式")
    #……文件读写操作
file_1.close   # 操作完后记得关闭文件 
```

#### （2） File.open 方法

可以使用 **File.open** 方法打开一个文件，创建一个 file 对象，并把该对象赋值给文件。File.open 与 File.new 是不同的：File.open 方法可以与块关联，而 File.new 方法不能。

```rb
File.open("文件名", "模式") do |file_1|
   #……文件读写操作
end 
```

打开文件有几种不同的模式，对应不同的权限：

*   r 只读模式。文件指针被放置在文件的开头。默认的模式。

*   r+ 读写模式。文件指针被放置在文件的开头。

*   w 只写模式。如果文件存在，则重写文件。如果文件不存在，则创建一个新文件用于写入。

*   w+ 读写模式。如果文件存在，则重写已存在的文件。如果文件不存在，则创建一个新文件用于读写。

*   a 只写模式。如果文件存在，则文件指针被放置在文件的末尾。也就是说，文件是追加模式。如果文件不存在，则创建一个新文件用于写入。

*   a+ 读写模式。如果文件存在，则文件指针被放置在文件的末尾。也就是说，文件是追加模式。如果文件不存在，则创建一个新文件用于读写。

### 2\. 读写文件

刚才介绍的只是新建和打开一个文件，而接下来是具体的，对文件的操作。

#### （1） sysread 方法

可以使用方法 sysread 来读取文件的内容，比如我们先创建了一个 secret.txt 文件，里面有一些内容，现在用 sysread 方法把它读出来：

```rb
#!/usr/bin/ruby

file_1 = File.open("secret.txt","r")

words = file_1.sysread(20)
puts words

file_1.close 
```

屏幕上将会显示出 secret.txt 文件的前 20 个字符。

#### （2） syswrite 方法

syswrite 方法用于向文件中写入内容，用法与 sysread 用法类似，我们可以选用“a”模式，把一段内容追加到文件后面：

```rb
#!/usr/bin/ruby

file_1 = File.open("secret.txt","r")

file_1.syswrite("i am syswrite!")

file_1.close 
```

还有很多 **File 类** 和 **IO 类** 的方法，这里不再多说，你可以点击查看 [File 类的方法](http://ruby-doc.com/docs/ProgrammingRuby/html/ref_c_file.html) 和 [IO 类的方法](http://ruby-doc.com/docs/ProgrammingRuby/html/ref_c_io.html)

## 四、对目录（Dir）的操作

文件都是包含在目录中，Ruby 提供了处理文件和目录的方式，File 类用于处理文件，Dir 类用于处理目录。

### 1\. 浏览目录

*   puts Dir.pwd 查看当前目录

*   Dir.cndir("/usr/bin") 改变当前目录为 "/usr/bin"

*   puts Dir.entries("/usr/bin") 显示当前目录下的文件和目录列表

### 2\. 创建和删除目录

很简单，直接举例说明：

```rb
Dir.mkdir("dir_name")   # 创建一个目录
Dir.delete("dir_name")  # 删除一个目录 
```

还有很多 **Dir 类** 的方法，你可以点击查看 [Dir 类的方法](http://ruby-doc.com/docs/ProgrammingRuby/html/ref_c_dir.html)

## 五、作业

在桌面上新建一个 new.txt 文件，写入英文内容，然后将上一个实验的作业程序做修改，使它能够读取 new.txt 文件中的内容，并将内容反转，再写入 new.txt 文件中。

这次作业没有参考答案。

## 六、参考文档

> * 本实验课程基于： [Ruby 官方教程](https://www.ruby-lang.org/zh_cn/documentation/)