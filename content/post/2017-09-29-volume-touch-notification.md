---
layout: post
title: 使用通知消息监听音量键
date: 2017-09-29 23:05:00
categories: ["AVPlayer"]
tags:  ["iOS"]
mathjax: true
---

在自定义视频播放的音量和亮度的提示框的的时候只是能替换手势调出来的 view
这篇文章是用监听通知的方式监听音量键的操作

**添加通知监听**

```objectivec
NSError *error;
[[AVAudioSession sharedInstance] setActive:YES error:&error];

[[NSNotificationCenter defaultCenter] addObserver:self 
selector:@selector(volumeChanged:) 
name:@"AVSystemController_SystemVolumeDidChangeNotification" 
object:nil];
```

>该消息除了监听音量键之外，应用从后台切换到前台也会发送该消息，所以，还需要判断消息的`userInfo`的字段`AVSystemController_AudioVolumeChangeReasonNotificationParameter`，确定发送消息的理由，value值为`ExplicitVolumeChange`就是我们所需要的。


**通知回调**

`在按下音量键的时候还是会弹出系统的音量提示框，我们可以自定义弹框`

这里的 hintView 是自定义的

[自定义音量提示 view](https://dongjiawang.github.io/2017/09/26/2017-09-26-mpvolumeView/)

```objectivec
- (void)volumeChanged:(NSNotification *)notification {
    if ([notification.name isEqualToString:@"AVSystemController_SystemVolumeDidChangeNotification"]) {
        NSDictionary *userInfo = notification.userInfo;
        NSString *reasonstr = userInfo[@"AVSystemController_AudioVolumeChangeReasonNotificationParameter"];
        if ([reasonstr isEqualToString:@"ExplicitVolumeChange"]) {
            self.volumeSlider.value = [userInfo[@"AVSystemController_AudioVolumeNotificationParameter"] floatValue];
            self.player.volume = self.volumeSlider.value;
            
            self.hintView.types = volumType;
            [self.hintView tinkerUp:self.volumeSlider.value With:0.0];
            if (self.hintHiddenTime == 3) {
                [self hiddenHintViewWithTime];
            }
            else {
                self.hintHiddenTime = 3;
            }
        }
    }
}
```
