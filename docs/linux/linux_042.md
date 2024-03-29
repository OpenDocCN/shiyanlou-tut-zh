# 第 1 节 防火墙的认识

## 实验介绍

网络的安全一直都是大众用户，企业用户的关注热点，在更好的利用防火墙这个工具之前，我们先来认识防火墙，通过本实验将让大家了解到防火墙的重要性与其大体的结构，与发展。在学习防火墙之前最好有学习过网络基础，对网络层次，应用协议有一定的认识与了解。

### 实验涉及的知识点

*   防火墙的认识
*   防火墙的实现
*   防火墙的比较

## 一、防火墙的认识

因为 Internet 服务，给与企业提供挖掘和共享 Internet 资源的条件，使其可以从中获取巨大收益，但是同时也为窃取企业秘密数据的非法用户提供了机会。并且随着移动信息时代的不断发展，越来越多的敏感信息在网络上流传，因此，网络安全成为了关键问题，防火墙（Firewall）技术就是针对网络的特点而建立的防范措施。

防火墙指的是一个由软件和硬件设备组合而成、在内部网和外部网之间、专用网与公共网之间的界面上构造的保护屏障。

基于实现方式，防火墙的发展分为四个阶段:

*   第一代防火墙：基于路由器的防火墙，由于多数路由器中本身就包含有分组过滤功能，故网络访问控制可通过路由控制来实现，从而使具有分组过滤功能的路由器成为第一代防火墙产品。
*   第二代防火墙：用户化的防火墙，将过滤功能从路由器中独立出来，并加上审计和告警功能。针对用户需求，提供模块化的软件包，是纯软件产品。
*   第三代防火墙：建立在通用操作系统上的防火墙，近年来在市场上广泛使用的就是这一代产品。包括分组过滤和代理功能。第三代防火墙有以纯软件实现的，也有以硬件方式实现的。
*   第四代防火墙：具有安全操作系统的防火墙：具有安全操作系统的防火墙本身就是一个操作系统，因而在安全性上得到提高

在防火墙的发展中，从第三代开始防火墙就开始在操作系统中广泛使用，而不再局限于硬件的防火墙产品。所以防火墙分为两类：

*   硬件防火墙
*   软件防火墙

防火墙就是一个网络安全系统，监视和控制的传入和传出的网络流量的基础上预定的安全规则。防火墙通常建立一个可信的，安全的内部网络和另一个外部网络，如互联网，被认为是不安全或不受信任的。

## 二、防火墙的实现

我们从防火墙的实现方式来认识到防火墙的不断壮大，在向着不同的平台在不断的延伸，从而我们也可以看出防火墙的重要性也是不断的深入人心，同样防火墙在功能上也在不断增强，随着时间慢慢的演变。

宏观来看防火墙就是可以通过监测、限制、更改欲跨越防火墙的数据流，从而达到尽可能地不对外部网络透露内部的信息、结构和运行状况，以此来实现网络的安全保护。防火墙以限制、分离，以及分析这三个方面，有效地控制了内部网和 Internet 之间的任何活动，保证了内部网络的安全。

防火墙的这些功能是这么演变而来的：

![实验楼](https://dn-simplecloud.qbox.me/1135081470116504678) （图片来源于：http://www.myshared.ru/）

*   第一代防火墙主要是基于包过滤（Packet filter）技术，称为：packet filtering firewalls,在 1988 年 DEC 的工程师开发的过滤系统被称为数据包过滤防火墙，他的主要工作在于单独的分析每个数据包，并且只是报头。在早期路由器中使用的便是 ACL（Access Control List），这样简单的访问控制

![实验楼](https://dn-simplecloud.qbox.me/1135081470116569622) （图片来源于：http://www.myshared.ru/）

*   第二代防火墙主要是基于状态检测（stateful filter）技术,称为：stateful inspection firewalls，开始在 1989-1990 年在贝尔实验室的三位同事开发出来，又称第二代防火墙为电路级网关（circuit-level gateways），虽然第二代的防火墙在第一代中执行，但是第二代防火墙相对于第一代更深入一些，主要是服务于 OSI 参考模型的第四层传输层，通过保留足够的数据包来监控 TCP/IP 层次的连接状态并出判断，到 1992 年，USC 信息科学院的 BobBraden 开发出了基于动态包过滤（Dynamic packet filter）技术，从而被人们所熟知。

![实验楼](https://dn-simplecloud.qbox.me/1135081470116646057) （图片来源于：http://slideplayer.com/）

*   第三代防火墙主要是基于应用层（application layer）实现的,称为：proxy firewalls，90 年代初 Marcus Ranum, Wei Xu, and Peter Churchyard 开发的防火墙工具集（firewall toolkit,简称为 FWTK），在 94 年 Wei Xu 对内核中的 ip 过滤以及 socket transparent 的增强来扩展 FWTK ，同年在 Trusted Information Systems 上发布了基于这种技术的商业化产品 Gauntlet firewall，这时候应用防火墙开始被人们熟知。【注释 2】

![实验楼](https://dn-simplecloud.qbox.me/1135081470116697856)

（图片来源于：http://slideplayer.com/）

## 三、防火墙的比较

第二代防火墙与第一代防火墙的不同在于，第一代的防火墙只是静态的数据包过滤，在静态的数据包过滤中只是检查数据包的报头，这意味着攻击者可以通过一些指示响应（indicating reply）来越过防火墙获取一些蕴含在报头中的信息。而第二代的状态检测防火墙可以分析到 OSI 的第四层 TCP/IP 的连接状态，甚至深入到应用层。通过一些 session 信息如 IP 地址、端口等来做一些静态数据包过滤所做不了的更严格、更安全的动态数据包过滤

第三代防火墙与第二代防火墙的不同在于第三代防火墙拦截所有的连接，并执行一个深入的应用层分析。每当外部客户端请求与内部服务器（或反之亦然）的连接时，客户端打开与防火墙的连接。如果连接符合防火墙规则库中的标准，代理防火墙将打开一个连接到所请求的服务器。这将防火墙放在逻辑连接的中间，并允许它在应用程序级别上监视恶意活动的任何迹象的流量。【注释 3】

通过防火墙在功能上的发展，在实现的发展上面，我们可以了解到防火墙从仅仅只是在路由器等上的一些网络设备，发展到单独的硬件设备，再延伸到服务器主机上，也可以看出人们对网络安全的越发重视，防火墙也越来越普及，存在于各个地方。而 Linux 对网络有相当稳定可靠的支持，防火墙定然也是少不了的，在 Linux 环境中主要的防火墙形式有

*   Netfilter 的数据包过滤机制
*   TCP Wrappers 的程序管理
*   Proxy 的代理服务器

其中 Netfilter 与 Proxy 是十分常用的而且是最主要的防火墙方式。

对网络较为熟悉的同学可以了解到，其实防火墙主要是在分析 OSI 参考模型中的数据链路层，网络层，传输层，以及应用层。因为数据包的信息主要都是在 OSI 参考模型中的这几个层次。

TCP Wrappers 主要是分析 TCP 的报头信息，并与/etc/hosts.allow 和/etc/hosts.deny 中的规则进行匹配，从而决定哪些主机可以访问系统服务或资源。他的功能并没有 Netfilter 那么强大，但是他的配置相对于 Netfilter 的 iptables 要简单许多。

在 Linux 中为我们提供了 Netfilter 这个机制主要就是分析进入主机的数据包，也就是將包的报头信息提取出来进行分析，以決定该链接是应该放行或阻挡下来。

由于这种方式可以直接分析数据报头的信息，而报头的信息主要就是 OSI 参考模型中的数据链路层，网络层，以及传输层的信息，如 MAC 地址, IP 地址 , TCP, UDP, ICMP 等的信息都可以进行分析过滤，并且 Netfilter 提供了 iptables 来作为防火墙封包过滤的指令。十分强大

![实验楼](https://dn-simplecloud.qbox.me/1135081470116806254) （图片来源于维基百科：https://upload.wikimedia.org/wikipedia/commons/3/37/Netfilter-packet-flow.svg）

而代理服务器的防火墙形式的实现，做个不恰当的比喻就像古时候的大小姐不能出门，有个丫鬟当她的传话员，而丫鬟一般听命于老爷，所以有一定的判断能力，而大小姐先把想要通知的事情以及人告诉传话员的丫鬟，然后丫鬟了解之后判断可以传话没有问题，她便去传话，若是这内容会初犯老爷，则不去传话。当然外面的人想给大小姐传话，同样也得经过丫鬟的判断，行则传，不行则不传。

而这里的丫鬟就是我们的代理服务器，这里的老爷意志就是防火墙的规则，大小姐就是我们的本地主机，而外面的人就像是外部的网络，传话、传物就像发送数据包一般。

![实验楼](https://dn-simplecloud.qbox.me/1135081470116909093) (此图来源于：http://www.networkworld.com/article/2255950/lan-wan/chapter-1--types-of-firewalls.html)

其实代理服务器这样的防火墙相对于 Netfilter 是更安全一些，若是外面的人给小姐的物品时有毒的（外面发送有害的数据，或者想获取 server 信息），那也是丫鬟先中毒，小姐很安全呀（代理服务器中招，内网还是安全的）。

但是这是因为所有的链接，所有的数据包都中转过一层，多了一个环境，就需要多消耗一些时间，效率便降低了，高带宽也会到达瓶颈，并且因为通过代理服务器的转发那么便需要修改表头中的信息，地址转换，这样的话对 VPN 的使用也会增加一定的难度。所以相对来说 Netfilter 比 代理服务器式的防火墙应用的更加的广泛

而为什么上文说相较而言，proxy 形式比 Netfilter 要安全些，是因为数据包式的防火墙主要是在网络的七层模型中处理这样的事情：

*   拒绝让外部网络的数据包进入主机的部分敏感的端口：比如我的 FTP 只是供给公司内部人员使用的，外面的人不能从中获取消息，那么添加规则让所有想访问该端口的请求数据包全部丢弃。
*   拒绝某些 IP 来源的数据包进入主机：就如我知道有一个 IP 地址属于某个黑客的常用 IP，这个 IP 的请求一定是来攻击我或者对我不利的，便可添加规则将只要是由这个 IP 地址发来的请求数据包全部丢弃
*   拒绝带有某些特殊标签（flag）的数据包进入：最常见的便是带有 SYN 的主动连接标签了，便可添加规则只有一发现这个标签，就把这个带有该标签的数据包丢掉
*   拒绝某些 MAC 地址的链接与数据包：如果内网中有人中病毒，或者他是有意捣乱网络的，封 IP 地址他可以更换，那么我们就直接封掉其 MAC 地址，这样即使更换 IP 地址也没有用。

这样的处理虽然可以阻挡大部分不利的数据，或者说攻击，但是还有许多的问题是无法防范的：

*   防火墙不能有效的阻挡病毒或者木马程序：比如内网中的 DMZ 区域有一台 web 服务器，有 web 服务供给外网访问势必我们就需要将 80 端口开放出去，让外部网络能够请求，否则外部网络得不到数据，该服务器的功能根本无法起作用，而 80 端口的开放是的外部网络的数据包可以进入主机中，若是 web 服务有漏洞，数据包中有病毒利用该漏洞做一些事情。
*   防火墙主要是针对外网的攻击添加规则，所以对于内网的攻击并无太强的招架之力。

防火墙并不是万能的，加了防火墙之后就可以衣食无忧了，但是不加防火墙的话，我们的 server 处境将岌岌可危，我所以防火墙还是很重要的，但是我们不能全全依赖与它。

## 实验总结

通过本实验我们能够对防火墙有一个大概的了解。了解到防火墙有什么样作用，防火墙是如何发展起来的，以及防火墙有哪些类别，并且服务于网络的哪一层，他们的优劣势比较，以后能够更好根据所在处的环境去选择最适合的方式。

## 参考资料

【注释 1】：此段参考于[鸟哥私房菜](http://linux.vbird.org/linux_server/0250simple_firewall.php#netfilter_syntax_policy)

【注释 2】：此段参考于[维基百科](https://en.wikipedia.org/wiki/Firewall_(computing))

【注释 3】：此段参考于[Mike Chapple](http://searchsecurity.techtarget.com/answer/How-do-circuit-level-gateways-and-application-level-gateways-differ)