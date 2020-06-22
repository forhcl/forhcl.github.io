Oracle WebLogic Server：WebLogic应用服务器是整个WebLogic产品的核心

## weblogic的安装最后需要去手动添加一个配置文件

添加配置文件的位置，在启动文件下同一目录下面./servers/AdminServer/下创建一个security文件夹，然后通过vi创建一个boot.properties文件，

```properties
username=
password=
```

然后在**第一次启动**的时候会编译这个文件，然后把用户名密码编译成看起来像乱码的字符串。

Linux下面的webLogic启动文件的一个路径`/home/redhat/Oracle/Middleware/Oracle_Home/user_projects/domains/base_domain/startWebLogic.sh`   启动在7001端口

http://192.168.91.129:7001/console 进行访问

其中192.168.91.7001是虚拟机的IP地址

## 启动weblogic

`nohup ./startWebLogic.sh & tail -f nohup.out`

&后面的命令可以等后面在打，主要用来显示weblogic的启动信息。如果没有的话就没有相应启动信息打印

`ifconfig` 展示配置信息，如ip等

`clear`:在SecureCRT中用来将命令行置顶

`cat`:用来查看文件的内容，可以翻页比vim方便。用于查看文件内容的命令还有`more、less、head、tail`等

`tab键`：按了首字母就可以帮助补全

`chown`：修改文件的所有者和所属组 语法：chown <u>-R</u> user:group directory     -R选项：对目录下面的所有目录递归这个操作

 `su`：切换用户 su = su - root  语法：su - username

`chkconfig`：设置服务开机自启

`service`：管理服务

`ls`;`ll` 相当于 `ls -l`



## weblogic的基本概念学习

###### 域（Domain）

一个管理服务器，多个受管服务器形成集群。一个域中可以有多个受管服务器，但是只能有一个管理服务器。通常情况下业务应用程序运行在受管服务器中，而管理服务器不会运行业务应用程序，仅仅用于管理域中的资源。



集群中的多台机器都要使用同一个版本的weblogic！！！





























































































































SecureCRT：一个类似于MobaXterm的软件

winscp：文件传输

我感觉MobaXterm一个顶两，但是没办法工作要前面两个









