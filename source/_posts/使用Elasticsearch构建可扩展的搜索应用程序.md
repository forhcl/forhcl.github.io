---
title: 使用Elasticsearch构建可扩展的搜索应用程序
hidden: true
date: 2019-12-29 18:33:10
tags:
- 全文搜索引擎
categories: Elasticsearch
---

<!-- 底层：Lucene(高性能搜索引擎包)，Lucene采用“倒排索引”对搜索过程进行优化。 -->

<!-- 为了提高搜索的性能和相关性，随着时间推移需要越来越多的磁盘空间来存储索引，从而导致建立索引和搜索过程变慢。因此，对Elasticsearch进行性能调优迫在眉睫。 -->

“搜索”功能的要求：搜索结果相关性高、要有统计数据（对结果进行分类、排序等等）、性能、用户友好（用户在使用搜索框时应该有关键字提示）。

Elasticsearch里的数据是通过文档形式表示的，它和Mongodb这种面向文档的存储搭配起来也很美妙。

Elasticsearch这种现代搜索引擎还能完好地存储数据，甚至可以将其直接作为带搜索功能的NoSQL数据存储来使用。

+ 基于关键字进行搜索
+ 模糊查询，同义词、派生词等等
+ 支持变体



Elasticsearch不支持事务

Elasticsearch是如何计算文档相关性的？具体采用了什么样的算法？





## 环境搭建

&emsp;&emsp;首先，由于Elasticsearch是Java编程语言编写的，同其他Java应用程序一样需要运行在JRE上。因此需要先安装Java运行环境，若不会绕道自行Google。