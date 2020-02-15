---
title: 攻略JDK8新特性--Lambda表达式
date: 2019-11-26 21:56:33
tags:
categories: Java
---

Lambda表达式？

# 一、什么是Lambda表达式？

**Lambda表达式，针对函数式接口的简单实现。**

+ Lambda表达式也被称为箭头函数、匿名函数、闭包。
+ Lambda表达式体现的是轻量级函数式编程思想
+ ‘->’符号是Lambda表达式核心操作符号，符号左侧是操作参数，符号右侧是操作表达式

## Model Code as Data

Model Code as Data，编码即数据，尽可能轻量级的将代码封装为数据。传统的实现方案为接口&实现类（匿名内部类），但是这种实现方式存在语法冗余、this关键字、变量捕获、数据控制不友好等问题。下面通过一个例子来展示传统开发方式中存在的问题。

```java
/*
项目问题：功能接口的设计及优化
  需求环境：线程类的创建
  解决方案：匿名内部类实现
*/
/**
 * @author Hogan_Lee
 * @create 2019-11-26 22:50
 */
public class Demo01 {
    public static void main(String[] args) {
        //1. 传统模式下，新线程的创建
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("threading...."+Thread.currentThread().getName());
            }
        },"Thread01").start();
        //2. jdk8新特性，lambda表达式优化线程模式
        new Thread(() -> {
            System.out.println("lambda threading..." + Thread.currentThread().getName());
        },"Thread02").start();
    }
}

```

在传统的模式下，与线程创建语义相关的只占一小部分，大部分的代码与数据相关，很明显出现了代码的冗余。

为什么要使用lambda表达式？

+ 它不是解决未知问题的新技术
+ 对现有解决方案的语义化优化
+ 需要根据实际需求考虑性能问题 

```java
/**
 * 使用Lambda表达式实现一个死锁
 * @author Hogan_Lee
 * @create 2019-11-27 12:38
 */
public class Deadlock {
    //临界资源1
    private static Object object1=new Object();
    //临界资源2
    private static Object object2=new Object();

    public static void main(String[] args) {
        new Thread(()->{
            synchronized (object1){
                System.out.println(Thread.currentThread().getName()+"取得object1的锁");
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (object2){
                    System.out.println(Thread.currentThread().getName()+"取得object2的锁");
                }
            }
        },"Thread1").start();
        new Thread(()->{
            synchronized (object2){
                System.out.println(Thread.currentThread().getName()+"取得object2的锁");
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (object1){
                    System.out.println(Thread.currentThread().getName()+"取得object1的锁");
                }
            }
        },"Thread2").start();
    }
}
```



## 函数式接口（function interface）

JDK8在传统的语法语义中抽象出来的语义化数据。

+ 函数式接口，就是Java类型系统中的普通接口

+ 函数式接口，是<span style="color:red">只包含一个没有方法体的接口方法</span>（可以同时存在多个默认方法和静态方法）的特殊接口

+ 语义化检测注解：`@FunctionInterface`

+ 如果抽象接口方法是从Object类继承过来的，那么不会计算到函数是接口中的抽象方法数量中

  ```java
  import java.util.List;
  
  /**
   * @author Hogan_Lee
   * @create 2019-11-27 10:21
   */
  @FunctionalInterface
  public interface MarkerInterface {
      /**
       * 通过用户id，查找对应的用户名的 接口
       * @param id 要查找的用户的id
       * @return 返回用户名
       */
      String findOne(int id);
      
  //    List<String> findAll();
  }
  ```

## 默认方法和静态方法

JDK1.8新特性-default方法，打破了Java之前版本对接口的语法限制，允许接口中存在默认的实现。

默认方法是给所有实现该接口的对象增加的通用的方法

静态方法 额，好像和普通的接口方法使用一样

```java
import java.util.List;

/**
 * @author Hogan_Lee
 * @create 2019-11-27 10:21
 */
public interface MarkerInterface {
    /**
     * 通过用户id，查找对应的用户名的 接口
     * @param id 要查找的用户的id
     * @return 返回用户名
     */
    String findOne(int id);

//    List<String> findAll();
    default String getName(){
        return "Name";
    }
    default String getId(){
        return "dd";
    }
    static boolean getTrue(){
        return true;
    }
    static boolean getFalse(){
        return false;
    }
}
```

## 函数式接口（function interface）

函数式接口，只包含一个操作方法

Lambda表达式，只能操作一个方法

<span style="color:red">Java中的Lambda表达式，核心就是一个**函数式接口**的实现。</span>

Lambda表达式常见用途就是在简化匿名内部类，注意只有当这个匿名内部类实现的是一个函数式接口的时候才可以使用lambda表达式。

()->{}

()中为接口方法的参数

{}为具体实现

####  JDK中常见的函数式接口

1. java.lang.Runnable
2. java.lang.Comparable
3. java.lang.Comparator
4. java.io.FileFilter
5. more......

针对lambda表达式，jdk8提供了java.util.function包，提供了常用的函数式功能接口（这里有点懵，自己自定义一个方法不行吗！？？ ）

+ `java.util.function.Predicate<T>` 接受参数对象T，返回一个boolean类型结果

  ```java
  Predicate<String> predicate=(String username)->{
       return true;
  };
  System.out.println(predicate.test("加油"));
  ```

+ `java.util.function.Consumer<T> `  接收参数对象T，不返回结果

  ```java
  Consumer<String> consumer=(String usenamer)->{
      System.out.println(usenamer);
  };
  consumer.accept("加油");
  ```

+ `java.util.function.Function<T,R>`  接受参数对象T，返回结果对象R

  ```java
  Function<String,String> function=(String message)->{
      return message;
  };
  System.out.println(function.apply("消息~"));
  ```

+ `java.util.function.Supplier<T>` 不接受参数，提供T对象的创建工厂

  ```java
  Supplier<String> supplier=()->{
      return UUID.randomUUID().toString();
  };
  System.out.println(supplier.get());
  System.out.println(supplier.get());
  System.out.println(supplier.get());
  ```

+ `java.util.function.UnaryOperator<T>` 接受参数对象T，返回结果对象T

  比如前端传递一张图片过来之后，我们要对图片进行处理

  ```java
  UnaryOperator<String> unaryOperator=(String img)->{
      return img+="[100*200]";
  };
  System.out.println(unaryOperator.apply("原图--"));
  ```

+ `java.util.function.BinaryOperator<T>` 接受两个T对象，返回一个T对象结果

  ```java
  BinaryOperator<Integer> binaryOperator=(Integer a,Integer b)->{
      return Math.max(a,b);
  };
  System.out.println(binaryOperator.apply(4,5));
  ```

## Lambda表达式基本语法

**1) 声明：就是和lambda表达式绑定的接口类型（必须的，不然怎么知道你要实现的是哪一个接口）**
**2) 参数：包含在一堆圆括号中，和绑定的接口中的抽象方法中的参数个数及顺序一致**  

(注意：

+ 只有一个参数情况下括号可以省略掉；

+ 在Lambda表达式中可以省略参数的类型，JVM会在运行时根据Lambda表达式绑定的接口抽象方法参数类型，自动推导对应的参数类型)

**3) 操作符：->**
**4) 执行代码块：包含在一对大括号中，出现在操作符的右侧**

（注意：

+ 执行代码块如果只有一行，可以省略大括号
+ 执行代码只有一行且省略大括号的情况下，有返回值时可以省略return关键字，单行代码的执行结果会自动返回）；如果添加了大括号，或者有多行代码，必须通过return关键字返回执行结果。

**[接口声明]=(参数)->{执行代码块};**

例子如下：

```java
/**
 * @author Hogan_Lee
 * @create 2019-11-26 22:50
 */
public class Demo {
    public static void main(String[] args) {
        //1. Lambda表达式的基本语法
        /*
        1) 声明：就是和lambda表达式绑定的接口类型
        2) 参数：包含在一堆圆括号中，和绑定的接口中的抽象方法中的参数个数及顺序一致
        3) 操作符：->
        4) 执行代码块：包含在一对大括号中，出现在操作符的右侧

        [接口声明]=(参数)->{执行代码块};
         */
        ILambda1 iLambda1 = () -> System.out.println("ILambda1");
        iLambda1.test();

        ILambda2 iLambda2=n->{
            System.out.println(n+"say:my year's old is ");
        };
        iLambda2.test("小明");

        ILambda3 iLambda3=message-> message;
        System.out.println(iLambda3.test("消息"));
        System.out.println(iLambda3.test("消息"));
    }

    //没有参数，没有返回值的lambda表达式绑定的接口
    interface ILambda1 {
        void test();
    }

    //带有参数，没有返回值的lambda表达式
    interface ILambda2{
        void test(String name);
    }

    //带有参数，带有返回值的Lambda表达式
    interface ILambda3{
        String test(String message);
    }
}
```

## 变量捕获

+ 匿名内部类中的变量捕获
+ lambda表达式的变量捕获

```java
/**
 * @author Hogan_Lee
 * @create 2019-11-27 20:16
 */
public class App2 {
    String s1="全局变量";

    //1.匿名内部类型中对于变量的访问
    public void testInnerClass(){
        String s2="局部变量";
        new Thread(new Runnable() {
            String s3="内部变量";
            @Override
            public void run() {
                //访问全局变量
//                System.out.println(this.s1);  this关键字~表示是当前内部类型的对象
                System.out.println(s1);

                //访问局部变量
                System.out.println(s2);  //不能对局部变量进行数据的修改[final]

                //访问内部变量
                System.out.println(s3);
                System.out.println(this.s3);
            }
        }).start();
    }

    //1.Lambda表达式变量捕获
    public void testLambda(){
        String s2="局部变量lambda";
        new Thread(()->{
           String s3="内部变量lambda";

           //访问全局变量
            System.out.println(this.s1); //this关键字，表示的就是所属方法所在类型的对象

            //访问局部变量
            System.out.println(s2);
//            s2="hello"; 不能进行数据的修改，默认推导变量的修饰符：final

            //访问内部变量
            System.out.println(s3);
        }).start();
    }

    public static void main(String[] args) {
        App2 app2=new App2();
//        app2.testInnerClass();
        app2.testLambda();
    }
}
```

总结：lambda表达式优化了匿名内部类访问全局变量的时候对于this关键字的使用；同时无论是匿名内部类还是Lambda表达式都不可以对局部变量进行修改，默认将巨变变量推导为fianl类型。