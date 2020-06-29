---
title: Spring Security身份认证和授权
date: 2019-11-07 14:43:05
tags:
- 身份认证和授权
categories: "Spring Security"
---

## `Spring Security`开发安全的`REST`服务

{% note primary %} 认证和授权-->最终表现为<span style="color:red">登录</span>功能。 {% endnote %}

&emsp;&emsp;最初对于登录的理解就是前台的一个登录表单，提交到后台查看数据库是否存在该用户。如果存在，则登录成功；不存在，则登录失败。

![简陋版的登录功能](http://cdn1.hikariblog.cn/%E7%AE%80%E9%99%8B%E7%89%88%E7%9A%84%E7%99%BB%E5%BD%95%E5%8A%9F%E8%83%BD.png)

但是在真正的生产环境中远没有这么简单，会有以下几个方面需要考虑

+ 同时支持多种认证登录方式：表单登录、手机短信登录、微信登录、QQ登录
+ 同时支持多种前端渠道：浏览器、手机APP（两种原理的不同的）
+ 支持集群登录(一台机器上登录了，其他机器怎么办)，跨应用工作（单点登录SSO），SESSION控制，控制用户的权限，防护与身份认证相关的攻击 

对于企业级的认证和授权，也就是登录功能的实现，主要有以下几个技术框架：

- Spring Security：授权机制的实现

- Spring Social：第三方认证

- Spring Security OAuth：手机APP认证，创建、管理和分发token。（Spring Security OAuth最终的效果就是能同时支持浏览器登录和手机App登录）

浏览器和服务器之间采用Session保存用户信息，App和服务器之间采用token保存用户信息（没有Session）。

![案例代码结构](http://cdn1.hikariblog.cn/%E6%A1%88%E4%BE%8B%E9%A1%B9%E7%9B%AE%E4%BB%A3%E7%A0%81%E7%BB%93%E6%9E%84.png)

![企业级的认证和授权](http://cdn1.hikariblog.cn/%E4%BC%81%E4%B8%9A%E7%BA%A7%E7%9A%84%E8%AE%A4%E8%AF%81%E5%92%8C%E6%8E%88%E6%9D%83.png)



#### 使用Spring Security开发基于表单的认证

认证（你是谁）

授权（你能做什么）

攻击防护（防止伪造身份）

内容简介

+ SpringSecurity基本原理

  关闭引入SpringSecurity之后默认拦截所有URL的HTTP基本认证

  ```properties
  security.basic.enabled=false
  # HTTP基本认证后才能访问对应的URL资源，其默认使用的用户名为user，密码则是动态生成并打印到控制台的一串随机码
  # 自定义HTTP基本认证的用户名和密码
  #  security:
  #    user:
  #      name: admin
  #      password: 123
  ```

  Spring Security其实就是一组过滤器链

+ 实现用户名+密码登录

+ 实现手机号+短信登录



#### 自定义用户认证逻辑

+ 处理用户信息获取逻辑

  SpringSecurity将用户信息获取逻辑封装在UserDetailService中

+ 处理用户校验逻辑

+ 处理密码加密解密















课程目标

1. 深入理解Spring Security及其相关框架的原理、功能和代码
2. 可以基于Spring Security及相关框架独立开发认证授权相关功能
3. 掌握抽象和封装的常用技巧，可以编写可重用的模块供他人使用



