---
layout: post
title: NSString 去除空格
date: 2017-11-23 09:30:00
categories: ["iOS"]
tags: ["NSString"] 
mathjax: true
---

**NSString 去掉两边空格**

```objectivec
NSString *utlStr = [urlString stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceCharacterSet]];
```

**去掉 NSString 中的空格**

```objectivec
NSString *utlStr = [urlString stringByReplacingOccurrencesOfString:@" " withString:@""];
```

