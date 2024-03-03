# 第 2 节 PHP 基本语法

## 一、实验说明

### 1\. 环境登录

无需密码自动登录，系统用户名 shiyanlou

### 2\. 环境介绍

本实验环境采用 Ubuntu Linux 桌面环境，实验中会用到桌面上的程序：

1.命令行终端: Linux 命令行终端，打开后会进入 Bash 环境，可以使用 Linux 命令

2.Firefox 及 Opera：浏览器，可以用在需要前端界面的课程里，只需要打开环境里写的 HTML/JS 页面即可

3.gvim：非常好用的 Vim 编辑器，最简单的用法可以参考课程[Vim 编辑器](http://www.shiyanlou.com/courses/2)

4.gedit 及 Brackets：如果您对 gvim 的使用不熟悉，可以用这两个作为代码编辑器，其中 Brackets 非常适用于前端代码开发

### 3\. 环境使用

使用编辑器输入实验所需的代码及文件，使用命令行终端运行所需命令进行操作。

“实验记录”页面可以在“我的主页”中查看，每次实验的截图及笔记，以及有效学习时间（指的是在实验桌面内操作的时间，如果没有操作，系统会记录为发呆时间）。这些都是您在实验楼学习的真实性证明。

### 4\. 课程介绍

本节介绍了 PHP 基本的语法，以帮助您快速上手 PHP。本节讲了在嵌入 HTML 时 PHP 用的标记有四种，`<?php ?>`,`<script language='php'> </script>`,`<? ?>`(需要开启 short*open*tag),`<% %>`(需要开启 asp_tags),可以在 php.ini 中开启，推荐使用`<?php ?>`。PHP 的注释有三种分别是//,#,/**/>。

## 二、PHP 标记

解析一个文件时，PHP 会寻找起始和结束标记，也就是 `<?php` 和 `?>`，这告诉 PHP 开始和停止解析二者之间的代码。此种解析方式使得 PHP 可以被嵌入到各种不同的文档中去，而任何起始和结束标记之外的部分都会被 PHP 解析器忽略。 如果文件内容是纯 PHP 代码，最好在文件末尾删除 PHP 结束标记。这可以避免在 PHP 结束标记之后万一意外加入了空格或者换行符，会导致 PHP 开始输出这些空白，而脚本中此时并无输出的意图。

```php
<?php
echo "Hello world";

// ... more code

echo "Last statement";

// 脚本至此结束，并无 PHP 结束标记 
```

## 三、从 HTML 中分离

凡是在一对开始和结束标记之外的内容都会被 PHP 解析器忽略，这使得 PHP 文件可以具备混合内容。 可以使 PHP 嵌入到 HTML 文档中去，如下例所示。

```php
<p>This is going to be ignored by PHP and displayed by the browser.</p>
<?php echo 'While this is going to be parsed.'; ?>
<p>This will also be ignored by PHP and displayed by the browser.</p> 
```

这将如预期中的运行，因为当 PHP 解释器碰到 ?> 结束标记时就简单地将其后内容原样输出（除非马上紧接换行 )直到碰到下一个开始标记；例外是处于条件语句中间时，此时 PHP 解释器会根据条件判断来决定哪些输出，哪些跳过。见下例。 使用条件结构：

```php
<?php if ($expression == true): ?>
  This will show if the expression is true.
<?php else: ?>
  Otherwise this will show.
<?php endif; ?> 
```

上例中 PHP 将跳过条件语句未达成的段落，即使该段落位于 PHP 开始和结束标记之外。由于 PHP 解释器会在条件未达成时直接跳过该段条件语句块，因此 PHP 会根据条件来忽略之。 要输出大段文本时，跳出 PHP 解析模式通常比将文本通过 echo 或 print 输出更有效率。

可以在 PHP 中使用四对不同的开始和结束标记。其中两种，`<?php ?>` 和 `<script language="php">` `</script>` 总是可用的。另两种是短标记和 ASP 风格标记，可以在 php.ini 配置文件中打开或关闭。尽管有些人觉得短标记和 ASP 风格标记很方便，但移植性较差，通常不推荐使用。 **Note: 此外注意如果将 PHP 嵌入到 XML 或 XHTML 中则需要使用 `<?php ?>` 标记以保持符合标准。**

## 四、指令分隔符

同 C 或 Perl 一样，PHP 需要在每个语句后用分号结束指令。一段 PHP 代码中的结束标记隐含表示了一个分号；在一个 PHP 代码段中的最后一行可以不用分号结束。如果后面还有新行，则代码段的结束标记包含了行结束。

```php
<?php
    echo "This is a test";
?>

<?php echo "This is a test" ?>

<?php echo 'We omitted the last closing tag'; 
```

**Note: 文件末尾的 PHP 代码段结束标记可以不要，有些情况下当使用 include 或者 require 时省略掉会更好些，这样不期望的空白符就不会出现在文件末尾，之后仍然可以输出响应标头。在使用输出缓冲时也很便利，就不会看到由包含文件生成的不期望的空白符。**

## 五、注释

PHP 支持 C，C++ 和 Unix Shell 风格（Perl 风格）的注释。例如:

```php
<?php
    echo "This is a test"; // This is a one-line c++ style comment
    /* This is a multi line comment
       yet another line of comment */
    echo "This is yet another test";
    echo 'One Final Test'; # This is a one-line shell-style comment
?> 
```

单行注释仅仅注释到行末或者当前的 PHP 代码块，视乎哪个首先出现。这意味着在 // ... ?> 或者 # ... ?> 之后的 HTML 代码将被显示出来：?> 跳出了 PHP 模式并返回了 HTML 模式，// 或 # 并不能影响到这一点。如果启用了 asp_tags 配置选项，其行为和 // %> 或 # %> 相同。不过，`</script>` 标记在单行注释中不会跳出 PHP 模式。

```php
<h1>This is an <?php # echo 'simple';?> example</h1>
<p>The header above will say 'This is an  example'.</p> 
```

C 风格的注释在碰到第一个 */ 时结束。要确保不要嵌套 C 风格的注释。试图注释掉一大块代码时很容易出现该错误。

```php
<?php
 /*
    echo "This is a test"; /* This comment will cause a problem */
 */
?> 
```

## 六、作业练习

请使用上面四种 php 标记，在网页中输出“shiyanlou is good”。

实验中有任何问题欢迎到[实验楼问答](http://www.shiyanlou.com/questions)提问。