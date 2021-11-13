---
layout: post
title: 点击推送消息跳转处理
date: 2016-02-27 20:25:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

当用户点击收到的推送消息时候，我希望打开APP，并且跳转到对应的界面，这就需要在AppDelegate里面对代理方法进行处理。

当用户点击推送消息打开APP的时候会调用

```objectivec
- (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
```
`launchOptions` 中会有推送消息的 `userInfo` 信息，此时我们可以通过

```objectivec
NSDictionary* remoteNotification = [launchOptions objectForKey:UIApplicationLaunchOptionsRemoteNotificationKey];
```
获取到推送的内容；如果 `remoteNotification `不为空，则说明用户是通过推送进入的APP，那么可以声明一个属性
```objectivec
@property (nonatomic) BOOL isLaunchedByNotification;
```
用于标识用户是否通过点击通知消息进入本应用。此时，

```objectivec
- (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo
```
一定会被调用，iOS7可以使用
```objectivec
- (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
```
因为此方法的调用时，MainViewController已经被初始化，所以我们已经可以在MainViewController注册推送消息的监听，用于展示对应的视图，如下：
```objectivec
//订阅展示视图消息，将直接打开某个分支视图
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(presentView:) name:@"PresentView" object:nil];
//弹出消息框提示用户有订阅通知消息。主要用于用户在使用应用时，弹出提示框
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(showNotification:) name:@"Notification" object:nil];
```
所以在AppDelegate的didReceiveRemoteNotification中可以通过判断isLaunchedByNotification来通知不同的展示方法。

 一个推送来临时，播放震动声音不停止的代码，不是播放音乐哦

首先包含头文件
```objectivec
 #import <AudioToolbox/AudioToolbox.h>
```
注册一段声音（本例中直接使用默认1007）
```objectivec
@property (nonatomic, assign) SystemSoundID soundID;
```

```objectivec
NSString *path = [[NSBundle mainBundle] pathForResource:soundName ofType:nil];
    AudioServicesCreateSystemSoundID((__bridge CFURLRef)[NSURL fileURLWithPath:path], &_soundID);

    AudioServicesAddSystemSoundCompletion(_soundID, NULL, NULL, soundCompleteCallback, NULL); // 核心代码 可重复执行
    AudioServicesPlaySystemSound(kSystemSoundID_Vibrate);
    AudioServicesPlaySystemSound(_soundID);

// block 用于 AudioServicesAddSystemSoundCompletion(_soundID, NULL, NULL, soundCompleteCallback, NULL); 函数调用
void soundCompleteCallback(SystemSoundID soundID,void * clientData)
{
    AudioServicesPlaySystemSound(kSystemSoundID_Vibrate);
    AudioServicesPlaySystemSound(soundID);
}

// 停止播放
-(void)stopAlertSoundWithSoundID:(SystemSoundID)soundID 
{
    AudioServicesDisposeSystemSoundID(kSystemSoundID_Vibrate);
    AudioServicesDisposeSystemSoundID(soundID);
    AudioServicesRemoveSystemSoundCompletion(soundID);
}
```

 增加一个小技巧，微信与好友开视频的推送，当微信应用到后台的时候，也可能是被kill了，本人很奇怪，为什么这个推送通知，声音和震动可以不停下来，一直提醒用户，而且iOS8上顶部的通知横幅也是一直显示，直到用户点击之后进入微信应用才会停止，这个是怎么做到的？

其实用一个小于30s的音频文件就搞定了

 

 

 

 

 

 

本文参考微信公众号：iOS开发

　　