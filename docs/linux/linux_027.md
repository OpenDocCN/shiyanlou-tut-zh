# 第 1 节 Linux 安装及配置邮件服务器

## 一、实验简介

每天我们都会收到很多的邮件，但是从来没有了解过邮件是怎么实现收发的，也没有自己配置过一台邮件服务器。如果是因为条件所限，而没有实现过邮件服务器，那么在我们的环境就可以实现了。从实践中学习，你值得拥有！

### 1.1 知识点

*   了解常见的邮件服务器软件
*   了解邮件收发的原理
*   实现邮件的发出

## 1.2 效果截图

![此处输入图片的描述](img/wm)

用我们的环境给自己发了一封邮件

## 二、常见的邮件服务器软件介绍

*   [Sendmail](http://www.sendmail.com/sm/open_source/)：sendmail　是最古老的　MAT（Mail Transfer Agent，电子邮件系统）之一，最早它诞生的时候，Internet　还没有被标准化，当时主机之间使用的是　UUCP　技术来交换邮件。它被设计得比较灵活，便于配置和运行于各种类型的机器。
*   [Qmail](http://www.qmail.org/)：qmail　是新生一代的　MTA　代表，它以速度快、体积小、易配置安装等特性而著称。作者　D. J. Bernstein 是一个数学教授，富有传奇色彩。他于 1995 年开发 qmail，1996 年发布 0.70 版，并使用了多种当时比较先进的 技术，包括　Maildir　，与　sendmail　单个　binary　不同的模块化设计，权限分离，以及使用了大量由他编写的配套工具，如　 daemontool　，　ucsip-tcp　等。qmai　迅速成为了　Internet　上最有名的　MTA，使用者众。
*   [postfix](http://www.postfix.org/)：postfix　如今已经独树一帜，流水线、模块化的设计，兼顾了效率和功能。灵活的配置和扩展，使得配置　postfix　变得富有趣味。其主要的特点是速度 快、稳定，而且配置/功能非常强大，并和　sendmail　类似，提供了与外部程序对接的　API/protocol　。尤其是配置部分，postfix　另一个优势是至今 依然保持活跃的开发工作，而且稳步发展，适合高流量大负载的系统，扩充能力较强。本实验也是基于　postfix　服务器软件来实现。

## 三、邮件服务器原理介绍

### 3.1 邮件服务器基本的工作原理

MUA(Mail User Agent)：功能是向外发送邮件的程序，以及提供用户浏览与编写邮件的功能。 MTA(Mail Transfer Agent)：(可以理解为　smtpd　服务器)帮忙将用户的邮件传递出去或接收进来。 MDA(Mail Delivery Agent)：将接收下来的邮件存放到对应用户邮筒当中的程序。 通过分析 MTA 所收到邮件的表头或内容，来判断此邮件是属于哪个用户，然后决定将此邮件投递给哪个用户的邮筒里 MRA(Mail Retrieval Agent)：通过 MRA 服务器提供的邮政服务协议　(POP)　来接收自己的邮件

　 ![此处输入图片的描述](img/wm)

用户　（MUA）　通过　SMTP　协议将邮件发送到服务器　（MTA），服务器分解发现如果是本地的邮件传输则通过　lmtp　来传送，如果是远程邮件将调用本地的客户端使用　SMTP　协议向对方服务器传送。

当对方的服务器接收到邮件之后，发现如果不是本机所负责的域，则将邮件交给本机的邮件客户端，根据邮件目标域请求　DNS　解析，将邮件发送到目标服务器，或者丢至下一跳此过程称为邮件中继，如果发现是本机负责区域内的用户，则　SMTPD　将邮件转交给　　MDA　，由　MDA　把邮件传递到用户的邮筒。

用户通过　MUA　查看邮件的时候，会将邮件从邮筒拉到用户家目录，保存为　mbox　的文件。

邮件中继： SMTPD　要支持邮件路由功能，需要打开　Open Relay　—开放式中继。 SMTPD　是根据收件人判断邮件是否需要中继，而且只给指定的客户端中继。

### 3.2 邮件服务器与　DNS　之间的关系

![此处输入图片的描述](img/wm)

关于　DNS　的介绍和　DNS　服务器的配置可以查看[《Linux 系统搭建及配置 DNS 服务器》](https://www.shiyanlou.com/courses/601)

1.  当发件方需要发送邮件给对方邮件服务器，需要向　DNS　请求查询对方邮件服务器的 MX 记录。
2.  DNS　通过查询　MX　表信息。
3.  将查询到的　MX　信息返回给发送方。
4.  发送方根据优先级，将邮件投递给对方优先级高的邮件服务器　(mail1)。
5.  如果主邮件服务器过于繁忙，或者不在线的时候，会将邮件投递给辅的邮件交换器　(mail2)　上。
6.  待主邮件服交换器空闲下来后，mail2　会将代替接收下来的邮件再转给主邮件交换器　(mail1)。

注：当　DNS　上存在多个　MX　记录的主机这时就是根据 DNS 的邮件级判断了。DNS　上定义的值越小，其优先级越高。 辅助交换器接受到邮件并不能保存邮件。而是这个辅助交换器会等到主邮件交换器空闲的时候把代替它接受下来的邮件在转给主邮件交换器。所以任何邮件处理都是主邮件交换器处理的。所以我们实际情况下，如果有 2 台服务器做邮件交换器，性能比较高的应该为主的。

## 四、安装配置　Postfix

安装配置过程中，postfix　通过　Internet　发送和接收　email　，并存储在用户邮箱里。同时客户也可以通过　IMAP　或者　POP3　恢复他们的邮件。其中的客户认证是通过　Courier Authdaemon　模块的。下图表示出了这个过程

![此处输入图片的描述](img/wm)

### 4.1 安装软件

```
$　sudo apt-get update
$　sudo apt-get install postfix 
```

安装 postfix 的时候会问你安装的服务器类型，对于我们的需要，我们选择　“Internet Site”,这是最符合我们的服务器类型，然后点**ENTER**确认。 ![此处输入图片的描述](img/wm)

接下来需要给我们的服务器设置一个域名，不妨就设置为　`shiyanlou.com`，然后点　**ENTER**　确认。 　 ![此处输入图片的描述](img/wm))

### ４.2　配置 postfix

`$　sudo vim /etc/postfix/main.cf` 我们需要找到　`myhostname`　段落, `myhostname = shiyanlou.com`
然后我们把　`alias_map`　段落指向　`/etc/postfix/virtual`,之后会讲解　`virtual`　里面填写什么， `alias_maps = hash:/etc/postfix/virtual` 之后设置　`mynetworks`　段落， 它定义可以使用这台服务器的主机，我们这里就用本地地址。你也可是设置为其他地址。

![此处输入图片的描述](img/wm)

### 4.3 添加本地用户

```
$　sudo  useradd –d /usr/master -m master
$  sudo passwd master    ＃设置密码
此命令创建了一个用户 master，其中-d 和-m 选项用来为登录名 master 产生一个主目
录/usr/master（/usr 为默认的用户主目录所在的父目录）。 
```

### 4.4 编辑邮件地址

我们编写之前设置的　virtual　文件，这些邮箱地址是用来传送邮件的。 `$ sudo vim /etc/postfix/virtual` 在这个文件里，我们将设置一个表，对应相关的邮件地址。

```
master@shiyanlou.com      master
shiyanlou@shiyanlou.com   shiyanlou
root@shiyanlou.com        root 
```

完成后保存关闭文件。

```
我们可以通过调用这个命令来实现我们的映射：
$   sudo postmap /etc/postfix/virtual
接着我们重启 postfix，完成我们的修改
$   sudo service postfix restart    #如果你已经开启了 postfix　，它可能会说已经开启了服务。 
```

## 五、测试我们的安装配置

我们可以用　**shiyanlou**　或者　**root**　这个账号给　**master**　这个账号发一封邮件。首先我们需要安装一个收发邮件的软件　[`mail`](http://www.cnblogs.com/xiaoshi1991/archive/2012/09/20/2695061.html)

`$ sudo apt-get install mailutils`

### 5.1 来写一封邮件

```
$   vim hello.txt 
```

![此处输入图片的描述](img/wm)

写完后保存退出。

### 5.2 发送邮件

```
$   mail -s "test" master@shiyanlou.com < hello.txt
＃然后我们切换到 master,查看我们的邮件。'-s' 是指邮件的标题,　'<' 后面是要发送的邮件。

$   su master
$   mail ＃ 用来查看邮件的命令 
```

因为我把邮件发了３次，所以这里显示三封,点击**ENTER**可以查看邮件内容，输入　`quit`　即可退出　mail 邮箱。 ![此处输入图片的描述](img/wm)

## 六、实验总结

通过本课程，我们初步了解到了安装配置邮件服务器，如果想搭建一台良好的服务器，我们还需要小心黑客攻击，关于服务器安全，可以查看我们的[信息安全课程](https://www.shiyanlou.com/courses/?course_type=all&tag=%E4%BF%A1%E6%81%AF%E5%AE%89%E5%85%A8)。

## 参考资料

*   [Linux 邮件服务器原理介绍，编译安装 Postfix 实现本地的邮件服务。](http://tchuairen.blog.51cto.com/3848118/1684872)
*   [`help.ubuntu.com/lts/serverguide/postfix.html`](https://help.ubuntu.com/lts/serverguide/postfix.html)
*   [how-to-run-your-own-mail-server-with-mail-in-a-box-on-ubuntu-14-04](https://www.digitalocean.com/community/tutorials/how-to-install-and-setup-postfix-on-ubuntu-14-04#)
*   [`www.ahlinux.com/server/mail/8878.html`](http://www.ahlinux.com/server/mail/8878.html)