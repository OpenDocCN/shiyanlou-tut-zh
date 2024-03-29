# 第 3 节 Nginx 进程与模块

这一章只是对 nginx 进程与模块的理论性简介（会比较枯燥），如果已经有一定了解，可直接进行下一章的简单配置实战（在实战中结合这一章的理论，学习起来会更有效果）

## **1.Nging 架构介绍**

Nginx 的代码是由一个**核心**和一系列的**模块**组成。

**核心**主要用于提供 WebServer 的**基本功能**，以及 Web 和 Mail 反向代理的功能；还用于启用网络协议，创建必要的运行时环境以及确保不同的模块之间平滑地进行交互。不过，大多跟协议相关的功能和应用特有的功能都是由 nginx 的**模块**实现的。

这些功能模块大致可以分为**事件模块、阶段性处理器、输出过滤器、变量处理器、协议、upstream 和负载均衡**几个类别，这些共同组成了 nginx 的 http 功能。事件模块主要用于提供 OS 独立的(不同操作系统的事件机制有所不同)事件通知机制如 kqueue 或 epoll 等。协议模块则负责实现 nginx 通过 http、tls/ssl、smtp、pop3 以及 imap 与对应的客户端建立会话。在 Nginx 内部，进程间的通信是通过模块的 pipeline 或 chain 实现的；换句话说，**每一个功能或操作都由一个模块来实现。**例如：压缩、通过 FastCGI 或 uwsgi 协议与 upstream 服务器通信、以及与 memcached 建立会话等。

### **2.Nginx 进程介绍**

首先我们要知道，nginx 是以多进程的方式来工作的，当然 nginx 也是支持多线程的方式的。nginx 启动后，在 unix 系统中会以 daemon 的方式在后台运行，后台进程包含一个 master 进程和多个 worker 进程（你可以理解为工人和管理员）。这里就主要讲解 nginx 的多进程模式。

#### **（1）在这之前我们看看 nginx 处理连接过程：**

nginx 不会为每个连接派生进程或线程，而是由 worker 进程通过监听共享套接字接受新请求，并且使用高效的循环来处理数千个连接。Nginx 不使用仲裁器或分发器来分发连接，这个工作由操作系统内核机制完成。监听套接字在启动时就完成初始化，worker 进程通过这些套接字接受、读取请求和输出响应。

#### **（2）接下来就是 master 与 worker 的功能与协作**:

#### **master：**

当 nginx 在启动后，会有一个 master 进程和多个 worker 进程。**master**进程主要用来管理**worker**进程，master 要做的就是：接收来自外界的信号，向各 worker 进程发送信号，监控 worker 进程的运行状态，当 worker 进程退出后(异常情况下)，会自动重新启动新的 worker 进程。

**主要完成如下工作：**

*   读取并验证配置信息；
*   创建、绑定及关闭套接字；
*   启动、终止 worker 进程及维护 worker 进程的个数；
*   无须中止服务而重新配置工作；
*   控制非中断式程序升级，启用新的二进制程序并在需要时回滚至老版本；
*   重新打开日志文件；
*   编译嵌入式 perl 脚本

#### **worker：**

对于基本的网络事件，则是放在 worker 进程中来处理了。多个 worker 进程之间是对等的，他们同等竞争来自客户端的请求，各进程互相之间是独立的。一个请求，只可能在一个 worker 进程中处理，一个 worker 进程，不可能处理其它进程的请求（一对一）。然而 nginx 没有专门地仲裁或连接分布的 worker,这项工作是由操作系统内核机制完成的。在启动时,创建一组初始的监听套接字，HTTP 请求和响应之时，worker 连续接收、读取和写入套接字。

#### **worker 进程主要完成的任务包括：**

*   接收、传入并处理来自客户端的连接；
*   提供反向代理及过滤功能；
*   nginx 任何能完成的其它任务

#### **nginx 的进程模型，可以由下图来表示：**

![enter image description here](img/md0417411architecture.jpg)

### **(3).下面来看一看一个完整的请求是怎样通过互相的协作来实现的：**

既然 worker 进程之间是平等的，每个进程，处理请求的机会也是一样的。当我们提供 80 端口的 http 服务时，一个连接请求过来，每个进程都有可能处理这个连接。那么问题来了，到底最后怎样处理，是由什么决定的呢？首先，每个 worker 进程都是从 master 进程 fork 过来，在 master 进程里面，先建立好需要 listen 的 socket（listenfd）之后，然后再 fork 出多个 worker 进程。所有 worker 进程的 listenfd 会在新连接到来时变得可读，为保证只有一个进程处理该连接，所有 worker 进程会在注册 listenfd 读事件前抢 accept_mutex，抢到互斥锁的那个进程注册 listenfd 读事件，然后在读事件里调用 accept 接受该连接。当一个 worker 进程在 accept 这个连接之后，就开始读取请求、解析请求、处理请求。产生数据后，再返回给客户端，最后才断开连接，这样一个完整的请求就是这样的了。我们可以看到：一个请求，完全由 worker 进程来处理，而且只在一个 worker 进程中处理。

也许你还有个疑问，那就是 nginx 采用多 worker 的方式来处理请求，每个 worker 里面只有一个主线程，那能够处理的并发数很有限啊，多少个 worker 就能处理多少个并发，何来高并发呢？然而，这就是 nginx 的高明之处，nginx 采用了**异步非阻塞**的方式来处理请求，也就是说，nginx 是可以同时处理成千上万个请求的。

### **（4）这里补充一下“异步非阻塞”**

异步的概念是和同步相对的，也就是不同事件之间不是同时发生的。非阻塞的概念是和阻塞对应的，阻塞是事件按顺序执行，每一事件都要等待上一事件的完成，而非阻塞是如果事件没有准备好，这个事件可以直接返回，过一段时间再进行处理询问，这期间可以做其他事情。

## **3.Nginx 模块介绍**

nginx 真正的魅力在于它的模块，整个应用程序建立在一个模块化系统之上，在编译时，可以对每一个模块进行启用或者禁用,需要什么就定制什么。对 nginx 模块的基本原理总结一下，基本就是：在特定地方调用函数。（Nginx 本身支持多种模块，如 HTTP 模块、EVENTS 模块和 MAIL 模块，这里只简单讲述 HTTP 的模块及其中的命令） 下面是配置文件结构图：

![图片描述](img/userid20406labid411time1422495978452.jpg)

Nginx 本身做的工作实际很少，当它接到一个 HTTP 请求时，它仅仅是通过查找配置文件将此次请求映射到一个 locationblock，而此 location 中所配置的各个指令则会启动不同的模块去完成工作，因此模块可以看做 Nginx 真正的劳动工作者。通常一个 location 中的指令会涉及一个 handler 模块和多个 filter 模块（当然，多个 location 可以复用同一个模块）。handler 模块负责处理请求，完成响应内容的生成，而 filter 模块对响应内容进行处理。因此 Nginx 模块开发分为 handler 开发和 filter 开发（本文不考虑 load-balancer 模块）。下图展示了一次常规请求和响应的过程。

![图片描述](img/userid20406labid411time1422497618598.jpg)

### 1.http index 模块

模块：ngx*http*index_module 配置范例：

```
location / {
    index index.$geo.html index.html;
} 
```

语法:

```
index file ...; 
```

默认值:

index index.html;

作用域: http, server, location 模块功能及注意： 定义将要被作为默认页的文件。 文件的名字可以包含变量。 文件以配置中指定的顺序被 nginx 检查。 列表中的最后一个元素可以是一个带有绝对路径的文件。 例子： index index.$geo.html index.0.html /index.html; 需要注意的是，index 文件会引发内部重定向，请求可能会被其它 location 处理。 比如，下面这个例子：

```
location = / {
    index index.html;
}

location / {
    ...
} 
```

请求“/”实际上将会在第二个 location 中作为“/index.html”被处理。

### 2.http log 模块

模块：ngx*http*log_module 配置范例：

```
log_format  gzip '$remote_addr-$remote_user[$time_local]'
:'$request$status $bytes_sent'
:'" $ http _ referer" "$http_user_agent" "$gzip_ratio"';
access_log  /spool/logs/nginx-access.log  gzip  buffer=32k; 
```

指令: access_log 语法:

```
access_log path [format [buffer=size | off ] 
```

默认值: access*log log/access.log combined 作用域： http, server, location 模块功能及注意： 指令 access*log 指派路径、格式和缓存大小。参数 "off" 将清除当前级别的所有 access_log 指令。如果未指定格式，则使用预置的 "combined" 格式。缓存不能大于能写入磁盘的文件的最大值。在 FreeBSD 3.0-6.0 ，缓存大小无此限制。

指令: log_format 语法：

```
log_format name format [format ...] 
```

默认值： log_format combined "..." 作用域： http server

### 3.Access 模块

模块：ngx*http*access*module 功能描述：此模块提供了一个简易的基于主机的访问控制. ngx*http*access*module 模块使有可能对特定 IP 客户端进行控制. 规则检查按照第一次匹配的顺序，此模块对网络地址有放行和禁止的权利。

配置范例：

```
location / {
: deny    192.168.1.1;
: allow   192.168.1.0/24;
: allow   10.1.1.0/16;
: deny    all;
} 
```

在上面的例子中,仅允许网段 10.1.1.0/16 和 192.168.1.0/24 中除 192.168.1.1 之外的 ip 访问.

1.放行语法:

```
allow [ address | CIDR | all ] 
```

作用域: http, server, location, limit_except allow 描述的网络地址有权直接访问

2.禁止语法:

```
deny [ address | CIDR | all ] 
```

作用域: http, server, location, limit_except deny 描述的网络地址拒绝访问

### 4.Rewrite 模块

模块：ngx*http*rewrite_module 功能描述：执行 URL 重定向,允许你去掉带有恶意的 URL，包含多个参数（修改）.利用正则的匹配，分组和引用，达到目的 配置范例：该模块允许使用正则表达式改变 URL，并且根据变量来转向以及选择配置

1.if 语法:

```
if (condition) { ... } 
```

作用域: server, location 例子如下：

```
if ($http_user_agent ~ MSIE) {
: rewrite  ^(.*)$  /msie/$1  break;
}
if ($http_cookie ~* "id=([^;] +)(?:;|$)" ) {
: set  $id  $1;
}
if ($request_method = POST ) {
: return 405;
}
if (!-f $request_filename) {
: break;
: proxy_pass  http://127.0.0.1;
}
if ($slow) {
: limit_rate  10k;
}
if ($invalid_referer) {
: return   403;
} 
```

2.return

语法:

```
return cod 
```

作用域: server, location, if

功能描述：这个指令根据规则的执行情况，返回一个状态值给客户端。可使用值包括：204，400，402-406，408，410，411，413，416 及 500-504。也可以发送非标准的 444 代码-未发送任何头信息下结束连接。

3.rewrite

语法:

```
rewrite regex replacement flag 
```

last :表示完成 rewrite break:本规则匹配完成后，终止匹配，不再匹配后面的规则 redirect:返回 302 临时重定向，地址栏会显示跳转后的地址 permanent:返回 301 永久重定向，地址栏会显示跳转后的地址

作用域: server, location, if

功能描述：这个指令根据正则表达式或者待替换的字符串来更改 URL。指令根据配置文件中的先后顺序执行生效。

### 5.Proxy 模块

模块：ngx*http*proxy_module 功能描述：此模块能代理请求到其它服务器.也就是说允许你把客户端的 HTTP 请求转到后端服务器(这部分的指令非常多，但不是全部都会被用到，这里是比较常见的指令简介)

指令：proxy*pass*header Server; 功能描述：该指令强制一些被忽略的头传递到客户端

指令：proxy_redirect off; 功能描述：允许改写出现在 HTTP 头却被后端服务器触发重定向的 URL,对响应本身不做任何处理

指令：proxy*set*header Host $http_host; 功能描述：允许你重新定义代理 header 值再转到后端服务器，目标服务器可以看到客户端的原始主机名

指令： proxy*set*header X-Real-IP $remote_addr; 功能描述：目标服务器可以看到客户端的真实 ip，而不是转发服务器的 ip

[proxy 更多的指令查询](http://nginx.org/en/docs/http/ngx_http_proxy_module.html)

### 6.upstream 模块

模块：ngx*http*upstream_module 功能简介：该指令使请求被上行信道之间的基于客户端的 IP 地址分布 配置范例：

```
upstream backend  {
  server backend1.example.com weight=5;
  server backend2.example.com:8080;
  server unix:/tmp/backend3;
}

server {
  location / {
    proxy_pass  http://backend;
  }
} 
```

指令: ip_hash 语法:

```
upstream name { ... } 
```

作用域： upstream

指令功能及注意： 指定服务器组的负载均衡方法，请求基于客户端的 IP 地址在服务器间进行分发。 IPv4 地址的前三个字节或者 IPv6 的整个地址，会被用来作为一个散列 key。 这种方法可以确保从同一个客户端过来的请求，会被传给同一台服务器。除了当服务器被认为不可用的时候，这些客户端的请求会被传给其他服务器，而且很有可能也是同一台服务器。 如果其中一个服务器想暂时移除，应该加上 down 参数。这样可以保留当前客户端 IP 地址散列分布。就像这样：

```
upstream backend {
  ip_hash;
  server   backend1.example.com;
  server   backend2.example.com;
  server   backend3.example.com  down;
  server   backend4.example.com;
} 
```

指令: server 指令功能及注意： 定义服务器的地址 address 和其他参数 parameters。 地址可以是域名或者 IP 地址，端口是可选的，或者是指定“unix:”前缀的 UNIX 域套接字的路径。如果没有指定端口，就使用 80 端口。 如果一个域名解析到多个 IP，本质上是定义了多个 server。

语法:

```
server address [parameters]; 
```

作用域： upstream

实例：

```
upstream  backend  {
  server   backend1.example.com    weight=5;
  server   127.0.0.1:8080          max_fails=3  fail_timeout=30s;
  server   unix:/tmp/backend3;
} 
```

指令：upstream 语法:

```
upstream name { ... } 
```

作用域: http

指令功能及注意： 这个指令描述了一个服务器的集合，该集合可被用于 proxy*pass 和 fastcgi*pass 指令中，作为一个单独的实体。这些服务器可以是监听在不同的端口，另外，并发使用同时监听 TCP 端口和 Unix 套接字的服务器是可能的。 这些服务器能被分配不同的权重。如果没有指定，则都为 1 。

示例：

```
 upstream backend {
  server backend1.example.com weight=5;
  server 127.0.0.1:8080 max_fails=3  fail_timeout=30s;
  server unix:/tmp/backend3;
} 
```

## **小结**

这一章主要讲述 nginx 的进程，及 http 主要模块.这里有[nginx 全要模块](http://wiki.nginx.org/Modules)，虽然很枯燥，但这是实践的理论依据，下一章会有较简单的模块开发实战，两边对照起来补充是最好的。