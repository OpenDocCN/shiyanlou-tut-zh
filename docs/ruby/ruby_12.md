# 第 11 节 Ruby 异常处理

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

本节课程为 Ruby 系列课程最后一节，主要涉及 Ruby 对异常的处理。您将学习到 Ruby 的各种异常处理方式。

## 二、rescue 语句

Ruby 提供了很好的异常处理机制。我们可以在 begin/end 块中附上可能抛出异常的代码，并使用 rescue 子句告诉 Ruby 要处理的异常类型。格式为：

```rb
begin  
# -  
rescue 异常 _1
# -  
rescue 异常 _2  
# -  
else  
# 其他异常
ensure
# 总是被执行
end 
```

Ruby 把抛出的异常与每个 rescue 子句进行轮流比较,如果 rescue 子句中命名的异常与当前抛出的异常类型相同，或者是该异常的父类，则匹配成功。

如果异常不匹配所有指定的错误类型，我们可以在所有的 rescue 子句后使用一个 else 子句。我们举例 rescue 的使用：

```rb
#!/usr/bin/ruby

begin
   file = open("/不存在的文件")
   if file
      puts "File opened successfully"
   end
rescue
      file = STDIN
end
print file, "==", STDIN, "\n" 
```

运行结果可见 STDIN 取代了 file ，说明发生了异常处理：

```rb
#<IO:0x0000000065a338>==#<IO:0x0000000065a338> 
```

## 三、retry 语句

在 rescue 语句中做异常处理，可以使用 retry 语句，从头开始执行 begin 块，比如：

```rb
#!/usr/bin/ruby

begin
   file = open("/不存在的文件")
   if file
      puts "File opened successfully"
   end
rescue
      fname = "/已经存在的文件"
      retry
end 
```

很容易看出处理流程：

*   打开文件发生异常（因为文件名不存在）。

*   跳到 rescue ，异常处理 fname 被重新赋值为另一个，已存在的文件。

*   通过 retry 跳到 begin 开头。

*   这次打开文件成功。

> 需要注意的是，如果 rescue 里，fanme 重新赋值的文件依然不存在，那么将陷入无限循环。所以谨慎使用 retry。

## 四、raise 语句

使用 raise 语句抛出异常，有以下几种方式：

*   1、raise 简单地重新抛出当前异常，传入异常之前需要解释异常。

*   2、raise "Error Mwssage" 创建一个新的 RuntimeError 异常，设置它的消息为给定的字符串。该异常之后抛出到调用堆栈。

*   3、raise ExceptionType, "Error Message" 第一个参数创建一个异常，然后设置相关的消息为第二个参数。

*   4、raise ExceptionType, "Error Message" condition 与第三种形式类似，您可以添加任何额外的条件语句（比如 unless）来抛出异常。

举例说明：

```rb
#!/usr/bin/ruby

begin 
    puts 'I am before the raise.' 
    raise 'An error has occurred.' 
    puts 'I am after the raise.' 
rescue 
    puts 'I am rescued.' 
end 
puts 'I am after the begin block.' 
```

运行结果便于观察：

```rb
I am before the raise. 
I am rescued. 
I am after the begin block. 
```

## 五、ensure 语句

有时候，无论是否抛出异常，都需要保证一些处理在代码块结束时完成。例如，您可能在进入时打开了一个文件，当您退出块时，您需要确保关闭文件。

ensure 子句做的就是这个。ensure 放在最后一个 rescue 子句后，并包含一个块终止时总是执行的代码块。它与块是否正常退出、是否抛出并处理异常、是否因一个未捕获的异常而终止，这些都没关系，ensure 块始终都会运行。

基本使用格式：

```rb
begin
   #…… 过程
   #…… 抛出异常
rescue
   #…… 处理错误
ensure
   #…… 最后确保执行
   #…… 这总是会执行
end 
```

## 六、else 语句

else 子句一般是放置在 rescue 子句之后，任意 ensure 之前，它只有在代码主体没有抛出异常时执行：

```rb
begin
   #…… 过程
   #…… 抛出异常
rescue
   #…… 处理错误
else
   #…… 如果没有异常则执行
ensure
   #…… 最后确保执行
   #…… 这总是会执行
end 
```

## 七、throw 和 catch

catch 和 throw 用于跳出一些嵌套结构，比如以下程序：

```rb
arr = ["a","b","c","d","e","f"]

def test(words)
    for x in words do
        throw :hello,1 if x == "d"
        print x
    end
end

catch :hello do 
    test(arr)
end 
```

程序依次打印出 arr[] 中的字符，直到遇到字符“d”为止，会发生一个 throw ，停止循环，因此运行结果是：

```rb
abc 
```

## 八、Exception 类

我们自己创建的异常类，必须是 Exception 类的子类或其子类的子类。Exception 类之下有 7 种不同的类型：

*   Interrupt
*   NoMemoryError
*   SignalException
*   ScriptError
*   StandardError
*   SystemExit

我们举例说明：

```rb
class FileSaveError < StandardError
   attr_reader :reason
   def initialize(reason)
      @reason = reason
   end
end 
```

接下来的实例，将用到上面我们创建的异常：

```rb
File.open(path, "w") do |file|
    begin
        # 写出数据
    rescue
        # 发生错误
    raise FileSaveError.new($!)
    end
end 
```

这里最重要的一行是 raise FileSaveError.new($!)。我们调用 raise 来示意异常已经发生，把它传给 FileSaveError 的一个新的实例，由于特定的异常引起数据写入失败。

## 九、作业思考

> * 异常处理是程序设计中非常重要的一块。不同情形下，可能需要不同的异常处理方式，这是值得思考的。

## 十、参考文档

> * 本实验课程基于：[Ruby 官方教程](https://www.ruby-lang.org/zh_cn/documentation/)