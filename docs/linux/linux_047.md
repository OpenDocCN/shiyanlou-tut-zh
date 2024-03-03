# 第 1 节 Linux 系统搭建及配置 DNS 服务器

## 一、实验简介

本实验是在 ubuntu 系统环境下搭建一个 DNS 服务器，你可以通过它来管理局域网的 ip 地址，更加方便管理和记忆。通过构建私有 DNS 服务器， 来学习 linux 的运维。

### 1.1 知识点

*   学习 linux 搭建和配置 DNS 服务器
*   了解关于 DNS 的原理
*   学习关于 [BIND](https://www.isc.org/downloads/bind/) 软件的安装和使用

### 1.2 效果截图

![此处输入图片的描述](img/wm)

## 二、DNS 的体系结构和原理

### 2.1 DNS 简述

DNS（Internet Domain Name System）中包含用来按照一种分层结构定义 Internet 上使用的主机名字的语法，还有名字的授权规则，以及为了定义名字和 IP 地址的对应，系统需要进行的所有设置。 实际上，DNS 是一个分布式数据库，它允许对整个数据库的各个部分进行本地控制； 同时整个网络也能通过客户。服务器方式访问每个部分的数据，借助备份和缓存机制，DNS 将更加强壮和足够的性能。 DNS 的结构如图，DNS 的分布式数据库是以域名为索引的，每个域名实际上就是一棵很大的逆 向树中路径，这棵逆向树称为域名空间。 ![](http://i.imgur.com/OcAHdtB.jpg)

### 2.2 DNS 基本原理

域名系统是分级的分布式的数据库。它储存对应于 IP 地址与主机名相对应的信息，邮 件路由信息和其他网络应用方面的数据。 用户通过询问解决库（解决库发送询问并对回应进行说明）在 DNS 上查询信息。 BIND9 软件分级同时包括了一个域名服务器和一个解决库。

### 2.3 系统的要求

DNS 硬件要求传统上是比较适度的。对许多配置来说，一些“退休”的服务器也能很 好的充当 DNS 服务器。但是 BIND9 的 DNSSEC 和 IPV6 功能对 CPU 要求很高，所以那些 频繁应用这些功能的机构应该考虑为这个软件配备大一些的系统。 BIND9 现在是完全多线 程的，允许完全的多程序系统的利用。

### 2.4 `BIND`软件介绍

BIND (Berkeley Internet Name Domain)是 Domain Name System (DNS) 协议的一个实现，提供了 DNS 主要功能的开放实现，包括

*   域名服务器 (named)
*   DNS 解析库函数
*   DNS 服务器运行调试所用的工具
*   Bind 是一款开放源码的 DNS 服务器软件，由美国加州大学 Berkeley 分校开发和维护的，按照 ISC 的调查报告，BIND 是世界上使用最多最广泛的域名服务系统。

### 2.5 搭建私有 DNS 的好处

当内部主机和私有 IP 地址过多时，这个方法可以更好的管理它们。通过搭建私有的 DNS 服务器，你可以把国外的一些网站 ip 地址放到自己的 DNS 服务器中实现**科学上网**。

* * *

### 三、实验步骤一

安装和配置 BIND

### 3.1 在 DNS 服务器上安装 BIND

```
sudo apt-get update

sudo apt-get install bind9 bind9utils bind9-doc 
```

### 3.2 IPv4 Mode

在继续配置 DNS 服务器之前，先修改一下 bind9 service 参数文件，设置 BIND 为 IPv4 Mode。

```
sudo vi /etc/default/bind9 
```

把 "-4" 添加到 OPTIONS 变量，如下所示 */etc/default/bind9*

```
OPTIONS="-4 -u bind" 
```

### 3.5 配置主 DNS 服务器

一般都会设置两个 DNS 服务器，一个主要的，一个备用的，这次的实验受实验环境的限制，我们只做主服务器。 首先查看本机的 ip 地址

```
ifconfig -a 
```

我的 ip 地址是`192.168.42.2`，每个人的是不一样的，具体而言。 然后获取几个有效的内网 ip ，可以试着去 ping 一下，我找的内网 ip 是 `192.168.42.1`。

## 四、实验步骤二

BIND 的 配置文件有很多组成，并以`named.conf`开头，我们开始来编写这些文件。

### 4.1 打开`named.conf.options`并编辑

```
sudo vi /etc/bind/named.conf.options 
```

在现有的文档之上，添加一个新的 ACL 块并命名 "trusted"，我们把以前找到的内网 ip 添加到信任名单里，并称为 ns1 ,只允许它们查询 DNS 服务器 ， 提高安全性。

```
acl "trusted" {
              192.168.42.2; #ns1  信任的名单
};
options {
    directory "/var/cache/bind";

    // If there is a firewall between you and nameservers you want
    // to talk to, you may need to fix the firewall to allow multiple
    // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

    // If your ISP provided one or more IP addresses for stable 
    // nameservers, you probably want to use them as forwarders.  
    // Uncomment the following block, and insert the addresses replacing 
    // the all-0's placeholder.

        recursion  yes;                 # enables resursive queries
        allow-recursion { trusted; };  # allows recursive queries from "trusted" clients
        listen-on { 192.168.42.2;  };   # ns1 private IP address - listen on private network only
        allow-transfer { none; };      # disable zone transfers by default

        forwarders {
                   114.114.114.114;        # 当本地的 dns 服务器中找不到记录时向上查询
        };
    //========================================================================
    // If BIND logs error messages about the root key being expired,
    // you will need to update your keys.  See https://www.isc.org/bind-keys
    //========================================================================
    dnssec-validation auto;

    auth-nxdomain no;    # conform to RFC1035
    listen-on-v6 { any; };
}; 
```

保存并退出 `named.conf.options`，通过上面的设置只有信任的主机才能查询 DNS 服务器，其他主机不能。

### 4.2 编辑 `Local` 文件

打开 `named.conf.local`并编辑：

```
sudo vi /etc/bind/named.conf.local 
```

我们修改配置文件，完成后如下，在配置文件中添加了[正向解析和反向解析](http://blog.csdn.net/jackxinxu2100/article/details/8145318)的文件在系统中的位置

```
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "shiyanlou.example.com" {
    type master;
    file "/etc/bind/zones/db.shiyanlou.example.com"; # zone file path
};

zone "168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/zones/db.192.168";  
}; 
```

添加完成后，保存退出。这样我们在 BIND 我们就有了指定的文件，接下来我们就编写正向解析和反向解析的域文件。

### 4.3 创建正向解析文件

正向解析：通过主机名获取其对应的广域网 IP 地址，我们以 "host1.shiyanlou.example.com" 为例来编写来这个文件

```
sudo mkdir /etc/bind/zones 
```

我们根据现有的域文件`db.local`，来复制一份作为`db.shiyanlou.example.com` 编辑这个正向解析文件

```
sudo vi /etc/bind/zones/db.shiyanlou.example.com 
```

我们在原来文件的基础上，修改了很多，具体如下

```
;
; BIND data file for local loopback interface
$TTL    604800
@       IN      SOA     ns1.shiyanlou.example.com. admin.shiyanlou.example.com. (
                  3       ; Serial
             604800     ; Refresh
              86400     ; Retry
            2419200     ; Expire
             604800 )   ; Negative Cache TTL
;
;
; name servers - NS records
     IN      NS      ns1.shiyanlou.example.com.
; name servers - A records
ns1.shiyanlou.example.com.          IN      A       192.168.42.2 

host1.shiyanlou.example.com.        IN      A      192.168.42.1 
```

**注意上面加点的地方不要漏写**

### 4.4 创建反向解析域文件

> 反向域名解析与通常的正向域名解析相反，提供 IP 地址到域名的对应。 IP 反向解析主要应用到邮件服务器中来阻拦垃圾邮件，特别是在国外。多数垃圾邮件发送者使用动态分配或者没有注册域名的 IP 地址来发送垃圾邮件，以逃避追踪，使用了域名反向解析后，就可以大大降低垃圾邮件的数量。

在之前的 `named.conf.local` 的文件里我们写了一个反向域名解析的文件名`db.192.168`，现在来编写它：

```
cd /etc/bind/zones
sudo vi /etc/bind/zones/db.192.168 
```

文件的具体内容如下:

```
$TTL    604800
@       IN      SOA     shiyanlou.example.com. admin.shiyanlou.example.com. (
                              3         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
; name servers
      IN      NS      ns1.shiyanlou.example.com.

; PTR Records
2.42   IN      PTR     ns1.shiyanlou.example.com.    ; 192.168.42.2
1.42   IN      PTR     host1.shiyanlou.example.com.  ; 192.168.42.1 
```

### 4.5 检验 BIND 配置文件有没有语法错误

运行下面的命令检验 `named.conf*` 有没有语法错误：

```
sudo named-checkconf 
```

如果没有报错则编写是正确的，如果报错了，那就根据报的错误去修改文件。 运行下面的命令查看正向解析和反向解析文件是否正确。

```
sudo named-checkzone shiyanlou.example.com /etc/bind/zones/db.shiyanlou.example.com
sudo named-checkzone 168.192.in-addr.arpa /etc/bind/zones/db.192.168 
```

做到这里，离成功已经不远了:-)，我们重启 BIND service

```
sudo service bind9 restart 
```

## 五、实验步骤三

配置 DNS 客户端，在我们的实验环境里，客户端和服务器是一体的，用自己的电脑的话，可以试试局域网的其他电脑做客户端。将客户端的 DNS 修改为服务器的 ip 地址。 因为我们用的是 Ubuntu 的系统，所以运行命令

```
sudo vi /etc/resolvconf/resolv.conf.d/head 
```

把文档修改为如下所示：

```
nameserver 192.168.42.2  # ns1 private IP address 
```

然后打开文档`resolv.conf`

```
sudo vi /etc/resolv.conf 
```

编辑为

```
options timeout:1 attempts:1 rotate
nameserver  192.168.42.2 
```

接下来运行 `resolvconf` 生成一个新的 `resolv.conf`文件

```
sudo resolvconf -u 
```

好了，你的客户端可以检验你的服务器是否可以正常使用了。

## 六、实验步骤四

测试我们的 DNS 服务器是否可以正常运行，使用[nslookup](https://www.ezloo.com/2011/04/nslookup.html),来查询你的服务器，（若使用其他的客户端， ip 地址 需要加入到 "trusted" ACL 里面）。先测试正向解析，通过输入网址找到 ip 地址 运行下面的代码

```
nslookup host1.shiyanlou.example.com 
```

![此处输入图片的描述](img/wm)

再测试反向解析，通过 ip 地址找到它的网址。**如果你的反向域名文件写错了，或者放置的位置不对，就会报错，可以在上文重新看看操作**

```
nslookup 192.168.42.1 
```

![此处输入图片的描述](img/wm)

好了，到此为止，一个简单的 DNS 服务器就搭建好了，你可以试试添加更多的 ip 地址，快来试试自己的 DNS 服务器吧！

## 参考资料

*   [How To Configure BIND as a Private Network DNS Server on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-14-04)
*   [BIND9 管理员参考手册](https://www.centos.bz/manual/BIND9-CHS.pdf)
*   [鳥哥的 Linux 私房菜](http://linux.vbird.org/linux_server/0350dns.php)
*   [BIND 相关资料](https://www.isc.org/downloads/bind/)