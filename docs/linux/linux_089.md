# 第 4 节 Nginx 模块开发实验

```
 ----echo "hello world" 
```

## 1.nginx 模块工作原理回顾

我们在上一章的 nginx 模块与进程中讲过：当 Nginx 接到一个 HTTP 请求时，它仅仅是通过查找配置文件将此次请求映射到一个 locationblock，而此 location 中所配置的各个指令则会启动不同的模块去完成工作，因此模块可以看做 Nginx 真正的劳动工作者。通常一个 location 中的指令会涉及一个 handler 模块和多个 filter 模块（当然，多个 location 可以复用同一个模块）。handler 模块（**这一章实验的重点**）负责处理请求，完成响应内容的生成，而 filter 模块对响应内容进行处理。因此 Nginx 模块开发分为 handler 开发和 filter 开发。

在这次实验中，我们需要参照 nginx 的工作原理，开发一个叫 echo 的 handler 模块，这个模块功能非常简单，它接收“echo”指令，指令可指定一个字符串参数，模块会输出这个字符串作为 HTTP 响应，直观来看，要实现这个功能需要三步：

1、读入配置文件中 echo 指令及其参数

2、进行 HTTP 包装（添加 HTTP 头等工作）

3、将结果返回给客户端

## 2.模块配置结构

首先这里要说一下，这里的模块配置的命令根据 Nginx 模块开发规则（也为了方便阅读），这个结构的命名规则为 ngx_http_[module-name]_[main|srv|loc]_conf_t，中间是模块名称，后面是表示模块运行在哪一层。

```
typedef struct {
    ngx_str_t  ed;
} ngx_http_echo_loc_conf_t; 
```

第一个结构体用于存储从配置文件中读进来的相关指令参数，即模块配置信息结构。其中字符串 ed 用于存储 echo 指令指定的需要输出的字符串。

```
typedef struct {
    size_t      len;
    u_char     *data;
} ngx_str_t; 
```

上面这两个字段分别表示字符串的长度和数据起始地址。注意在 Nginx 源代码中对数据类型进行了别称定义，如 ngx_int_t 为 intptr_t 的别称，为了保持一致，在开发 Nginx 模块时也应该使用这些 Nginx 源码定义的类型而不要使用 C 原生类型。除了 ngx_str_t 外，其它三个常用的 nginx type 分别为：

```
typedef intptr_t        ngx_int_t;
typedef uintptr_t       ngx_uint_t;
typedef intptr_t        ngx_flag_t; 
```

这些就是最基础的配置文件结构，总的来说：两个结构体，一个类型定义。

## 3.模块配置指令

我们要清楚 echo 模块需要接收指令“echo”。Nginx 模块使用一个 ngx_command_t 数组表示模块所能接收的所有模块，其中每一个元素表示一个条指令（这是在 nginx 中常用的模式）。

```
struct ngx_command_s {
    ngx_str_t             name;
    ngx_uint_t            type;
    char            *(*set)(ngx_conf_t *cf, ngx_command_t *cmd, void *conf);
    ngx_uint_t            conf;
    ngx_uint_t            offset;
    void                 *post;
}; 
```

name: 配置指令的名称。

type: 该配置的类型，其实更准确一点说，是该配置指令属性的集合。nginx 提供了很多预定义的属性值（一些宏定义），通过逻辑或运算符可组合在一起，形成对这个配置指令的详细的说明。

set: 是一个函数指针，用于指定一个参数转化函数，这个函数一般是将配置文件中相关指令的参数转化成需要的格式并存入配置结构体。Nginx 预定义了一些转换函数，可以方便我们调用，这些函数定义在 core/ngx_confile.h 中，一般以“_slot”结尾，例如 ngx_conf_set_flag_slot 将“on 或 off”转换为“1 或 0”，再如 ngx_conf_set_str_slot 将裸字符串转化为 ngx_str_t。

conf: 用于指定 Nginx 相应配置文件内存其实地址，一般可以通过内置常量指定，如 NGX_HTTP_LOC_CONF_OFFSET，offset 指定此条指令的参数的偏移量。

下面是 echo 模块的指令定义：

```
static ngx_command_t  ngx_http_echo_commands[] = {
    { ngx_string("echo"),
        NGX_HTTP_LOC_CONF|NGX_CONF_TAKE1,
        ngx_http_echo,
        NGX_HTTP_LOC_CONF_OFFSET,
        offsetof(ngx_http_echo_loc_conf_t, ed),
        NULL },
        ngx_null_command
}; 
```

*   NGX_HTTP_LOC_CONF: 可以出现在 http server 块里面的 location 配置指令里。

*   NGX_CONF_TAKE1：配置指令接受 1 个参数。

*   offset: 指定该配置项值的精确存放位置，一般指定为某一个结构体变量的字段偏移。因为对于配置信息的存储，一般我们都是定义一个结构体来存储的。比如我们定义了一个结构体 A，该项配置的值需要存储到该结构体的 b 字段，那么在这里就可以填写为 offsetof(A, b)。对于有些配置项，它的值不需要保存或者是需要保存到更为复杂的结构中时，这里可以设置为 0。

*   ngx_http_hello_commands: 这个数组每 5 个元素为一组，用来描述一个配置项的所有情况。那么如果有多个配置项，只要按照需要再增加 5 个对应的元素对新的配置项进行说明。

*   需要注意的是，ngx_http_[module-name]_commands 这个数组定义的最后，都要加一个 ngx_null_command 作为结尾。

参数转化函数的代码为：

```
static char *
ngx_http_echo(ngx_conf_t *cf, ngx_command_t *cmd, void *conf)
{
    ngx_http_core_loc_conf_t  *clcf;
    clcf = ngx_http_conf_get_module_loc_conf(cf, ngx_http_core_module);
    clcf->handler = ngx_http_echo_handler;
    ngx_conf_set_str_slot(cf,cmd,conf);
    return NGX_CONF_OK;
} 
```

这个函数除了调用 ngx_conf_set_str_slot 转化 echo 指令的参数外，还修改了核心模块配置（也就是这个 location 的配置），将其 handler 替换为我们编写的 handler：ngx_http_echo_handler。如果不替换的话，他会自动调用默认的 handler 模块，现在就可以使用 ngx_http_echo_handler 产生 HTTP 响应。

下一个模块 Context

这里首先需要定义一个 ngx_http_module_t 类型的结构体变量，这个结构主要用于定义各个 Hook 函数用于。下面是 echo 模块的 context 结构：

```
static ngx_http_module_t  ngx_http_echo_module_ctx = {
    NULL,                 /* preconfiguration */
    NULL,                  /* postconfiguration */
    NULL,               /* create main configuration */
    NULL,                /* init main configuration */
    NULL,             /* create server configuration */
    NULL,              /* merge server configuration */
ngx_http_echo_create_loc_conf,/* create location configration */
ngx_http_echo_merge_loc_conf/* merge location configration */
}; 
```

可以看到一共有 8 个 Hook 注入点，分别会在不同时刻被 Nginx 调用，由于我们的模块仅仅用于 location 域，这里将不需要的注入点设为 NULL 即可。其中 create_loc_conf 用于初始化一个配置结构体，如为配置结构体分配内存等工作；merge_loc_conf 用于将其父 block 的配置信息合并到此结构体中，也就是实现配置的继承。这两个函数会被 Nginx 自动调用。

这里是 echo 模块的两个函数:

```
static void *
ngx_http_echo_create_loc_conf(ngx_conf_t *cf)
{
    ngx_http_echo_loc_conf_t  *conf;
    conf = ngx_pcalloc(cf->pool, sizeof(ngx_http_echo_loc_conf_t));
    if (conf == NULL) {
        return NGX_CONF_ERROR;
    }
    conf->ed.len = 0;
    conf->ed.data = NULL;
    return conf;
}
static char *
ngx_http_echo_merge_loc_conf(ngx_conf_t *cf, void *parent, void *child)
{
    ngx_http_echo_loc_conf_t *prev = parent;
    ngx_http_echo_loc_conf_t *conf = child;
    ngx_conf_merge_str_value(conf->ed, prev->ed, "");
    return NGX_CONF_OK;
} 
```

其中 ngx_pcalloc 用于在 Nginx 内存池中分配一块空间，是 pcalloc 的一个包装。使用 ngx_pcalloc 分配的内存空间不必手工 free，Nginx 会自行管理，在适当时释放。

create_loc_conf 新建一个 ngx_http_echo_loc_conf_t，分配内存，并初始化其中的数据，然后返回这个结构的指针；

而 merge_loc_conf 将父 block 域的配置信息合并到 create_loc_conf 新建的配置结构体中。

## 4.handler 模块

handler 模块处理的结果通常有三种情况:处理成功，处理失败（处理的时候发生了错误）或者是拒绝处理。在拒绝处理的情况下，这个 location 的处理就会由默认的 handler 模块来进行处理（例如，当请求一个静态文件的时候，如果关联到这个 location 上的一个 handler 模块拒绝处理，就会由默认的 ngx_http_static_module 模块进行处理，该模块是一个典型的 handler 模块。）。

这个模块是核心，前面的都是铺垫，让我们稍微整理一下思路，回顾一下实现一个 handler 的步骤: 读入模块配置，处理功能业务，产生 HTTP header，产生 HTTP body。

```
static ngx_int_t
ngx_http_echo_handler(ngx_http_request_t *r)
{
    ngx_int_t rc;
    ngx_buf_t *b;
    ngx_chain_t out;
    ngx_http_echo_loc_conf_t *elcf;
    elcf = ngx_http_get_module_loc_conf(r, ngx_http_echo_module);
    if(!(r->method &amp; (NGX_HTTP_HEAD|NGX_HTTP_GET|NGX_HTTP_POST)))
    {
        return NGX_HTTP_NOT_ALLOWED;
    }
    r->headers_out.content_type.len = sizeof("text/html") - 1;
    r->headers_out.content_type.data = (u_char *) "text/html";
    r->headers_out.status = NGX_HTTP_OK;
    r->headers_out.content_length_n = elcf->ed.len;
    if(r->method == NGX_HTTP_HEAD)
    {
        rc = ngx_http_send_header(r);
        if(rc != NGX_OK)
        {
            return rc;
        }
    }
    b = ngx_pcalloc(r->pool, sizeof(ngx_buf_t));
    if(b == NULL)
    {
        ngx_log_error(NGX_LOG_ERR, r->connection->log, 0, "Failed to allocate response buffer.");
        return NGX_HTTP_INTERNAL_SERVER_ERROR;
    }
    out.buf = b;
    out.next = NULL;
    b->pos = elcf->ed.data;
    b->last = elcf->ed.data + (elcf->ed.len);
    b->memory = 1;
    b->last_buf = 1;
    rc = ngx_http_send_header(r);
    if(rc != NGX_OK)
    {
        return rc;
    }
    return ngx_http_output_filter(r, &amp;out);
} 
```

*   获取模块配置信息：这一块只要简单使用 ngx_http_get_module_loc_conf 就可以了。

*   功能逻辑：因为 echo 模块非常简单，只是简单输出一个字符串，所以这里没有功能逻辑代码。

*   设置 response header：Header 内容可以通过填充 headers_out 实现，我们这里只设置了 Content-type 和 Content-length 等基本内容，ngx_http_headers_out_t 定义了所有可以设置的 HTTP Response Header 信息。

*   输出 Response body：首先了解下 Nginx 的 I/O 机制，Nginx 允许 handler 一次产生一组输出，可以产生多次，Nginx 将输出组织成一个单链表结构，链表中的每个节点是一个 chain_t，定义在 core/ngx_buf.h 。

    **到了这里 我们要做的 就只是将这些小模块集合到一起。**

```
ngx_module_t  ngx_http_echo_module = {
    NGX_MODULE_V1,
    &amp;ngx_http_echo_module_ctx,     /* module context */
    ngx_http_echo_commands,     /* module directives */
    NGX_HTTP_MODULE,                  /* module type */
    NULL,                             /* init master */
    NULL,                             /* init module */
    NULL,                            /* init process */
    NULL,                             /* init thread */
    NULL,                            /* exit thread */
    NULL,                            /* exit process */
    NULL,                            /* exit master */
    NGX_MODULE_V1_PADDING 
}; 
```

模块可以提供一些回调函数给 nginx，当 nginx 在创建进程线程或者结束进程线程时进行调用。但大多数模块在这些时刻并不需要做什么，所以都简单赋值为 NULL。这里主要需要填入的信息从上到下以依次为：context、指令数组、模块类型以及若干特定事件的回调处理函数（不需要可以置为 NULL）。

注意我们的 echo 是一个 HTTP 模块，所以这里类型是 NGX_HTTP_MODULE，其它可用类型还有 NGX_EVENT_MODULE（事件处理模块）和 NGX_MAIL_MODULE（邮件模块）。

下面是 ngx_module_t 的定义，可与 echo 模块进行对应：

```
typedef struct ngx_module_s      ngx_module_t;
struct ngx_module_s {
    ngx_uint_t            ctx_index;
    ngx_uint_t            index;
    ngx_uint_t            spare0;
    ngx_uint_t            spare1;
    ngx_uint_t            abi_compatibility;
    ngx_uint_t            major_version;
    ngx_uint_t            minor_version;
    void                 *ctx;
    ngx_command_t        *commands;
    ngx_uint_t            type;
    ngx_int_t           (*init_master)(ngx_log_t *log);
    ngx_int_t       (*init_module)(ngx_cycle_t *cycle);
    ngx_int_t      (*init_process)(ngx_cycle_t *cycle);
    ngx_int_t       (*init_thread)(ngx_cycle_t *cycle);
    void            (*exit_thread)(ngx_cycle_t *cycle);
    void           (*exit_process)(ngx_cycle_t *cycle);
    void            (*exit_master)(ngx_cycle_t *cycle);
    uintptr_t             spare_hook0;
    uintptr_t             spare_hook1;
    uintptr_t             spare_hook2;
    uintptr_t             spare_hook3;
    uintptr_t             spare_hook4;
    uintptr_t             spare_hook5;
    uintptr_t             spare_hook6;
    uintptr_t             spare_hook7;
}; 
```

## 5.完整实验代码下载

使用

sudo git clone http://git.shiyanlou.com/shiyanlou/ngx*http*echo_module

就可以将代码文件夹克隆到当前文件夹

## 6.模块编译安装

完成总体模块的编写以后，要想模块能工作，就是安装编写好的模块。因为 Nginx 不支持动态链接模块，所以安装模块需要将模块代码与 Nginx 源代码进行重新编译。

```
sudo wget http://labfile.oss.aliyuncs.com/nginx-1.7.9.tar.gz 
```

下载以后再解压，然后先放在那里吧（由于这个源在国外，下载速度很慢是正常现象，趁这个时候我们可以另开一个终端进行 config 文件的编写，总不能干等着吧）

### （1）config 文件的编写

我们需要把刚才编写好的(或者说，刚才从 github 下载的)这个 ngx*http*echo_module .c 文件放到一个文件夹里(小编在桌面新建了文件夹名为 ng)，同时在这个文件夹里新建一个 config 文件。这个 config 文件的内容就是告诉 nginx 的编译脚本，该如何进行编译。我们来看一下 hello handler module 的 config 文件的内容，然后再做解释。

```
ngx_addon_name=所添加模块名称
HTTP_MODULES="$HTTP_MODULES 所添加模块名称"
NGX_ADDON_SRCS="$NGX_ADDON_SRCS $ngx_addon_dir/c 文件文件名" 
```

这是小编的：

![img](img/configneirong.jpg)

### （2）编译源码

当你写完 config 文件，nginx 的源码应该也下载的差不多了吧，下面我们就开始编译吧。编译命令之中 后面两个参数是 nginx 安装路径和即将添加的模块路径。如果你不规定 nginx 安装路径，那你只需要加后面一个参数如图所示，这是小编在实验时的具体例子（ng 文件夹在桌面上）：

![](img/ng.jpg)

![img](img/zhixingconfig.jpg)

然后使用命令：

```
make

sudo make install 
```

安装完成以后你可以开浏览器访问本地测试下，看 nginx 是否在运行（现在肯定是没起来的），这种源码安装不能像 apt 安装，用 server 启动，启动要到源码文件夹下的 objs 文件夹下执行。如图

![enter image description here](img/userid20406labid430time1422605701253.jpg)

然后你再访问下 localhost，看看 ngin 是否在运行了。

### （3）修改 http 模块配置文件

要想达到访问本地地址回显你想要的字符串，你就得在 nginx 对应的 http 模块 server 中加入一个 location 来执行（注意格式），如下图

![img](img/httpservicehelloworld.jpg)

补充：如果你不知道要改的配置文件在哪里，你可以回头看看，刚在编译之后的信息：

![img](img/configsuozaiwenjianjia.jpg)

你还记得吗，在修改完配置文件以后，我们还要做什么，第二章中，我们修改好配置我文件，都是采用 reload，但是现在，我们只需要重启 nginx 就行，他会自动重新加载配置文件。先杀了他，在启一下就好。

```
sudo killall nginx 
```

切入 nginx 源码包，重新开启 nginx：

```
sudo ./nginx 
```

### （4）结果展示

现在我们可以访问 localhost/echo

![img](img/zuihoujieguo.jpg)

## 小结

虽然后面安装模块比价复杂，但这绝不是本章的重点，本章目的主要通过一个较简单的实验，初步展示了一个请求到完成响应的过程，加深了对 nginx 模块的理解，尽量与上一章联系起来学习。

## 练习

动手完成这个 echo 实验

## 参考文献：

http://tengine.taobao.org/book/chapter_03.html