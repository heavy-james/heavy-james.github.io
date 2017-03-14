---
layout: post
title:  Android Unit Test
description: Android 单元测试学习
category: blog
---


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