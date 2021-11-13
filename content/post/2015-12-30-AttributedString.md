---
layout: post
title: 富文本的简单使用
date: 2015-12-30 15:00:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

常常会有一段文字显示不同的颜色和字体，或者给某几个文字加删除线或下划线的需求。

使用富文本 `NSMuttableAttstring`（带属性的字符串），上面的一些需求都可以很简便的实现。
**1、实例化方法和使用方法**

实例化方法：
```objectivec
//使用字符串初始化
- (id)initWithString:(NSString *)str;
```
例如：
```objectivec
NSMutableAttributedString *AttributedStr = [[NSMutableAttributedStringalloc]initWithString:@"今天天气不错呀"];
```

字典中存放一些属性名和属性值，如：
```objectivec
NSDictionary *attributeDict = [NSDictionarydictionaryWithObjectsAndKeys:[UIFontsystemFontOfSize:15.0],
                                                                        NSFontAttributeName,
                                                                        [UIColorredColor],
                                                                        NSForegroundColorAttributeName,
                                                                        NSUnderlineStyleAttributeName,
                                                                        NSUnderlineStyleSingle,nil];

NSMutableAttributedString *AttributedStr = [[NSMutableAttributedStringalloc]initWithString:@"今天天气不错呀" attributes:attributeDict];
```

使用NSAttributedString初始化，跟NSMutableString，NSString类似
```objectivec
- (id)initWithAttributedString:(NSAttributedString *)attester;
```

使用方法：

为某一范围内文字设置多个属性
```objectivec
- (void)setAttributes:(NSDictionary *)attrs range:(NSRange)range;
```

为某一范围内文字添加某个属性
```objectivec
- (void)addAttribute:(NSString *)name value:(id)value range:(NSRange)range;
```

为某一范围内文字添加多个属性
```objectivec
- (void)addAttributes:(NSDictionary *)attrs range:(NSRange)range;
```

移除某范围内的某个属性
```objectivec
- (void)removeAttribute:(NSString *)name range:(NSRange)range;
```

**2.常见的属性及说明**
```objectivec
NSFontAttributeName //字体

NSParagraphStyleAttributeName //段落格式 

NSForegroundColorAttributeName //字体颜色

NSBackgroundColorAttributeName  //背景颜色

NSStrikethroughStyleAttributeName//删除线格式

NSUnderlineStyleAttributeName     //下划线格式

NSStrokeColorAttributeName       //删除线颜色

NSStrokeWidthAttributeName//删除线宽度

NSShadowAttributeName //阴影
```

**3.使用实例**
```objectivec
UILabel *testLabel = [[UILabel alloc]initWithFrame:CGRectMake(0, 100, 320, 30)];

   testLabel.backgroundColor = [UIColor lightGrayColor];

   testLabel.textAlignment = NSTextAlignmentCenter;

   NSMutableAttributedString *AttributedStr = [[NSMutableAttributedString alloc]initWithString:@"今天天气不错呀"];

   [AttributedStr addAttribute:NSFontAttributeName

                         value:[UIFont systemFontOfSize:16.0]

                         range:NSMakeRange(2, 2)];

   [AttributedStr addAttribute:NSForegroundColorAttributeName

                         value:[UIColor redColor]

                         range:NSMakeRange(2, 2)];

   testLabel.attributedText = AttributedStr;

   [self.view addSubview:testLabel];
```

*其他可以设置text 的控件（如UIButton，UITextField）也都有该属性，该文章不够详细，只是简单介绍。*