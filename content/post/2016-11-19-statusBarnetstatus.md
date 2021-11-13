---
layout: post
title: 通过顶部状态栏获取当前的网络状态
date: 2016-11-19 23:50:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

通过顶部状态栏获取当前的网络状态

> 定义网络状态枚举
```objectivec
    typedef enum : NSUInteger {
        NetworkStatusUnkown,
        NetworkStatus2G,
        NetworkStatus3G,
        NetworkStatus4G,
        NetWorkStatusWiFi,
    } Networkstatus;
```

> 获取状态栏上的网络图标

参考这篇
[iOS 状态栏的图标](https://dongjiawang.github.io/2016/11/19/2016-11-19-statusBar/)

> 获取当前的网络状态码

```objectivec
      NSUInteger networkCode = [[vv valueForKeyPath:@"dataNetworkCode"] integerValue];
            Networkstatus netState = NetworkStatusUnkown;
            switch (networkCode) {
                case 0:
                    netState = NetworkStatusUnkown;
                    break;
                case 1:
                    netState = NetworkStatus2G;
                    break;
                case 2:
                    netState = NetworkStatus3G;
                    break;
                case 3:
                    netState = NetworkStatus4G;
                    break;
                case 4:
                    netState = NetWorkStatusWiFi;
                    break;
                    
                default:
                    break;
            }
```
