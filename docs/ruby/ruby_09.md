# 第 8 节 字符串、数组、Hash

## 一、实验说明

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

本节课讲解 Ruby 中的字符串（String）、数组（Array）以及 Hash，以及它们的用法。

## 二、字符串 (String)

前面的实验其实已经使用过 String 类，这里我们将更详细地讨论更多的用法。

这样一个简单的程序：

```rb
#!/usr/bin/ruby

str = 'hello ruby'
puts str 
```

str 是一个 string 类的对象，运行结果已经不必多说。

### 1\. 转义字符

我们想要它的内容中有更多丰富的东西，不可避免会使用到 **转义字符** 。Ruby 中的转义字符和其它很多语言类似，以下是一些常见的：

```rb
| 符号  |    描述      |
|:------|:-------------|
| \a    | 报警符       |
| \b    | 退格         |
| \f    | 换页符       |
| \n    | 换行符       |
| \r    | 回车符       |
| \s    | 空格符       |
| \t    | 制表符       |
| \v    | 垂直制表符符 |
```

注意以下情况：

```rb
#!/usr/bin/ruby

str1 = 'hello \n str1'
puts str1

str2 = "hello \n str2"
puts str2 
```

执行结果将会是：

![图片描述信息](img/834c0c4c748b654547ece53e8ea1e60a.jpg)

> 这是因为双引号括起来的字符串内，转义字符会被解释； > 而单引号括起来的字符串内，转义字符会被保留为普通字符。

### 2\. String 类的内置方法

和其它很多编程语言一样，Ruby 中对 String 类有很多内置的方法。下面举例说明。

```rb
#!/usr/bin/ruby

str1 = "I love Ruby"
str2 = str1.reverse     # 倒序
str3 = str1.delete("y") # 删除字符“y”
str4 = str1.sub("love","hate")  # 替换

puts str2
puts str3
puts str4 
```

运行结果：

![图片描述信息](img/c4e242724bfbbe8aca4afdd8286bdaec.jpg)

String 类的内置方法非常多，在这里就不赘述，你可以 [点击这里查看 String 类的更多内置方法](http://ruby-doc.com/docs/ProgrammingRuby/html/ref_c_string.html)

## 三、数组 (Array)

Ruby 中的数组从 0 开始编号，这与其它很多语言一样。数组中可以存储 String, Integer, Float 等类型的元素，甚至也可以是另一个数组。

数组有多种创建方式，比如：

```rb
shuzu_1 = Array.new     # 创建一个数组
shuzu_2 = Array.new(10) # 创建一个有 10 个元素的数组
shuzu_3 = Array.new(4,"test") # 4 个元素，每个元素都是 'test'
shuzu_4 = Array[1,2,3,4]  # 4 个元素的数组，给每个元素赋值 
```

数组类也有很多内置的方法，这里也举例说明：

```rb
shuzu_1 = Array['a','b','c','d','e']
shuzu_2 = Array['c','d','f','g','e']

shuzu_3 = shuzu_1 & shuzu_2  # 两个数组的交集
shuzu_4 = shuzu_1 + shuzu_2  # 两数组连接
shuzu_5 = shuzu_1 | shuzu_2  # 两数组合并，去除重复
shuzu_6 = shuzu_1 - shuzu_2  # 两数组相减
shuzu_1.delete_at(3)         # 去除第 3 个元素
shuzu_7 = shuzu_1.insert(3,'d') # 在第 3 位置插入元素 'd'
shuzu_8 = shuzu_2.sort       # 对数组中的元素排序 
```

数组类的内置方法非常多，你可以 [点击这里查看数组类的更多内置方法](http://ruby-doc.com/docs/ProgrammingRuby/html/ref_c_array.html)

## 四、Hash

Hash 与 数组 十分相似，不过 Hash 保存的是 **键值对** （key-value）的组合，键和值可以是 String, Integer, Float, Array, Hash 各种类型。

与数组很像，我们有各种方式创建 Hash：

```rb
hs = Hash.new     # 创建一个 Hash
hs_2 = Hash.new("month") # 创建一个默认值是 "month" 的 Hash
hs_3 = Hash["ja"=>"January", "fe"=>"February"] # 第 1 对：key=ja, value=January；第 2 对：key=fe, value=February 
```

Hash 也有各种内置方法，比如：

```rb
hs = Hash["t"=>"Tom", "m"=>"Mary", "r"=>"Rose", "k"=>"King"] 

hs.["t"]         # 使用键 "t" 从 Hash 引用值
hs.["t"]="Tony"  # 将键 "t" 对应的值改为 "Tony"
hs.delete("m")   # 删除键 "m" 对应的键值对
hs.index("Rose") # 查找值 "Rose" 对应的键 
```

Hash 类的内置方法非常多，你可以 [点击这里查看 Hash 类的更多内置方法](http://ruby-doc.com/docs/ProgrammingRuby/html/ref_c_hash.html)

## 五、作业思考

> * 用数组和 Hash 实现本课中的例子。

## 六、参考文档

> * 本实验课程基于： [Ruby 官方教程](https://www.ruby-lang.org/zh_cn/documentation/)