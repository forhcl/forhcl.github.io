---
title: SpringDataJPA
date: 2019-08-09 09:17:20
tags:
categories: Spring Data JPA
---

### _数据持久化_

+ JPA简介
  - JPA（Java Persistence API）是用于管理Java EE和Java SE环境中的持久化，以及对象/关系映射的Java API
  - 最新规范为“JSR 338”
  - 实现：EclipseLink、Hibernate、Apache OpenJPA
+ Spring Data JPA用法介绍
+ Spring Data JPA、Hibernate与Spring Boot集成
+ 数据持久化实战

##### _JPA核心概念_

__实体__

+ 实体表示关系数据库中的表
+ 每个实体实例对应与该表中的行
+ 类必须用javax.persistence.Entity注解
+ 类必须有一个public或protected的无参数的构造函数
+ 实体实例被当作值以分离对象方式进行传递（例如通过会话bean的远程业务接口），则该类必须实现Serializable接口
+ 唯一的对象标识符：简单主键（javax.persistence.id)、复合主键（javax.persistence.Embeddedld和javax.persistence.IdClass)

