---
layout: post
title: 提交审核报错：No .app bundles found in the package
date: 2016-10-18 16:29:00
categories: ["Xcode"]
tags: ["iOS"]
mathjax: true
---

昨天把系统升级到了`10.12`，今天准备提交一个版本APP进行审核，Xcode版本是 `8.0`，结果老是报错，重新打包多次无效。
后来发现用`Xcode 8.0`内置的`Application Loader`提交就没有任何问题，而且貌似提交速度比以往还要快点。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926120647.png)

