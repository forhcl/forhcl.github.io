---
title: 数据库连接池中间件--HikariCP
hidden: true
date: 2020-01-17 13:58:29
tags:
categories:
---

&emsp;&emsp;在应用系统开发过程中，池化技术，如对象池、连接池、线程池等被广泛用来提升性能，原理是通过复用对象而减少创建、释放连接的消耗由于<span style="color:red">TCP连接创建开支是昂贵的，数据库所能承受的TCP并发连接数也是有限制的</span>，针对这种场景，数据库连接池应运而生。

&emsp;&emsp;数据库连接池的原理是：在系统初始化的时候，在内存中开辟一片空间，将一定数量的数据库连接作为对象存储在对象池里，并对外提供数据库连接的获取和归还方法。用户访问数据库时，并不是建立一个新的连接，而是从数据库连接池中取出一个已有的空闲连接对象；使用完毕归还后连接也不会马上被关闭，而是由数据库连接池统一管理回收，为下一次借用做好准备。如果由于高并发请求导致数据库连接池中的连接被借用完毕，其他线程就会等待，直到有连接被归还。整个过程中，连接不会被关闭，而是源源不断地循环使用，有借有还。数据库连接池还可以通过设置其参数来控制连接池中的初始连接数、连接的上下限数，以及每个连接的最大使用次数、最大空闲次数等，也可以通过其自身的管理机制来监视数据库连接的数量、使用情况等。

&emsp;&emsp;重用数据库连接最主要的原因是可以减少应用程序与数据库之间创建和销毁TCP连接的开销。如果不使用连接池，TCP四次挥手过程中TIME_WAIT的性能调优是相对比较复杂的。

> TCP中的TIME_WAIT
>
> &emsp;&emsp;TCP四次握手结束后，连接双方都不再交换信息，但主动关闭的一方保持这个连接在一段时间内不可用，TIME_WAIT的存在是为了保证网络中迷失的数据包正常过期。

&emsp;&emsp;在高并发场景下，由于频繁地关闭连接会出现TCP大量TIME_WAIT状态，这会消耗很多系统资源；端口的数量只有65535，占用一个就会少一个，进而严重影响新连接。

# HikariCP

HikariCP的坐标如下，省略版本号。当然在SpringBoot2.x中，存在一些启动器也包含HikariCP，如spring-boot-starter-data-jpa，spring-boot-starter-jdbc

```xml
        <dependency>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
        </dependency>
```

&emsp;&emsp;在Spring 1.5.x版本中，数据库连接池的默认配置是Tomcat Pool->HikariCP->Commons DBCP->Commons DBCP2；然而在2.x版本中，HikariCP被提升为默认的数据库连接池，数据库连接的默认配置顺序变成HikariCP->Tomcat Pool-Commons DBCP2。当然，用户也可以通过设置`spring.datasource.type`属性来绕过上述使用的顺序，进而指定想要使用的数据库连接池。接下来我们就从源码角度为读者详细介绍数据库连接池默认配置顺序的原理。

&emsp;&emsp;`org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration`是SpringBoot中加载数据源的核心类。其中调用了一个findType的方法来加载资源文件。

```java
private static final String[] DATA_SOURCE_TYPE_NAMES = new String[] { "com.zaxxer.hikari.HikariDataSource",
			"org.apache.tomcat.jdbc.pool.DataSource", "org.apache.commons.dbcp2.BasicDataSource" };

public static Class<? extends DataSource> findType(ClassLoader classLoader) {
		for (String name : DATA_SOURCE_TYPE_NAMES) {
			try {
				return (Class<? extends DataSource>) ClassUtils.forName(name, classLoader);
			}
			catch (Exception ex) {
				// Swallow and continue
			}
		}
		return null;
	}
```

在findType()方法中，for循环顺序遍历DATA_SOURCE_TYPE_NAMES常量数组，加载到哪个类就使用哪个类的连接池，所以就是HikariCP->Tomcat Pool-Commons DBCP2这样的顺序。但是在1.5.x版本中，由于常量数组DATA_SOURCE_TYPE_NAMES的定义不同，因此是Tomcat Pool->HikariCP->Commons DBCP->Commons DBCP2的顺序。因此要在SpringBoot 1.5.x版本中使用HikariCP，除了在`spring.datasource.type`属性中设置以外，还有一个简单的方法就是Maven排除DATA_SOURCE_TYPE_NAMES常量数组中排在HikariCP前面的数据库连接池实现。