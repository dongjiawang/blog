---
layout: post
title: UIView设置少于四个的圆角
date: 2016-04-21 10:34:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

最近的需求中有个label需要设置右下角为圆角，其余三个为直角，一开始用的是重写drawRect，然后用绘图重绘每个角的样子，计算起来还是麻烦

后来发现了下面的方法：

```objectivec
//以一个UILabel为例，只让右下角为圆弧
UILabel *courseStyleLabel = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, 37, 16)]; 
courseStyleLabel.backgroundColor = CreateColorByRGB(TitleBgImgViewColor); 
courseStyleLabel.textColor = [UIColor whiteColor]; 
courseStyleLabel.textAlignment = NSTextAlignmentCenter; 
courseStyleLabel.font = [UIFont systemFontOfSize:12.0]; 
courseStyleLabel.layer.masksToBounds = YES; 

//这里设置需要绘制的圆角
UIBezierPath *maskPath = [UIBezierPath bezierPathWithRoundedRect:courseStyleLabel.bounds
                                               byRoundingCorners:UIRectCornerBottomRight//右下角
                                                     cornerRadii:CGSizeMake(3, 3)];//设置圆角大小，弧度为3
CAShapeLayer *maskLayer = [[CAShapeLayer alloc] init];
maskLayer.frame = courseStyleLabel.bounds;
maskLayer.path = maskPath.CGPath;
courseStyleLabel.layer.mask = maskLayer;
[courseImage addSubview:courseStyleLabel];
```

>通过这几个枚举值判断画哪个圆角

```objectivec
UIRectCornerTopLeft = 1 << 0,
 UIRectCornerTopRight = 1 << 1, 
UIRectCornerBottomLeft = 1 << 2, 
UIRectCornerBottomRight = 1 << 3, 
UIRectCornerAllCorners = ~0UL
```