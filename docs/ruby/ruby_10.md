# 第 9 节 Ruby 迭代器

## 一、实验说明介绍

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou

### 2\. 环境介绍

本实验环境采用 Ubuntu Linux 桌面环境，实验中会用到桌面上的程序：

1.  命令行终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令
2.  Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可
3.  gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)
4.  gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

### 3\. 环境使用

使用编辑器输入实验所需的代码及文件，使用命令行终端运行所需命令进行操作。

“实验记录”页面可以在“我的主页”中查看，每次实验的截图及笔记，以及有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您在实验楼学习的真实性证明。

### 4\. 实验介绍

本节介绍 Ruby 迭代器（iterator），迭代器通常用来遍历集合内的元素，以达到某些特定操作。内容涵盖 each、collect 迭代器等等知识点。

## 二、each 迭代器

在很多语言里可以使用 for、while 做循环（Ruby 也可以），但是 Ruby 还有一种独特的循环处理方式，叫做 **迭代器**。比如：

```rb
#!/usr/bin/ruby

shuzu = Array[1,2,3,4]

shuzu.each {|n| puts 2*n} 
```

这就是一个 each 迭代器，each 反复调用 **大括号** 中的代码块，将数组的每个元素乘 2，打印出来。

> 代码块可以用“{|x| ……}”形式，也可以是“do |x| …… end”形式。一般情况下我们选择“{}”写法，代码块多行时采用“do end”写法。

刚才的迭代器其实可以用 for 循环实现：

```rb
for n in 1..4 do
    puts 2*n
end 
```

但显然使用迭代器更加方便。

## 三、collect 迭代器

除了 each 迭代器，还有一种常用的 collect 迭代器。

它们之间的区别是，each 迭代器连续访问集合中的每个元素，而 collect 迭代器从集合中获得各个元素，返回的结果生成新的集合。举例说明更好理解：

```rb
shuzu = Array[1,2,3,4]

shuzu_each = shuzu.each {|n| n=2*n}
shuzu_collect = shuzu.collect {|n| n=2*n}

puts "#{shuzu}"
puts "#{shuzu_each}"
puts "#{shuzu_collect}"
puts "#{shuzu}" 
```

观察运行结果：

```rb
[1, 2, 3, 4]  # 原始的集合（数组）
[1, 2, 3, 4]  # each 迭代后，不会产生新的集合
[2, 4, 6, 8]  # collect 迭代产生新的集合
[1, 2, 3, 4]  # 迭代之后，原始集合没有受改变 
```

## 四、作业

现在，实际应用一下迭代器：编写一个 Ruby 程序，输入一句话，输出这句话的反转。

**注意：**是一句话的反转，而不仅是反转所有的字符，比如输入“Tom love Mary”，需要输出“Mary love Tom”，而不是“yraM evol moT”。

参考答案：

```rb
def reverse_words(str)
    str=str.reverse    # 反转整个句子
    shuzu=str.split()    # 以空格为分隔符，将 str 分割为多个子字符串，作为数组元素
    str=shuzu.collect{|x| x.reverse}.join(' ')  # 再对每个子字符串做反转，将数组元素合并为字符串，每个元素间插入空格
    puts str  # 打印出反转后的句子
end

print "enter your words: "
words=gets    # 输入句子

print "result: "
reverse_words(words)    # 调用 reverse_words 方法，处理句子 
```

## 五、参考文档

> * 本实验课程基于： [Ruby 官方教程](https://www.ruby-lang.org/zh_cn/documentation/)