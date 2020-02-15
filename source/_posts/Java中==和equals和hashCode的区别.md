---
title: Java中==和equals和hashCode的区别
date: 2019-06-10 16:46:49
tags:
- Java
- 面试
categories: Java
---

#### <span style="color:#CD0000">==运算符</span>

两种情况：

1）基本类型数据：比较两个变量中存储的值是否相等。

2）引用类型：比较两个变量存储的对象引用是否相等，则实例对象的存储地址是否一致。



#### <span style="color:#CD0000">equals方法</span>

##### Object类中的equals方法

​	__`Object`类中的`equals`方法用于检测一个对象是否等于另外一个对象。__源码如下：

```java
public boolean equals(Object obj) {
        return (this == obj);
}
```

​	分析可知，`Object`类中的`equals`方法判断两个对象是否具有相同的引用，如果两个对象有相同的引用，则判定它们相等；如果它们们存储的对象引用（对象的存储地址）一样，则它们指向同一个实例对象。这种比较是完全没有实际意义的，就像证明“你妈是你妈”一样荒唐。<span style="color:#00008B">实际情况下，对象的比较应该由内容导出，当两个对象中存储的指定内容一样。就判定它们相等。所以当需要进行对象的比较时，应该在自定义类中根据实际需求去重写`Object`类中的`equals`方法。</span>

##### 以下情景没有重写equals方法的必要：

1. 类的每一个实例都是固定唯一的，例如 `Thread` 这种代表活动实体而不是值的类。
2. 类的实例之间没有比较内容相等的需要，则无需重写 `equals` 方法。
3. 父类已经重写equals方法，且父类的 `equals` 方法实现适用于子类。
4. 类是私有的或包级私有的，以及你可以确定它的 equals 方法永远不会被调用时。

##### 完美equals方法的重写建议

```java
// 显式参数命名为otherObject，稍后需要将它转换成另一个叫做other的变量。
public boolean equals(Object otherObject){
/* 检测this和otherObject是否引用同一个对象，这条语句只是一个优化。实际上，这是一种经常采用的形式。因为计算这个等式要一个一个地比较类中的域所付出的代价小得多。 */
    if(this==otherObject)
        return true;
// 检测otherObject是否为null，如果为null，返回false。这项检测是很必要的。
    if(otherObject==null)
        return false;
/* optional:比较this与otherObject是否属于同一个类。如果equals的语义在每个子类中有所改变，就使用getClass检测: */
    if(getClass()!=otherObject.getClass())
        return false;
/* optional:如果所有的子类都拥有统一的语义，就使用instanceof检测: */
    if(!(otherObject instanceof ClassName))
        return false;
// 将otherObject装换成相应的类类型变量：向下转型
    ClassName other=（ClassName）otherObject；
/* 现在开始对所有需要比较的域进行比较了。使用==比较基本类型域，使用Objects.equals()比较对象域。如果所有的域都匹配，就返回true；否则返回false */
    return field1==other.field1&&Object.equals(field2,other.field2)&&···;
}
//提示：对于数组类型的域，可以使用静态的Array.equals方法检测相应的数据元素是否相等。为了防备field2和other.fiield2可能为null的情况，需要使用Objects.equals方法。如果两个参数都为null，Objects.equals(a,b)调用将返回true；如果其中一个参数为null，则返回false；否则，如果两个参数都不为null，则调用a.equals(b)。
```



#### <span style="color:#CD0000">hashCode方法</span>

**散列码（hash code）是由对象导出的一个整型值。**`Object`中的`hashCode`方法是由对象的存储地址导出的。所以和`equals`方法一样，当需要进行对象的比较时，应该在自定义类中根据实际需求去重写`Object`类中的`hashCode`方法。



#### <span style="color:#CD0000">hashCode方法和equals方法的区别和使用场景</span>

###### 区别

既然两个方法最后的作用都是用于对象的比较，那为什么还要同时存在呢？可以从两方面进行说明

1. 性能

   __`hashCode`效率高，`equals`效率低。__

   `equals`方法中封装着比较全面复杂的算法步骤，每次比较都需要进入方法中执行这些算法步骤，效率低下。

   `hashCode`方法则导出一个整型的散列码，将对象的比较转化成整型值的比较，效率较高。

2. 可靠性

   __`equals`可靠，`hashCode`不可靠。__

   用`equals`方法比较的两个对象相等，则这两个对象通过`hashCode`方法导出的散列码一样。

   而当两个对象通过`hashCode`方法导出的散列码一致时，由`equals`方法判断两个对象不一定相等。

###### 使用场景

1. 当进行比较的对象的数量不多时，则直接使用`equals`方法进行比较。
2. 当进行比较的对象的数量较多时，则先使用`hashCode`方法进行比较，如果散列码不相等，则两个对象肯定不相等，当散列码相等时，再调用`equals`进行比较，如果`equals`比较的出两个对象相等，则这两个对象就真的相等了。如不允许重复的集合在添加数据时，则会 执行这一步！！

{% asset_img 不可重复集合对象的插入.jpg 不可重复集合对象的插入 %}

#### <span style="color:#CD0000">扩展</span>

阿里巴巴开发规范中明确规定：

{% asset_img 关于hashCode和equals的处理.jpg 关于hashCode和equals的处理 %}