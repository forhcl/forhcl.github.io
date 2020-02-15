---
title: 荔枝FM面试题整理
hidden: true
date: 2019-12-18 14:37:51
tags:
categories:
---

1.  笔试

   + 编写一个单例模式例子

     ```java
     /**
      * 单例模式的实现主要有双重校验锁、同步方法、枚举、序列化、静态内部类，掌握一种其它的总得了解一下吧
      * 双重校验锁实现单例模式
      * @author Hogan_Lee
      * @create 2019-12-18 14:55
      */
     public class Singleton {
         //私有构造器
         private Singleton(){}
     
         /**
          * 延迟实例化的对象
          * volatile关键字主要确保操作的可见性和禁止指令的重排序
          */
         //延迟实例化的对象
         private static volatile Singleton uniqueInstance;
         //提供给外界获取单例的入口
         public static Singleton getInstance(){
             if(uniqueInstance==null){
                 /**
                  * 为什么有轻量级锁volatile还要使用synchronized？
                  * 因为volatile关键字只保证了操作的可见性和顺序性，但是确没有保证对共享变量写的原子性
                  * 所以对共享变量进行写操作还是线程不安全的，需要加上synchronized锁
                  * 1.当前线程首先从主存中拷贝共享变量到自己的工作内存
                  * 2.然后对工作内存里的变量进行处理
                  * 3.处理完成后更新变量到主存中
                  *
                  * 好像synchronized保证了对共享变量读和写的可见性，即读写操作都直接对共享内存中的共享变量进行操作
                  * 而volatile保证了读的可见性，对共享变量进行读操作的时候直接对共享内存中的共享变量进行操作，但是
                  * 对于共享变量的写操作即是通过以上三个步骤进行实现的。
                  */
                 synchronized (Singleton.class){
                     /**
                      * 当两个线程同时进行第一次判断的时候都通过了，这时其中一个线程取得锁完成了创建实例的操作
                      * 这时候另外一个线程取得锁，就应该判断一下，避免最后取到的是不同的实例对象
                      */
                     if (uniqueInstance==null){
                         uniqueInstance=new Singleton();
                     }
                 }
             }
             return uniqueInstance;
         }
     }
     ```

   + 1000万个字符串去重

     

2. 一面技术面（50分钟）

   <span style="color:red">主要考察点总结：计算机网络、多线程、集合、数据结构、算法。</span> 

   + 描述TCP\IP连接的建立和断开，**具体状态**和**报文数据**

   + TCP和UDP的区别（不进行详细说明，自己回忆）

     1. 面向连接vs无连接         逻辑连接 建立连接，传输数据，释放连接

        由于TCP是面向连接的所以只能用于端到端的通讯，因为连接双方传输的报文段里面存放着维护逻辑连接的一些信息，所以不支持多播，广播；而UDP支持一对一、一对多、多对一、多对多的交互通信。

     2. 可靠性                   差错检测，错误重传，滑动窗口

        UDP则是只管发送，不理会对方的反馈

     3. 有序性                   接收数据的时候对数据进行排序，保证了~

     4. 速度                     TCP需要保证数据传输的可靠性和有序性，需要做像建立连接释放连接之类的额外工作

     5. 量级                     体现在首部字段UDP8个字节，而TCP20个字节

     6. 传输单位                 报文段/用户数据报

   + 为什么说TCP连接可靠

   + 滑动窗口，以及作用

   + synchronized、volatile、lock的区别和底层实现

   + synchronized修饰普通方法和静态方法有什么区别

     synchronized关键字加到static静态方法上是给Class类上锁，而synchronized关键字加到非静态方法上是给对象上锁。两种锁之间互不干扰，会出现异步的情况，Class锁可以对类的所有对象实例起作用。synchronized(class)等同于synchronized static

   + 除了volatile，还有什么办法可以保证内存可见性

   + 用过哪些多线程的东西

   + 线程池，作用，参数（最大最小）

   + 用过哪些java工具及其作用列举

     单纯Java：maven（构建Java项目、引入和管理第三方Jar包）、junit（编写执行单元测试）、

     lombok：Lombok是一个java库，为我们提供一些常用的注解，在类或者属性上添加这些注解可以在源代码编译时期为我们自动生成一些源代码，从而达到对我们类功能的增强。

     、guava、validation        

     框架插件：pagehelper、swagger

   + 了解nio吗

     

   + 三种Map(Hashmap、TreeMap和LinkedHashMap)的特性、底层实现和区别

     HashMap底层数据结构：数组+链表+红黑树

     LinkedHashMap继承了HashMap具有HashMap的所有特性

     TreeMap底层数据结构是红黑树

   + HashMap和TreeMap的区别

   + hashmap什么时候扩容

     Map在面试中，占据了很大一部分的面试题目，其中以HashMap为主，这些面试题目有的可以说得清楚，有的很难解释清楚，如果是面对面面试的话，建议画一画

     

   + ArrayList和LinkedList区别

     ArrayList和LinkedList之间最大的不同就是底层数据结构的不同，ArrayList底层是数组，而LinkedList底层则是<span style="color:red">双向链表</span>。

     然后就通过某个API作为突破口——>由于两者底层数据结构的不同也导致了操作的API在实现上有所差异，如新增操作ArrayList会判断后进行相应的扩容操作，然后将新增的数据写入到数组中，但是LinkedList不需要进行扩容操作仅仅需要改变插入节点和其前后节点的指向位置关系就可以了。

     然后具体的区别也是和数组和链表之间的区别一样，ArrayList适用于对数据的随机访问，因为对于在指定位置的新增和删除需要后移或前移指定位置后面的数据，而且还要时不时的进行扩容操作，耗费的时间也是特别多的，所以不适合频繁的增删操作。LinkedList则不适用与数据的随机访问，因为需要从链表头逐一进行遍历。但对于增加和删除操作则是较为方便。注意：不适用并不是说不能够实现。两者的优缺点刚好是相反的，我们可以根据使用场景给选择合适的List。<span style="color:red">这是因为在底层上数组的存储空间是连续的，我们可以根据索引找到对应的位置。而链表存储空间则是不连续的，所以丧失了随机访问的能力。</span>
     
     问题无关的拓展：但是Java中的LinkedList的实际大小是使用int来保存的，所以是存在最大容量的。而ArrayList底层的数组指定大小的时候也存在int最大容量
     
     

   

   + 现场编程：1、括号匹配

     ![](荔枝FM面试题整理/括号匹配.png)

     ```java
     import java.util.Stack;
     /**
      * 给定一个"只包括" '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。
      *
      * 有效字符串需满足：
      *
      * 左括号必须用相同类型的右括号闭合。
      * 左括号必须以正确的顺序闭合。
      * 注意空字符串可被认为是有效字符串。
      *
      * 来源：力扣（LeetCode）
      * 链接：https://leetcode-cn.com/problems/valid-parentheses
      * 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
      * @author Hogan_Lee
      * @create 2019-12-18 10:17
      */
     public class Tester {
         public static void main(String[] args) {
             System.out.println(isValid("(]"));
         }
     
         public static boolean isValid(String s) {
             if (s == null && s.length() == 0)
                 return true;
             Stack<Character> stack = new Stack<>();
             for (int i = 0; i < s.length(); i++) {
                 //取单个字符
                 char c = s.charAt(i);
                 if (c=='('|| c=='['|| c=='{')
                     stack.push(c);
                 else {
                     //没有普适性
                     if (stack.isEmpty())
                         return false;
                     char topChar = stack.pop();
                     if (c==')' && topChar!='(') {
                         return false;
                     }
                     if (c==']' && topChar!='[') {
                         return false;
                     }
                     if (c=='}' && topChar!='{') {
                         return false;
                     }
                 }
             }
             return stack.isEmpty();
         }
     }
     ```

   + 现场编程：2、二叉树的非递归排序

     

   + 针对我的项目提出项目架构图

     

   + 1000万字符串去重，如果内存和磁盘都有限怎么办（对我提出用哈希值分而治之的方式不满意）

     

   + 平时如何学习

     

   + 遇到错误时谷歌找不到如何解决

     

   + 看过Spring源码吗？

     

   + 我没有问题了，你有没有什么想提问我的？

     

3. 二面HR面

   + 上一场面试感受
   + 项目经历
   + 实习经历
   + 针对项目经历和实习经历提出一些问题
   + 实习时间安排
   + 薪资
   + 你有没有什么想问我的？





自己的缺点：在学习上犯“完美主义”的毛病！！可以去看波波老师的专栏，通过这个扯缺点