---
layout: post
title: iOS 关闭自动锁屏
date: 2016-07-08 11:32:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

## 关闭自动锁屏

```objectivec
[UIApplication sharedApplication].idleTimerDisabled = YES;
```

## 关闭距离感应（防止黑屏）

```objectivec
 [UIDevice currentDevice].proximityMonitoringEnabled = NO;
// 不允许临近检测
```







写项目的时候一进入视频播放界面就会被手指挡住距离感应黑屏了，试了好久，才发现多加了一行代码。

想要试试更多的系统的监测可以看看下面这篇

[IOS设备 UIDevice 获取操作系统 版本 电量 临近手机触发消息检测](http://www.2cto.com/kf/201404/290796.html)