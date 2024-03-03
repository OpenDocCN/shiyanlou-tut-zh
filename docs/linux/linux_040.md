# 第 3 节 聊天室客户端程序

## 一、实验简介

《聊天室客户端程序》 实验利用套接字编程模型实现一个聊天室客户端软件。通过该实验，可以学习 Linux 网络编程，Linux 多进程编程等知识。

### 1.1 知识点

*   客户机服务器模型
*   套接字编程相关函数

### 1.2 效果截图

聊天室客户端效果展示：

![此处输入图片的描述](img/wm)

*   源码可以在 https://labfile.oss.aliyuncs.com/courses/602/chatroom.zip 下载

### 1.3 设计流程

整个流程大概步骤：首先与服务器取得连接，此时 TCP 层以下的连接已建立。 然后调用 `joinagroup` 函数发送加入聊天室请求信息，此时完成了应用层的连接。最后不停地循环读写消息。详细的流程如下：

![此处输入图片的描述](img/wm)

## 二、 main 函数

### 2.1 首先创建客户端套接字并与服务器取得 TCP 层面的连接

*   在主函数声明套接字描述符，然后调用 `hooktoserver` 函数:

```
int sock;
sock = hooktoserver(); 
```

### 2.2 hooktoserver 函数实现与服务器端的 TCP 层面的连接

*   其中 `hooktoserver` 的主要功能是通过 `connect(sd, (struct sockaddr *) &amp;address, sizeof(address)` 与服务器取得连接,返回连接成功的套接字，定义在 "chatlinker.c"。它主要完成客户端和服务器端的连接，程序的框架如下：

![此处输入图片的描述](img/wm)

*   声明字符串，用于保存服务器进程的主机名和端口号的信息：

```
int sd;                 
char linkname[MAXNAMELEN];
char linktrgt[MAXNAMELEN];
char *servhost;
char *servport;
int bytecnt; 
```

*   获取服务器地址：

```
sprintf(linkname, "%s/%s", getenv("HOME"), PORTLINK);
bytecnt = readlink(linkname, linktrgt, MAXNAMELEN); 
```

*   获得服务器 IP 地址和端口号：

```
 servport = index(linktrgt, ':');
    *servport = '\0';
    servport++;
    servhost = linktrgt;
    unsigned short number = (unsigned short) strtoul(servport, NULL, 0); 
```

*   调用函数 socket 创建 TCP 套接字：

```
sd = socket(AF_INET, SOCK_STREAM, 0); 
```

*   获取服务器协议地址：

```
 struct hostent *hostinfo;
    struct sockaddr_in address;

    hostinfo = gethostbyname(servhost); /* 得到服务器主机名 */
    address.sin_addr = *(struct in_addr *) *hostinfo->h_addr_list;
    address.sin_family = AF_INET;
    address.sin_port = htons(number); 
```

*   与服务器取得连接：

```
 if (connect(sd, (struct sockaddr *) &amp;address, sizeof(address)) < 0)
    {
        perror("connecting");
        exit(1);
    }
    printf("admin: connected to server on '%s' at '%s'\n",
        servhost, servport); 
```

### 2.2 发送加入聊天室的请求

在 TCP 层面连接成功以后，应该发送加入某个聊天室的请求（取得应用层面的连接）。

*   在主函数中:

```
if (!joinagroup(sock))
    continue; 
```

### 2.3 IO 复用

通过 `select(FD_SETSIZE, &amp;tempfds, NULL, NULL, NULL)` 语句完成标准输入（描述符为 0，客户键盘输入）和客户套接字 `sock` 的读状态条件。

*   注意，这里只用监督两个描述符的读状态就可以,所以只有如下两个描述符置位：

```
FD_SET(sock, &amp;clientfds); /* 设置服务器套接字在 clientfds 中的比特位 */
FD_SET(0, &amp;clientfds); /* 设置标准输入在 clientfds 中的比特位 */ 
```

## 三、 函数 joinagroup 完成应用层层面的连接

自定义函数 `joinagroup(sock)` 应该做什么呢？

*   首先向服务器发送请求聊天室列表信息的消息，标志位为`LIST_GROUPS`,即完整调用语句为： `sendpkt(sock, LIST_GROUPS, 0, NULL)`

*   在客户端打印接收到的聊天室的信息供用户选择，并让用户选择所要加入的组、聊天室：

```
 showgroups(pkt->lent, pkt->text);
    printf("which group? ");
    fgets(bufr, MAXPKTLEN, stdin); 
```

*   发送加入聊天室的信息到服务器：

```
sendpkt(sock, JOIN_GROUP, bufrlen, bufr) 
```

*   接收服务器的消息，分析加入是否成功：

```
pkt = recvpkt(sock);
if (!pkt) 
{
    fprintf(stderr, "error: server died\n");
    exit(1);
}
if (pkt->type != JOIN_ACCEPTED &amp;&amp; pkt->type != JOIN_REJECTED) 
{
    fprintf(stderr, "error: unexpected reply from server\n");
    exit(1);
} 
```

*   如果拒绝显示其原因:

```
if (pkt->type == JOIN_REJECTED)
{
    printf("admin: %s\n", pkt->text);
    free(gname);
    free(mname);
    return (0);
} 
```

*   加入成功通知用户：

```
printf("admin: joined '%s' as '%s'\n", gname, mname);
free(gname);
free(mname);
return (1); 
```

## 四、实验总结

本实验用 C 语言实现了一个简单的聊天室客户端程序，通过该程序，进一步强化客户机服务器模型。另外在套接字编程的方面，使用了 `connect` 函数与服务器连接。

*   本实验的完整代码

```
/* 聊天室客户端程序 */
#include <stdio.h>
#include <fcntl.h>
#include <string.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <netdb.h>
#include <time.h>
#include <errno.h>
#include <stdlib.h>

#include "common.h"

#define QUIT_STRING "/end"

/* 打印聊天室名单 */
void showgroups(long lent, char *text) 
{
    char *tptr;

    tptr = text;
    printf("%15s %15s %15s\n", "group", "capacity", "occupancy");
    while (tptr < text + lent) 
    {
        char *name, *capa, *occu;

        name = tptr;
        tptr = name + strlen(name) + 1;
        capa = tptr;
        tptr = capa + strlen(capa) + 1;
        occu = tptr;
        tptr = occu + strlen(occu) + 1;

        printf("%15s %15s %15s\n", name, capa, occu);
    }
}

/* 加入聊天室 */
int joinagroup(int sock) {

    Packet * pkt;
    char bufr[MAXPKTLEN];
    char * bufrptr;
    int bufrlen;
    char * gname;
    char * mname;

    /* 请求聊天室信息 */
    sendpkt(sock, LIST_GROUPS, 0, NULL);

    /* 接收聊天室信息回复 */
    pkt = recvpkt(sock);
    if (!pkt) 
    {
        fprintf(stderr, "error: server died\n");
        exit(1);
    }

    if (pkt->type != LIST_GROUPS) 
    {
        fprintf(stderr, "error: unexpected reply from server\n");
        exit(1);
    }

    /* 显示聊天室 */
    showgroups(pkt->lent, pkt->text);

    /* 从标准输入读入聊天室名 */
    printf("which group? ");
    fgets(bufr, MAXPKTLEN, stdin);
    bufr[strlen(bufr) - 1] = '\0';

    /* 此时可能用户想退出 */
    if (strcmp(bufr, "") == 0
            || strncmp(bufr, QUIT_STRING, strlen(QUIT_STRING)) == 0)
    {
        close(sock);
        exit(0);
    }
    gname = strdup(bufr);

    /* 读入成员名字 */
    printf("what nickname? ");
    fgets(bufr, MAXPKTLEN, stdin);
    bufr[strlen(bufr) - 1] = '\0';

    /* 此时可能用户想退出 */
    if (strcmp(bufr, "") == 0
            || strncmp(bufr, QUIT_STRING, strlen(QUIT_STRING)) == 0) 
    {
        close(sock);
        exit(0);
    }
    mname = strdup(bufr);

    /* 发送加入聊天室的信息 */
    bufrptr = bufr;
    strcpy(bufrptr, gname);
    bufrptr += strlen(bufrptr) + 1;
    strcpy(bufrptr, mname);
    bufrptr += strlen(bufrptr) + 1;
    bufrlen = bufrptr - bufr;
    sendpkt(sock, JOIN_GROUP, bufrlen, bufr);

    /* 读取来自服务器的回复 */
    pkt = recvpkt(sock);
    if (!pkt) 
    {
        fprintf(stderr, "error: server died\n");
        exit(1);
    }
    if (pkt->type != JOIN_ACCEPTED &amp;&amp; pkt->type != JOIN_REJECTED) 
    {
        fprintf(stderr, "error: unexpected reply from server\n");
        exit(1);
    }

    /* 如果拒绝显示其原因 */
    if (pkt->type == JOIN_REJECTED)
    {
        printf("admin: %s\n", pkt->text);
        free(gname);
        free(mname);
        return (0);
    }
    else /* 成功加入 */
    {
        printf("admin: joined '%s' as '%s'\n", gname, mname);
        free(gname);
        free(mname);
        return (1);
    }
}

/* 主函数入口 */
main(int argc, char *argv[]) 
{
    int sock;

    /* 用户输入合法性检测 */
    if (argc != 1) 
    {
        fprintf(stderr, "usage : %s\n", argv[0]);
        exit(1);
    }

    /* 与服务器连接 */
    sock = hooktoserver();
    if (sock == -1)
        exit(1);

    fflush(stdout); /* 清除标准输出缓冲区 */

    /* 初始化描述符集 */
    fd_set clientfds, tempfds;
    FD_ZERO(&amp;clientfds);
    FD_ZERO(&amp;tempfds);
    FD_SET(sock, &amp;clientfds); /* 设置服务器套接字在 clientfds 中的比特位 */
    FD_SET(0, &amp;clientfds); /* 设置标准输入在 clientfds 中的比特位 */

    /* 循环 */
    while (1) 
    {
        /* 加入聊天室 */
        if (!joinagroup(sock))
            continue;

        /* 保持聊天状态 */
        while (1) 
        {
            /* 调用 select 函数同时监测键盘和服务器信息 */
            tempfds = clientfds;

            if (select(FD_SETSIZE, &amp;tempfds, NULL, NULL, NULL) == -1) 
            {
                perror("select");
                exit(4);
            }

            /* 对于所有在 tempfds 中被置位的文件描述符，检测它是否是套接字描述符，
            如果是，意味服务器传来消息。如果它文件描述符是 0，则意味有来自用户
            键盘的输入要发送给服务器 */

            /* 处理服务器传来信息 */
            if (FD_ISSET(sock,&amp;tempfds)) 
            {
                Packet *pkt;
                pkt = recvpkt(sock);
                if (!pkt) 
                {
                    /* 服务器宕机 */
                    fprintf(stderr, "error: server died\n");
                    exit(1);
                }

                /* 显示消息文本 */
                if (pkt->type != USER_TEXT) 
                {
                    fprintf(stderr, "error: unexpected reply from server\n");
                    exit(1);
                }

                printf("%s: %s", pkt->text, pkt->text + strlen(pkt->text) + 1);
                freepkt(pkt);
            }

            /* 处理键盘输入 */
            if (FD_ISSET(0,&amp;tempfds)) 
            {
                char bufr[MAXPKTLEN];

                fgets(bufr, MAXPKTLEN, stdin);
                if (strncmp(bufr, QUIT_STRING, strlen(QUIT_STRING)) == 0) 
                {
                    /* 退出聊天室 */
                    sendpkt(sock, LEAVE_GROUP, 0, NULL);
                    break;
                }

                /* 发送消息文本到服务器 */
                sendpkt(sock, USER_TEXT, strlen(bufr) + 1, bufr);
            }

        }

    }
} 
```

## 参考资料

*   [《UNIX 环境高级编程》](https://book.douban.com/subject/1788421/)
*   https://github.com/santoshkt/chat-client-server