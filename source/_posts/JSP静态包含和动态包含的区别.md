---
title: JSP静态包含和动态包含的区别
date: 2019-06-14 20:20:29
tags:
- 面试
- JSP
- Java
categories: JSP
---

&emsp;&emsp;`JSP`静态包含和动态包含作用都是为了在`JSP`页面内某处整体嵌入一个文件，从而实现代码的复用；如多个`JSP`页面共享同一个导航栏文件。下面我们来看看这两种方式有什么具体的区别？

+ 实现形式

&emsp;&emsp;静态包含使用`include`指令标记，如下：

```jsp
<%@ include file="文件的URL" %>
```

&emsp;&emsp;动态包含采用`include`动作标记，如下：

```jsp
<jsp:include page="文件的URL"/>
或
<jsp:include page="文件的URL">
    param子标记
</jsp:include>
```

+ 生成字节码文件

&emsp;&emsp;静态包含是将两个文件合二为一，然后生成一个`Servlet`和对应的字节码文件。

&emsp;&emsp;动态包含是两个页面<span style="color:red">各自</span>生成自己的`Servlet`和对应的字节码文件。

+ 传参

&emsp;&emsp;动态包含可以借助`param`动作标记作为子标记进行传参。而静态包含不可以。如下：

```jsp
<!-- myfile/target.jsp -->
String sideA=request.getParameter("sideA");
String sideB=request.getParameter("sideB");
```

将`myfile/target.jsp`文件动态包含到`JSP`页面，并传入参数`sideA`和`sideB`。

```jsp
<jsp:include page="myfile/target.jsp">
    <jsp:param name="sideA" value="12"/>
    <jsp:param name="sideB" value="15"/>
</jsp:include>
```

+ 发生时间

&emsp;&emsp;静态包含发生在`JSP`——>`Servlet`期间。

&emsp;&emsp;动态包含发生在执行`class`文件期间，执行到`include`动作指令时才加载包含的文件。

需要注意的：

+ 静态包含和动态包含的内置对象`request`是一样使用的，不会有所差别。
+ 静态包含的两个页面的`<%@ page contentType="text/html;charset=gb2312"%>`应保持一致。

&emsp;&emsp;静态包含（include指令标记）是在编译阶段就处理所需要的文件，被处理的文件在逻辑和语法上依赖于当前的JSP页面，其优点是页面的执行速度快；而动态包含（include动作标记）是在JSP页面运行时才处理文件，被处理的文件在逻辑和语法上独立于当前JSP页面，其优点是可以使用param子标记更灵活地处理所需要的文件。