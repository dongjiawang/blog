---
layout: post
title: iOS 状态栏的图标
date: 2016-11-19 23:02:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

> 获取状态栏上的 view 数组

```objectivec
    UIApplication *app = [UIApplication sharedApplication];
    // 获取状态栏
    UIView *statusView = [app valueForKey:@"statusBar"];
    NSArray *subViews = [[statusView valueForKey:@"foregroundView"] subviews];
```

> 打印 subviews 就能看到状态栏上的图标数据

```objectivec
    // 信号强度
    "<UIStatusBarSignalStrengthItemView: 0x102112070; frame = (6 0; 35 20);     
userInteractionEnabled = NO; layer = <CALayer: 0x1702282a0>> [Item = <UIStatusBarItem: 0x170228260> [UIStatusBarSignalStrengthItemView (Left)]]",
    // 服务商
    "<UIStatusBarServiceItemView: 0x102112e50; frame = (44 0; 50 20); userInteractionEnabled = NO; layer = <CALayer: 0x170228440>> [Item = <UIStatusBarItem: 0x1702283a0> [UIStatusBarServiceItemView (Left)]]",
    // 网络状态码
    "<UIStatusBarDataNetworkItemView: 0x1021137c0; frame = (99 0; 13 20); userInteractionEnabled = NO; layer = <CALayer: 0x170228500>> [Item = <UIStatusBarItem: 0x1702284a0> [UIStatusBarDataNetworkItemView (Left)]]",
    // 电池标志
    "<UIStatusBarBatteryItemView: 0x10220cac0; frame = (282 0; 33 20); userInteractionEnabled = NO; layer = <CALayer: 0x174229f80>> [Item = <UIStatusBarItem: 0x174229fe0> [UIStatusBarBatteryItemView (Right)]]",
    // 电池数字
    "<UIStatusBarBatteryPercentItemView: 0x10220f680; frame = (254 0; 25 20); userInteractionEnabled = NO; layer = <CALayer: 0x174229420>> [Item = <UIStatusBarItem: 0x17422a000> [UIStatusBarBatteryPercentItemView (Right)]]",
    // 闹钟
    "<UIStatusBarIndicatorItemView: 0x1021060a0; frame = (238 0; 10 20); userInteractionEnabled = NO; layer = <CALayer: 0x170228800>> [Item = <UIStatusBarItem: 0x174229fc0> [UIStatusBarIndicatorItemView:Alarm (Right)]]",
    // 时间
    "<UIStatusBarTimeItemView: 0x102114680; frame = (145 0; 35 20); userInteractionEnabled = NO; layer = <CALayer: 0x170228880>> [Item = <UIStatusBarItem: 0x170228280> [UIStatusBarTimeItemView (Center)]]"

```

> 上面的是我的真机的打印结果,不同的状态栏可能结果不同，还有其他的显示的图标，如下：

```objectivec
    // back to app 
    "UIStatusBarBreadcrumbItemView"
    // 右上角的打开 Safari
    "UIStatusBarOpenInSafariItemView"
    // 定位标志
    "UIStatusBarLocationItemView"
```

## 还有其余的图标，可能根据不同的需求来获取##