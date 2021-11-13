---
layout: post
title: 屏幕截图的方法
date: 2016-01-10 10:25:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---


不用 2d截图   直接截图
```objectivec
- (UIImage *)screenShots {
     //截取整个backview
    UIGraphicsBeginImageContext(self.backgroundView.bounds.size);
    [self.backgroundView.layer renderInContext:UIGraphicsGetCurrentContext()];
    UIImage *sourceImage = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
    return sourceImage;
}
```

上面的方法会压缩图片，用下面的方法可以设置是否缩放图片

```objectivec
UIGraphicsBeginImageContextWithOptions( self.view.frame.size, NO, 0.0);
[self.view.layer renderInContext:UIGraphicsGetCurrentContext()];
UIImage *sourceImage = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();
```