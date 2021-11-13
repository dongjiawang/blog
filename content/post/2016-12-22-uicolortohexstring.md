---
layout: post
title: UIColor 转成 Hex 16进制色值
date: 2016-12-22 14:36:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

```objectivec
- (NSString *)hexStringFromColor:(UIColor *)color {
    const CGFloat *components = CGColorGetComponents(color.CGColor);
    
    CGFloat r = components[0];
    CGFloat g = components[1];
    CGFloat b = components[2];
    
    return [NSString stringWithFormat:@"#%02lX%02lX%02lX",
            lroundf(r * 255),
            lroundf(g * 255),
            lroundf(b * 255)];
}

```