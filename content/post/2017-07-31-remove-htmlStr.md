---
layout: post
title: 去除 HTML 标签
date: 2017-07-31 15:50:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

###这个方法可以很简单的去掉 HTML 的标签
```objectivec
// 去掉 HTML 标签
-(NSString *)filterHTML:(NSString *)html {
    NSScanner * scanner = [NSScanner scannerWithString:html];
    NSString * text = nil;
    while([scanner isAtEnd]==NO)
    {
        [scanner scanUpToString:@"<" intoString:nil];
        [scanner scanUpToString:@">" intoString:&text];
        html = [html stringByReplacingOccurrencesOfString:[NSString stringWithFormat:@"%@>",text] withString:@""];
    }
    return html;
}
```