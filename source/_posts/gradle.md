---
title: gradle
date: 2019-08-06 16:49:27
tags:
categories:
---

## 新一代构建工具Gradle

### _1 Gradle是什么？_

&emsp;&emsp;一个开源的<span style="color:red">项目自动化构建工具</span>，建立在 `Apache Ant` 和 `Apache Maven` 概念的基础上，并引入了基于 `Groovy` 的特定领域语言（DSL），而不在使用XML形式管理构建脚本，能具有更高的灵活性和可拓展性。

##### _1.1 Gradle安装_

+ 确保电脑上已经安装JDK，可以通过 `java -version` 命令进行确认。
+ 从Gradle官网下载Gradle，<http://gradle.org>
+ 配置环境变量，<span style="color:red">GRADLE_HOME</span>
+ 添加到Path，<span style="color:red">%GRADLE_HOME%\bin;</span>
+ 验证是否安装成功，<span style="color:red">gradle -v</span>

### _2 Groovy是什么？_

&emsp;&emsp;`Groovy` 是用于 `Java` 虚拟机的一种敏捷的动态语言，它是一种成熟的面向对象编程语言，既可以用于面向对象编程，又可以用作纯粹的脚本语言。使用该种语言不必编写过多的代码，同时又具有闭包和动态语言中的其他特性。

与 `Java` 比较

+ `Groovy` 完全兼容 `Java` 的语法

+ 分号是可选的

+ 类、方法默认是 `public` 的，没有 `default` 这种访问权限

+ 编译器给属性自动添加getter/setter方法

+ 属性可以直接用点号获取

+ 最后一个表达式的值会被作为返回值

+ ==等同于equals()，不会抛空指针异常

+ assert语句

+ 弱类型 如def 变量。会自动推断

+ 可选的括号

+ 字符串

  单引号：仅仅是字符串

  双引号：可以通过${}插入变量

  三引号：可以换行

+ 集合API

+ 闭包

