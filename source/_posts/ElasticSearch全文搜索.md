---
title: ElasticSearch全文搜索
date: 2019-08-09 16:31:21
tags:
categories:
---

## _<span style="color:SeaGreen">全文搜索</span>_

##### _数据结构_

+ 结构化：指具有固定格式或有限长度的数据，如数据库，元数据等。
+ 非结构化：指不定长或无固定格式的数据，如邮件，word文档等。

###### _非结构化数据的检索_

+ 顺序扫描法（Serial Scanning）

+ 全文搜索（Full-text Search）：全文搜索是一种将文件中所有文本与搜索项匹配的文字资料检索的方法。

  - 全文搜索实现原理

  ![](ElasticSearch全文搜索/全文搜索实现原理.png)

  - 全文搜索实现技术（基于Java的开源实现）
    + Lucene
    + ElasticSearch
    + Solr

  将非结构化数据转换成结构化数据，建立索引后进行搜索

### _ElasticSearch简介_

##### _ElasticSearch是什么？_

+ 高度可扩展的开源全文搜索和分析引擎
+ 快速地、近实时地对大数据进行存储、搜索和分析
+ 用来支撑有复杂的数据搜索需求的企业级应用

##### _ElasticSearch特点_

+ 分布式
+ 高可用
+ 多类型
+ 多API  支持HTTPRESTful
+ 面向文档
+ 异步写入
+ 近实时
+ 基于Lucene
+ Apache协议

##### _ElasticSearch核心概念_

+ 近实时
+ 集群
+ 节点
+ 索引
+ 类型
+ 文档
+ 分片
+ 副本

### _Elasticsearch与Spring Boot集成_

配置环境

+ Elasticsearch

+ Spring Data Elasticsearch（Spring对Elasticsearch的支持）

  ```xml
  <!-- 添加Spring Data Elasticsearch的依赖 -->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
  </dependency>
  ```

+ JNA 4.3.0（用来访问操作系统原生的一些应用）

  ```xml
  <!-- 添加JNA的依赖 -->
  <dependency>
      <groupId>net.java.dev.jna</groupId>
      <artifactId>jna</artifactId>
      <version>4.1.0</version>
  </dependency>
  ```

