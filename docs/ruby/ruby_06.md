# 第 5 节 Ruby 代码块和运算符

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

本次课程讲解 Ruby 中的代码块和运算符。将为您介绍什么是代码块，运算符的使用等等知识点。

## 二、代码块

### 1.什么是块？

块由大量的代码组成；

代码包含在大括号或者 `do…end` 内；

块只能跟在方法调用之后，例如您的块名称为 `block\_te`，那么必须要 `block\_te` 函数来调用块。

定义：

```rb
block_name{
statements
} 
```

### 2.yield 语句

Tips：在方法内部，yield 的作用是占位，当方法执行到 yield 时实际执行的是代码块中的内容； `|X|` 是在代码块中使用的，用于接收 yield 传递的参数，且参数传给 `X`。

范例：

```rb
#!/usr/bin/ruby        #文件名为 yield_NO.rb

def test
   puts "You are in the method"
   yield                #执行代码块
   puts "You are again back to the method"
   yield                #执行代码块
end
test {puts "You are in the block"} 
```

运行：

```rb
$ ruby yield_NO.rb 
```

![图片描述信息](img/f57e8a03f3f2486348715d91d13f3c4b.jpg)

```rb
#!/usr/bin/ruby        #文件名为 yield_YES.rb

def test
   yield 5            #执行代码块
   puts "You are in the method test"
   yield 100            #执行代码块
end
test {|i| puts "You are in the block #{i}"} 
```

运行：

```rb
$ ruby yield_YES.rb 
```

![图片描述信息](img/55d8754e582fe80a57cad9d226fa7239.jpg)

### 3\. BEGIN 和 END 块

BEGIN 和 END 块分别定义程序被加载就执行的代码块和程序结束时执行的代码块。

范例：

```rb
#!/usr/bin/ruby        #文件名为 be_and_in.rb

BEGIN { 
  #BEGIN 代码块
  puts "BEGIN code block."
}

END { 
  #END 代码块
  puts "END code block."
}
puts "MAIN code." 
```

运行：

```rb
$ ruby be_and_in.rb 
```

![图片描述信息](img/e9d5e5b706343e9ea88fa994cb6c334b.jpg)

## 三、运算符

### 1.与其他语言的运算符关系

在 Ruby 中很多的运算符跟其他语言都是相同的，（比如基本的 `+`、`-`、`*`、`/` **算术运算** ，以及 **位运算** 和 **逻辑运算** 等）但是还是有一部分是不一样的，下面将对 Ruby 的部分运算符进行操作实验。

### 2.比较运算符

.eql?：如果原参数与接收到的参数具有相同的类型和值，则返回 true。

范例：

`2.eql?(2.0)` 返回 `false`

equal?：如果原参数与接收到的参数具有相同的对象 id，则返回 true。

### 3.并行赋值

范例：

```rb
a,b,c=10,20,30 
```

等价于：

```rb
a=10 b=20 c=30 
```

### 4.变量交换

```rb
a,b=b,a 
```

交换了 a 和 b 的值，也可以多个变量同时交换

### 5.三元运算符

三元运算符 -?:

范例：

```rb
a>b?c=b:c=a
#如果 a 大于 b 则将 b 的值赋给 c，否则将 a 的值赋给 c 
```

### 6.范围运算符

在 Ruby 中有两个范围运算符，分别是 `..`和 `…`

`..` ：表示创建一个从开始点到结束点的范围（包含结束点）

`…` ：表示创建一个从开始点到结束点的范围（不包含结束点）

范例：

```rb
1..10
#创建从 1 到 10 的范围

1…10
#创建从 1 到 9 的范围 
```

### 7.defined?运算符

defined? 运算符是以调用相关方法的形式来判断传递的参数是否已经定义，如果表达式未定义则返回 nil，如果已定义则会返回参数描述。

范例：

```rb
#!/usr/bin/ruby        #文件名为 defined.rb
a=100
puts “a is defined?”,defined? a
puts “test is defined?”,defined? test 
```

运行：

```rb
$ ruby defined.rb
a is defined?
local-variable
test is defined? 
```

### 8.点运算符和双冒号运算符

在 Ruby 中类和方法都可以被当做常量来使用 `::` 运算符： 可以使用一个模块名称和双冒号来引用一个常量； 双冒号运算符允许在类或模块内定义常量、实例方法和类方法，可以从类或模块外的任何地方进行访问。

范例：

```rb
#!/usr/bin/ruby                #文件名为 const_te.rb
CONST = ' out there'            #定义常量
class Inside_one
   CONST = proc {' in there'}     
   def where_is_my_CONST
      ::CONST + ' inside one'    #引用常量
   end
end
class Inside_two
   CONST = ' inside two'
   def where_is_my_CONST
      CONST
   end
end
puts Inside_one.new.where_is_my_CONST
puts Inside_two.new.where_is_my_CONST
puts Object::CONST + Inside_two::CONST
puts Inside_two::CONST + CONST
puts Inside_one::CONST
puts Inside_one::CONST.call + Inside_two::CONST 
```

若未使用前缀表达式，则默认使用主 Object 类。 Proc 是对块及其 context(局部变量的作用域以及栈框架)进行对象化处理之后得到的过程对象。

## 四、作业思考

> * 运算符介绍的比较多，建议多加练习。 > * 思考分析 “块和方法” 什么关系。

## 五、参考文档

> * 本实验课程基于：[Ruby 官方教程](https://www.ruby-lang.org/zh_cn/documentation/)