---
layout: post
title:  手动屏幕旋转
date: 2017-11-24 09:30:00
categories: ["iOS"]
tags: ["iOS"] 
mathjax: true
---

做 APP 的时候大部分界面是不支持屏幕旋转的，一般在做视频播放的时候，会要求手动全屏播放，这时候就需要用到旋转屏幕方向的操作，我在代码中式这样实现的。

#### AppDelegate 中的配置
在`AppDelegate.h`中定义屏幕支持的方向的枚举：

```objectivec
typedef NS_ENUM(NSUInteger, ScreenDirection) {
    ScreenDirectionMaskPortraitOnly, // 只支持竖屏
    ScreenDirectionMaskLandscapOnly, // 只支持横向
    ScreenDirectionMaskAllButUpsideDown, // 跟随设备方向
};
```

在代码中根据当前的枚举，判断是否支持屏幕旋转.

在 `AppDelegate.m`实现下面的方法：

```objectivec
#pragma mark ----设置支持旋转方向----
-(UIInterfaceOrientationMask)application:(UIApplication *)application supportedInterfaceOrientationsForWindow:(UIWindow *)window {
    switch (GB_CanCrossView) {
        case ScreenDirectionMaskPortraitOnly:
        {
            return UIInterfaceOrientationMaskPortrait;
            break;
        }
        case ScreenDirectionMaskLandscapOnly:
        {
            return UIInterfaceOrientationMaskLandscape;
            break;
        }
        case ScreenDirectionMaskAllButUpsideDown:
        {
            return UIInterfaceOrientationMaskAllButUpsideDown;
            break;
        }
        default:
        {
            return UIInterfaceOrientationMaskPortrait;
            break;
        }
    }
}
```

`GB_CanCrossView` 是一个全局变量，用于判定是否支持屏幕旋转

#### 需要旋转的控制器的配置
重写系统方法

```objectivec
-(BOOL)shouldAutorotate {
      return YES;
 }
```

点击旋转按钮

```objectivec
-(void)RotationClicked {
    BOOL isVertical = NO;
    // 根据播放器的 view 的宽度判断当前是横向还是纵向
     if (self.avplayerCtrl.view.frame.size.width == self.banner.width) {
        GB_CanCrossView = ScreenDirectionMaskLandscapOnly;
        isVertical = YES;
    }
    else {
        GB_CanCrossView = ScreenDirectionMaskPortraitOnly;
    }
    
    // 更改设备方向
    if (isVertical) {
      //这句话是防止手动先把设备置为横屏,导致下面的语句失效.
        [[UIDevice currentDevice] setValue:[NSNumber numberWithInteger:UIDeviceOrientationPortrait] forKey:@"orientation"];
        
        [[UIDevice currentDevice] setValue:[NSNumber numberWithInteger:UIDeviceOrientationLandscapeLeft] forKey:@"orientation"];
        
        // 更改需要改变 frame 的 view
        // 横向布局
    }
    else {
        //横屏点击按钮, 旋转到竖屏
        //这句话是防止手动先把设备置为竖屏,导致下面的语句失效.

        [[UIDevice currentDevice] setValue:[NSNumber numberWithInteger:UIDeviceOrientationLandscapeLeft] forKey:@"orientation"];
        
        [[UIDevice currentDevice] setValue:[NSNumber numberWithInteger:UIDeviceOrientationPortrait] forKey:@"orientation"];
        
		  // 更改需要改变 frame 的 view
         // 纵向布局
    }
}
```