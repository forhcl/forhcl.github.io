---
title: Java高效编程
hidden: true
date: 2019-12-19 22:18:27
tags:
categories:
---

### Lombok

Lombok是一个java库，为我们提供一些常用的注解，在类或者属性上添加这些注解可以在源代码编译时期为我们自动生成一些源代码，从而达到对我们类功能的增强。

Lombok实现的原理

注解的两种解析方式：

+ 运行时解析：反射、动态代理

+ 编译时解析：lombok就是运行在编译时期的（可以去看看编译后的class文件）引入Jar的依赖范围

  ```xml
  <scope>provided</scope>
  ```

  

编译时解析的两种机制：

+ Annotation Processing Tool（注解处理器）已被移除
+ Pluggable Annotation Processing API（JSR269插入式注解处理器）![](http://cdn1.hikariblog.cn/%E6%B3%A8%E8%A7%A3%E5%A4%84%E7%90%86%E5%99%A8%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.PNG)

Lombok的常用注解:

![](http://cdn1.hikariblog.cn/Lombok%E6%B3%A8%E8%A7%A3.PNG)

日志这个记住，每次写logger有点烦



Lombok注解介绍

小技巧：在idea的maven窗口下载第三方Jar包源码，通过ctrl跳过去就不是反编译的代码了，而是第三方源码了

`@Getter` 为属性生成get方法。

注解类：为类中的所有属性添加getter方法

注解属性：为该属性生成getter方法

```java
/**
 * @Getter注解
 * 为属性生成get方法
 */
public class GetterTest {

    //懒加载：单例模式的懒加载那样子第一次使用的时候再加载，从而时间换空间
    @Getter(
            lazy = true
    )
    private final String field1 = "zhangxiaoxi";

    @Getter(

            value = AccessLevel.PRIVATE,
            onMethod_={@NotNull}
    )
    private String field2;

}
```

`@Setter` 为属性添加Setter方法

注解类：为类中的所有属性添加getter方法

注解属性：为该属性生成getter方法

```java
/**
 * @Setter注解
 * 为属性生成set方法
 */
public class SetterTest {

    @Setter
    private String field1;

    @Setter(
            value = AccessLevel.PRIVATE,
        //方法的参数上
            onParam_={@NotNull}
    )
    private String field2;

}
```

`@ToString` 生成toString方法

必须作用在类上！！！

```java
package com.imooc.zhangxiaoxi.lombok;

import lombok.Setter;
import lombok.ToString;
import org.junit.Test;

/**
 * @ToString注解
 * 生成toString方法
 */
@ToString(
        includeFieldNames = false,
    //排除掉某个属性
//        exclude = {"field1"},
    //只包含某个属性，优先级比exclude高 
//        of = {"field1"},
        doNotUseGetters = false
)
public class ToStringTest {

    @Setter
    private String field1;

    @Setter
    private String field2;

    public String getField2() {
        System.out.println("调用get方法！");
        return this.field2;
    }

    @Test
    public void test() {
        ToStringTest toStringTest = new ToStringTest();
        toStringTest.setField1("zhang");
        toStringTest.setField2("xiaoxi");

        System.out.println(toStringTest.toString());
    }

}
```

`@EqualsAndHashCode` 生成Equals方法和HashCode方法

语法规范要求两者要同时重写！！

```java
/**
 * @EqualsAndHashCode注解
 * 生成Equals方法和HashCode方法
 */
@EqualsAndHashCode(
        exclude = {"field1"}
)
public class EqualsAndHashCodeTest {

    private String field1;

    private String field2;

}
```

`@Data` 包括`@Getter`、`@Setter`、`@ToString`、`@EqualsAndHashCode`四个注解

```java
@Data
public class DataTest {

    private String field1;

    private String field2;

}
```

`val`接口 弱语言变量

```java
/**
 * @val注解
 * 弱语言变量
 */
public class ValTest {

    public ValTest() {
        val field = "zhangxiaoxi";

        val list = new ArrayList<String>();
        list.add("zhangxiaoxi");
    }

}
```

此时只能一句卧槽，Java强类型语言硬生生被你玩成弱类型，Lombok是作用在生成字节码过程中的，生成字节码lombok会推断且生成相应Java类型的字节码。

`NouNull` 生成非空检查

```
/**
 * @NonNull注解
 * 生成非空检查
 */
public class NonNullTest {

    public NonNullTest(@NonNull String field) {
        System.out.println(field);
    }

}
```

额，不方便统一异常处理，涉及持久层具体业务逻辑的判断和抛指定类型和自定义的异常

`@AllArgsConstructor` 有参

`@NoArgsConstructor` 无参

`@RequireConstructor` 必要参数

Required arguments are final fields and fields with constraints such as {@code @NonNull}.

```java
/**
 * @AllArgsConstructor
 * @NoArgsConstructor
 * @RequiredArgsConstructor
 */
@RequiredArgsConstructor
public class ConstructorTest {

    private final String field1;

    @NonNull
    private String field2;

    private String field3;

}
```



`@Cleanup` 生成对资源进行关闭的一些代码

```java
/**
 * @Cleanup注解
 * 资源关闭
 */
public class CleanupTest {

    public void copyFile(String in, String out)
            throws Exception {

        @Cleanup FileInputStream fileInputStream =
                new FileInputStream(in);
        @Cleanup FileOutputStream fileOutputStream =
                new FileOutputStream(out);

        int r;

        while ((r = fileInputStream.read()) != -1) {
            fileOutputStream.write(r);
        }


    }

}
```

Ensures the variable declaration that you annotate will be cleaned up by calling its close method, regardlessof what happens. Implemented by wrapping all statements following the local variable declaration to theend of your scope into a try block that, as a finally action, closes the resource.

与`try-with-result`作用一致，都是在编译时期将关闭语句封装到finally语句中，所以还是使用`try-with-resource`.官方一点



lombok的优点：

+ 通过注解自动生成样板代码，提高开发效率
+ 代码简洁，只关注相关属性
+ 新增属性后，无需刻意修改相关方法

lombok的缺点：

+ 降低了源代码的可读性和完整性
+ 加大对于问题排查的难度
+ 需要IDE的相关插件的支持



































# 验证框架

通过提供的校验注解和自定义校验注解对数据进行校验。

JSR规范、验证框架的原理

```java
public void newHandler(Order order){
    //实例化验证框架对象
        Validator validator= Validation.buildDefaultValidatorFactory().getValidator();
        //对数据进行校验
        Set<ConstraintViolation<UserInfo>> set=validator.validate(userInfo);
        //验证结果集不为空，合并后抛出异常
        if(set.size()>0){
            StringBuilder stringBuilder=new StringBuilder();
            set.forEach(e->{
                stringBuilder.append(e.getMessage());
                stringBuilder.append(';');
            });
            throw new RuntimeException(stringBuilder.toString());
        }
}
```

##### 分层验证与JavaBean验证

+ 分层验证模型

![](http://cdn1.hikariblog.cn/%E5%88%86%E5%B1%82%E9%AA%8C%E8%AF%81%E6%A8%A1%E5%9E%8B.PNG)

将验证逻辑写在每一层上，由于验证逻辑重复性很大。所以会导致许多重复验证代码分布在不同的层中。

+ JavaBean验证模型

![](http://cdn1.hikariblog.cn/JavaBean%E9%AA%8C%E8%AF%81%E6%A8%A1%E5%9E%8B.PNG)

将验证逻辑通过注解约束在域模型上，从而使验证模型从每一层中剥离出来。 

##### JCP,JSR简介

JCP(Java Community Process)成立于1998年，是使有兴趣的各方参与定义Java的特征和未来版本的<span style="color:red">正式过程</span>。

JCP使用JSR(<span style="color:red">Java规范请求</span>，Java Specification Requests)作为<span style="color:red">正式规范文档</span>，描述被提议加入到Java体系中的规范的技术。

有关数据校验的JSR

+ JSR303:Bean Validation 1.0                    JavaEE6
+ JSR349:Bean Validation 1.1                    JavaEE7
+ JSR380:Bean Validation 2.0                    JavaEE8

可以到维基百科中了解更多。

##### Bean Validation与Hibernate Validator

Bean Validation仅仅只是一个规范，而Hibernate Validator则是这个规范的实现

+ Bean Validation 1.0参考实现：Hibernate Validator 4.3.1.Final
+ Bean Validation 1.1参考实现：Hibernate Validator 5.1.1.Final
+ Bean Validation 2.0参考实现：Hibernate Validator 6.0.1.Final

`Hibernate Validator`和`ORM框架Hibernate`只是同一个团队开发的，除此之外没有半毛钱关系。也就是即使你使用的ORM框架是MyBatis也可以使用`Hibernate Validator`。

##### Hibernate Validator与Spring Validation

Spring Validator在Hibernate Validator的基础上，对其进行了<span style="color:red">二次封装</span>，以满足在Spring环境中更简单、高效的对数据进行校验。

### 常用的约束注解

```xml
<dependency>
  <groupId>javax.validation</groupId>
  <artifactId>validation-api</artifactId>
  <version>2.0.1.Final</version>
</dependency>
<dependency>
  <groupId>org.hibernate</groupId>
  <artifactId>hibernate-validator</artifactId>
  <version>6.0.16.Final</version>
</dependency>
<!-- 验证结果支持el表达式，但el表达式是web容器自带的，我们使用junit进行测试所以要引入相关jar包 -->
<dependency>
  <groupId>javax.el</groupId>
  <artifactId>javax.el-api</artifactId>
  <version>3.0.0</version>
</dependency>
<dependency>
  <groupId>org.glassfish.web</groupId>
  <artifactId>javax.el</artifactId>
  <version>2.2.6</version>
</dependency>
```

##### 初级约束注解

- 空值校验类：`@Null`,`@NonNull`,`@NotEmpty`,`@NotBlank`等

  `@Null`:空

  `@NotNull`:不能是null但是可以是空字符串""

  `@NotEmpty`：不能是null和不能是空字符串""

  `@NotBlank`：不能是null和不能是空字符串""，同时不能全部都是空格"  "，即会自动调用trim()去掉字符串中前后空格

- 范围校验类：`@Length`(字符串的长度)、`@Min`/`@Max`(数值校验),`@Size`(集合元素的个数),`Digits`,`@Future`/`@Past`,`@Negative`等

- 其他校验类：`@Email`(邮箱必须为有效邮箱，但是可以为空),`@URL`,`@AssertTrue`,`@Pattern`等

```java
package io.github.forhcl.effectivecoding.validation;

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import javax.validation.ConstraintViolation;
import javax.validation.Validation;
import javax.validation.Validator;
import java.util.Set;

/**
 * @author Hogan_Lee
 * @create 2019-12-25 22:40
 */
@SpringBootTest
class ValidationTest {

    //验证器对象
    private Validator validator;
    //待验证对象
    @Autowired
    private UserInfo userInfo;
    //验证失败的结果集
    Set<ConstraintViolation<UserInfo>> constraintViolationSet;

    /**
     * 初始化操作
     */
    @BeforeEach
    public void init(){
        //初始化验证器
        validator=Validation.buildDefaultValidatorFactory().getValidator();
    }

    @Test
    public void nullValidation(){
//        userInfo.setUserId("");
//        userInfo.setUserName("  ");
//        userInfo.setPassWord("wwweew");
//        userInfo.setEmail("222@163.com");
        //使用验证器对对象进行验证
     constraintViolationSet=validator.validate(userInfo);
    }

    /**
     * 结果打印
     */
    @AfterEach
    public void print(){
        StringBuilder stringBuilder=new StringBuilder();
        constraintViolationSet.stream().forEach(e->{
            stringBuilder.append(e.getMessage());
            stringBuilder.append(';');
        });
        System.out.println(stringBuilder.toString());
    }
}
```

##### 中级约束注解

- 级联验证

  对实体类中存储相同实体类型对象集合类型属性中的对象进行校验。通过`@Valid`注解实现，验证的是默认分组！！如下面的例子

  ```java
  import lombok.Data;
  import org.springframework.stereotype.Component;
  import javax.validation.constraints.*;
  /**
   * 待验证对象实体类
   * 用户信息类
   *
   * @author Hogan_Lee
   * @create 2019-12-25 22:37
   */
  @Data
  @Component
  public class UserInfo {
      /**
       * 好友列表
       */
      @Size(min = 2,max = 4)
      private List<@Valid UserInfo> friends;
  }
  
  ```

- 分组验证

  使用场景：如希望校验注解在某些情况下可用，在某些情况下不可用。

  + 定义一个接口

  ```java
  //用来校验注解分组
      public interface GroupVaild{}
  ```

  + 相应的校验注解上面添加属性`groups`进行分组

  ```java
  @NotBlank(message = "密码不能为空~",groups = {GroupVaild.class})
  ```

  + 使用分组

  注解方式：最后在控制器的处理器方法上`@Validated`中指明使用的校验分组

  ```java
  @Validated(User.GroupVaild.class)
  ```

  校验器方式：

  ```java
  validator=Validation.buildDefaultValidatorFactory().getValidator();
  constraintViolationSet=validator.validate(userInfo, UserInfo.TestGroup.class)
  ```

- 组排序

  同时使用几个检验分组，第一种方式

  - 注解方式：最后在控制器的处理器方法上`@Validated`中指明使用的校验分组，默认分组也得指明出来！！如果有多个参数的情况下，要
  注解在要校验的参数前面，如果只有要校验的参数一个参数，则可以注解在方法上！！

    ```java
    @Validated({User.GroupVaild.class,User.GroupValid1.class})
    ```

    校验器方式：

    ```java
    validator=Validation.buildDefaultValidatorFactory().getValidator();
    constraintViolationSet=validator.validate(userInfo, UserInfo.TestGroup.class,User.GroupValid1.class)
    ```

    这种方式有一个缺点，就是不是先验证一个组之后再验证另外一个组，而是根据属性分布进行校验有的时候我们希望先校验耗时短的分组再校验长的分组，这时候我们就应该控制各个分组执行的前后顺序。所以我们就需要采用第二种方式组排序

  1. 定义组排序接口，在大组里面定义各个小组和它们的前后顺序

     ```java
         @GroupSequence({
                 User.GroupVaild.class,
                 User.GroupValid1.class,
                 Default.class
         })
         public interface Group{}
     ```

  ##### 高级约束注解使用

  一般情况下我们都是对实体类进行校验，但除此之外还有其他的情况。

  ###### 校验参数

  

  ###### 校验返回值

  ###### 校验构造方法