---
layout: post
title: 使用 Reachability 监听网络状态
date: 2016-11-02 17:47:00
categories: ["网络"]
tags: ["iOS"]
mathjax: true
---

为了适配 ipv6 ，原来使用的监听是 AFN 自带的，不知道为什么在 ipv6 下监听容易出现延迟甚至获取状态错误，于是使用 Reachability 这个框架

为了让 APP 在运行中一直监听，需要给 Appdelegate声明一个 Reachability  属性：
```objectivec
@property(nonatomic, strong) Reachability *reachability;
```

在`didFinishLaunchingWithOptions`方法中启动监听，使用通知收取网络状态的变化：
```objectivec
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(reachabilityChanged:) name:kReachabilityChangedNotification object:nil];
self.reachability = [Reachability reachabilityWithHostName:@"www.baidu.com"];
[self.reachability startNotifier];
```
监听网络状态回调
```objectivec
- (void)reachabilityChanged:(NSNotification *)note {
    Reachability* curReach = [note object];
    NSParameterAssert([curReach isKindOfClass:[Reachability class]]);
    NetworkStatus netStatus = [curReach currentReachabilityStatus];
    switch (netStatus)
    {
        case NotReachable:        {// 网络不能使用
           
            break;
        }
            
        case ReachableViaWWAN:        { // 使用的数据流量
            
            break;
        }
        case ReachableViaWiFi:        { // 使用的 WiFi
           
            break;
        }
    }
}
```

[Reachability 官方 Demo](https://developer.apple.com/library/content/samplecode/Reachability/Listings/Reachability_APLViewController_m.html#//apple_ref/doc/uid/DTS40007324-Reachability_APLViewController_m-DontLinkElementID_7)