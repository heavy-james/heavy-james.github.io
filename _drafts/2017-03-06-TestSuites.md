---
layout: post
title: TestSuites
description: 单元测试主要维护流程分解
category: blog
---


## Test Suites
### 进入流程
入口数据-->取频道数据-->刷新频道列表-->更新频道列表焦点-->取视频列表数据（第一个频道-->刷新视频列表-->播放默认位置视频-->更新播放状态（title、视频列表焦点）

### 交互流程
1. 切换频道列表焦点  
切换频道焦点-->[取视频列表数据]-->更新视频列表
2. 切换视频列表焦点  
切换视频列表焦点-->[取频道数据、取下一组数据]-->[刷新视频列表]
3. 切换播放器菜单频道列表焦点
切换播放器菜单频道列表焦点-->[取视频列表数据]-->更新播放器菜单数据
4. 切换播放器菜单视频列表焦点
切换播放器菜单视频列表焦点-->[取下一组数据]-->更新播放器菜单数据
5. 点击视频列表  
点击视频列表-->播放视频

### 强关联流程
更新视频列表<-->更新频道列表焦点

### 自动播放流程
1. 播放结束
频道最后一个视频播放结束-->[取频道数据、取下一组数据]-->更新视频列表-->播放视频
2. 播放开始
播放开始-->更新播放状态

### 异常处理流程
1. 进入流程网络请求失败-->拉起错误窗口

