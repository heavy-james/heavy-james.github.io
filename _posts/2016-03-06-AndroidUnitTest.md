---
layout: post
title: 初探 Android Unit Test
description: Android 单元测试学习
category: blog
---

## 初探 Android Unit Test
转到Android Studio开发之后，每次新建一个空项目，AS总会默认创建androidTest和test目录，感觉goole还是推荐使用单元测试来提高代码质量的，所以决定学习一下Android上的单元测试的使用，以及它的利弊。

### 几个问题
* java单元测试，被测试的类都是自己封装的，直接运行在虚拟机中，主要测试方法的逻辑是否符合预期，各种临界条件是否有处理，测试逻辑容易理解，且结果非常容易收集。对比到android test，主要测试什么，怎么测试，测试结果如何收集。  

### 最原始的代码测试
假设有一个类：

    class Caculator{
        plubic int plus(int a, int b){
		    return a + b;
	 	}
	 	
	 	public int minus(int a, int b){
	 		return a - b;
	 	}
	
		public int multiple(int a, int b){
			return a * b;
		}
		
		public int devide(int a, int b){
			return a / b;
		}
    }  
    
如果在它被使用前，要测试这个类的方法是否在各种条件下正确，我们可以给它写一些测试代码：  

    class TestClass{
    	public static void main(String[] args){
    		Caculator caculator = new Caculator();
    		int a = 10;
    		int b = 3;
    		int result = caculator.plus(a ,b);
    		if(result == 13){
    			System.out.println("Caculator plus test succed.")
    		}else{
    			System.out.println("Caculator plus test faild.")
    		}
    		result = caculator.minus(a ,b);
    		if(result == 7){
    			System.out.println("Caculator minus test succed.")
    		}else{
    			System.out.println("Caculator minus test faild.")
    		}
    		result = caculator.multiple(a ,b);
    		if(result == 30){
    			System.out.println("Caculator multiple test succed.")
    		}else{
    			System.out.println("Caculator multiple test faild.")
    		}
    		result = caculator.divide(a ,b);
    		if(result == 3){
    			System.out.println("Caculator divide test succed.")
    		}else{
    			System.out.println("Caculator divide test faild.")
    		}
    	}
    }

然后我们运行这个测试类，可以根据控制台输出查看方法的执行结果。很明显这样会存在一些问题。 
 
* 测试代码很多，看起来不直观  
* 覆盖率和错误率等不能直接统计，测试结果不够直观
* 如果有多个类和方法，每个类都需要重复if判断和日志打印，增加工作量
* 如果有多个类和方法，每个类的测试代码都写在main里，不利于维护
* 总是需要手动填写参数，局限性很强

所以需要一个测试框架，去完成一些通用的工作，如测试结果统计、简化测试代码的编写维护、自动填写参数等。

### JUnit框架

#####  重要的几个类：

| 序号	| 类的名称	| 类的功能 |  
| ----| ---- | ---- |
| 1 | Assert | assert 方法的集合 |
| 2 | TestCase | 一个定义了运行多重测试的固定装置|
| 3 | TestResult | TestResult 集合了执行测试样例的所有结果|
| 4 | TestSuite | TestSuite 是测试的集合|

##### Assert类的方法：

| 序号 | 方法 | 描述 |
| ----| ---- | ---- |
| 1 | void assertEquals(boolean expected, boolean actual) | 检查两个变量或者等式是否平衡 |
| 2 | void assertFalse(boolean condition) | 检查条件是假的 |
| 3	 | void assertNotNull(Object object) | 检查对象不是空的
| 4 |	void assertNull(Object object) |检查对象是空的 |
| 5 |	void assertTrue(boolean condition) | 检查条件为真 |
| 6 | void fail() | 在没有报告的情况下使测试不通过 |

##### TestCase类的方法：

| 序号 | 方法 | 描述 | 
| ----| ---- | ---- |
| 1 | int countTestCases() | 为被run(TestResult result) 执行的测试案例计数
| 2 | TestResult createResult() | 创建一个默认的 TestResult 对象 |
| 3 | String getName() | 获取 TestCase 的名称 |
| 4 | TestResult run() | 一个运行这个测试的方便的方法，收集由TestResult 对象产生的结果 |
| 5	 | void run(TestResult result) | 在 TestResult 中运行测试案例并收集结果
| 6 |	void setName(String name) | 设置 TestCase 的名称 |
| 7	 | void setUp() | 创建固定装置，例如，打开一个网络连接 |
| 8 | void tearDown() | 拆除固定装置，例如，关闭一个网络连接 |
| 9 |	String toString() | 返回测试案例的一个字符串表示 |

##### TestResult类的方法：

| 序号 | 方法 | 描述 |
| ---- | ---- | ---- |
| 1 | void addError(Test test, Throwable t) |在错误列表中加入一个错误 |
| 2	 | void addFailure(Test test, AssertionFailedError t) | 在失败列表中加入一个失败 |
| 3	 | void endTest(Test test) |显示测试被编译的这个结果 |
| 4 | int errorCount() | 获取被检测出错误的数量 |
| 5 | Enumeration errors() | 返回错误的详细信息 |
| 6 | int failureCount() | 获取被检测出的失败的数量 |
| 7 | void run(TestCase test) | 运行 TestCase |
| 8 | int int runCount() | 获得运行测试的数量 |
| 9 | void startTest(Test test) | 声明一个测试即将开始 |
| 10 | void stop() | 标明测试必须停止 |

##### TestSuites类的方法：

| 序号	| 方法 | 描述 |
| ----| ---- | ---- |
| 1	 | void addTest(Test test) | 在套中加入测试。 |
| 2	 | void addTestSuite(Class<? extends TestCase> testClass) | 将已经给定的类中的测试加到套中。
| 3 |	int countTestCases() | 对这个测试即将运行的测试案例进行计数。
| 4 |	String getName() | 返回套的名称。
| 5 |	void run(TestResult result) | 在 TestResult 中运行测试并收集结果。
| 6	 | void setName(String name) | 设置套的名称。
| 7 |	Test testAt(int index) | 在给定的目录中返回测试。
| 8 |	int testCount() | 返回套中测试的数量。
| 9	 | static Test warning(String message) | 返回会失败的测试并且记录警告信息。|
  
&nbsp;  

##### 用JUnit实现的CaculatorTest：
  
&nbsp; 
    
    package test;

    import org.junit.AfterClass;
    import org.junit.Assume;
    import org.junit.BeforeClass;
    import org.junit.Ignore;
    import org.junit.Test;
    import junit.framework.Assert;
    import model.Caculator;

    //@Ignore
    public class MyTestCases {
	    static int a;
	    static int b;
	    static Caculator caculator;

	    @BeforeClass
	    public static void setUp() {
		    a = 10;
		    b = 3;
		    caculator = new Caculator();
		    System.out.println("MyTestCases setUp");
	    }

	    @AfterClass
	    public static void tearDown() {
		    // do some clean work
		    System.out.println("MyTestCases tearDown");
	    }

	    @Test
	    public void plus() {
		    Assume.assumeTrue(13 == caculator.plus(a, b));
	    }

	    @Ignore("ignored")
	    @Test
	    public void minus() {
		    System.out.println("MyTestCases testMinus");
		    Assert.assertEquals(7, caculator.minus(a, b));
	    }

	    @Test(timeout = 1000)
	    public void multiple() {
		    Assert.assertEquals(30, caculator.multiple(a, b));
	    }

	    @Test(expected = ArithmeticException.class)
	    public void devide() {
		    Assert.assertEquals(3, caculator.devide(a, b));
	    }

    }

执行结果：  
![](https://heavy-james.github.io/images/android_test/result_junit.jpeg)  

#### Android Test

现在我们把这个单元测试，用Android Studio来运行起来。  

Android Studio 在创建工程的时候，默认创建了两个目录用于存放测试代码，test用于存放和framework无关的java类测试代码，可以直接运行在虚拟机上，androidTest则用于存放framework相关类的测试代码。移动完代码，解决一下包名和导入，最终的效果如图：

![](https://heavy-james.github.io/images/android_test/file_structure.png)

这里没有复制TestRunner类，是因为AS支持直接运行JUnit的TestSuite和TestCase类，也可以在项目的根目录，运行./gradlew test来执行Junit测试。结果在Eclipse中运行一样，但是多了覆盖率统计。

![](https://heavy-james.github.io/images/android_test/result_as_run.png)
![](https://heavy-james.github.io/images/android_test/result_coverage_as_run.png)
  
如果用./gradlew test来运行，则是html页面的的统计展示，放在pp/build/reports/tests/debug/index.html更加直观。

![](https://heavy-james.github.io/images/android_test/result_html.png)




### 单元测试的目标函数主要有三种：

* 有明确的返回值，如上图的dosomething(Boolean param)，做单元测试时，只需调用这个函数，然后验证函数的返回值是否符合预期结果。
* 这个函数只改变其对象内部的一些属性或者状态，函数本身没有返回值，就验证它所改变的属性和状态。
* 一些函数没有返回值，也没有直接改变哪个值的状态，这就需要验证其行为，比如点击事件。
* 既没有返回值，也没有改变状态，又没有触发行为的函数是不可测试的，在项目中不应该存在。
* 当存在同时具备上述多种特性时，建议采用多个case来真对每一种特性逐一验证，或者采用一个case，逐一执行目标函数并验证其影响。
* 构造用例的原则是测试用例与函数一对一，实现条件覆盖与路径覆盖。Java单元测试中，良好的单元测试是需要保证所有函数执行正确的，即所有边界条件都验证过，一个用例只测一个函数，便于维护。在Android单元测试中，并不要求对所有函数都覆盖到，像Android SDK中的函数回调则不用测试。