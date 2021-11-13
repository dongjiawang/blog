---
layout: post
title: 横向滚动标题栏
date: 2017-05-16 15:30:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---


做了一个横向滚动的标题栏，用于标题文字过长的时候展示整个标题
[GitHub 的 demo](https://github.com/dongjiawang/ScrollLabel/tree/master)


![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926121832.gif)

大概的原理就是 一个`UILabel`做主展示`label`，一个`label`作为辅助。在第一个滚动完的时候，跟在后面，防止第二次滚动还没开始的时候，后面空白。

没有用`NSTimer`,这样基本不会出现循环引用的问题。

还有就是在控制器的`view`隐藏的时候和 App 进入后台的时候会停止动画
所以在控制器中判断`view`是否又显示出来，APP 是否又进入前台，在这两种情况下重新启动动画。
进入前后台已经加了通知，用的时候只需要在控制器里增加是否显示的判断就行。
