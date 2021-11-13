---
layout: post
title: 自定义音量提示 view
date: 2017-09-26 12:00:00
categories: ["AVPlayer"]
tags:  ["iOS"]
mathjax: true
---

`AVFoundation` 框架提供了播放音频和视频的工具，使用 AVFoundation 基本能满足我们的大部分的播放需求。

`AVPlayer` 提供了音量调节的功能，但是 这种调节属于 APP 级别的控制，是独立于系统音量，调节大小不会影响系统音量。但是有时候我们需要调节系统音量，以免如果系统音量过小，APP 调节音量的效果并不明显。

### 使用`MPVolumeView` 调节系统音量

> `MPVolumeView` 是 `Media Player Framework` 中的一个UI组件，直接包含了对系统音量和`Airplay` 设备的音频镜像路由的控制功能。其中包含一个 `MPVolumeSlider` 的 `subview` 用来控制音量。这个 `MPVolumeSlider` 是一个私有类，我们无法手动创建此类，但这个类是`UISlider`的子类。`MPVolumeView` 的使用很简单，只需要将其加入到一个父视图中，给予父视图合适的大小，再创建MPVolumeView示例，将其加入到父视图中即可，苹果官方的文档中有示例代码可以参考。

但是使用 `MPVolumeView` 有个缺点就是 UI 不能自定义，显示出来的效果始终是系统的样式。而且不能添加手势控制

### 自定义音量提示


![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926122219.png)

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926122231.png)

要获取系统的音量 需要使用

```
#import <MediaPlayer/MPVolumeView.h>
```


```objectivec

/**
 音量的 view
  把系统的音量的 view 的 frame 设置到根本看不见的地方，这样就不会覆盖自定义的 提示 view
 */
- (MPVolumeView *)volumeView {

    if (_volumeView == nil) {
        // 如果要显示音量的 view  可在这里设置，默认只调整音量，没有显示View
        _volumeView = [[MPVolumeView alloc] initWithFrame:CGRectMake(-20, -20, 10, 10)];
        _volumeView.hidden = NO;
        [self addSubview:_volumeView];
    }
    return _volumeView;
}

/**
 音量 slider
 
 @return slider
 */
- (UISlider *)volumeSlider {
    if (_volumeSlider== nil) {
        for (UIView  *subView in [self.volumeView subviews]) {
            if ([subView.class.description isEqualToString:@"MPVolumeSlider"]) {
                _volumeSlider = (UISlider*)subView;
                break;
            }
        }
    }
    return _volumeSlider;
}

```

把 `volumeView` 的 frame 放到屏幕以外的地方，这样就不会看到系统的音量提示，而且不影响其他的操作（这个是不能隐藏的，设置 `hidden` 无效）

`volumeSlider.value` 就是当前获取到的系统的音量

然后在当前播放的 view 上添加滑动手势，监听手势的滑动方向和范围来改变 `volumeSlider.value` 就是改变了 系统的音量

### 手势控制

```objectivec
- (void)addPanGestureRecoginizer {
    self.panGesture = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(controlVolumeAndLigint:)];
    [self addGestureRecognizer:self.panGesture];
}
```

```objectivec
- (void)controlVolumeAndLigint:(UIPanGestureRecognizer *)gesture {
    // 获取手势位置
    CGPoint locationPoint = [gesture locationInView:self];
    // 获取手势速度
    CGPoint speed = [gesture velocityInView:self];
    
    switch (gesture.state) {
        case UIGestureRecognizerStateBegan:
        {
            self.isPanOnView = YES;
            CGFloat x = fabs(speed.x);
            CGFloat y = fabs(speed.y);
            if (x < y) {
                self.panDirection = LiveAVPLayerPanDirectionVerticalModed;
                // 开始滑动的时候,状态改为正在控制音量
                if (locationPoint.x > self.bounds.size.width / 2) {
                    
                }else { 
                // 状态改为显示亮度调节
                    
                }
            }
        }
            break;
            
        case UIGestureRecognizerStateChanged:
        {
            switch (self.panDirection) {
                case LiveAVPLayerPanDirectionVerticalModed:
                {
                    // 音量或者亮度调整
                    [self verticalMoved:speed.y];
                }
                    break;
                case LiveAVPLayerPanDirectionHorizontalMoved:
                {
                    // 水平移动  暂时不做进度调整
                }
                    break;
                    
                default:
                    break;
            }
        }
            break;
        case UIGestureRecognizerStateEnded:
        {
            [self hiddenHintView];
        }
            break;
        default:
            break;
    }
}
```

在这里根据滑动的距离计算需要修改的数值

`hintView` 是我自定义的提示 view，可以根据类型显示不同的图片，根据数值显示 百分比

这里就可以根据自己的需求去自定义了

```objectivec
- (void)verticalMoved:(CGFloat)value {
    //该value为手指的滑动速度，一般最快速度值不会超过10000，保证在0-1之间，往下滑为正，往上滑为负 所以用 “-=”
    value = value / 1000;
    if (self.isVolume) {
        [self.volumeSlider setValue:(self.volumeSlider.value - value) animated:NO];
        
        if (self.volumeSlider.value > 1.0f) {
            [self.volumeSlider setValue:1 animated:NO];
        }
        else if (self.volumeSlider.value < 0.0f) {
            [self.volumeSlider setValue:0 animated:NO];
        }
        [self.volumeSlider sendActionsForControlEvents:UIControlEventTouchUpInside];
        self.hintView.types = volumType;
        [self.hintView tinkerUp:self.volumeSlider.value With:0.0];
    }
    else {
        [UIScreen mainScreen].brightness -= value;
        if ([UIScreen mainScreen].brightness > 1.0f) {
            [UIScreen mainScreen].brightness = 1.0f;
        }
        else if ([UIScreen mainScreen].brightness < 0.0f) {
            [UIScreen mainScreen].brightness = 0.0f;
        }
        self.hintView.types = lightType;
        [self.hintView tinkerUp:[UIScreen mainScreen].brightness With:0.0];
    }
}
```

> [UIScreen mainScreen].brightness = 1.0f;

> [UIScreen mainScreen].brightness 是系统的亮度，建议在退出播放的时候恢复调整之前的亮度
