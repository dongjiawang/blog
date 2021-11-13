---
layout: post
title: AFN监听网络状态
date: 2016-07-13 10:25:00
categories: ["网络"]
tags: ["iOS"]
mathjax: true
---


在
```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
```
中添加以下代码就可以了
```objectivec
      // 监测网络情况
        [[AFNetworkReachabilityManager sharedManager] setReachabilityStatusChangeBlock:^(AFNetworkReachabilityStatus status) {
            switch (status) {
                case AFNetworkReachabilityStatusNotReachable:
                    //没有网络
                    break;
            case AFNetworkReachabilityStatusReachableViaWWAN:
                   //使用的数据流量
                    break;
            case AFNetworkReachabilityStatusReachableViaWiFi:
                    //使用WiFi
                    break;
                    
                default:
                    break;
            }
        }];
        
        [[AFNetworkReachabilityManager sharedManager] startMonitoring];
```