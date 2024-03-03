# 第 1 节 Ansible 初试

## 一、实验介绍

随着云的不断成熟，随着企业服务器数量越来越多，当服务器到达几百台，上千台服务器之后，服务器日常管理也逐渐繁杂，自动化的运维方式逐渐成为主流，提高效率，降低错误几率。本实验将带领大家认识较为常用的自动化运维工具 Ansible

### 1.1 实验涉及的知识点

*   Ansible 的用处
*   Ansible 的优势
*   Ansible 的 Inventory
*   Ansible 的 Ad-Hoc

## 二、Ansible 的简介

随着云的不断成熟，随着企业服务器数量越来越多，当到达上百台，上千台服务器之后，服务器日常管理也逐渐繁杂，而且如此多的服务器若是每天通过人工去频繁的更新或者部署及管理这些服务器，即使是都有成套的脚本，但也需要人工的一台台去执行，这样浪费大量的时间，效率很是低下，而且人为的操作很容易因为不细心、马虎而出现纰漏。

运维自动化是指将 IT 运维中日常的、大量的重复性工作自动化，把过去的手工执行转为自动化操作。这样可以大大提高我们的工作效率，能够更加及时，高效的解决问题。

而常用的自动化运维工具有许多，Chef, Puppet, Ansible, SaltStack and Fabric 等等，各个工具有各个工具的好处，今天我们要学习的是 Ansible。

Ansible 是一款由 python 开发出来的自动化运维的工具。而使用 Ansible 有这样的一些优点:

*   Ansible 是使用 python 开发出来的，维护相对于 ruby 较为简单。开发库要比基于 Ruby 的运维工具更多
*   Ansible 是基于 paramiko 开发的，所以无需服务端也不用客户端。是基于 SSH 工作的。
*   Ansible 可以通过命令执行一些简单的任务，也可以使用 playbook 来执行大量的任务。并且 playbook 不用分发到远程，在本地即可执行。
*   Ansible 的 playbook 使用的是 Jinja2，非常的简单易学，不用再去学习一门语言。
*   Ansible 是基于模块工作的,易于扩展，并且 Ansible 是开源的软件，在 [github 公开了代码](https://github.com/ansible/ansible)，可以借鉴开发自己需要的[自定义模块](http://docs.ansible.com/ansible/developing_modules.html)来使用。并且可以使用任意的语言开发。

Ansible 基于模块工作的，本身没有批量部署的能力。 Ansible 所运行的模块才是真正实现批量部署的关键，Ansible 提供的是一种框架。结构大体如下：

*   connection plugins：主要用于本地与操作端之间的连接与通信；
*   host inventory：指定操作的主机，是一个配置文件里面定义监控的主机；
*   各种模块：核心模块、自定义模块等等；
*   使用插件完成记录日志、邮件等功能；
*   playbook：执行多任务，可多个节点也可以单个节点

![实验楼](https://dn-simplecloud.qbox.me/1135081470635637349) （此图来源<http://tekslate.com/tutorials/ansible/>）

## 三、Ansible 的使用

### 3.1 Ansible 的安装

通过上文的介绍，相信大家对 Ansible 有一个大体上的认识了，我们先来安装 ansible（我们可以通过源安装，也可以通过 git 源码来安装，也可以使用 pip 安装，这里是使用源安装）

```
#更新软件包的信息
sudo apt-get update

#首先安装管理安装软件库的工具
sudo apt-get install software-properties-common

#添加 ansible 的源
sudo apt-add-repository ppa:ansible/ansible

#更新软件包信息
sudo apt-get update

#安装 ansible
sudo apt-get install ansible

#验证是否正确安装，且是最新版
ansible --version 
```

在添加源的时候记得确认添加

![add_ppa](https://dn-simplecloud.qbox.me/1135081470637371186)

![All_command](https://dn-simplecloud.qbox.me/1135081470637544754)

### 3.2 Ansible 的 Inventory

Ansible 可以同时对多台机器或者单台机器亦或者部分机器操作便是通过 Inventory，Inventory 默认是存放在 ·`/etc/ansible/hosts` 中。

在 Inventory 中我们列出了我们需要操作的机器，我们可以单纯的列出这些主机，但是推荐很有条理的为他们分组，在使用时可以只对其中的某组操作，更多的高级的配置可以[参考官方文档](http://docs.ansible.com/ansible/intro_inventory.html)

在 Inventory 中以 `#` 开头的便是注释

```
sudo vim /etc/ansible/hosts

#此为配置文件内容，我们可以这样的来使用

#为常用的机器分组，这里为 test 组，可以只有一台，也可以有多台
[test]
127.0.0.1 

#添加对改组机器登陆的用户与验证的方式配置
[dev_test]
54.11.11.11 ansible_ssh_user=ubuntu ansible_ssh_private_key_file=/path/of/keyfile

#或者不分组，给特别的机器去别名,指定其 ip，与特别开放的端口号
jumper ansible_port=5555 ansible_host=192.168.1.50 
```

在这里将 ssh 登陆的密码写在 Inventory 的方式并不推荐，可以在使用 ansible 命令使用 --ask-pass 的参数，此处使用只是因为本实验环境的密码很容易输入错误，若是在这里出现错误可以看看下文的常见错误

![实验楼](https://dn-simplecloud.qbox.me/1135081470646367410))

### 3.3 Ansible 的 Ad-Hoc

我们可以使用 ping 模块来尝试使用 ansible 命令

```
ansible test -m ping 
```

![实验楼](https://dn-simplecloud.qbox.me/1135081470642062116)

这里使用的 test 是我们之前设置的分组名，而若是使用的 all 则表示对所有的机器执行命令，`-m` 便是指定使用的模块

```
#我们可以使用这样的方法让所有我们需要操作的机器输出 hello world
ansible test -a "/bin/echo hello world" 
```

![实验楼](https://dn-simplecloud.qbox.me/1135081470642855904)

```
#我们可以使用这样的方法查看所有我们需要操作的机器的信息
ansible test -a setup 
```

![实验楼](https://dn-simplecloud.qbox.me/1135081470643195158)

通过以上的例子我们可以看出 Ansible 的 Ad-Hoc 命令的格式大致是这样的：

```
ansible 主机名或者组名 -m 模块名 -a "模块参数" 其他参数 
```

Ansible 不仅仅能做上面这些查看信息的一些操作，看这样的一个例子：

```
#在我们所操作的 test 组中所有的主机的/home/shiyanlou 目录下创建一个名为 testfile 的文件，并且权限为 777
ansible test -m file -a "dest=/home/shiyanlou/testfile state=touch mode=777" 
```

![实验楼](https://dn-simplecloud.qbox.me/1135081470647253521)

其实 Ansible 为我们提供了很多的模块，可以创建文件，文件夹，可以修改文件内容，可以复制文件，可以使用 AWS 上预留的接口等等。

Ansible 基本上可以支持我们所能想到的常用操作，并且 Ansible 也在不断的完善当中，来支持我们更多需要的操作。

若是你有需要的 shell 操作翻阅 Ansible 文档中没有支持，我们还有万不得已的解决方法就是直接使用 shell 模块，使用 shell 命令即可

从上文的截图，以及常见错误截图中我们可以看到返回的有三种类型

*   success：这样的结果表示这个操作成功，这其中有两种情况，第一是做一些查询等等的一些简单操作不需要修改内容的，表示该操作没问题，第二种情况就是若是这个操作曾经做过再做是便会直接表示成功

*   changed：true 这样的结果表示你做的一些修改行的操作执行成功，如上文的创建了一个文件，或者修改了配置文件，复制了一个文件等等这类的操作就会有这样的结果

*   failed：这样的结果表示这个操作执行失败，可能是密码错误，参数错误等等，具体看提示中的 msg 的值。并且在 playbook 中有多个任务，中间的某个任务出现这样的情况便不会继续往下执行。（playbook 会在后续的试验中详细讲解，当然在 2.0 之后出了一个新功能来补救这个缺陷）

ansible 更多的参数我们可以使用 `ansible --help` 或者是 `man ansible` 来查看

ansible 有许许多多的模块供我们来使用，更多的是用参数以及模块我们可以查阅 ansible 的 文档中的[模块章节](http://docs.ansible.com/ansible/list_of_all_modules.html)

Ad-Hoc 的作用就是在我们只需要做一些简单的操作，不用脚本记录下来的时候，能够简单、快速的在我们需要的机器中做出相应的修改或者查看。

灵活的使用 Ad-Hoc 能给我们带来很大的便捷,希望在学习之时多多查阅 Ansible Document，新功能的添加与提醒都会在其中体现。

## 四、常见的错误

若是遇到这样的错误信息：

![实验楼](https://dn-simplecloud.qbox.me/1135081470640868808)

这是因为 ssh 连接时，可以使用 `-o` 参数将 `StrictHostKeyChecking` 设置为 no,在 ansible 配置文件中 `host_key_checking=False` 是注释的。解决方法便是将 `/etc/ansible/ansible.cfg` 中的 `host_key_checking` 的注释符删除即可

解决方法[参考](http://noodle.blog.51cto.com/2925423/1769433)

## 五、实验总结

通过本实验带大家认识了 Ansible 是个什么样的工具，同时也带大家使用了 Ansible Ad-Hoc，领略了它的便捷，无需客户端便可为我们批量的操作大量的机器。一定要多翻阅 ansible document，灵活使用。

## 六、参考资料

[1] Ansible 官方文档：<http://docs.ansible.com/ansible/>

[2] Ansible 官方文档介绍 Ad-Hoc：<http://docs.ansible.com/ansible/intro_adhoc.html>