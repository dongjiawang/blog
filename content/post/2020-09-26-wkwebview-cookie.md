---
layout: post

title: WKProcesspool方式共享WKWebView的进程和cookie
 
date: 2020-09-26 18:00:00

categories: ["iOS"]

tags:  ["webView"]

mathjax: true

---



以前的时候总结过两次`WKWebView`的`cookie`使用方式

[《WKWebView添加Cookie》](https://blog.dongjiawang.top/2016/08/16/2016-08-16-cookie/)

[《WKWebView添加Cookie总结》](https://blog.dongjiawang.top/2018/07/09/2018-07-09-WKWebView-cookie-add/)

后来发现还可以使用`WKProcesspool`的方式共享`webView`进程池的方式共享`cookie`。

把`WKProcessPool`做成一个单例，需要用到相同`cookie`的地方都使用这个`processPool`，当然在清理`cookie`缓存的时候也要记得清理一下这个`processPool`。

