# 第 11 节 简单的文本处理

## 实验介绍

这一节我们将介绍这几个命令`tr`（注意不是 tar），`col`，`join`，`paste`。实际这一节是上一节关于能实现管道操作的命令的延续，所以我们依然将结合管道来熟悉这些命令的使用。

## 一、常用的文本处理命令

## 二、文本处理命令

### 1.tr 命令

tr 命令可以用来删除一段文本信息中的某些文字。或者将其进行转换。

#### 使用方式：

```
tr [option]...SET1 [SET2] 
```

#### 常用的选项有：

```
选项|说明
----|---
-d | 删除和 set1 匹配的字符，注意不是全词匹配也不是按字符顺序匹配
-s | 去除 set1 指定的在输入文本中连续并重复的字符
```

#### 操作举例：

```
# 删除 "hello shiyanlou" 中所有的'o','l','h'
$ echo 'hello shiyanlou' | tr -d 'olh'
# 将"hello" 中的 ll,去重为一个 l
$ echo 'hello' | tr -s 'l'
# 将输入文本，全部转换为大写或小写输出
$ cat /etc/passwd | tr '[:lower:]' '[:upper:]'
# 上面的'[:lower:]' '[:upper:]'你也可以简单的写作'[a-z]' '[A-Z]',当然反过来将大写变小写也是可以的 
```

![](img/md04173379-1.jpg)

更多 tr 的使用，你可以使用`--help`或者`man tr`获得。

### 2.col 命令

col 命令可以将`Tab`换成对等数量的空格建，或反转这个操作。

#### 使用方式：

```
col [option] 
```

#### 常用的选项有：

```
选项 | 说明
----|----
-x | 将 Tab 转换为空格
-h | 将空格转换为 Tab（默认选项）
```

#### 操作举例：

```
# 查看 /etc/protocols 中的不可见字符，可以看到很多 ^I ，这其实就是 Tab 转义成可见字符的符号
$ cat -A /etc/protocols
# 使用 col -x 将 /etc/protocols 中的 Tab 转换为空格,然后再使用 cat 查看，你发现 ^I 不见了
$ cat /etc/protocols | col -x | cat -A 
```

![](img/md04173379-2.jpg)

### 3.join 命令

学过数据库的用户对这个应该不会陌生，这个命令就是用于将两个文件中包含相同内容的那一行合并在一起。

#### 使用方式：

```
join [option]... file1 file2 
```

#### 常用的选项有：

```
选项 | 说明
----|----
-t | 指定分隔符，默认为空格
-i | 忽略大小写的差异
-1 | 指明第一个文件要用哪个字段来对比，，默认对比第一个字段
-2 | 指明第二个文件要用哪个字段来对比，，默认对比第一个字段
```

#### 操作举例：

```
# 创建两个文件
$ echo '1 hello' > file1
$ echo '1 shiyanlou' > file2
$ join file1 file2
# 将/etc/passwd 与/etc/shadow 两个文件合并，指定以':'作为分隔符
$ sudo join -t':' /etc/passwd /etc/shadow
# 将/etc/passwd 与/etc/group 两个文件合并，指定以':'作为分隔符, 分别比对第 4 和第 3 个字段
$ sudo join -t':' -1 4 /etc/passwd -2 3 /etc/group 
```

![](img/md04173379-3.jpg)

### 4.paste 命令

`paste`这个命令与`join` 命令类似，它是在不对比数据的情况下，简单地将多个文件合并一起，以`Tab`隔开。

#### 使用方式：

```
paste [option] file... 
```

#### 常用的选项有：

```
选项 | 说明
----|-----
-d | 指定合并的分隔符，默认为 Tab
-s | 不合并到一行，每个文件为一行
```

#### 操作举例：

```
$ echo hello > file1
$ echo shiyanlou > file2
$ echo www.shiyanlou.com > file3
$ paste -d ':' file1 file2 file3
$ paste -s file1 file2 file3 
```

![](img/md04173379-4.jpg)

## 三、小结

上面这些命令不是所有你都会经常用到，不过它们确是很实用的，熟练掌握之后，可以减轻很多工作量，比如不停的用鼠标操作在 gedit 里面复制粘贴赋值粘贴，将两个文件的内容合并为一个文件，这原本只需要一个命令就能完成。

## 作业

1、在《文件打包与解压缩》一节实验中提到 Windows/dos 与 Linux/UNIX 文本文件一些特殊字符不一致，如断行符 Windows 为 CR+LF(`\r\n`)，Linux/UNIX 为 LF(`\n`)。使用`cat -A 文本` 可以看到文本中包含的不可见特殊字符。Linux 的`\n`表现出来就是一个`$`，而 Windows/dos 的表现为`^M$`，可以直接使用`dos2unix`和`unix2dos`工具在两种格式之间进行转换，使用`file`命令可以查看文件的具体类型。不过现在希望你**在不使用上述两个转换工具的情况下，使用前面学过的命令手动完成 dos 文本格式到 UNIX 文本格式的转换。**

2、还记得小时候在小霸王上面玩的小蜜蜂游戏么？它的正统名字应该是[Space Invaders:太空侵略者](http://en.wikipedia.org/wiki/Space_Invaders)。

使用下面这个命令可以安装，之所以叫 ninvaders 是因为这款游戏是基于 ncurses 命令行图形库做的：

```
sudo apt-get install ninvaders 
```

![](img/md0417337invaders.jpg)