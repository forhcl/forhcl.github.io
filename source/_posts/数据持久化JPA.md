---
title: 数据持久化JPA
hidden: true
date: 2020-01-01 18:45:57
tags:
categories:
---

# 是什么？

+ JPA(Java Persistence API)是用于管理Java EE和Java SE环境中的持久化，以及对象/关系映射的Java API。简而言之，JPA就是用于处理数据持久化的规范。

+ 最新的规范为“JSR 338：Java Persistence 2.1”

  <https://jcp.org/en/jsr/detail?id=338>

+ 实现：EclipseLink、Hibernate、Apache OpenJPA

## JPA核心概念

**实体**

- 表示关系数据库中的表

- 每个实体实例对应于该表中的行

- 类必须用javax.persistence.Entity注解

- 类必须有一个public或protected的无参数的构造函数

- 实体实例被当作值以分离对象的方式进行传递（例如通过会话bean的远程业务接口），则该类必须实现Serializable接口

- 唯一的对象标识符：简单主键(javax.persistence.Id)、复合主键(javax.persistence.IdClass和javax.persistence.EmbeddedId)

  ```java
  package cn.crud.pojo;
  
  import lombok.AllArgsConstructor;
  import lombok.Data;
  import lombok.NoArgsConstructor;
  
  import javax.persistence.Entity;
  import javax.persistence.GeneratedValue;
  import javax.persistence.GenerationType;
  import javax.persistence.Id;
  
  @Data
  @NoArgsConstructor
  @AllArgsConstructor
  @Entity(name = "tbl_userinfo")
  public class UserInfo {
      @Id
      @GeneratedValue(strategy = GenerationType.IDENTITY)
      private Integer id;
      private String username;
      private String email;
      private String sex;
      private String province;
      private String hobby;
  }
  
  ```

**关系**

- 一对一：@OneToOne
- 一对多：@OneToMony
- 多对一：@ManyToOne
- 多对多：@ManyToMany

**EntityManager**

- 定义用于与持久性上下文进行交互的方法

- 创建和删除持久实体实例，通过实体的主键查找实体

- 允许在实体上运行查询

  ```java
      @PersistenceUnit
      EntityManagerFactory emf;
      EntityManager em;
      @Resource
      UserTransaction utx;
      ...
          em=emf.createEntityManager();
          try{
          utx.begin();
          em.persist(SomeEntity);
          em.merge(AnotherEntity);
          em.remove(ThridEntity);
          utx.commit();}catch (Exception e){
              utx.rollback();
      }
  ```

  



