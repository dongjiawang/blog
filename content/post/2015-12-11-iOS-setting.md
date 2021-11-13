---
layout: post
title: 如何跳转到各个系统设置界面
date: 2015-12-11 14:11:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

APP需要用到的权限有时候没有打开，就需要提示用户打开某项权限，最好是直接引导用户跳转到对应的设置界面。下面是一些直接跳转到设置界面的方法。


### 定位

```objectivec
 //定位服务设置界面
 NSURL *url = [NSURL URLWithString:@"prefs:root=LOCATION_SERVICES"];
if ([[UIApplication sharedApplication] canOpenURL:url]) {
    [[UIApplication sharedApplication] openURL:url];
}
```

### 墙纸

```objectivec
//墙纸设置界面
NSURL *url = [NSURL URLWithString:@"prefs:root=Wallpaper"];
if ([[UIApplication sharedApplication] canOpenURL:url])  {
    [[UIApplication sharedApplication] openURL:url];
}
```

### 蓝牙

```objectivec
//蓝牙设置界面
 NSURL *url = [NSURL URLWithString:@"prefs:root=Bluetooth"];
if ([[UIApplication sharedApplication] canOpenURL:url]) {
    [[UIApplication sharedApplication] openURL:url];
}
```

### iCloud

```objectivec
//iCloud设置界面
 NSURL *url = [NSURL URLWithString:@"prefs:root=CASTLE"];
 if ([[UIApplication sharedApplication] canOpenURL:url]  {
  [[UIApplication sharedApplication] openURL:url];
}
```

>总结起来就是想要跳到哪个设置界面，就打开prefs:root=对应的设置界面的值。

这个在iOS6，7和8中确实可以跳转，只是还少了一个步骤。在`URL Types`中添加一个新项。某没有深入研究，只填写**prefs**就可以了。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926114825.png)


![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926114901.png)

**在网上找到了一些整理好的对应的参数配置列表**

```objectivec
About — prefs:root=General&path=About   //关于手机
Accessibility — prefs:root=General&path=ACCESSIBILITY  //辅助功能
Airplane Mode On — prefs:root=AIRPLANE_MODE  //飞行模式
Auto-Lock — prefs:root=General&path=AUTOLOCK   //自动锁屏
Brightness — prefs:root=Brightness  //亮度
Bluetooth — prefs:root=General&path=Bluetooth //蓝牙
Date & Time — prefs:root=General&path=DATE_AND_TIME
FaceTime — prefs:root=FACETIME
General — prefs:root=General
Keyboard — prefs:root=General&path=Keyboard
iCloud — prefs:root=CASTLE
iCloud Storage & Backup — prefs:root=CASTLE&path=STORAGE_AND_BACKUP
International — prefs:root=General&path=INTERNATIONAL
Location Services — prefs:root=LOCATION_SERVICES
Music — prefs:root=MUSIC
Music Equalizer — prefs:root=MUSIC&path=EQ
Music Volume Limit — prefs:root=MUSIC&path=VolumeLimit
Network — prefs:root=General&path=Network
Nike + iPod — prefs:root=NIKE_PLUS_IPOD
Notes — prefs:root=NOTES
Notification — prefs:root=NOTIFICATIONS_ID
Phone — prefs:root=Phone
Photos — prefs:root=Photos
Profile — prefs:root=General&path=ManagedConfigurationList
Reset — prefs:root=General&path=Reset
Safari — prefs:root=Safari
Siri — prefs:root=General&path=Assistant
Sounds — prefs:root=Sounds
Software Update — prefs:root=General&path=SOFTWARE_UPDATE_LINK
Store — prefs:root=STORE
Twitter — prefs:root=TWITTER
Usage — prefs:root=General&path=USAGE
VPN — prefs:root=General&path=Network/VPN
Wallpaper — prefs:root=Wallpaper
Wi-Fi — prefs:root=WIFI
```