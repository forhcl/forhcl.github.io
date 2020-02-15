---
title: RESTful入门
date: 2019-07-26 16:04:51
tags:
- RESTful
- SpringMVC
categories: RESTful
---

## RESTful

### 什么是RESTful？

REST：Representational State Teansfer(表达性状态转移)

REST并不是一种创新技术，它指的是一组架构约束条件和原则

符合REST的约束条件和原则的架构，就称它为RESTful架构。

### RESTful核心内容

1. 资源与URI(资源的地址)
2. 资源的表述——JSON、XML等
3. 状态转移

### RESTful架构特点

1. 统一了客户端访问资源的接口
2. url更加简洁，易于理解，便于扩展
3. 有利于不同系统之间的资源共享

#### RESTful开发风格

查询课程：`http://localhost:8080/course/id method='get'`

添加课程: `http://localhost:8080/course methos='post'`

删除课程: `http://localhost:8080/course/id methos='delete'`

修改课程: `http://localhost:8080/course/id method='put'`

1. __用URL描述资源。__

2. __使用HTTP方法（GET、POST、DELETE、PUT、PATCH等）描述行为。__

   __使用HTTP状态码来表示不同的结果（后台可以通过@ResponseStatus指定自定义异常处理方法的响应状态码、Spring也内置了一些场景的状态码）。__

3. __使用JSON交互数据，资源表述。（不一定是JSON，XML等也可以，但是JSON较流行）。__

4. __RESTful只是一种风格，并不是强制的标准。__

{% asset_img REST成熟度模型.png %}

RESTful支持处理GET、POST、PUT、DELETE、PATCH

问题：默认是能处理GET、POST请求的，而PUT和DELETE请求是不能够处理的，通过特殊的方式将POST请求转换为PUT请求或者DELETE请求。

首先在web.xml中配置一个过滤器

```xml
  <!-- REST风格代码开发过滤器  -->
  <filter>
    <filter-name>hiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>hiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```





使用Spring MVC开发Restful API.(用户增删改查TDD)

+ 编写第一个Restful API

  - __编写针对RestfulAPI的测试用例（自己再去学MockMVC吧，链式编程一脸懵）__

    ```java
    @RunWith(SpringRunner.class)
    @SpringBootTest
    public class UserControllerTest {
        @Autowired
        private WebApplicationContext wac;
    
        private MockMvc mockMvc;
    
        @Before
        public void setup(){
            mockMvc= MockMvcBuilders.webAppContextSetup(wac).build();
        }
    
        @Test
        public void whenQuerySuccess() throws Exception {
            String result=mockMvc.perform(MockMvcRequestBuilders.get("/user")
                    .param("username","xiaoming")
                    .contentType(MediaType.APPLICATION_JSON_UTF8))
                    .andExpect(MockMvcResultMatchers.status().isOk())
                    .andExpect(MockMvcResultMatchers.jsonPath("$.length()").value(3))
                    .andReturn().getResponse().getContentAsString();
            System.out.println(result);
        }
    }
    ```

  - 使用注解声明RestfulAPI

    `@RestController` 标明此Controller提供RestAPI

    `@RequestMapping`及其变体。映射http请求url到java方法

    `@RequestParam` 映射请求参数到java方法的参数中

    `@PageableDefault` 指定分页参数的默认值 这个是SpringData的，我自己一般使用PageHelper插件

  - 在RestfulAPI中传递参数

+ 编写用户详情服务

  - 相关注解

    `@PathVariable` 映射url片段到java方法的参数（路径参数）

    1. 在url声明中使用正则表达式，对路径参数的格式作限定

       ```java
           @RequestMapping(value = "/user/{id:\\d+}",method = RequestMethod.GET)
           public User getInfo(@PathVariable(value = "id")String id){
               System.out.println(id);
               User user=new User();
               user.setUsername("tom");
               return user;
           }
       ```

       实际业务场景可以根据需求撰写更加复杂的正则表达式

    `@JsonView` 控制json输出内容。额，这个类似于Jackson的`@JsonIgnore`注解，但是Jackson的`@JsonIgnore`注解是一直不显示某一个属性，但是我想的是我想让他显示就显示，不想让他显示就不显示。

    1. 使用接口来声明多个视图

    2. 在值对象的get方法上指定视图

       ```java
       import com.fasterxml.jackson.annotation.JsonView;
       
       /**
        * @author Hogan_Lee
        * @create 2019-11-15 21:10
        */
       public class User {
           private String username;
       
       
           private String password;
       
           @JsonView(UserSimpleView.class)
           public String getUsername() {
               return username;
           }
       
           public void setUsername(String username) {
               this.username = username;
           }
           @JsonView(UserDetailView.class)
           public String getPassword() {
               return password;
           }
       
           public void setPassword(String password) {
               this.password = password;
           }
       
           public interface UserSimpleView {
           }
           //注意：这里继承了UserSimpleView，表示不仅会显示自己的password还会显示simpleview
           public interface UserDetailView extends UserSimpleView{
           }
       }
       ```
       
    3. 在Controller方法上指定视图

       ```java
   import com.fasterxml.jackson.annotation.JsonView;
       import com.imooc.dto.User;
       import org.springframework.web.bind.annotation.*; 
       import java.util.ArrayList;
       import java.util.List;
       
       /**
        * @author Hogan_Lee
        * @create 2019-11-15 21:03
        */
       @RestController
       public class UserController {
           @RequestMapping(value = "/user",method = RequestMethod.GET)
           @JsonView(User.UserSimpleView.class)
           public List<User> query(@RequestParam(defaultValue = "xiao") String username){
               System.out.println(username);
               List<User> users=new ArrayList<>();
               users.add(new User());
               users.add(new User());
               users.add(new User());
               return users;
           }
       
           @RequestMapping(value = "/user/{id:\\d+}",method = RequestMethod.GET)
           @JsonView(User.UserDetailView.class)
           public User getInfo(@PathVariable(value = "id")String id){
               System.out.println(id);
               User user=new User();
               user.setUsername("tom");
               return user;
           }
       }
       ```
  
+ 处理创建请求

  - 相关注解

    `@RequestBody` 映射请求体到java方法的参数。

    1. 状态码：405请求的method后台不支持(存在url一致但是method方法不一样的处理器方法)

    日期类型参数

    &emsp;&emsp;类型转换器，"YY-mm-HH"之类的并不适用与前后端分离的app；前后端分离的app可能会有多个前端，每个前端可能对日期的显示格式不一样。传不带格式的时间戳，然后前台再对它进行处理，通过时间戳可以很方便的在前台和后台的时间戳和日期格式进行转换。

    ```java
        @PostMapping
        public User create(@RequestBody User user){
            System.out.println(user);
            user.setId("1");
            return user;
    
        }
    	@Test
        public void whenCreateSuccess() throws Exception {
            Date date=new Date();
            //日期格式的对象的时间戳
            System.out.println(date.getTime());
    
            //JSON格式的字符串，作为post请求的内容
            String content="{\"username\":\"tom\",\"password\":null,\"birthday\":"+date.getTime()+"}";
            String resuslt=mockMvc.perform(MockMvcRequestBuilders.post("/user")
                    .contentType(MediaType.APPLICATION_JSON_UTF8)
                    .content(content))
                    .andExpect(MockMvcResultMatchers.status().isOk())
                    .andExpect(MockMvcResultMatchers.jsonPath("$.id").value("1"))
                    .andReturn().getResponse().getContentAsString();
            System.out.println(resuslt);
        }
    ```
    

{% asset_img 日期格式和时间戳.png %}
`@Vaild`注解和`BindingResult`验证请求参数的合法性并处理校验结果
    

1. 状态码400：请求的格式错误（如参数校验出现错误）
   
2. `@Vaild`校验出错就会直接抛出异常，进不到处理器方法中。但是有的时候我们既要校验，又要对校验的结果进行日志记录，单单`@Vaild`就实现不了了。这时候就要和`BindingResult`配合使用了，如果校验有错误就会把错误放到`BindingResult`中，然后继续执行处理器方法。
   
   ```java
           @PostMapping
           public User create(@RequestBody @Valid User user, BindingResult errors){
               if(errors.hasErrors()){
                   //lambda表达式
                   errors.getAllErrors().stream().forEach(error-> System.out.println(error.getDefaultMessage()));
               }
               System.out.println(user);
               user.setId("1");
               return user;
           }
   ```

+ 开发用户信息修改和删除服务

  - 数据校验

    常用的验证注解

    1. @NotNull:用在基本数据类型上，不能为Null，但可以为空字符串

    2. @NotEmpty:用在集合类上，不能为null，并且长度必须大于0
    
    3. @NotBlank:只能作用在String上，不能为Null，而且调用trim()后，长度必须大于0
    
    4. @NonNull：在方法或构造函数的参数上使用，生成一个空值检查语句
    
       {% asset_img 参数校验注解.png %}
    
       {% asset_img 参数校验注解1.png %}
    
    每一个校验注解都有一个message，可以用于自定义错误消息
    
    自定义校验注解
    
    ```java
    /**
     * 自定义校验注解
     * @author Hogan_Lee
     * @create 2019-11-21 10:23
     */
    //这个注解可以被用在什么地方
    @Target({ElementType.FIELD,ElementType.METHOD})
    //运行时注解
    @Retention(RetentionPolicy.RUNTIME)
    //指定校验逻辑所在的类
    @Constraint(validatedBy = MyConstraintVaildator.class)
    public @interface MyConstraint {
        String message() default "{org.hibernate.validator.constraints.NotBlank.message}";
    
        Class<?>[] groups() default {};
    
        Class<? extends Payload>[] payload() default {};
    }
    
    /**
     * @author Hogan_Lee
     * @create 2019-11-21 10:27
     */
    //Spring容器能够自动找到这个类，因为实现了~  所以不用我们写@component
    //两个泛型，其中String说明注解只能放在String类型的域上面
    public class MyConstraintVaildator implements ConstraintValidator<MyConstraint,String> {
        @Override
        public void initialize(MyConstraint myConstraint) {
            System.out.println("init");
        }
    
        //返回值代表检验的结果
        @Override
        public boolean isValid(String s, ConstraintValidatorContext constraintValidatorContext) {
            System.out.println(s);
            return false;
        }
    }
    ```
    
    数据校验失败状态码是400

#### RESTful API 错误处理

- Spring Boot 中默认的错误处理机制

  额，默认的异常处理机制也很好用！！

  Spring Boot自定义错误页面的存放位置！好像只有2.0以下的是这个样子，尝试了2.0以上的不可以

  {% asset_img 错误页面.png %}

  ![](RESTful入门/错误页面.png)

- 自定义异常处理

#### RESTful API的拦截

需求：记录所有服务的处理时间

1. **过滤器（Filter）**

   ```java
   package com.imooc.web.filter;
   
   import org.springframework.stereotype.Component;
   
   import javax.servlet.*;
   import java.io.IOException;
   import java.util.Date;
   
   /**
    * @author Hogan_Lee
    * @create 2019-12-03 14:09
    */
   //加上@Compont之后就生效了，拦截所有的请求
   @Component
   public class TimeFilter implements Filter {
       @Override
       public void init(FilterConfig filterConfig) throws ServletException {
           System.out.println("time filter init");
       }
   
       //doFilter执行整个处理方法的逻辑
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           System.out.println("time filter start");
           long start=new Date().getTime();
           //服务处理
           filterChain.doFilter(servletRequest,servletResponse);
           System.out.println("time filter:"+(new Date().getTime()-start));
           System.out.println("time filter finish");
       }
   
       @Override
       public void destroy() {
           System.out.println("time filter destroy");
       }
   }
   
   ```

   第三方的Filter加到自己的项目中，在配置类或启动类中加上以下内容

   ```
   @Bean
   public FilterRegistrationBean timeFilter(){
       FilterRegistrationBean registrationBean=new FilterRegistrationBean();
       TimeFilter timeFilter=new TimeFilter();
       //要使用的过滤器Bean
       registrationBean.setFilter(timeFilter);
       //过滤器生效的路径
       List<String> urls=new ArrayList<>();
       urls.add("/*");
       registrationBean.setUrlPatterns(urls);
       return registrationBean;
   }
   ```

   <span style="color:red">采用J2EE规范来编写的，只能取得request和response两个对象；实际上是由哪个控制器的哪个处理器方法来处理的并不知道。</span>

   额，如果有很多个过滤器要执行流程是怎么样子的？

2. **拦截器（Interceptor）**

   注意：PreHandler方法返回的布尔值决定了后面的方法是不是要执行！！！可以拦截处理器方法

   ```java
   package com.imooc.web.interceptor;
   
   import org.springframework.stereotype.Component;
   import org.springframework.web.method.HandlerMethod;
   import org.springframework.web.servlet.HandlerInterceptor;
   import org.springframework.web.servlet.ModelAndView;
   
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   import java.util.Date;
   
   /**
    * @author Hogan_Lee
    * @create 2019-12-03 14:35
    */
   //注意这里单单加上@Component还是不生效的，还需要额外的配置
   @Component
   public class TimeInterceptor implements HandlerInterceptor {
       @Override
       public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
           System.out.println("preHandler");
           httpServletRequest.setAttribute("startTime", new Date().getTime());
           //相比拦截器多了一个对象
           //打印类命
           System.out.println(((HandlerMethod) o).getBean().getClass().getName());
           //打印处理器方法名字
           System.out.println(((HandlerMethod) o).getMethod().getName());
           return true;
       }
   
       @Override
       public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
           System.out.println("postHandler");
           Long start = (Long) httpServletRequest.getAttribute("startTime");
           System.out.println("time interception 耗时" + (new Date().getTime() - start));
       }
   
       //不管处理器方法是否抛出异常都会进入到afterCompletion方法中，抛出异常不会进入到postHandler方法
       //注意：控制器通知类比这个类先执行，异常会被控制器通知类偷走了
       @Override
       public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
           System.out.println("AfterCompletion");
           Long start = (Long) httpServletRequest.getAttribute("startTime");
           System.out.println("time interception 耗时" + (new Date().getTime() - start));
           System.out.println("e is" + e);
       }
   }
   
   ```

   拦截器注册

   ```java
   import com.imooc.web.interceptor.TimeInterceptor;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
   import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
   
   /**
    * @author Hogan_Lee
    * @create 2019-12-03 14:51
    */
   @Configuration
   public class WebConfig extends WebMvcConfigurerAdapter {
       @Autowired
       private TimeInterceptor timeInterceptor;
       //拦截器注册器
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(timeInterceptor);
       }
   }
   ```

3. **切面（Aspect）**

   

使用Spring MVC处理其它web应用常见的需求和场景.

+ 如何处理静态资源和异常
+ 如何使用Spring MVC拦截器
+ 如何处理文件的上传下载
+ 如何进行请求的异步开发

Restful API开发常用辅助框架







除了查询，如更新，创建等要返回什么





## 文件的上传和下载

#### 1、文件上传

现在前端页面大部分都是单页应用，不会去刷新。不会有表单提交，上传文件的时候也是异步的，只会提交一个文件的路径上来。文件的上传往往都是单做的。

文件上传单元测试：

```java
@Test
public void whenUploadSuccess() throws Exception {
    String result=mockMvc.perform(MockMvcRequestBuilders.fileUpload("/file")
                                  //第一个参数和控制器参数名称一致，第二个为file的值，第四个为文件内容的字节流
            .file(new MockMultipartFile("file", "test.txt", "multipart/form-data", "hello upload".getBytes("UTF-8"))))
            .andExpect(MockMvcResultMatchers.status().isOk())
            .andReturn().getResponse().getContentAsString();
    System.out.println(result);
}
```

文件上传控制器

```java
@RestController
@RequestMapping("/file")
public class FileController {

    //人家上传过来至少要返回获取文件的路径给他
    @PostMapping
    public FileInfo upload(MultipartFile file) throws IOException {
		//文件保存的位置
        String folder="G:\\test\\imooc-security\\imooc-security-demo1\\src\\main\\java\\com\\imooc\\web\\controller";
        File localFile=new File(folder,new Date().getTime()+".txt");
        //将传上来的文件写到自己新建的文件
        file.transferTo(localFile);
        return new FileInfo(localFile.getAbsolutePath());

    }
```

#### 2、文件下载

文件下载控制器：

```java
    @GetMapping("/{id}")
    public void download(@PathVariable String id, HttpServletRequest request, HttpServletResponse response) throws Exception{
        //try-with-resource
        try(
                //输入流
                InputStream inputStream=new FileInputStream(new File(folder,id+".txt"));
                //输出流
                OutputStream outputStream=response.getOutputStream()
        ){
            response.setContentType("application/x-download");
            response.addHeader("Content-Disposition","attachment;filename=test.txt");
            //commons-io包，这个方法的作用是把输入流拷贝到输出流中
            IOUtils.copy(inputStream,outputStream);
            outputStream.flush();
        }
    }
```





自动生成接口文档——与前端开发并行工作

&emsp;&emsp;使用swagger自动生成html文档

如果没有这个工具，需要同时维护自己的代码和文档，很繁琐。而swagger可以根据代码实时生成接口文档。

```xml
        <!-- 扫描程序生成文档数据 -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>
        <!-- 生成最终的可视化界面 -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
```

然后在配置类中添加@EnableSwagger2注解，启动后访问localhost:8060/swagger-ui.html.

Swagger常用API描述注解：

@APIOperation  方法描述

@APIModelProperty  实体类属性描述

@APIParam  方法参数没有封装成对象的属性

&emsp;&emsp;使用WireMock快速伪造RESTful服务

