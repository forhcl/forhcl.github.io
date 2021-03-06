---
title: Linux命令行大全
date: 2020-06-04 08:47:35
tags:
categories:
hidden:
---

### 第一章 shell是什么？

&emsp;&emsp;当我们启动Terminal 终端（或者其它远程工具的命令行界面）的时候，在每一行开始的地方会有shell提示符`[root@localhost ~]#`，其中root为当前用户名，localhost为主机名称，`~`为主机的名称。当shell提示符的最后一个字符是`#`，而不是一个`$`符号，那么终端会话将享有超级用户特权。这就意味着要么我们是以根用户身份登录，<u>要么我们选择的终端仿真器可以提供超级用户（管理）特权</u>。

+ 命令历史记录

  向上或者向下可以查看命令历史记录，大部分的Linux发行版本能够存储最近输入的**500**个命令。

+ 复制和粘贴

  Linux中的复制粘贴不同于Windows的Ctrl+C和Ctrl+V，Linux中的复制、粘贴命令为Ctrl+Insert和Shift+Insert。至于为什么不同去问微软吧，毕竟Linux比windows先出现。

+ date

  查看系统当前的时间，注意：虚拟机查出来的时间和本机的不同，怎么去把它调成一样？

+ cal

  查看系统当月的日历

+ df

  查看磁盘可用的空间

+ free

  查看可用的内存

+ exit/logout  结束终端会话

### Linux中导航文件系统

&emsp;&emsp;在Windows系统中，每个存储设备都有一个独立的文件系统树。而在类UNIX系统中，如Linux，无论多少驱动器或存储设备与计算机相连，通常只有一个文件系统树。根据管理员的设置，存储设备将会连接（更准确的说是“挂载”）到文件系统树的不同位置。。 **如何通过命令行访问到挂载的存储设备（U盘、光盘）？**

+ pwd

  当前工作目录的绝对路径

每个用户账号都有一个主目录，**作为普通用户操作时，这是唯一一个允许用户<span style="color:red">写</span>文件的地方。**那这样子的话安装的软件就安装在当前用户的主目录中，不会对其它的用户造成影响。所以尽量不要采用root安装软件，不然可能造成不好的影响。

+ cd

  - 绝对路径

    以`/`开始

  - 相对路径

    绝对路径名是从根目录开始，通向目标目录，而相对路径名则是从工作目录开始的。为了实现这个目的，它通常使用一些特殊符号来表示文件系统树的相对位置，这些特殊符号是“.”(当前工作目录)和“..”（上一级工作目录/父目录）。“.”当前工作目录使用的情况很少，因为它是隐含的，比如你要进入子目录./bin的时候完全不需要cd ./bin而是可以直接cd bin。

![cd常用快捷方式](http://cdn1.hikariblog.cn/cd%E5%B8%B8%E7%94%A8%E5%BF%AB%E6%8D%B7%E6%96%B9%E5%BC%8F.png)

+ ls

  **以“.”字符开头命名的文件是隐藏的。**这仅说明ls不会列出这些文件，除非输入ls -a。在创建用户账号时，主目录里会放置一些隐藏文件，用来配置账号信息。仔细观察这样的文件，可以使我们了解如何自定义工作环境。此外，一些应用程序也会将他们的配置文件和设置文件以隐藏文件的形式放在主目录下面。

  与UNIX一样，在Linux中，文件名与命令是区分大小写的。文件名File1和file1指向不同的文件。

  **文件名不用采用空格，不然对于命令的执行可以很不友好！！采用下划线代替空格。**

### 第3章 Linux系统

`ls -l`可以缩写为`ll`







WebLogic





### 高级键盘技巧

+ clear清屏  在shell里面的确是清屏，但是在SecureCRT里面的话只是把要输入命令的那一行置顶。
+ history：显示历史列表的记录。一般能保存大概500个历史命令。

Ctrl+A移动到行首，Ctrl+E移动到行末

搜索历史命令记录，Ctrl+R进入历史命令搜索模式，搜索到了Enter键执行，Ctrl+J将搜索到的命令输出到当前命令行中以作进一步的编辑。









看一下权限和用户！！！！！和软件安装yum。然后安装一下Oracle





### Linux根目录下各个文件夹的含义

![Linux根目录](http://cdn1.hikariblog.cn/Linux%E6%A0%B9%E7%9B%AE%E5%BD%95.png)

+ bin

  包含二进制可执行文件。
  在单用户模式下，你需要使用的常见linux命令都位于此目录下。系统的所有用户使用的命令都设在这里。
  例如：ps、ls、ping、grep、cp

+ boot

  引导加载程序相关的所有文件

+ home

  存储所有普通用户的主目录

+ var

  变量文件

+ tmp

  临时文件，当系统重新启动的时候，这个目录下的文件都将被删除掉

+ media

  可移动存储介质的挂载目录  怎么把U盘挂载到Linux虚拟机上面！！

