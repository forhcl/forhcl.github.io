---
title: Pro git
date: 2019-06-05 17:03:44
tags:
- git
- github
categories: VCS
---

参考资料：[Pro git](http://iissnan.com/progit/ "Pro Git 简体中文版")

`Git`仓库中的名为`.git`的目录，存放着所有`Git`需要的数据和资源。

`README`文件：项目介绍文档（写给对项目有兴趣的人看的）

#### 起步

&emsp;&emsp;版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。可对任何类型的文件进行版本控制。

&emsp;&emsp;分布式版本控制系统（ Distributed Version Control System，简称 DVCS ）面世了。在这类系统中，像 Git，Mercurial，Bazaar 以及 Darcs 等，客户端并不只提取最新版本的文件快照，<span style="color:red">而是把代码仓库完整地镜像下来</span>。这么一来，任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复。因为每一次的提取操作，实际上都是一次对代码仓库的完整备份。

&emsp;&emsp;需要在 Windows 命令行中使用 Git 的用户，必须注意：在参数中间有空格的时候，必须使用双引号将参数括起来（在 Linux 中是单引号）；另外，如果扬抑符（^）作为参数的结尾，并且作为这一行的最后一个字符，则这个参数也需要用双引号括起来。因为扬抑符在 Windows 命令行中表示续行（译注：即下一行为这一行命令的继续）。

#### 一、取得项目的Git仓库

&emsp;&emsp;有两种取得Git仓库的方法。第一种是在现存的目录下，通过导入所有所有文件来创建新的 Git 仓库。第二种是从已有的 Git 仓库克隆出一个新的镜像仓库来。

###### 在工作目录中初始化新仓库

&emsp;&emsp;对<u>现有的某个项目</u>开始用Git管理，只需到此项目所在的目录，执行：

```bash
$ git init
```

&emsp;&emsp;初始化后，在当前目录下会生成一个名为`.git`的目录。

&emsp;&emsp;目前仅仅是按照既有的结构框架初始化好了里面所有的文件和目录，但我们还没有开始跟踪管理项目中的任何一个文件。

&emsp;&emsp;如果当前目录下有几个文件想要__纳入版本控制__，需要先用 `git add` 命令告诉 Git 开始对这些文件进行跟踪，然后提交：

```bash
$ git add *.c
$ git add README
$ git commit -m 'initial project version'
```

------

&emsp;&emsp;应用场景，如Blog向github备份项目每次都要执行：

```bash
$ git add .
$ git commit -m '~'（项目版本）
$ git push origin ~(要把项目提交到的分支名称)
```

 <!-- 因为github上面没有创建对应的远程仓库，所以先不研究git push命令 -->

###### 从现有仓库克隆

__初次克隆某个仓库时，工作目录中的所有文件都属于已跟踪文件，且状态为未修改__

格式：

```bash
$ git clone [url]  [指定/新建的项目目录]
```

例：

```bash
$ git clone git://github.com/schacon/grit.git
```

在当前目录下创建一个名为`grit`的目录，其中包含一个 `.git` 的目录

```bash
$ git clone git://github.com/schacon/grit.git mygrit
```

在当前目录下创建一个名为`mygrit`的目录，其中包含一个`.git`的目录。

------

&emsp;&emsp;Git 支持许多数据传输协议。之前的例子使用的是 `git://` 协议，不过你也可以用 `http(s)://` 或者 `user@server:/path.git` 表示的 SSH 传输协议。

#### 二、记录每次更新到仓库

&emsp;&emsp;`git add` 的潜台词就是把目标文件快照放入暂存区域，也就是 add file into staged area，同时未曾跟踪过的文件标记为需要跟踪。

使用 Git 时的文件状态变化周期:

 

![File Statue Lifecycle](Pro-git/Git-文件状态生命周期.png)

相关的英语单词：

1. track：跟踪
2. modify：修改
3. stage：暂存

注意：已跟踪的文件，在修改后要提交需要再次执行`git add`将文件放进暂存区。

&emsp;&emsp;`git add` 命令（这是个多功能命令，根据目标文件的状态不同，此命令的效果也不同：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等）

注意：每次提交时`commit`对象保存的是已放进暂存区的文件，所以每次提交的时候尽量使用`git status`命令查看是否有将要保存的文件放入暂存区。

###### 忽略某些文件

&emsp;&emsp;一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件模式。

编写`.gitignore`文件的具体语法：<http://iissnan.com/progit/html/zh/ch2_2.html>

###### 移除文件

`git rm` ：将文件从已跟踪文件清单中移除（确切地说，是从暂存区域移除），<span style="color:red">并连带从工作目录中删除指定的文件。</span>如果不想删除目录中的文件而被老板炒鱿鱼，慎用。可通过配置`.gitignore`来忽略文件纳入版本管理。

###### 查看已暂存和未暂存的更新

`git diff`：工作目录中当前文件和暂存区域快照之间的差异，也就是修改之后还没有暂存起来的变化内容。

`git diff --staged`：已经暂存起来的文件和上次提交时的快照之间的差异。

###### 提交更新

git commit`：将当前暂存区中的文件实际保存到仓库的历史记录中。

有以下两种提交方式：

- 记述一行提交信息

```bash
$ git commit -m "提交信息"
```

- 记述详细的提交信息

```bash
$ git commit
```

​	在`vim`编辑器中记述提交信息的格式如下：

​		第一行：用一行文字简述提交的更改内容。

​		第二行：空行。

​		第三行以后：记述更改的原因和详细内容。

​	这种情况如果将提交信息留空并直接关闭编辑器，这次提交就会被中止。

&emsp;&emsp;`Git` 提供了一个跳过使用暂存区域的方式，只要在提交的时候，给 `git commit` 加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤。

###### 重命名文件

`git mv [oldName] [newName]`

###### 自动补全

在输入 Git 命令的时候可以敲两次跳格键（Tab），就会看到列出所有匹配的可用命令建议。

一次跳格键（Tab）如果只有一个符合会自动补全。

###### Git命令别名

------

#### `Git`分支

&emsp;&emsp;<span style="color:#d12573">`Git`保存的不是文件差异或者变化量，而只是一系列文件快照。</span>这句话有点不明白，如果是将文件整个保存，岂不是占大量存储空间，是不是还有将内容进行压缩？

&emsp;&emsp;`Pro Git`中是这样定义分支的，分支本质上仅仅是个指向`commit`对象的可变指针。切换分支之前，留心你的暂存区或者工作目录里，那些还没有提交的修改，它会和你即将检出的分支产生冲突从而阻止`Git`为你切换分支。切换分支的时候最好保持一个清洁的工作区域。通过`stashing` 和 `commit amending`两种方法之一可以绕过切换分支前对未提交内容的提交。

&emsp;&emsp;`Git`中通过`HEAD`指针指向你正在工作中的本地分支（译注：将`HEAD`想象为当前分支的别名。

创建一个新的分支：

`git branch [branchName]`

切换分支：

`git checkout [branchName]`

创建并切换到新分支：

`git checkout -b [branchName]`

合并指定分支到当前分支：

`git merge [branchName]`

&emsp;&emsp;如果顺着一个分支走下去可以到达另一个分支的话，那么 `Git` 在合并两者时，只会简单地把指针右移，因为这种单线的历史分支不存在任何需要解决的分歧，所以这种合并过程可以称为快进（`Fast forward`）。

删除指定分支：

`git branch -d [branchname]`

&emsp;&emsp;当出现合并冲突时，`Git`会在发生冲突的文件内容上自动作出标记，可以手动打开文件进行冲突处理。处理完冲突后使用`git add` 将把它们标记为已解决状态（译注：实际上就是来一次快照保存到暂存区域。），确认所有冲突都已解决，也就是进入了暂存区，就可以用 `git commit` 来完成这次合并提交。当然我们也可以安装一些处理合并冲突的`Git`插件。

显示所有的分支：

`git branch`

查看各个分支最后一个提交对象的信息：

`git branch -v`

查看哪些分支已被并入当前分支（译注：也就是说哪些分支是当前分支的直接上游。）

`git branch --merge`

查看尚未合并的工作：

`git branch --no-merged`

删除不包含未提交内容的分支：

`git branch -d [branchName]`

强制删除包含为提交内容的分支：

`git branch -D [branchName]`

<span style="color:red">额，谷歌看一下有没有可以实时查看这些选项方法没有，大脑内存不足</span>

<span style="color:red">没错，是有的。比如，要学习config命令可以怎么用，运行：</span>

```bash
$ git help config
```

<span style="color:red">会弹出对应命令的网页。</span>或者利用代码的自动补全功能查看选项的全称。