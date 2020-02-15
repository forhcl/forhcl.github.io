---
title: Maven之坐标、依赖
date: 2019-05-29 10:05:41
tags: Maven
categories: Mavan
---

### _坐标 ：唯一标识Java构件（artifact）_

在Maven世界中，任何一个依赖、插件或者项目构建的输出，都可以称为 _构件_

```xml
<groupId>org.sonatype.nexus</groupId>
<artifactId>nexus-indexer</artifactId>
<version>2.0.0</version>
```

*Maven项目的坐标与仓库路径以及文件名对应的关系：（以上述坐标为例，具体可到本地仓库查看引入的依赖目录）*

> 目录：<u>org/sonatype/nexus</u>/<u>nexus-indexer</u>/<u>2.0.0</u>/**nexus-indexer-2.0.0.jar**
>
> 文件名规则：artifactId-version[-classifier].packaging，中括号表示可选

###### 构成Maven坐标的一些元素

一个实际项目可拆分为多个Maven项目（模块）

1. _必须定义的元素_

+ __groupId__：公司或组织域名倒序.实际项目
+ __artifactId__：实际项目-【模块（Maven项目）】
+ __version__：版本

2. _可选的元素_

+ __packaging__：jar/war（默认为jar）

  <u>注意：packaging不一定与构件扩展名对应，比如packaging为maven.plugin的构件扩展名为jar。</u>

3. 不能直接定义的元素

+ __classifier__：该元素用来帮助定义构建输出的一些附属构件。

  <u>注意：不能直接定义项目的classifier，因为附属构件不是项目直接默认生成的，而是由插件帮助生成。</u>

------

__依赖__：

###### 依赖的配置

根元素project下的deppendencies可以包含一个或者多个dependency元素，以声明一个或者多个项目依赖。每个依赖可以包含的元素有：

+ __groupId、artifactId和version__：依赖的基本坐标，对于任何一个依赖来说，基本坐标是最重要的，Maven根据坐标才能找到需要的依赖。
+ __type__：依赖的类型，对应于项目坐标定义的packaging。大部分情况下，该元素不必声明，其默认值为jar。
+ __scope__：依赖的范围。
+ __optional__：标记依赖是否可选。
+ __exclusion__：用来排除传递性依赖。

大部分依赖声明只包含基本坐标，然而在一些特殊情况下，其他元素至关重要。

###### 依赖范围

&emsp;&emsp;Maven在编译项目主代码的时候需要使用一套classpath，Maven在编译和执行测试的时候会使用另一套classpath，实际运行Maven项目的时候，又会使用一套classpath。

&emsp;&emsp;依赖范围就是用来控制依赖与这三种classpath（编译classpath、测试classpath、运行classpath）的关系。

Maven有以下几种依赖范围：

1. compile：编译依赖范围，对于三种classpath都有效。（默认使用）
2. test：测试依赖范围，值对于测试classpath有效。
3. 

{% asset_img 依赖范围和classpath的关系.png 依赖范围和classpath的关系 %}

###### 传递性依赖

_概念_

A依赖B，B依赖C，A对于B是第一直接依赖，B对于C是第二直接依赖，A对于C是传递性依赖。这时Maven的传递性依赖机制会解析各个直接依赖的POM，将那些必要的间接依赖，以传递性依赖的形式引入到当前的项目中。



_传递性依赖和依赖范围_

{% asset_img 依赖范围影响传递性依赖.png 依赖范围影响传递性依赖 %}

仔细琢磨该表是有一定规律可循的，不必死记硬背。

