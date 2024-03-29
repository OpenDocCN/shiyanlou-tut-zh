# 第 5 节 Linux 监控的 Python 脚本

## 实验简介

目前 Linux 下有一些使用 Python 语言编写的 Linux 系统监控工具 比如 inotify-sync（文件系统安全监控软件）、glances（资源监控工具）在实际工作中，Linux 系统管理员可以根据自己使用的服务器的具体情况编写一下简单实用的脚本实现对 Linux 服务器的监控。 本文介绍一下使用 Python 脚本实现对 Linux 服务器 CPU 内存 网络的监控脚本的编写。

## 一、工作原理：基于/proc 文件系统

Linux 系统为管理员提供了非常好的方法，使其可以在系统运行时更改内核，而不需要重新引导内核系统，这是通过/proc 虚拟文件系统实现的。/proc 文件虚拟系统是一种内核和内核模块用来向进程（process）发送信息的机制，这个伪文件系统允许与内核内部数据结构交互，获取有关进程的有用信息，在运行中改变设置（通过改变内核参数）。与其他文件系统不同，/proc 存在于内存而不是硬盘中。proc 文件系统提供的信息如下：

```
进程信息：系统中的任何一个进程，在 proc 的子目录中都有一个同名的进程 ID，可以找到 cmdline、mem、root、stat、statm，以及 status。某些信息只有超级用户可见，例如进程根目录。每一个单独含有现有进程信息的进程有一些可用的专门链接，系统中的任何一个进程都有一个单独的自链接指向进程信息，其用处就是从进程中获取命令行信息。

CPU 信息：利用/proc/CPUinfo 文件可以获得中央处理器的当前准确信息。

负载信息：/proc/loadavg 文件包含系统负载信息。

系统内存信息：/proc/meminfo 文件包含系统内存的详细信息，其中显示物理内存的数量、可用交换空间的数量，以及空闲内存的数量等。 
```

## 二、Python 实现对 CPU 监测

```
$ touch cpu.py

$ gedit cpu.py 
```

在 cpu。py 中写入以下代码：

```
#!/usr/bin/env Python
from __future__ import print_function
from collections import OrderedDict
import pprint

def CPUinfo():
    ''' Return the information in /proc/CPUinfo
    as a dictionary in the following format:
    CPU_info['proc0']={...}
    CPU_info['proc1']={...}
    '''
    CPUinfo=OrderedDict()
    procinfo=OrderedDict()

    nprocs = 0
    with open('/proc/cpuinfo') as f:
        for line in f:
            if not line.strip():
                # end of one processor
                CPUinfo['proc%s' % nprocs] = procinfo
                nprocs=nprocs+1
                # Reset
                procinfo=OrderedDict()
            else:
                if len(line.split(':')) == 2:
                    procinfo[line.split(':')[0].strip()] = line.split(':')[1].strip()
                else:
                    procinfo[line.split(':')[0].strip()] = ''

    return CPUinfo

if __name__=='__main__':
    CPUinfo = CPUinfo()
    for processor in CPUinfo.keys():
        print(CPUinfo[processor]['model name']) 
```

操作截图：

![图片描述信息](img/10)

运行此 python 文件：

```
$ python cpu.py 
```

结果如下：

操作截图：

![图片描述信息](img/10)

程序简要解析：

读取/proc/CPUinfo 中的信息，返回 list，每核心一个 dict。其中 list 是一个使用方括号括起来的有序元素集合。List 可以作为以 0 下标开始的数组。Dict 是 Python 的内置数据类型之一, 它定义了键和值之间一对一的关系。OrderedDict 是一个字典子类，可以记住其内容增加的顺序。常规 dict 并不跟踪插入顺序，迭代处理时会根据键在散列表中存储的顺序来生成值。在 OrderedDict 中则相反，它会记住元素插入的顺序，并在创建迭代器时使用这个顺序。

## 三、Python 实现系统负载监测

建立 sys_load.py, 并写入以下代码：

```
$  touch sys_load.py

$ gedit sys_load.py 
```

写入如下代码：

```
#!/usr/bin/env Python   
import os 
def load_stat(): 
    loadavg = {} 
    f = open("/proc/loadavg") 
    con = f.read().split() 
    f.close() 
    loadavg['lavg_1']=con[0] 
    loadavg['lavg_5']=con[1] 
    loadavg['lavg_15']=con[2] 
    loadavg['nr']=con[3] 
    loadavg['last_pid']=con[4] 
    return loadavg 
print "loadavg",load_stat()['lavg_15'] 
```

操作截图：

![图片描述信息](img/10) 运行结果脚本：

```
$ Python sys_load.py 
```

操作截图：

![图片描述信息](img/10)

脚本简要解析： 读取/proc/loadavg 中的信息，import os ：Python 中 import 用于导入不同的模块，包括系统提供和自定义的模块。其基本形式为：import 模块名 [as 别名]，如果只需要导入模块中的部分或全部内容可以用形式：from 模块名 import *来导入相应的模块。OS 模块 os 模块提供了一个统一的操作系统接口函数，os 模块能在不同操作系统平台如 nt，posix 中的特定函数间自动切换，从而实现跨平台操作。

## 四、Python 实现内存信息的获取

建立 mem.py，并编辑脚本：

```
$ touch mem.py

$ gedit mem.py 
```

代码如下:

```
#!/usr/bin/env Python

from __future__ import print_function
from collections import OrderedDict

def meminfo():
    ''' Return the information in /proc/meminfo
    as a dictionary '''
    meminfo=OrderedDict()

    with open('/proc/meminfo') as f:
        for line in f:
            meminfo[line.split(':')[0]] = line.split(':')[1].strip()
    return meminfo

if __name__=='__main__':
    #print(meminfo())

    meminfo = meminfo()
    print('Total memory: {0}'.format(meminfo['MemTotal']))
    print('Free memory: {0}'.format(meminfo['MemFree'])) 
```

操作截图：

![图片描述信息](img/10)

运行脚本：

```
$ Python mem.py 
```

操作截图：l

![图片描述信息](img/10)

代码简要解析：

读取 proc/meminfo 中的信息，Python 字符串的 split 方法是用的频率还是比较多的。比如我们需要存储一个很长的数据，并且按照有结构的方法存储，方便以后取数据进行处理。当然可以用 json 的形式。但是也可以把数据存储到一个字段里面，然后有某种标示符来分割。 Python 中的 strip 用于去除字符串的首位字符，最后清单 3 打印出内存总数和空闲数。

## 五、Python 实现网络接口的监测

建立 net.py，并编辑：

```
$ touch net.py

$ gedit net.py 
```

代码如下：

```
#!/usr/bin/env Python
import time
import sys

if len(sys.argv) > 1:
    INTERFACE = sys.argv[1]
else:
    INTERFACE = 'eth0'
STATS = []
print 'Interface:',INTERFACE

def rx():
    ifstat = open('/proc/net/dev').readlines()
    for interface in  ifstat:
        if INTERFACE in interface:
            stat = float(interface.split()[1])
            STATS[0:] = [stat]

def tx():
    ifstat = open('/proc/net/dev').readlines()
    for interface in  ifstat:
        if INTERFACE in interface:
            stat = float(interface.split()[9])
            STATS[1:] = [stat]

print   'In         Out'
rx()
tx()

while   True:
    time.sleep(1)
    rxstat_o = list(STATS)
    rx()
    tx()
    RX = float(STATS[0])
    RX_O = rxstat_o[0]
    TX = float(STATS[1])
    TX_O = rxstat_o[1]
    RX_RATE = round((RX - RX_O)/1024/1024,3)
    TX_RATE = round((TX - TX_O)/1024/1024,3)
    print RX_RATE ,'MB      ',TX_RATE ,'MB' 
```

操作截图：

![图片描述信息](img/10)

运行结果：

```
 $ python net.py 
```

操作截图：

![图片描述信息](img/10)

对于有多个网卡啊主机，可以利用如下命令查看其它网卡流量,例如 eth1，由于本实验环境只有一个网卡，所有只能跟踪 eth0。 ：

```
$ python net.py eth1 
```

代码简要解析：

读取/proc/net/dev 中的信息，Python 中文件操作可以通过 open 函数，这的确很像 C 语言中的 fopen。通过 open 函数获取一个 file object，然后调用 read()，write()等方法对文件进行读写操作。另外 Python 将文本文件的内容读入可以操作的字符串变量非常容易。文件对象提供了三个“读”方法： read()、readline() 和 readlines()。每种方法可以接受一个变量以限制每次读取的数据量，但它们通常不使用变量。 .read() 每次读取整个文件，它通常用于将文件内容放到一个字符串变量中。然而 .read() 生成文件内容最直接的字符串表示，但对于连续的面向行的处理，它却是不必要的，并且如果文件大于可用内存，则不可能实现这种处理。.readline() 和 .readlines() 之间的差异是后者一次读取整个文件，象 .read() 一样。.readlines() 自动将文件内容分析成一个行的列表，该列表可以由 Python 的 for … in … 结构进行处理。另一方面，.readline() 每次只读取一行，通常比 .readlines() 慢得多。仅当没有足够内存可以一次读取整个文件时，才应该使用 .readline()。最后清单 4 打印出网络接口的输入和输出情况。