---
title: JSON数据格式
date: 2019-09-29 13:56:17
tags:
categories:
---

### JSON

<span style="color:red">以数据为中心的表述性状态转移(REST)对JSON的定义是——用于**表述REST资源**的一种形式。表述是REST中很重要的一个方面，它是关于客户端和服务端针对某一资源是如何通信的。</span>`JSON`是当前行业内使用最为广泛的数据传输格式。最常见的就是服务端提供给客户端的`API`，即控制器方法一般就是采用JSON作为数据传输的格式。NoSQL数据库采用JSON作为数据储存格式。JSON还为我们提供了一种对象序列化的方式。

#### JSON的基础知识

###### 什么是JSON？ 

&emsp;&emsp;JSON是一种与开发语言无关的、轻量级的<span style="color:red">数据格式</span>。全称`JavaScript Object Notation`.

+ 最早源于JavaScript这门语言，后面使用得原来越广泛。几乎每种语言都有处理JSON的API

优点：易于人的阅读和编写，易于程序解析和生产

一个简单的JSON的样例

```json
{
    "name":"JSON快速入门（Java版）",
    "author":"李广",
    "content":["JSON基础入门","常用JSON处理"],
    "time":{
        "value":30,
        "unit":"分钟"
    }
}
```

###### 标准的JSON数据表示

数据结构

Object:使用花括号{}包含的<span style="color:blue">键值对</span>结构，Key必须是string类型，value为任何的<span style="color:red">基本类型</span>或<span style="color:red">数据结构</span>。

![JSON对象表示](http://cdn1.hikariblog.cn/JSON%E5%AF%B9%E8%B1%A1%E8%A1%A8%E7%A4%BA.png)

Array:使用中括号[]来起始，并用逗号，来分割元素

![JSON数组表示](http://cdn1.hikariblog.cn/JSON%E6%95%B0%E7%BB%84%E8%A1%A8%E7%A4%BA.png)

基本类型

​	string,number,true,false,null

#### Java中两种常见的JSON处理方式

Json中没有日期这样的数据格式，所以要采用字符串或数字的时间戳来表示

Json本身不支持任何形式的注释！！

能自动将Java中的对象转换为Json吗？

###### Json In Java

Java中有许多Json的Jar包，如

```xml
<!-- https://mvnrepository.com/artifact/org.json/json -->
<dependency>
    <groupId>org.json</groupId>
    <artifactId>json</artifactId>
    <version>20190722</version>
</dependency>

```

如何使用Json生成我们想要的JSON数据，有两种方法

1. 使用JsonObject生成Json格式的数据

#### 综合运用

idea快捷键

Ctrl+Shift+O : 将没有用到的包去掉

# `SpringBoot`响应客户端渲染数据

+ `SpringBoot`构造并返回一个json对象，注意在实际的开发中肯定不会单单返回JSON数据，会对JSON格式的数据进行进一步的封装。具体见Spring In Action书籍P440.

  `@Responsebody`:将对象转换为资源表述(如JSON或XML)。

  `@RequestBody`:将资源表述转换为对象。

  使用`@RESTController`代替`@Controller`，Spring将会为该控制器的所有处理方法应用消息转换功能。我们不必再为每个方法都添加 `@Responsebody` 注解。

  ![JSON数据封装](http://cdn1.hikariblog.cn/JSON%E6%95%B0%E6%8D%AE%E5%B0%81%E8%A3%85.png)

  

+ Jackson基本演绎法

  `@JacksonIgnore`注解某一个字段就可以在返回给前端的该对象从JSON数据移除这个属性