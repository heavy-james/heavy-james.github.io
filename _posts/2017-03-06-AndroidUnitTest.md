---
layout: post
title:  Android Unit Test
description: Android 单元测试学习
category: blog
---

#  Android Unit Test
转到Android Studio开发之后，每次新建一个空项目，AS总会默认创建androidTest和test目录，感觉goole还是推荐使用单元测试来提高代码质量的，所以决定学习一下Android上的单元测试的使用，以及它的利弊。

* [android test 官方指南](https://developer.android.com/studio/test/index.html)

## 概要
* Android Test是基于Junit框架的，它既可以运行在本机的JVM上，也可以运行在设备或者安卓虚拟机上。

![](https://heavy-james.github.io/images/android_test/test_types_2x.png) 

* Roboletric  
如果运行在JVM上，当需要测试android framework相关的类时，虽然android sdk提供了android.jar供Junit加载相关类，但是android 的 Activity Service等组件不能直接实例化，生命周期也不会自动执行，view等控件也不能直接实例化，自动填充，所以需要Roboletric框架来帮助实例化android的组件，执行生命周期回调，加载控件等。
* mock 机制  
当测试一些依赖关系比较复杂的类时，如果实例化它所依赖的全部对象，可能会导致测试代码编写难度过高，或者工作量过大，这时可以使用mock机制，创建一些未完全实例化的对象，给它指定一些必要的行为，来满足我们测试需求。比较流行的mock框架有mockito、powermock等。
* jacoco   
如果用gradle执行test任务，在本机JVM上运行Android Test，android gradle插件还会在app/build/reports目录下，生成html页面的测试结果。如果要查看覆盖率，还可以使用jacoco插件。
* espresso框架  
android 官方的测试框架，提供了整套的API来操作组件和控件，用以在android环境下模拟交互动作、获取组件状态。

## JUnit 框架
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

### JUnit框架概览

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

#### JUnitTest机制

JUnit定义了一些java 运行时级别的注解，在编写测试代码的时候，需要带上注解，JUnit实例化Runner对象时，会创建一个TestClass对象，使用了注解的类和方法就是在这里被收集和绑定的：

构造方法中创建了两个map，key是自定义的Annotaiton,value是对应的method和field的list。并调用扫描方法收集。

    public TestClass(Class<?> clazz) {
    	......

        Map<Class<? extends Annotation>, List<FrameworkMethod>> methodsForAnnotations =
                new LinkedHashMap<Class<? extends Annotation>, List<FrameworkMethod>>();
        Map<Class<? extends Annotation>, List<FrameworkField>> fieldsForAnnotations =
                new LinkedHashMap<Class<? extends Annotation>, List<FrameworkField>>();

        scanAnnotatedMembers(methodsForAnnotations, fieldsForAnnotations);

       ......
    }

遍历classes，收集信息：

    protected void scanAnnotatedMembers(Map<Class<? extends Annotation>, List<FrameworkMethod>> methodsForAnnotations, Map<Class<? extends Annotation>, List<FrameworkField>> fieldsForAnnotations) {
        for (Class<?> eachClass : getSuperClasses(clazz)) {
            for (Method eachMethod : MethodSorter.getDeclaredMethods(eachClass)) {
                addToAnnotationLists(new FrameworkMethod(eachMethod), methodsForAnnotations);
            }
            
            for (Field eachField : getSortedDeclaredFields(eachClass)) {
                addToAnnotationLists(new FrameworkField(eachField), fieldsForAnnotations);
            }
        }
    }
    
调用Runner的run方法，将结果收集到Result中：    
 
    public Result run(Runner runner) {
        Result result = new Result();
        RunListener listener = result.createListener();
        notifier.addFirstListener(listener);
        try {
            notifier.fireTestRunStarted(runner.getDescription());
            runner.run(notifier);
            notifier.fireTestRunFinished(result);
        } finally {
            removeListener(listener);
        }
        return result;
    }
    
最终调用的是Runner的runChild方法，我们的测试方法在这里被执行：
    
       @Override
    protected void runChild(final FrameworkMethod method, RunNotifier notifier) {
        Description description = describeChild(method);
        if (isIgnored(method)) {
            notifier.fireTestIgnored(description);
        } else {
            runLeaf(methodBlock(method), description, notifier);
        }
    }
## Android Test

### 使用Android Test Library

假如现在在APP中也有一个Caculator,也给它编写了同样的测试代码，可以用 Android JUnit Runner来运行。

* 首先在app build.gradle中添加 
  
    
        dependencies {
            // Required -- JUnit 4 framework
            testCompile 'junit:junit:4.12'
        }
        
* 将测试代码复制到main/test/java下

* 选中TestSuit类，直接运行，在AS的run 窗口中可以查看结果：

![](https://heavy-james.github.io/images/android_test/result_as_run.png)

* 在Coverage窗口中查看覆盖率信息

![](https://heavy-james.github.io/images/android_test/result_coverage_as_run.png)
  
* 如果用./gradlew test来运行，打开app/build/reports/tests/debug/index.html查看结果。

![](https://heavy-james.github.io/images/android_test/result_html.png)


### Roboletric框架
如果我们要在JVM上直接测试android组件、控件，直接实例化是不能够正常运行的，组件的生命周期不会自动回调，控件也不会自动填充，需要使用Roboletric框架来模拟android 环境。

* Roboletric 使用

        public class MainActivityTest {
            private ActivityController<MainActivity> mController;
            private MainActivity activity;

            @Before
            public void setUp() throws Exception {
    
            // Create new activity
            this.mController = buildActivity(MainActivity.class);
            this.activity = this.mController.create().postCreate(null).start().resume().visible().get();
            }

            @After
            public void tearDown() throws Exception {

                // Destroy activity
                this.mController.pause().stop().destroy();
                this.activity.finish();
            }

            @Test
            public void testOnCreateNotNull() {
                assertThat(this.activity).isNotNull();
            }
            
            
            @Test
            public void clickButton() {
                Button button = (Button) activity.findViewById(R.id.button);
                assertNotNull("test button could not be found", button);
                assertTrue("button does not contain text 'Click Me!'", "Click Me".equals(button.getText()));
                }
            }




* Roboletric 原理
 大致上是利用shadow原理（代理模式），封装了android真实的类，并且拓展了原来类的一些逻辑，以解除对android运行环境的依赖。

### mockito框架
* mockito 使用  

        List<String> myMockList = Mockito.mock(List<String>.class);
        Mockito.when(myMockList.get(0)).thenReturn("Hello, I am James");
        assertEquals("Hello, I am James", myMockList.get(0));

* [mockito 原理](http://blog.csdn.net/jamesdoctor/article/details/50019103)

   ![](https://heavy-james.github.io/images/android_test/mock_mock.png)   
        
### Insturmentation
[to android developer](https://developer.android.com/reference/android/app/Instrumentation.html)
[原理简介](http://www.cnblogs.com/zhitang2009/p/3423068.html)  
当运行android.test.InstrumentationTestRunner的时候，android.app.Instrumentation这个类会最先初始化，然后加载app代码，并提供系统的所有与app交互的接口。如图所示：  
![](https://heavy-james.github.io/images/android_test/instumenation_explain.png)
   
### Espresso 框架
Espresso是android提供的，基于intrumentation的一套UI测试框架。它提供了view的匹配、搜索、操作和测试等功能，易于使用。

* Espresso 框架结构示意图
![](https://heavy-james.github.io/images/android_test/espresso-cheat-sheet-2.1.0.png)
* [Espresso 机制源码分析](http://www.jianshu.com/p/1fb248b77b36)

### jacoco 报告  
* jacoco 执行结果  
![](https://heavy-james.github.io/images/android_test/reports_jacoco.png)
* [jacoco 原理介绍](http://tmq.qq.com/2016/08/java-code-coverage-tools-jacoco-principle/)
* [jacoco gradle插件使用](http://www.dzwanli.com.cn/?p=1374#codesyntax_3)

### Android 单元测试的使用场景

android test 非常灵活，可以分别以case、suite、包粒度执行，或者全部执行，这点让它变得更加实用。

#### 针对性测试
* 正在开发的一个需求，为了避免重复的手动测试，可以编写一个针对性的单元测试。
* 某些通用组件，建立单元测试可以保证稳定性，也可以起到文档的作用。

#### 保障性测试
* APP的主要路径，任意版本都需要保证稳定性，编写单元测试可以避免重复劳动。

#### 性能测试
* 利用timeout属性，可以准确地测试某些场景消耗的时间

#### 兼容性测试
* 某些可能存在兼容问题的场景，单元测试也可以避免重复劳动