---
layout: post
title: 图片设置圆角的三种方法
date: 2016-03-15 22:42:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

一般我们在iOS开发的过程中设置圆角都是如下这样设置的。

### 第一种

```objectivec
imageView.clipsToBounds = YES;
[imageView.layer setCornerRadius:50];
```

这样设置会触发离屏渲染，比较消耗性能。比如当一个页面上有十几头像这样设置了圆角

会明显感觉到卡顿。
这种就是最常用的，也是最耗性能的。

注意：ios9.0之后对UIImageView的圆角设置做了优化，UIImageView这样设置圆角
不会触发离屏渲染，ios9.0之前还是会触发离屏渲染。而UIButton还是都会触发离屏渲染。

### 第二种

```objectivec
imageView.clipsToBounds = YES;
imageView.layer setCornerRadius:50];
imageView.layer.shouldRasterize = YES;
```

shouldRasterize=YES设置光栅化，可以使离屏渲染的结果缓存到内存中存为位图， 使用的时候直接使用缓存，节省了一直离屏渲染损耗的性能。

但是如果layer及sublayers常常改变的话，它就会一直不停的渲染及删除缓存重新 创建缓存，所以这种情况下建议不要使用光栅化，这样也是比较损耗性能的。

### 第三种

这种方式性能最好，但是UIButton上不知道怎么绘制，可以用UIimageView添加个 点击手势当做UIButton使用

```objectivec
UIGraphicsBeginImageContextWithOptions(avatarImageView.bounds.size, NO, [UIScreen mainScreen].scale); 
[[UIBezierPath bezierPathWithRoundedRect:avatarImageView.bounds cornerRadius:50] addClip]; 
[image drawInRect:avatarImageView.bounds]; 
avatarImageView.image = UIGraphicsGetImageFromCurrentImageContext(); 
UIGraphicsEndImageContext();
```

这段方法可以写在SDWebImage的completed回调里，也可以在UIImageView+WebCache.h 里添加一个方法，isClipRound判断是否切圆角，把上面绘制圆角的方法封装到里面。