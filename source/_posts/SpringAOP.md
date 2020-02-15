---
title: SpringAOP
date: 2019-09-13 22:34:53
tags:
categories:
---

&emsp;&emsp;日常开发中都是直接使用AOP框架进行开发的，对于基于JDK动态代理的AOP传统开发一知半解，所以花一些时间来整理一下。

+ AOP的概述

  什么是AOP？

  - AOP Aspect Oriented Programing 面向切面编程
  - AOP采取__横向抽取机制__，取代了__传统纵向继承体系__来（减少重复性代码（性能监视、事务管理、安全检查、缓存））
  - Spring AOP使用纯JAVA实现，不需要专门的编译过程和类加载器，在运行期通过__代理方式__向目标类织入增强代码。

  AOP相关术语

  - Jointpoint（连接点）:所谓连接点是指那些可以被拦截到的点。在Spring中，这些点指的是方法，因为spring只支持方法类型的连接点
  - Pointcut（切入点）:所谓切入点是指我们要对哪些JoinPoint进行拦截的定义。

+ AOP的底层实现

+ Spring的传统AOP

  - 不带切入点的切面
  - 带有切入点的切面

+ Spring的传统AOP的自动代理

  - 基于Bean名称的自动代理
  - 基于切面信息的自动代理


