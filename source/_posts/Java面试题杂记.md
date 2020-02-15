---
title: Java面试题杂记
hidden: true
date: 2020-01-07 14:58:10
tags:
categories:
---

#### `final`和`static final`之间的区别，为什么标准库源码常使用`static final`？

通过`static final`定义的常量只能在定义的时候进行赋值，在<span style="color:red">类加载的时候</span>被初始化。在整个使用过程中只能被初始化一次，保证了在类的所有实例对象中唯一。

通过`final`定义的常量可以在定义的时候进行赋值或者在构造器中进行赋值，当在<span style="color:red">实例化的时候</span>进行初始化时同一个类的不同实例对象中可能会有不同的值，因此`final`只能保证在实例对象中的唯一性。

