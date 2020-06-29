---
title: a=a+b与a+=b有什么区别？
date: 2019-06-23 09:41:20
tags:
- 面试
- Java
categories: Java基础
---

从<span style="color:red">执行效率</span>和<span style="color:red">类型转换</span>两个方面来比较`a=a+b`和`a+=b`之间的区别

#### 执行效率

&emsp;&emsp;不考虑编译器的优化的话，`a=a+b`的执行效率是低于`a+=b`的，因为它多进行了一步中间变量的操作，而且会多占用一个变量的空间。而Java编译器默认对其进行了优化，优化之后两条语句都当做`a+=b`来执行了，所以实际上是没有任何区别的。

#### 类型转换

我们通过一个例子来说明两者的区别

```java
TestDemo.java
public class TestDemo {
    public static void main(String[] args) {
        int a=2;
        float b=6;
        a+=b;
        //a=a+b;
        a= (int) (a+b);
    }
}
```

&emsp;&emsp;在执行`a=a+b`时，当使用a和b两个数值进行二元操作时（例如上面的`a+b`，a是整数，b是浮点数），先要将两个操作数转换成同一种类型，然后再进行运算。数值类型之间的合法转换如图所示，可见a会被转换成`float`类型。

![](http://cdn1.hikariblog.cn/%E6%95%B0%E5%80%BC%E7%B1%BB%E5%9E%8B%E4%B9%8B%E9%97%B4%E7%9A%84%E5%90%88%E6%B3%95%E8%BD%AC%E6%8D%A2.png)

&emsp;&emsp;此时，再将`a+b`的运算结果赋值给a，明显是非法的、编译器会报错。在`Java`中，允许进行这种数值之间的类型转换。当然，有可能会丢失一些信息。在这种情况下，需要通过强制类型转换（cast）实现这个操作。强制类型转换的语法格式是在圆括号中给出想要转换的目标类型，后面紧跟待转换的变量名。所以我们对`(a+b)`进行强制类型装换操作。但是为什么`a+=b`没有报错呢？我们对TestDemo.class进行<span style="color:blue">反编译</span>：

```java
TestDemo.java
public class TestDemo {
    public TestDemo() {
    }

    public static void main(String[] args) {
        int a = 2;
        float b = 6.0F;
        int a = (int)((float)a + b);
        int a = (int)((float)a + b);
    }
}
```

明显可以看出，`a=(int)(a+b)`等同于`a+=b`，它们编译后的结果是一样的。编译器在编译时会默认为`a+=b`进行强制类型转换。

<span style="color:red">总结：`a=a+b`和`a+=b`的区别主要体现在`a=a+b`需要进行强制类型转换的时候。其它情况下基本是没有差别的。</span>

