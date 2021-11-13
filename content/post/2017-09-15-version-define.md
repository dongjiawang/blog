---
layout: post
title: iOS 利用宏判断系统版本
date: 2017-09-15 13:00:00
categories: ["iOS"]
tags:  ["iOS"]
mathjax: true
---

一般判断系统版本都时候，都会这么写

```
#define SysVersion [[UIDevice currentDevice] systemVersion].floatValue
```

其实系统已经定义了很多类似的宏，不需要我们去再次定义
就在这个路径下

>(__IPHONE_OS_VERSION_MAX_ALLOWED 这个定义是在Simulator /usr/include/AvailabilityInternal.h文件中）

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926122106.png)

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926122043.png)	
使用都时候可以这样

然后使用：

```
#ifdef __IPHONE_10_3
  //iOS10 的新特性代码
#endif
```

或者判断是否大于某个版本：

```
#if __IPHONE_OS_VERSION_MAX_ALLOWED >= __IPHONE_10_3

  //系统版本大于iOS 10.3 的新特性代码
#endif
```

> `__IPHONE_OS_VERSION_MIN_REQUIRED` 
>支持最低的系统版本

>`__IPHONE_OS_VERSION_MAX_ALLOWED`
允许最高的系统版本