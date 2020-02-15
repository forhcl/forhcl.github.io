---
title: mybatis入门笔记
date: 2019-07-25 09:07:02
tags:
- mybatis
- Java
- 框架
- 数据库
categories: 框架
---

#### 通过自动回复机器人学mybatis基础篇

__案例分析__

基本功能：

+ 接收发送指令
+ 根据指令回复对应的内容

模块划分

1. 回复内容维护
2. 对话功能
3. 回复内容列表
4. 回复内容删除

__version：1.0-SNAPSHOT__

相关技术：Jsp+Jdbc+SpringMVC

&emsp;&emsp;页面放在WEB-INF下面，便于控制和管理，如果放在根目录下是可以被用户在浏览器直接访问到的，不安全。放在WEB-INF下必须通过后台进行访问。

&emsp;&emsp;不要写select *,就算可以也不要，机器还得去转换一遍。不要嫌麻烦，自己把需要的列名列出来，否则显得不专业，被老板骂。

&emsp;&emsp;通过PreparedStatement可以防止sql注入。

&emsp;&emsp;where 1==1;然后在sql语句上再加条件

&emsp;&emsp;实际开发中尽量使用复制，别因为打错了，调试半天。

###### mybatis的概述

+ ORM(Object Relational Mapping)持久层框架的佼佼者
+ 正真实现了SQL语句与Java代码的分离
+ 优秀的功能：动态SQL、缓存、插件-pageHelper(主要进行物理分页的优秀开源项目)等

实际场景中mybatis的应用

- SQL语句中使用IN的情况，可以使用数组封装IN中的值
- 批量操作数据的情况，可以把操作的数据封装在数组中

###### mybatis的环境搭建

1. mybatis的包和源码,链接:<https://github.com/mybatis/mybatis-3/releases>
2. 源码中演示版核心配置文件的详细路径：src\test\java\org\apache\ibatis\submitted\complex_property\Configuration.xml；将它复制到项目的资源文件目录中，按照需求修改里面的配置。
3. MyBatis官方文档:<http://www.mybatis.org/mybatis-3/zh/getting-started.html>

__在使用Mybatis框架时，主要设计两个核心对象：`SqlSessionFactory`和`SqlSession`__

1. _SqlSessionFactory_

   &emsp;&emsp;`SqlSessionFactory` 是单个数据库映射关系经过编译后的内存镜像，其主要的作用是创建 `SqlSession`。下面将通过XML配置文件进行 `SqlSessionFactory` 实例的构建。

   ```java
   //读取配置文件
   InputStream inputStream=Resources.getResourceAsStream("配置文件位置");
   //根据配置文件构建SqlSessionFactory
   SqlSessionFactory sqlSessionFactory=new SqlSessionFactoryBuilder().build(inputStream);
   ```

   &emsp;&emsp;`SqlSessionFactory` 对象是线程安全的，它一旦被创建，在整个应用执行期间都会存在。如果我们多次地创建同一个数据库的 `SessionFactory`，那么此数据库的资源将很容易被耗尽。为了解决此问题，通常每一个数据库都会只对应一个`SqlSessionFactory`，所以在构建 `SqlSessionFactory` 实例时，建议使用单例模式。

2. _SqlSession_

   &emsp;&emsp;`SqlSession` 是应用程序与持久层之间执行交互操作的一个<span style="color:red">单线程对象</span>，其主要作用就是执行持久化操作以及实现数据库事务的控制。

   ```java
   //通过sqlSessionFactory打开一个数据库会话
   SqlSession sqlSession=sqlSessionFactory.openSession();
   try{
       //此处执行持久化操作
   }finally{
       sqlSession.close();
   }
   ```

   &emsp;&emsp;每一个线程都应该有自己的 `SqlSession` 实例，并且该实例是不能被共享的。同时，`SqlSession` 实例也是线程不安全的，因此其使用范围最好在一次请求或一个方法中，绝不能将其放在一个类的静态字段、实例字段或任何类型的管理范围（如 `Servlet` 的 `HttpSession`）中使用。使用完 `SqlSession` 对象之后，要及时地关闭它，通常可以在 `finally` 块中关闭。

   &emsp;&emsp;每个方法执行时都需要读取配置文件，并根据配置文件的信息构建 `SqlSessionFactory` 对象，然后创建 `SqlSession` 对象，这导致了大量的重复代码。为了简化开发，我们可以把重复代码封装到一个工具类中，然后通过工具类来创建SqlSession。

   ```java
   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   
   import java.io.Reader;
   
   /**
    * @author Hogan_Lee
    * @create 2019-09-06 10:22
    * 工具类：创建SqlSession
    */
   public class MybatisUtils {
       private static SqlSessionFactory sqlSessionFactory = null;
   
       //初始化SqlSessionFactory对象
       static {
           try {
               //使用Mybatis提供的Resources类加载Mybatis的配置文件
               Reader reader = Resources.getResourceAsReader("mybatis-config.xml");
               //构建SqlSessionFactory工厂
           } catch (Exception e) {
               e.printStackTrace();
           }
       }
   
       //获取SqlSession对象的静态方法
       public static SqlSession getSession() {
           return sqlSessionFactory.openSession();
       }
   }
   ```

###### Sql的基本配置与执行

1. 源码中演示版Sql配置文件的详细路径：src\test\java\org\apache\ibatis\submitted\complex_property\User.xml；将它复制到项目的资源文件目录中，按照需求修改里面的配置。
2. Sql配置文件作用，配置Sql语句让SqlSession能读到并执行。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="Message">

    <resultMap type="entity.Message" id="Message">
        <!-- 主键 -->
        <id column="ID" jdbcType="INTEGER" property="id"/>
        <!-- 普通的列 -->
        <!-- jdbcType为java.sql.Types类中的常量名有一种对应关系 -->
        <result column="COMMAND" jdbcType="VARCHAR" property="command"/>
        <result column="DESCRIPTION" jdbcType="VARCHAR" property="description"/>
        <result column="CONTENT" jdbcType="VARCHAR" property="content"/>
    </resultMap>

    <select id="allMessage" resultMap="Message">
        select ID, COMMAND, DESCRIPTION, CONTENT
        from message
    </select>
  

</mapper>
```

然后通过核心配置文件中的`<Mapper>`元素引入Sql配置文件。

###### 动态Sql拼接

动态SQL元素和JSTL或基于类似XML的文本处理器相似。在MyBatis之前的版本中，有很多元素需要花时间了解。MyBatis3大大精简了元素种类，现在只需学习原来一半的元素即可。Mybatis采用功能强大的基于OGNL的表达式来淘汰其它大部分元素。

注意：特殊字符要进行转义

<table>
    <tr>
        <th colspan="4" style="text-align:center">MyBatis中的OGNL表达式</th>
    </tr>
    <tr>
        <td style="text-align:center">取值范围</td>
        <td colspan="3" style="text-align:center">标签的属性中</td>
    </tr>
    <tr>
        <td rowspan="5" style="text-align:center">取值写法</td>
        <td style="text-align:center">String与基本数据类型</td>
        <td style="text-align:center" colspan="2">_parameter</td>
    </tr>
    <tr>
        <td style="text-align:center">自定义类型(Message)</td>
        <td style="text-align:center" colspan="2">属性名(command)</td>
    </tr>
    <tr>
        <td rowspan="3" style="text-align:center">集合</td>
        <td style="text-align:center" colspan="2">数组：array</td>
    </tr>
    <tr>
        <td style="text-align:center" colspan="2">List:list</td>
    </tr>
    <tr>
        <td style="text-align:center" colspan="2">Map:_parameter</td>
    </tr>
    <tr>
        <td rowspan="2" style="text-align:center">操作符</td>
        <td style="text-align:center">java常用操作符</td>
        <td colspan="2" style="text-align:center">+、-、*、/、==、!=、||、&amp;&amp;等</td>
    </tr>
    <tr>
        <td style="text-align:center">自己特有的操作符</td>
        <td colspan="2" style="text-align:center">and、or、mod、in、not in</td>
    </tr>
    <tr>
        <td rowspan="6" style="text-align:center">从集合中取出一条元素</td>
        <td rowspan="2" style="text-align:center">数组</td>
        <td colspan="2" style="text-align:center">array[索引](String[])</td>
    </tr>
    <tr>
        <td colspan="2" style="text-align:center">array[索引].属性名(Message[])</td>
    </tr>
    <tr>
        <td rowspan="2" style="text-align:center">List</td>
        <td colspan="2" style="text-align:center">list[索引](List&lt;String&gt;)</td>
    </tr>
    <tr>
        <td colspan="2" style="text-align:center">list[索引]属性名(List&lt;Message&gt;)</td>
    </tr>
    <tr>
        <td rowspan="2" style="text-align:center">Map</td>
        <td colspan="2" style="text-align:center">_parameter.key(Map&lt;String,String&gt;)</td>
    </tr>
    <tr>
        <td colspan="2" style="text-align:center">key.属性名(Map&lt;String,Message&gt;)</td>
    </tr>
    <tr>
        <td rowspan="4" style="text-align:center">利用foreach标签从集合中取出数据</td>
        <td colspan="3" style="text-align:center">&lt;foreach collection="array" index="i" item="item"&gt;</td>
    </tr>
    <tr>
        <td style="text-align:center">数组</td>
       <td rowspan="2" style="text-align:center">i：索引(下标)</td>
        <td rowspan="3" style="text-align:center">item<br/><br/>item.属性名</td>
    </tr>
    <tr>
        <td style="text-align:center">List</td>
    </tr>
    <tr>
        <td style="text-align:center">Map</td>
        <td style="text-align:center">i：key</td>
    </tr>
</table>

配置示例：

```xml
<select id="allMessage" resultMap="Message">
        select ID, COMMAND, DESCRIPTION, CONTENT
        from message
    </select>
    <select id="selectWithCondition" parameterType="entity.Message" resultMap="Message">
        select ID,COMMAND,DESCRIPTION,CONTENT from message where 1=1
        <if test="command!=null and !&quot;&quot;.equals(command.trim())">and COMMAND=#{command}</if>
        <if test="description!=null and !&quot;&quot;.equals(description.trim())">and DESCRIPTION like '%'
            #{description} '%'
        </if>
    </select>
```

###### 应用log4j调试动态Sql

```properties
log4j.properties
log4j.rootLogger=DEBUG , stdout
log4j.logger.org.mybatis=DEBUG
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p %d %C: %m%n
```

`properties`文件主要采用"key=value"的书写方式，用到的时候通过key找到对应的value。

log4j.rootLogger:使用log4j输出日志时的级别和输出的位置。

级别：debug<info<warn<error,只有级别大于等于配置的才会被输出

###### 删除实现

增删改和查询不一样，是有事务控制的，需要手动提交；

sqlSession.commit();

mybatis在封装jdbc的增删改操作时将jdbc默认的自动提交事务设置为false

`conn.setAutoCommit(false)；`所以我们要么将它重新设置为true（不建议），要么每一次手动提交。





任务：删除要有提示信息

删除之后检索条件没有保留





##### _以数组为传递参数进行数据的查询_

实际应用：

1. SQL语句中使用IN的情况，可以使用数组封装IN中的值
2. 批量操作数据的情况，可以把操作的数据封装在数组中

重要知识点

1. Mybatis的入参的处理
2. Mybatis的动态SQL(foreach标签)

__Mybatis入参处理__

参数处理

+ 传递单个参数的形式（mybatis会自动进行参数的赋值）

  不会关注参数的名字

+ 传递多个参数（mybatis会自动封装在Map集合中）

+ Collection、List、Array作为参数，封装为Map，但是有一定的规则

首先，如果你的数据库支持自动生成主键的字段（比如 MySQL 和 SQL Server），那么你可以设置 useGeneratedKeys=”true”，然后再把 keyProperty 设置到目标属性上就 OK 了。例如，如果上面的 Author 表已经对 id 使用了自动生成的列类型，那么语句可以修改为：

```
<insert id="insertAuthor" useGeneratedKeys="true"
    keyProperty="id">
  insert into Author (username,password,email,bio)
  values (#{username},#{password},#{email},#{bio})
</insert>
```

如果不添加这个会不会出现问题？以前使用JDBC也是直接没有添加这一行的