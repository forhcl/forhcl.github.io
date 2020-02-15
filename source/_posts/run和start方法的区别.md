---
title: run和start方法的区别
date: 2019-06-10 20:52:48
tags:
- Java
- 多线程
- 面试
categories: Java
---

##### 面试题：`run`和`start`方法的区别

`run`方法和`start`方法是`java.lang.Thread`类中的两个方法。我们通过一个例子来分析它们的不同之处。

```java
public class Test extends Thread {
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Test a = new Test() {
			//重写Thread类的run方法，输出当前线程
			@Override
			public void run() {
				// TODO Auto-generated method stub
				super.run();
				System.out.println("current thread is:" + Thread.currentThread().getName());
			}
		};
		//调用run方法
		a.run();
		//中断执行4秒
		try {
			Thread.sleep(4000);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		//调用start方法
		a.start();
	}
}
```

输出结果如下图：

{% asset_img run和start方法.png run和start方法 %}

`run`方法在主线程中执行，和普通的方法调用一样；而`start`方法在非`main`线程中执行，这中间发生了什么？

start方法的源码如下：

```java
public synchronized void start() {
        /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
        group.add(this);

        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }
```

主要内容就在于`start0`方法的调用，查看`start0`方法源码。

```java
private native void start0();
```

`native`关键字：使用native关键字说明这个方法是原生函数，也就是这个方法是用C/C++语言实现的，并且被编译成了DLL，由java去调用。其对应的源码可以到OpenJDK网站查看。

关于start0这里不做过多介绍，毕竟我不懂cpp，看了头痛。`start0`方法的主要任务的如下：

&nbsp;&nbsp;&nbsp;&nbsp;Thread#start()方法调用Thread#start0()方法，调用JVM_StartThread方法，创建一个子线程，然后在子线程中调用Thread#run()方法。

__<span style="color:red">调用`start`方法会创建一个新的子线程并启动，在子线程中进行`run`方法的调用。</span>__

__<span style="color:red">`run`方法只是`Thread`的一个普通方法调用，还是在主线程中执行。</span>__





#### 关于start方法的拓展

Java的线程是不允许启动两次的，当线程执行完`run`方法或主线程执行完`main`方法进入死亡状态就不能复生了。

{% asset_img Java中线程状态.jpg [Java线程状态] %}

故如果多次调用`start`方法，则会出现异常`Exception in thread "main" java.lang.IllegalThreadStateExceprion`。如下

```java
public static void main(String[] args) {
		// TODO Auto-generated method stub
		Thread thread = new Thread(new Runnable() {
			@Override
			public void run() {
				// TODO Auto-generated method stub
				System.out.println("线程启动");
			}
		});
		thread.start();
		thread.start();
	}
```

运行结果如图

{% asset_img 运行结果.png %}