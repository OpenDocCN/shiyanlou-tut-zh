# 第 1 节 密钥加解密实验（上）

## 一、实验描述

本实验的学习目的是让学生熟悉加密的概念，熟悉和了解加密算法(cipher)、加密模式(encryption mode)、 填充(padding)、以及初始向量(IV)的定义与作用。此外，学生还可以通过使用工具和编写程序加密/解密信息。

## 二、实验环境

本实验中，我们将使用 openssl 命令行工具及其库。实验环境中已自带命令行工具，需安装 openssl 开发库。

```
$ sudo apt-get update
$ sudo apt-get install libssl-dev 
```

编辑器使用 bless 十六进制编辑器,需预先安装。

```
$ sudo apt-get install bless 
```

系统用户名: shiyanlou

## 三、实验内容

### 实验 1\. 使用不同的加密算法与加密模式进行加密

你可以使用 openssl enc 命令来加密／解密一个文件。输入 man openssl 和 man enc 命令以查看手册

```
$ openssl enc ciphertype -e -in plain.txt -out cipher.bin \
-K 00112233445566778899aabbccddeeff \
-iv 0102030405060708 
```

请将上述代码中的 ciphertype 替换成指定的加密类型，比如-aes-128-cbc, -aes-128-cfb, -bf-cbc 等,在本实验中你至少需要使用三种不同的密码(cypher)与加密模式(encryption mode)可以通过输入`man enc`命令了解到各选项的含义以及 openssl 所支持的所有加密类型。

密码(cipher):指 bf, cast, des, aes, rs2 等 加密模式(encryption mode):指 cbc, ecb, cfb, ofb 等

以下列出部分主要选项释义：

```
| 参数        | 含义            | 含义(中文) |
| ---------- |---------------   |-----    | 
|-in         |<file> input file |输入文件      |
|-out        |<file> output file|输出文件    |
|-e          |encrypt           |加密       |
|-d          |decrypt           |解密       |
|-K/-iv      |key/iv in hex is the next argument |十六进制密钥与初始向量|
|-[pP] |print the iv/key (then exit if -P) |打印初始向量与密钥|
```

### 实验 2\. 加密模式——ECB 与 CBC 的区别。

![](http://www.cis.syr.edu/~wedu/seed/Labs_12.04/Crypto/Crypto_Encryption/files/pic_original.bmp)

[图片地址](http://www.cis.syr.edu/~wedu/seed/Labs_12.04/Crypto/Crypto_Encryption/files/pic_original.bmp)

pic_original.bmp 是一张简单的图片，我们将对它进行加密，这样没有密钥的人就无法看到这张图是什么了。请分别使用 ECB 和 CBC 模式。

1.  加密后的图片 bmp 文件头是损坏的，我们需要用 bless 编辑该图片文件。（注：bmp 头长度为 54 字节）

2.  修改完后，打开图片，描述你的观察结果。

修改加密后文件的文件头 ![](img/md0417787ssMTTDx.jpg)

观察结果(左:cbc 中:ecb 右:原图) ![](img/md0417787Fc2NBrY.jpg)

### 实验 3: 加密模式——损坏的密文

为了理解各种不同加密模式的特性，我们需要做以下练习：

1.  创建一个长度至少 64 字节的文本文件。
2.  使用 AES-128 对其进行加密
3.  不幸的是，密文的第 30 位损坏了。请用编辑器模拟损坏，编辑该位，将其反转。
4.  使用相同的 key 与 iv 对密文进行解密。

1.分别用 ECB，CBC，CFB，OFB 四种模式解密损坏的文件，观察有多少数据是正确恢复的，造成这种结果的原因是什么？

![](img/md0417787OGwpH6k.jpg) (注：test.txt 为原文件，test*(模式)*d.txt 为对应解密文件)

### 实验 4: 填充

对于分组密码来说，当明文大小不是分组大小的倍数的时候，就需要使用填充了。请做以下练习：

1.openssl 手册（man openssl）中提到 openssl 使用 pkcs5 标准来做填充。请在查询 pkcs5 标准给出的填充方法后设计实验验证这一点。

2.使用 ECB，CBC，CFB，OFB 四种模式加密同一个文件，说明哪种模式会使用填充，哪种模式不会，为什么有些模式不需要填充。

## 四、作业

### 按要求完成实验内容并回答每节实验给出的问题。

## 版权声明

本课程所涉及的实验来自 Syracuse SEED labs，并在此基础上为适配实验楼网站环境进行修改，修改后的实验文档仍然遵循 GNU Free Documentation License。

本课程文档 github 链接：https://github.com/shiyanlou/seedlab

附 Syracuse SEED labs 版权声明： >Copyright Statement Copyright 2006 – 2014 Wenliang Du, Syracuse University. The development of this document is funded by the National Science Foundation’s Course, Curriculum, and Laboratory Improvement (CCLI) program under Award No. 0618680 and 0231122\. Permission is granted to copy, distribute and/or modify this document under the terms of the GNU Free Documentation License, Version 1.2 or any later version published by the Free Software Foundation. A copy of the license can befound at http://www.gnu.org/licenses/fdl.html.