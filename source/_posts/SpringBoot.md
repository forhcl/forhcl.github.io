---
title: SpringBoot
date: 2019-08-05 16:32:46
tags:
categories:
---

1. 介绍Spring Boot

   + 为所有Spring开发提供一个更快更广泛的入门体验
   + 开箱即用，不合适时也可以快速抛弃
   + 提供一系列大型项目常用的非功能性特征
   + 零配置（不需要XML配置，遵循“约定大于配置”）

   抛弃了传统JavaEE 项目繁琐的配置、学习过程，让企业级应用开发过程变得so easy！

   ![Spring应用开发流程](http://cdn1.hikariblog.cn/Spring%E5%BA%94%E7%94%A8%E7%9A%84%E5%BC%80%E5%8F%91%E6%B5%81%E7%A8%8B.png)

   让Spring应用开发变得简单粗暴，将之前的框架进行完美整合。

   ![Spring Boot应用开发流程](http://cdn1.hikariblog.cn/Spring%20Boot%E5%BA%94%E7%94%A8%E5%BC%80%E5%8F%91%E6%B5%81%E7%A8%8B.png)

   Spring Boot核心特性

   + 极低的学习成本
   + 可独立运行的Spring项目，最后web项目打成jar包，SpringBoot在构建过程将tomcat内嵌了
   + “习惯优于配置”，极大的提高了开发效率。
   + 极简的组件依赖，自动发现与自动装配
   + 提供运行时的应用监控
   + 与分布式架构和云计算的天然集成

2. 掌握Spring Boot应用的构建方法

3. 讲解SpringBoot执行流程和配置选项

+ Spring Boot约定的目录结构（项目初期构建好）

  | /src/main                         | 项目根目录              |
  | --------------------------------- | ----------------------- |
  | /java                             | Java源代码目录          |
  | /resources                        | 资源目录                |
  | /resources/static                 | 静态资源目录            |
  | /resources/templates              | 表示层页面目录          |
  | /resources/application.properties | Spring Boot核心配置文件 |
  | /test                             | 测试文件目录            |

+ Spring boot的基础引用

  ```xml
      <parent>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-parent</artifactId>
          <version>2.1.4.RELEASE</version>
      </parent>
  ```

  &emsp;&emsp;`spring-boot-starter-parent` 本身不提供依赖关系，而是提供一些配置的默认值。

+ 真正的依赖

  ```xml
      <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
      </dependencies>
  ```

+ 将所有资源打包为一个可独立运行的jar包

  ```xml
      <build>
          <plugins>
              <plugin>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-maven-plugin</artifactId>
              </plugin>
          </plugins>
      </build>
  ```

  SpringBoot和Spring MVC的开发方式基本是一样的。如何启动SpringBoot呢？我们还需要编写一个入口类，一般情况下，入口类名字（项目名+Application）

  ```java
  @SpringBootApplication
  //这个注解说明这是一个SpringBoot应用的入口类，当然也可以使用@EnableAutoConfiguration 这个注解告诉Spring Boot根据你添加的jar依赖关系“猜测”你想要如何配置Spring
  public class MySpringBootApplication {
      public static void main(String[] args) {
          //启动SpringBoot应用,将入口类作为参数传递进去
    SpringApplication.run(MySpringBootApplication.class);
      }
  }
  ```

  右键入口类，run

  浏览器输入<http://localhost:8080/out>检验

### Spring Initialzr构建Spring Boot应用（超级赞）

从一开始就选择Initialzr，然后该填的填一路next。

1. 自动生成SpringBoot目录结构
2. 自动生成根包+入口类

### _Spring Boot入口类_

+ 入口类命名通常以*Application结尾
+ 入口类上增加@SpringBootApplication注解
+ 利用SpringApplication.run()方法启动应用

### _Spring Boot启动流程_

![SpringBoot启动流程](http://cdn1.hikariblog.cn/SpringBoot%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B.png)

### _Spring Boot内置配置选项_

在Application.properties中配置

日志级别：debug->info->warn->error->fatal

Spring boot启动后的默认级别为info

### _Spring Boot支持两种配置文件_

+ 属性文件：application.properties
+ YAML格式：application.yml

YAML的语法

​	YAML是一种简洁的非标记语言。YAML以数据为中心，使用空白，缩进，分行组织数据，从而使得表示更加简洁易读。相比properties更具可读性

​	YAML语法格式

+ 标准格式：key:(空格)value
+ 使用空格代表层级关系，以":"结束

### _Spring Boot自定义配置项_

&emsp;&emsp;Spring Boot允许我们自定义应用配置项，在程序运行时允许动态加载，这为程序提供了良好的可维护性。

外化文本是从模板文件中提取模板代码的片段，以便它们可以保存在单独的文件（通常是`.properties`文件）中，并且可以使用其他语言编写的等效文本（称为国际化或简称为*i18n*）轻松替换它们。外化的文本片段通常称为*“消息”*。

消息总是有一个标识它们的键，而Thymeleaf允许您指定文本应该与具有以下`#{...}`语法的特定消息相对应：

```yaml
mall:
 config:
  name: 博客
  description: 这是我的个人博客
  hot-sales: 20
  show-advert: true
```

使用自定义配置项

```java
@Controller
public class MyController {
    @Value("${mall.config.name}")
    private String name;
    @Value("${mall.config.description}")
    private String description;
    @Value("${mall.config.hot-sales}")
    private Integer hotSales;
    @Value("${mall.config.show-advert}")
    private Boolean showAdvert;
    @RequestMapping("/out")
    @ResponseBody
    public String out(){
        return String.format("name:%s,description:%s,hot-sales:%s,show-advert:%s",name,description,hotSales,showAdvert);
    }
}
```

### _环境配置文件_

+ Spring Boot可针对不同的环境提供不同的Profile文件

+ Profile文件的默认命名格式为application-{env}.yml

+ 使用spring.profile.active选项来指定不同的profile

  对于多环境开发和协同开发极为重要

### _打包与运行_

+ 利用Maven的package命令，生成可独立运行的jar包。（Maven实战中要说到）

  mvn package

+ 利用java -jar xxx.jar 命令启动Spring Boot应用

+ Jar包可自动加载同目录的application配置文件

  打包后配置文件也在里面了，我们要指定profile不可能把它解压再修改吧，所以SpringBoot提供了自动解析Jar包同目录配置文件的功能。



三种运行方式

+ 使用`java -jar`命令运行可执行jar包，同时可以使用`jar tvf`命令查看可执行jar包内的目录结构。
+ 以“java application”运行
+ 构建工具，如maven的启动命令`mvn spring-boot:run`

------

@RestController：@Responsebody和@Controller注解的组合

------

### _Spring Boot官方文档阅读笔记_

###### _通过以下命令检查电脑上的 `Java` 安装情况_

```
java -version
```

### _1 Getting Started_

&emsp;&emsp;The `spring-boot-starter-parent` is a special starter that peovides useful Maven defaults. The `mvn dependency:tree` command prints a tree representation of your project dependencies. You can see that `spring-boot-starter-parent` provides no dependencies by itself. To add the necessary dependencies, edit your `pom.xml` and add the `spring-boot-starter-web` dependency immediately below the `parent`section:

```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>
```

`spring-boot-starter-web` including the embed tomcat web server. 







































































内嵌Servlet容器



maven项目依赖的生命周期怎么划分的