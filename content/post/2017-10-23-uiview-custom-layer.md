---
layout: post
title: 绘制 UIView 指定的边框
date: 2017-10-23 12:30:00
categories: ["iOS"]
tags:  ["layer"]
mathjax: true
---

一般绘制 `UIView` 的的边框的时候我们都会用 `layer` 的 `borderWidth` 和 `borderColor` 这两个属性，但是这样出来的效果是四周的边框都会被画上线条，有事需求就是指定画某个边框或者不画某个边框，这个自带的属性就不能满足需求了。
其实边框就是在 `layer` 上绘制，那么我们也可以在 `UIView` 上添加自定义的 `layer`
就可以用下面的方法添加指定的边框

```objectivec
- (void)setBorderWithView:(UIView *)view top:(BOOL)top left:(BOOL)left bottom:(BOOL)bottom right:(BOOL)right borderColor:(UIColor *)color borderWidth:(CGFloat)width {
    if (top) {
        CALayer *layer = [CALayer layer];
        layer.frame = CGRectMake(0, 0, view.frame.size.width, width);
        layer.backgroundColor = color.CGColor;
        [view.layer addSublayer:layer];
    }
    if (left) {
        CALayer *layer = [CALayer layer];
        layer.frame = CGRectMake(0, 0, width, view.frame.size.height);
        layer.backgroundColor = color.CGColor;
        [view.layer addSublayer:layer];
    }
    if (bottom) {
        CALayer *layer = [CALayer layer];
        layer.frame = CGRectMake(0, view.frame.size.height - width, view.frame.size.width, width);
        layer.backgroundColor = color.CGColor;
        [view.layer addSublayer:layer];
    }
    if (right) {
        CALayer *layer = [CALayer layer];
        layer.frame = CGRectMake(view.frame.size.width - width, 0, width, view.frame.size.height);
        layer.backgroundColor = color.CGColor;
        [view.layer addSublayer:layer];
    }
}
```

