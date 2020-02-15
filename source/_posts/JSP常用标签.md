---
title: JSP常用标签
date: 2019-07-26 09:12:27
tags:
- JSP
- Java Web
- EL
- JTSL
categories: JSP
---

&emsp;&emsp;JSTL(JavaServer Pages Standard Tag Library)/JSP标准标签库是JSP提供的一个自定义标签库。使用JSTL有利于我们实现JSP页面中的<span style="color:red">代码复用</span>例如用于————的 `<c:foreach></c:foreach>` 标签。

JSTL标签的可分为四大分类

- 核心标签--最常用、最重要
- 格式化标签
- SQL标签
- XML标签

除此之外，还有JSTL函数也是比较常用的

#### JSTL的环境搭建

&emsp;&emsp;JSTL标签和Server及JSP页面有着比较严格的版本对应关系，版本对应不正确很容易抛出异常。

JSTL的jar包下载地址:<https://mvnrepository.com/artifact/javax.servlet/jstl>

拓展：引入jar包的三种方式：

1. 引入jar到WEB-INF/lib文件目录下，然后右键jar包，选择add as library；
2. `build path`引入jar包在文件系统中的一个外部链接；
3. 使用maven项目通过坐标进行引入进行引入。

#### JSTL的入门Demo

为了使用JSTL，需要在JSP页面中对其进行声明:

```jsp
<!-- 如：引入JSTL的核心标签库 -->
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

接下来我们就可以使用JSTL进行开发了，首先让我们按照惯例来输出一个Hello World!

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>$Title$</title>
</head>
<body>
  <c:out value="Hello World!"></c:out>
</body>
</html>
```

#### JSTL和EL表达式

JSTL标签中对常量的值输出是较为简单的，但是对于变量的操作则要借助EL表达式(Expression Language)。

##### EL表达式的格式

- 用美元符号“$”定界，内容包括在花括号“{}”中

  &emsp;&emsp;&emsp;&emsp;&emsp;${表达式}

- "."与"[]"运算符

  + 通常情况是通用的`${user.sex}`等同于`${user["sex"]}`

  + "[]"还可以用来进行集合元素中的定位`${booklist[0].price}`

  + 以下两种情况下"."与"[]"之间存在区别

    - 包含特殊字符

      只能使用"[]"，如`${user["first-name"]}`

    - 通过变量动态取值

      同样只能使用"[]"，如`${user[param]}`,其中param可以是name or sex or others

##### EL变量

| JSP内置对象 | EL名称           |
| ----------- | ---------------- |
| Page        | PageScope        |
| Request     | RequestScope     |
| Session     | SessionScope     |
| Application | ApplicationScope |

如果没有指定，则按照PageScope-->RequestScope-->SessionScope-->ApplicationScope的顺序进行逐一查找。

<span style="color:red">>如果找不到值为null，但是EL对其进行优化会输出空字符串。</span>

##### EL的自动类型转换

JSP的getparameter()方法每次获得都是字符串，需要我们手动进行数据类型的转换，当时现在EL表达式可以进行自动数据类型的转换，常见与SpringMVC的数据绑定。

##### EL隐式对象

| 隐式对象         | 意义                                         |
| ---------------- | -------------------------------------------- |
| PageContext      | PageContext实例对应于当前页面的处理          |
| PageScope        | 与页面作用域的名称和值相关联的Map类          |
| requestScope     | 与请求作用域属性的名称和值相关联的Map类      |
| sessionScope     | 与会话作用域属性的名称和值相关联的Map类      |
| applicationScope | 与应用程序作用域属性的名称和值相关联的Map类  |
| param            | 按名称存储的请求参数的主要值的Map类          |
| paramValues      | 按请求参数的所有值作为String数组存储的Map类  |
| Header           | 按名称存储请求头主要值的Map类                |
| headerValue      | 按请求头的所有值作为String数组存储的Map类    |
| cookie           | 按名称存储请求附带的cookie的Map类            |
| intParam         | 按名称存储Web应用程序上下文初始化参数的Map类 |

##### EL运算符

+ 运算符允许对数据和文字进行组合以及比较

+ EL运算符：

  | 类别       | 运算符                                                   |
  | ---------- | -------------------------------------------------------- |
  | 算术运算符 | +、-、*、/(或div)和%(或mod)                              |
  | 关系运算符 | ==(或eq)、!=(或ne)、<(或It)、>(或gt)、<=(或le)和>=(或ge) |
  | 逻辑运算符 | &&(或and)、\|\|(或or)和!(或not)                          |
  | 验证运算符 | empty                                                    |

#### JSTL核心标签库

JSTL的核心标签库标签共13个，从功能上可以分为4类：

- 表达式控制标签：out set remove catch
- 流程控制标签：if choose when otherwise
- 循环标签：forEach forTokens
- URL操作标签：import url redirect

##### out标签

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>$Title$</title>
</head>
<body>
<!-- 使用out标签输出常量 -->
<c:out value="Hello World"></c:out><br/>
<!-- 使用out标签输出变量 -->
<%
    session.setAttribute("name", "小明");
%>
<c:out value="${sessionScope.name}"></c:out>
<!-- 当变量不存在时输出默认值 -->
<c:out value="${sex}" default="男"></c:out>
<!-- 当escapeXML为flase时，转义字符会进行转义，默认为true -->
<c:out value="2&nbsp;2/n22" escapeXml="flase"></c:out>
</body>
</html>
```

##### set标签

```jsp
<jsp:useBean id="ds" class="user.User"></jsp:useBean>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>$Title$</title>
</head>
<body>
<!-- 存值到scope当中 -->
<!-- 1.值放到value中 -->
<c:set value="today" var="day" scope="session"></c:set>
<c:out value="${day}"></c:out>
<!-- 值放到容器标签当中 -->
<c:set var="age" scope="session">12</c:set>
<c:out value="${age}"></c:out>
<!-- 存值到JavaBean当中 -->
<c:set target="${ds}" value="as" property="name"></c:set>
<c:out value="${ds.name}"></c:out>
</body>
</html>
```

