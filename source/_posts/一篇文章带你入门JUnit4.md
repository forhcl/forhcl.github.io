---
title: 一篇文章带你入门JUnit4
date: 2019-07-06 12:33:21
tags:
- 单元测试
- Java
- JUnit
categories: 单元测试
---

注：文章内容基于 `JUnit4.8`

额，成为标题党了，以前一直认为写博客要特别严谨:disappointed_relieved:这样子写一遍博客耗费的时间特别长，更新的积极性都没有了，而且太过枯燥大家也是没兴趣看下去。然而又怎样呢？严谨一点总没错😏略略略

&emsp;&emsp;时至今日，`JUnit` 已成为 `Java` 中开发单元测试的事实上的标准框架。JUnit官网是这样描述JUnit的：JUnit是用于编写可复用测试的简单框架，是xUnit测试框架的一个子集。xUnit是一套基于测试驱动开发的测试框架。

&emsp;&emsp;__一个典型的单元测试通常可以描述为：“确保方法接受预期范围内的输入，并且为每一次测试输入返回预期的值”。__该描述要求我们通过方法的接口来测试方法的行为。如果我们将 `x` 值传给方法，那么它会返回 `y` 值吗？如果我们改为将 `z` 值传给方法，那么它会正确地抛出异常吗？我们可以了解到，<span style="color:#20B2AA">单元测试通常关注的是一个方法是否遵循它的 `API` 契约中的条款</span>，也就是方法对外提供的 `API` 能否达到预期的结果。可以看出，单元测试用来判断方法是否能达到想要的预期结果，但是对于方法内部的逻辑错误无能为力。比如你要测试的是 `1+1=2`，但是方法内部直接给你 `return 2` 单元测试的角度看来也是正确的。常年霸占leetcode contest全球排名第一的日本选手uwi，在解答[石子游戏][https://leetcode-cn.com/problems/stone-game/]一题时就直接 `return true`，大家可以膜拜一下，但千万不要模仿，面试官会让你回去等通知的​​😂

_一个典型的单元测试包含三个步骤：_

1. 准备测试类及数据
2. 执行要测试的行为
3. 检查结果

&emsp;&emsp;单元测试的核心原则是“任何没有经过自动测试的程序功能都可以当作不存在”。

&emsp;&emsp;__单元测试框架应该遵循以下几条最佳实践规则。__

+  每个单元测试都必须独立于其他所有单元测试而运行；单元测试之间不能有任何的依赖。。

+ 框架应该以单个测试为单位来检测和报告错误；

+ 应该易于定义要运行哪些单元测试。

  __JUnit的设计目标。__

+ 框架必须帮助我们编写有用的测试；

+ 框架必须帮助我们创建具有长久价值的测试；

+ 框架必须帮助我们通过复用代码来降低编写测试的成本。

  __使用JUnit测试__

+ 针对每个单元测试，单独测试类实例和类加载器，以避免副作用。

+ JUnit注解提供了资源初始化和回收方法＠Before 、 ＠BeforeClass、＠After 和 @AfterClass；

+ 各种不同的 assert方法使得检查测试结果更加简单

+ 与各种流行工具（如 Ant 和 Maven ）的整合，以及与流行IDE （如eclipse 、NetBeans、lntelliJ 和 JBuilder ）的整合．

__测试类：__

1. 必须是公共类并且包含一个无参的构造函数。
2. 测试类中至少要有一个测试方法。
3. 最好的做法是按照 `XXXTest` 模式命名测试类。

__测试方法：__

1. 必须使用`@Test`注解，是公共的，不带任何参数，并且返回void类型。
2. 最好的做法是按照 `testXXX` 模式命名测试方法。
3. 每个测试方法都必须独立于其他所有测试方法而运行，可以通过右键对应的测试方法单独运行；测试方法之间不能有任何的依赖。

一个简单的例子如下：

```java
public class Calculator {
    public double add(double number1,double number2){
        return number1+number2;
    }
}

```

对应的测试类

```java
import org.junit.Test;
import static org.junit.Assert.assertEquals;
public class CalculatorTest {
    @Test
    public void testAdd() {
        assertEquals(60, new Calculator().add(50, 10), 0);
    }
}
```

<span style="color:red">注意:单元测试代码是不会出现在最终产品当中的。</span>除非开发的产品是类似于 `leetcode` 之类的在线评测系统，所以我们应该新建一个测试代码目录_test_来存放单元测试，测试代码目录_test_和被测试代码目录的包保持一致。在产品发布时再将测试代码文件夹_test_删除。

------

&emsp;&emsp;<span style="color:red">`JUnit` 在调用（执行）每个 `@Test` 方法之前，为测试类创建一个新的实例</span>，这有助于提供测试方法之间的独立性，并且避免在测试代码中产生意外的副作用。<span style="color:#20B2AA">因为每个测试方法都运行于一个新的测试类实例上，所以我们就不能在测试方法之间重用各个实例变量值。</span>

```java
import org.junit.Test;
import static org.junit.Assert.assertEquals;
public class CalculatorTest {
    private int a;
    @Test
    public void testAdd() {
        assertEquals(60, new Calculator().add(50, 10), 0);
        System.out.println(a++);
    }
    @Test
    public void testXXX() {
        System.out.println(a);
    }
```

以上例子的两个输出都是0。

JUnit采用断言机制，对运行结果作一个预期判断，从而实现测试的目的。

静态导入断言方法，进行测试验证，具体的断言方法查看帮助文档。

```java
import static org.junit.Assert.*;

```

__JUnit的运行流程：__

```java
public class XXXTest {
    @BeforeClass
    public static void setUpBeforeClass() throws Exception {
        System.out.println("Method with @BeforeClass annotation");
    }

    @AfterClass
    public static void tearDownClass() throws Exception {
        System.out.println("Method with @AfterClass annotation");
    }

    @Before
    public void setUp() throws Exception {
        System.out.println("Method with @Before annotation");
    }

    @After
    public void tearDown() throws Exception {
        System.out.println("Method with @After annotation");
    }

    @Test
    public void test1() {
        System.out.println("Test method--test1");
    }

    @Test
    public void test2(){
        System.out.println("Test method--test2");
    }
}
```

运行结果：![运行结果](http://cdn1.hikariblog.cn/Junit_%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C.png)

1.  `@BeforeClass` 修饰的方法会在所有方法被调用前执行，而且该方法是静态的，所以当测试类被加载后接着就会运行它。而且在内存中它只会存在一份实例，它比较适合加载配置文件。
2.  `@AfterClass` 所修饰的方法通常用来对资源的清理，如关闭数据库的连接
3.  `@Before` 会在每个测试方法运行前执行一次。
4.  `@After` 会在每个测试方法运行后执行一次。

__JUnit常用注解:__

`@Test` 将一个普通的方法修饰成为一个测试方法

​	`@Test(expected=异常类)` 预期会抛出什么异常

​	`@Test(timeout=毫秒)` 限定测试运行的时间，超出时间自动终止运行。

`@Ignore` 所修饰的测试方法会被测试运行器忽略。

`@RunWith` 可以更改测试运行器 `org.junit.runner.Runner`

__测试失败的两种情况：__

1. `Failure` 一般由单元测试使用的断言方法判断失败所引起的，表示程序输出的结果和我们预期的结果不一样。
2. `Error` 是由代码异常引起的，它可以产生于测试代码本身的错误，也可能是被测试代码中的一个隐藏的 `bug`。
3. <span style="color:red">测试用例不是用来证明你是对的，而是用来证明你没有错。</span>

&emsp;&emsp;当你需要一次运行多个测试用例时，你就要创建另一个叫做测试套件/测试集（test suite或Suite）的对象。你的测试套件也是一个特定的测试运行器（或者Runner），因此可以像运行测试用例那样运行它。一旦你理解了测试用例，Suite与Runner是如何工作的，你就可以编写你所需要的任何测试了。这3个对象形成了JUnit框架的核心。

&emsp;&emsp;在日常工作中，你只需要编写测试类与测试集，其他类会在幕后帮你完成测试。

+ 测试用例/测试类（`Test class`或`TestCase`或`test case`）——一个包含一个或者多个测试（测试方法）的类，而这些测试就是指那些用 `@Test` 注释的方法。使用一个测试用例，可以把具有公共行为的测试归为一组。<span style="color:red">通常在生产类和测试类之间都存在着一对一的对应关系</span>。
+ 测试套件/测试集（ `Suite` 或 `test suite`）——一组测试，测试套件是一种把多个相关测试用例归入一组的便捷方式。比如，如果你没有为测试类定义一个测试套件，那么JUnit会自动提供一个测试套件，包含测试类中的所有的测试。<span style="color:red">一个测试套件通常将同一个包中的测试类归为一组</span>。
+ 测试运行器（ `Runner` 或 `test runner` ）——执行测试套件的程序。JUnit提供了多种运行器来执行你的测试。

------

### 参数化测试@RunWith(Paramterized.class)

&emsp;&emsp;<span style="color:red">Paramterized（参数化）的测试运行器允许你使用不同的参数多次运行同一个测试。</span>通常在测试中需要考虑多种情况的发生，这时候参数化测试就非常有效。例子如下：

```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.Parameterized;

import java.util.Arrays;
import java.util.Collection;

import static org.junit.Assert.assertEquals;

/**
 * @author Hogan_Lee
 * @create -07-12 8:07
 */
@RunWith(value = Parameterized.class)
public class CalculatorTest {
    private double expected;
    private double valueOne;
    private double valueTwo;

    public CalculatorTest(double expected, double valueOne, double valueTwo) {
        this.expected = expected;
        this.valueOne = valueOne;
        this.valueTwo = valueTwo;
    }

    @Parameterized.Parameters
    public static Collection<Integer[]> getTestParameters() {
        return Arrays.asList(new Integer[][]{
                {2, 1, 1},
                {3, 2, 1},
                {4, 3, 1}

        });
    }

    @Test
    public void testAdd() {
        assertEquals(expected, new Calculator().add(valueOne, valueTwo), 0);
    }

}
```

&emsp;&emsp;要使用Parameterized的测试运行器来运行一个测试用例，那就必须要满足以下要求：

1. 测试用例必须使用 `@RunWith` 注解，并且要将 `Parameterized` 类作为它的参数。也就是将测试运行器更改为  `RunWith(Parameterized.class)` 

2. 声明测试中使用到的实例变量。同时提供一个 `@Parameterized` 注解的方法。这个方法的修饰符和返回值必须是 `@Parameters public static java.util.Collection`,无任何参数。 `Collection` 元素必须是相同长度的数组，这个数组的长度必须要和这个唯一的公共构造函数的参数数量相匹配。

3. 注意：该测试用例没有无参数的构造函数，而有一个可以为测试接受参数的构造函数。

4. 调用提供的参数编写一个 `@Test` 注解的测试。


&emsp;&emsp;这里我们逐步分析上面例子的JUnit的运行过程：首先，JUnit调用了静态方法 `getTestParameters`。接下来，JUnit为 `getTestParameters` 集合中的每个数组进行循环。然后，JUnit调用唯一的公共构造函数。如果存在多个公共构造函数，JUnit就会抛出一个断言错误。JUnit使用由数组元素构成的一系列参数来调用构造函数。在这个示例中，JUnit使用数组中的第一个元素调用有3个参数的构造函数，而这个元素本身就是一个数组：{2,1,1}.然后JUnit会像平时一样调用 `@Test` 方法。JUnit会为 `getTestParameters` 集合中的下一个数组重复以上的过程。

> 如果没有显式指定测试运行器，则默认由JUnit的facade决定使用哪个运行器来运行你的测试。

------

### 测试套件@RunWith(Suite.class)

&emsp;&emsp;用Suite(测试套件 )来组合测试用例。Suite是一个容器，用来将一个或多个测试用例归在一起，并把它们作为一个集合<span style="color:red">一起运行</span>。用注解 `@RunWith` 和 `@SuiteClasses` 注释类，作为测试套件的入口类，这个类中不能包含其它的方法。

`@RunWith(Suite.class)` 指定测试运行器 `Suite.class`。

`@SuiteClasses` 列出我们想要在这个测试集中包含的所有测试类，这些测试类中的所有 `@Test` 方法都将包含到该 `Suite` 中。

```java
import org.junit.runner.RunWith;
import org.junit.runners.Suite;
@RunWith(Suite.class)
@Suite.SuiteClasses(value={CalculatorTest.class})
public class AllTest {
  // the class remains empty,
  // used only as a holder for the above annotations
}
```

&emsp;&emsp;上面展示了如何将多个测试用例组合成测试套件，同理也能将多个测试套件组合成一个主测试套件。<span style="color:blue">除了JUnit的Suite之外，各种构建工具和IDE也提供了运行指定的多组测试用例和测试套件的功能。</span>大家可以根据实际工作需要再做扩展学习。



### 单元测试的必要性

单元测试的主要目的就是验证你的应用程序可以按照预期的方式正常运行，以及尽早地发现错误。

__带来更高的测试覆盖率__

&emsp;&emsp;功能测试应该是任何应用程序所采用的首选测试类型。功能测试可以覆盖大约70%的应用程序代码，相比之下，单元测试能够提供更高的测试覆盖率，以及更容易地模拟错误条件。

__提高团队效率__

&emsp;&emsp;单元测试相比其他类型的测试所测试的单位较小，对每一个方法进行测试，而不必等到其他所有的组件都完成以后。

__监测衰退和减少调试__

&emsp;&emsp;单元测试会告诉你那个方法出错了，而不必每次都去慢慢调试程序，为找出错误所在的地方而耗费大量的时间。

__自信地重构__

&emsp;&emsp;重构之后的代码能在优化的同时继续通过单元测试，类似于测试驱动开发TDD了，但是重构是为了更好的实现，而不是从零开始。单元测试框架xUnit的流行推动了敏捷开发

__改进实现__

&emsp;&emsp;良好的代码实现应该是易于测试的，如果测试代码太长或者笨拙，则说明带测试代码本身的设计不够灵活。

__将预期的行为文档化__

&emsp;&emsp;单元测试本身就是展示方法API最好的示例。



### 对输出语句 `System.out.println()` 进行单元测试。

[System Rules库](https://stefanbirkner.github.io/system-rules/index.html)中存在一个JUnit规则 `StandardOutputStreamLog` 能够基于控制台的输出编写断言。样例代码如下：

```java
import org.junit.Rule;
import org.junit.Test;
import org.junit.contrib.java.lang.system.SystemOutRule;
import static org.junit.Assert.*;
/**
 * @author Hogan_Lee
 * @create 2019-07-20 10:10
 */
public class PrintlnTest {
    @Rule
    public final SystemOutRule systemOutRule = new SystemOutRule().enableLog();
    @Test
    public void testPrintln() {
        System.out.println("Playing");
        assertEquals("Playing\r\n", systemOutRule.getLog());
    }
}
```