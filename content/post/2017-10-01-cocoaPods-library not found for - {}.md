---
layout: post
title: cocoaPods-library not found for - {}
date: 2017-10-01 16:05:00
categories: ["CocoaPods"]
tags:  ["iOS"]
mathjax: true
---


更换电脑后 check out 项目后，发现运行不起来了
Debug 的时候提示 `ld: library not found for - AFNetWorking`， `Release`的时候没问题，仔细寻找后，发现所有的第三方库都在提示这个错误。
我们的第三方库都是用`cocoaPods`管理，所以应该就是这里出现问题了
而且报错中也有 `libPods.a` 的错误，所以基本就能锁定是 `cocoaPods` 出现问题.

执行
`pod install`

`pod update`

还是报刚才的错误。

最终发现错误的原因是 pod 的配置出现问题了，如图：
把`Yes` 改为 `No`

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926122256.png)