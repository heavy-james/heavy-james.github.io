---
layout: post
title:  设计模式总览
description: 设计模式总览
category: blog
---

### 招式和内功
软件开发技术也包括一些招式和内功：Java、C#、C++等编程语言，Eclipse、Visual Studio等开发工具，JSP、ASP.net等开发技术，Struts、Hibernate、JBPM等框架技术，所有这些我们都可以认为是招式；而数据结构、算法、设计模式、重构、软件工程等则为内功。招式可以很快学会，但是内功的修炼需要更长的时间。


### 模式的起源
Christopher Alexander博士及其研究团队用了约20年的时间，对住宅和周边环境进行了大量的调查研究和资料收集工作，发现人们对舒适住宅和城市环境存在一些共同的认同规律，Christopher Alexander在著作A Pattern Language: Towns, Buildings, Construction中把这些认同规律归纳为253个模式，对每一个模式(Pattern)都从Context（前提条件）、Theme或Problem（目标问题）、 Solution（解决方案）三个方面进行了描述，并给出了从用户需求分析到建筑环境结构设计直至经典实例的过程模型。

### 模式的定义
模式是在特定环境下人们解决某类重复出现问题的一套成功或有效的解决方案。

### 为什么需要模式
我们可以无数次地重用那些已有的成功的解决方案，无须再重复相同的工作。

### 软件模式的诞生
GoF将模式的概念引入软件工程领域，这标志着软件模式的诞生。

### 软件的模式和建筑模式的区别
软件模式是为了复用软件设计，促使软件系统可以良好地解决具体的问题，同时具有更好的扩展性、可维护性等。它的关联知识是计算机和软件科学。

建筑模式是为了复用建筑设计，促使建筑可以满足舒适性等要求。它的关联知识是建筑科学。

### 软件模式的描述方式

![](http://my.csdn.net/uploads/201204/02/1333301568_8769.gif)

### 软件模式的应用
从1995年至今，无论是在大型API或框架（如JDK、.net Framework等）、轻量级框架（如Struts、Spring、 Hibernate、JUnit等）、还是应用软件的开发中，设计模式都得到了广泛的应用。

### 软件设计模式的衍生作用

* 【软件开发方面】用这些方案将可以让我们避免做一些重复性的工作，也许我们冥思苦想得到的一个“自以为很了不起”的设计方案其实就是某一个设计模式。在时间就是金钱的今天，设计模式无疑会为有助于我们提高开发和设计效率。

* 【协作方面】设计模式提供了一套通用的设计词汇和一种通用的形式来方便开发人员之间沟通和交流，使得设计方案更加通俗易懂。无论你使用哪种编程语言，做什么类型的项目，甚至你处于一个国际化的开发团队，当面对同一个设计模式时，你和别人的理解并无二异，因为设计模式是跨语言、跨平台、跨应用、跨国界的。

* 【软件维护方面】合理使用设计模式并对设计模式的使用情况进行文档化，将有助于别人更快地理解系统。

* 【软件维护方面】大部分设计模式都兼顾了系统的可重用性和可扩展性，这使得我们可以更好地重用一些已有的设计方案、功能模块甚至一个完整的软件系统，避免我们经常做一些重复的设计、编写一些重复的代码。

* 【学习方面】学习设计模式将有助于初学者更加深入地理解面向对象思想，让你知道：如何将代码分散在几个不同的类中？为什么要有“接口”？何谓针对抽象编程？何时不应该使用继承？如何不修改源代码增加新功能？同时还让你能够更好地阅读和理解现有类库（如JDK）与其他系统中的源代码，让你早点脱离面向对象编程的“菜鸟期”。

## 设计模式的学习方法
* 这个设计模式的意图是什么，它要解决一个什么问题，什么时候可以使用它；它是如何解决的，掌握它的结构图，记住它的关键代码；能够想到至少两个它的应用实例，一个生活中的，一个软件中的；这个模式的优缺点是什么，在使用时要注意什么。

* “如果想体验一下运用模式的感觉，那么最好的方法就是运用它们”。严格一点说：不会在开发中灵活运用一个模式基本上等于没学。所以一定要做到：少说多做。

* 设计模式的“上乘”境界：“手中无模式，心中有模式”。 

* 千万不要滥用模式，滥用模式不如不用模式，因为滥用的结果得不到“艺术品”一样的软件，很有可能是一堆垃圾代码。 

* 模式并不能替代人来完成软件系统的创造，它们只不过会给那些缺乏经验但却具备才能和创造力的人带来希望。 


