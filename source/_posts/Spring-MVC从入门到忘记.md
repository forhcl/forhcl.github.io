---
title: Spring MVC从入门到忘记
date: 2019-07-19 10:16:27
tags:
categories:
---

### 什么是MVC设计模式？

&emsp;&emsp;`MVC` 设计模式指的是我们可以把一个web应用程序分成三层。

+ Controller：负责接受并处理请求，响应客户端。
+ Model：模型数据，业务逻辑。
+ View：呈现模型，与用户进行交互。

![MVC组件之间的典型合作](http://cdn1.hikariblog.cn/MVC%E7%BB%84%E4%BB%B6%E4%B9%8B%E9%97%B4%E7%9A%84%E5%85%B8%E5%9E%8B%E5%90%88%E4%BD%9C.png)

### SpringMVC

#### SpringMVC的概念

&emsp;&emsp;`SpringMVC` 是目前最好的实现 `MVC` 设计模式的框架，是 `Spring` 框架的一个后续产品/子模块，二者可以很好的结合使用，不需要整合。

__SpringMVC核心组件__

1. DispatcherServlet：前置控制器。
2. Handler：处理器，完成具体的业务逻辑。
3. HandlerMapping：将请求映射到Handler。
4. HandlerInterceptor：处理器拦截器。
5. HandlerExecutionChain：处理器执行链。
6. HandlerAdapter：处理器适配器。
7. ModelAndView：装载模型数据和视图信息。
8. ViewResolver：视图解析器。

__SpringMVC实现流程__

1. 客户端请求被DispatcherServlet接收。
2. DispatcherServlet将请求映射到Handler。
3. 生成Handler以及HandlerInterceptor。
4. 返回HandlerExecutionChain(Handler+HandlerInterceptor)给DispatcherServlet。
5. DispatcherServlet通过HandlerAdapter执行Handler。
6. 返回一个ModelAndView。
7. DispatcherServlet通过ViewResolver进行解析。
8. 返回填充了模型数据的View，响应给客户端。

![SpringMVC流程](http://cdn1.hikariblog.cn/SpringMVC%E6%B5%81%E7%A8%8B.png)

#### SpringMVC的使用

##### 传统意义上的配置：

1. 大部分组件由框架提供，开发者只需通过配置进行关联。

2. 开发者只需手动编写Handler，View。

   ###### 基于XML配置的使用

3. SpringMVC基础配置

   + 导入相关的依赖

     ```xml
         <!-- SpringMVC相关的jar包 -->
         <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-webmvc</artifactId>
           <version>${spring-version}</version>
         </dependency>
     <!-- 导入Servlet相关jar包 -->
         <dependency>
           <groupId>javax.servlet</groupId>
           <artifactId>javax.servlet-api</artifactId>
           <version>3.1.0</version>
         </dependency>
     ```

   + web.xml中配置前置控制器DispatcherServlet

     ```xml
     <!--web.xml-->
     <!DOCTYPE web-app PUBLIC
      "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
      "http://java.sun.com/dtd/web-app_2_3.dtd" >
     
     <web-app>
       <display-name>Archetype Created Web Application</display-name>
       <servlet>
         <servlet-name>SpringMVC</servlet-name>
         <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
         <!-- 指定项目配置文件 -->
         <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc.xml</param-value>
         </init-param>
       </servlet>
       <servlet-mapping>
         <servlet-name>SpringMVC</servlet-name>
         <url-pattern>/</url-pattern>
       </servlet-mapping>
     </web-app>
     ```

4. XML配置Controller，HandlerMapping组件映射。

   编写一个控制器

   ```java
   import org.springframework.web.servlet.ModelAndView;
   import org.springframework.web.servlet.mvc.Controller;
   
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   
   /**
    * @author Hogan_Lee
    * @create 2019-07-19 12:15
    */
   public class MyHandler implements Controller {
       @Override
       public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
           //装载模式数据和逻辑视图
           ModelAndView modelAndView=new ModelAndView();
           //添加模型数据
           modelAndView.addObject("name","Tom");
           //添加逻辑视图
           modelAndView.setViewName("show");
           return modelAndView;
       }
       //第二种控制器编写方式：Model传值，String进行视图解析
       public String ModelTest(Model model){
           //填充模型数据
           model.addAttribute("name","Jerry");
           //设置逻辑视图
           return "show";
       }
       //第三种控制器编写方式：Map传值，String进行视图解析
       public String MapTest(Map<String,String> map){
           //填充模型数据
           map.put("name","Cat");
           //设置逻辑视图
           return "show";
       }
   ```

   spring配置文件中配置处理器映射

   ```xml
   <!-- 配置HandlerMapping，将url请求映射到Handler -->
   <bean id="handlerMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
       <!-- 配置Mapping -->
       <property name="mappings" >
           <props>
               <prop key="/test">testHandler</prop>
           </props>
       </property>
   </bean>
   <!-- 配置Handler -->
   <bean id="testHandler" class="io.github.forhcl.MyHandler"></bean>
   ```

5. spring配置文件中XML配置ViewResolver组件映射。

   ```xml
       <!-- 配置视图解析器 -->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <!-- 配置前缀 -->
           <property name="prefix" value="/"></property>
           <!-- 配置后缀 -->
           <property name="suffix" value=".jsp"></property>
       </bean>
   ```

   ###### 基于注解方式的使用

   __Handler通过自动扫描进行创建Bean__

   __HandlerMapping通过`@RequestMapping("")`对方法进行注解__

##### 新版本的配置（推荐使用，这种方式完全可以将web.xml替代）

1. 配置DispatcherServlet。

   &emsp;&emsp;通过继承抽象类 `AbstractAnnotationConfigDispatcherServletInitializer` 重写三个抽象方法来配置 `DispatcherServlet`。

   ```java
   package spittr.config;
   
   import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;
   
   /**
    * @author Hogan_Lee
    * @create 2019-07-19 16:13
    */
   public class SpittrWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
       //指定~~的配置类(用来配置ContextLoaderListener创建的应用上下文中的Bean)
       @Override
       protected Class<?>[] getRootConfigClasses() {
           return new Class<?>[]{RootConfig.class};
       }
       //指定应用的配置类(用来定于DispatcherServlet应用上下文中的Bean)
       @Override
       protected Class<?>[] getServletConfigClasses() {
           return new Class<?>[]{WebConfig.class};
       }
       //将DispatcherServlet映射到“/”
       @Override
       protected String[] getServletMappings() {
           return new String[]{"/"};
       }
   }
   ```

2. 启用Spring MVC

   使用 `@EnableWebMvc` 注解在注释应用的配置类。

   ```java
   package spittr.config;
   
   import org.springframework.context.annotation.Configuration;
   import org.springframework.web.servlet.config.annotation.EnableWebMvc;
   
   /**
    * @author Hogan_Lee
    * @create 2019-07-19 16:31
    */
   @Configuration
   @ComponentScan(basePackages = "spitter.web")
   @EnableWebMvc
   public class WebConfig {
           //配置JSP视图解析器
       @Bean
       public ViewResolver viewResolver(){
           InternalResourceViewResolver resolver=new InternalResourceViewResolver();
           resolver.setPrefix("/WEB-INF/views/");
           resolver.setSuffix(".jsp");
           resolver.setExposeContextBeansAsAttributes(true);
           return resolver;
       }
       //配置静态资源的处理
       @Override
       public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
           configurer.enable();
       }
   }
   ```

__总结:__

&emsp;&emsp;SpringMVC是一个实现MVC模式的框架，对MVC模式进行了封装，让开发人员能够更加方便、快捷地进行基于MVC设计模式的软件开发，同时SpringMVC是Spring框架的一个后续产品，可以和Spring框架很好的结合起来使用，不需要做任何的整合处理。

#### SpringMVC数据绑定

```xml
//编码过滤器，在web.xml中配置解决中文乱码问题
  <filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
      <param-name>forceEncoding</param-name>
      <param-value>true</param-value>
    </init-param>
  </filter>
<!-- 捕获所有请求 -->
  <filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```

##### SpringMVC数据绑定的原理

+ 什么是数据绑定？

  <span style="color:blue">将HTTP请求中的参数绑定到Handler业务方法的形参列表当中。</span>

  <!-- JSP是通过创建Servlet(继承于HTTPServlet的类，也是JSPMVC里面的控制器)，重写里面的doGet/doPost方法，由方法的HttpServletRequest类型的参数获得HTTP请求中的参数，从而实现数据的绑定。这种做法有一个缺点，就是传输的数据到最后都是String类型的，需要进行类型的转换 -->

  Spring<span style="color:red">自动</span>将信息进行封装,我们可以直接使用封装好的形参完成后续业务方法的逻辑操作。

  ![SpringMVC数据绑定](http://cdn1.hikariblog.cn/SpringMVC%E6%95%B0%E6%8D%AE%E7%BB%91%E5%AE%9A.png)

##### SpringMVC数据绑定的使用

+ 常用的数据绑定类型

  1. 基本数据类型

     注解：

     `@ResponseBody`：可以不经过视图解析器，直接将业务方法的返回值打印到网页中

     `@RequestParam(value="i",required=false)`：指定HTTP请求要传入的参数名称，用于处理HTTP请求中的参数名称和Handler业务方法的形参名称不一致的情况，进行数据的间接绑定。

     GET请求方式：`http://localhost:8080/spring_webapp_war_exploded/home?i=8`

     ```java
         @ResponseBody
         @RequestMapping(value = "/primaryType")
         public String primaryType(@RequestParam(value = "id") int id){
             return "id:"+id;
         }
     ```

  2. 包装类

     包装类和基本数据类型之间可以自动拆装箱，所以操作和基本数据类型一致。需要注意的如果不传入值，则包装类变量默认为null，而基本数据类型会报错。<span style="color:red">(注意：这里说的没有传入值说的是在请求参数列表中找到对应的参数名但是没有找到值；而`defaultValue`属性指的是在请求参数列表中连参数名都没有找到)</span><span style="color:blue">可以使用`@RequestParam`注解的`defaultValue`属性为形参设置默认值，当参数没有传进来的时候使用默认值。</span>

     ```java
         @ResponseBody
         @RequestMapping(value = "/wrapperType")
         public String wrapperType(@RequestParam(value = "id") Integer id){
             return "id:"+id;
         }
     ```

  3. 数组

     ```java
         @ResponseBody
         @RequestMapping(value = "/arrayType")
         public String arrayType(@RequestParam(value = "name") String[] name){
             StringBuffer sbf=new StringBuffer();
             for(String item:name){
                 sbf.append(item).append(" ");
             }
             return sbf.toString();
         }
     ```

     GET请求方式：`<http://localhost:8080/spring_webapp_war_exploded/home?i=2&i=3>`

     注意：数组绑定请求参数列表中多个请求参数名称一致

  4. 对象

     实体类,可以级联，即对象当中包含对象。

     教师类：
  
     ```java
     public class Author{
         private int id;
         private String name;
         public int getId() {
             return id;
         }
     
         public void setId(int id) {
             this.id = id;
         }
     
         public String getName() {
             return name;
         }
     
         public void setName(String name) {
             this.name = name;
         }
   }
     ```

     课程类
  
     ```java
     public class Course{
         private int id;
         private String name;
         private double price;
         private Author author;
             public int getId() {
             return id;
         }
     
         public void setId(int id) {
             this.id = id;
         }
     
         public String getName() {
             return name;
         }
     
         public void setName(String name) {
             this.name = name;
         }
     
         public double getPrice() {
             return price;
         }
     
         public void setPrice(double price) {
             this.price = price;
         }
     
         public Author getAuthor() {
             return author;
         }
     
         public void setAuthor(Author author) {
             this.author = author;
         }
   }
     ```

     数据绑定Demo
  
     ```java
     //控制器对应参数
     @RequestMapping(value ="/pojoType" )
       public String pojoType(Model model,Course course){
             //将穿过来的course插入到Map中
           courseDao.add(course);
             model.addAttribute("courses",courseDao.getAll());
           return "index";
     }
     //表单中name的书写要求
     <form action="pojoType" method="post">
       请输入课程编号:<input type="text" name="id"/>
         请输入课程名称:<input type="text" name="name"/>
       请输入课程价格:<input type="text" name="price"/>
         请输入教师编号:<input type="text" name="author.id"/>
       请输入教师名称:<input type="text" name="author.name"/>
         <input type="submit" value="提交"/>
   </form>
     ```
  
     
  
  5. 集合（List，Set，Map）
  
     不能直接为集合进行绑定，需定义一个包装类
  
     List集合通过下标进行绑定List[1].id，Map集合通过key进行绑定Map[key].id
  
     Set集合有些特殊，需要先向集合中添加两个实例，否则无法完成绑定，然后和List集合一样，通过下标进行绑定List[1].id。
  
  6. JSON
  
     SpringMVC是无法完成JSON格式数据的绑定的，需另外添加Jar包jackson-databind.jar,然后到配置文件中配置一个消息转换器。
  
     ```xml
         <mvc:annotation-driven>
             <mvc:message-converters>
                 <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter"></bean>
             </mvc:message-converters>
         </mvc:annotation-driven>
     ```
  
     ```jsp
     <%@ page contentType="text/html;charset=UTF-8" language="java" %>
     <html>
     <head>
         <title>Title</title>
     </head>
     <script type="text/javascript" src="js/jquery-1.8.3.min.js"></script>
     <script type="text/javascript">
     
         $(function(){
             var course = {
                 "id":"8",
                 "name":"SSM框架整合",
                 "price":"200"
             };
             $.ajax({
                 url:"jsonType",
                 data:JSON.stringify(course),
                 type:"post",
                 contentType:"application/json;charse=UTF-8",
                 dataType:"json",
                 success:function(data){
                     alert(data.name+"---"+data.price);
                 }
             })
         })
     
     </script>
     <body>
     
     </body>
     </html>
     ```
  
     
  
     ```java
         @RequestMapping(value = "/jsonType")
         @ResponseBody
         public  Course jsonType(@RequestBody  Course course){
             course.setPrice(course.getPrice()+100);
             return course;
         }
     ```
  
     

#### SpringMVC拦截器

拦截器起作用的地方：HandlerAdapter--->Handler

##### SpringMVC的环境配置与安装

注意：使用maven archetype创建的web.xml内容版本较低，应更新（去别的地方复制粘贴过来）

##### SpringMVC的拦截器在项目开发中的重要性

##### SpringMVC的拦截器和过滤器的区别

