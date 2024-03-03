# 第 3 节 linux 系统监控工具——Conky

### 实验楼在 2015 年 4 月 23 日之后对实验环境的外网连接进行了限制，本实验受此影响可能无法正常完成，详情请参阅常见问答：[实验楼环境是否可以连接外网](https://www.shiyanlou.com/questions/235)。

## 实验简介

Conky 是一个应用于桌面环境的系统监视软件，可以在桌面上监控系统运行状态、网络状态等一系列参数，而且可自由定制，但对于新手来说可能会比较难于上手。

Conky 是一种自由软件，用于 X 视窗系统的系统监视，可以在 FreeBSD、OpenBSD 和各种 Linux 发布上使用的自由软件。Conky 具有很高的可配置性，可以监视许多系统参数，如：CPU、内存、交换内存、硬盘使用情况等状态；各种硬件的温度；系统的进程（top）；网络状态；电池电量；系统信息和邮件收发；各种音乐播放器 MPD、XMMS2、BMPx、Audacious）的控制。不像其他系统监视器那样需要高级别的部件工具箱（widget toolkits）来渲染他们的信息，Conky 可以直接在 X 视窗下渲染，这意味着在相同配置下 Conky 可以消耗更少的资源。

## 一、Conky 安装

Conky 软件安装方法很简答。加入一个第三方软件源即可。下面直接贴上安装命令：

```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:teejee2008/ppa
$ sudo apt-get update
$ sudo apt-get install conky-manager 
```

如果要卸载该软件：

```
$ sudo apt-get remove conky-manager 
```

去除 PPA：

```
$ sudo apt-get install ppa-purge
$ sudo ppa-purge ppa:teejee2008/ppa 
```

## 二、Conky 运行

Conky 启动：

```
$ conky 
```

操作截图：

![图片描述信息](img/10)

上图显示了本机的系统名 ，正常运行时间， CPU 使用率，内存使用率，磁盘使用情况，进程的统计资料，以及网络监控等情况。底部是进程的详细信息。

使用 ctrl+c 结束 conky。

查看帮助：

```
$ conky -h 
```

Conky-manager 不仅用于监测 linux 系统，同样也是一种主题桌面。

Conky-manager 启动：

```
$ conky-manager 
```

操做截图：

![](img/10)

在 conky-manager 的主界面中选择需要监测的项目，在 linux 的主界面中便会出现相应的图表。包括 CPU 使用率，内存使用率，硬盘使用率，进程统计。不同的选项有不同的风格。笔者选择了多项，界面效果如下：

操作截图：

![图片描述信息](img/10)

再次打开终端，运行 conky-manager 之后去除打沟的选项即可消除界面上的监控图表。

conky-manager 同时还有精美的桌面主题，在“theme”选项中，再本实验中不做讲解。

操作截图：

![图片描述信息](img/10)