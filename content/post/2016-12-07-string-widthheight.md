---
layout: post
title: NSString占用的高度和范围
date: 2016-12-07 11:46:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

```objectivec
NSString *string = @"现在想来，我们这波第一批老去的90后还是挺幸运的。在我们最好的年龄遇到了最好的华语乐坛（周杰伦巅峰 林俊杰 SHE  潘玮柏 蔡依林…），遇到了巅峰的星爷，遇到了最好的西科东艾北卡南麦，动画城陪我们成长，周杰伦陪我们成熟，我们看着星爷老去，见证科比退役，或许我们不是最好的一代，但一定是最精彩的一代。";
```

# 1.sizeWithAttributes计算宽、高 size#
```objectivec
CGSize size_0 = [string sizeWithAttributes:@{NSFontAttributeName: [UIFont systemFontOfSize:17]}];
```

>这种方式计算的宽度会根据字符串的长度无限的增加

# 2、boundingRectWithSize计算宽、高的 rect#
```objectivec
CGRect size_1 = [string boundingRectWithSize:CGSizeMake(320, MAXFLOAT)
                                             options:NSStringDrawingUsesFontLeading | NSStringDrawingUsesLineFragmentOrigin | NSStringDrawingTruncatesLastVisibleLine
                                          attributes:@{NSFontAttributeName : [UIFont systemFontOfSize:14.0]}
                                             context:nil];
```

>这种方式的计算的宽度在达到预设的宽的时候会自动换行计算高度


# options 枚举#

```objectivec
NSStringDrawingUsesLineFragmentOrigin   //整个文本将以每行组成的矩形为单位计算整个文本的尺寸
NSStringDrawingUsesFontLeading      //使用字体的行间距来计算文本占用的范围，即每一行的底部到下一行的底部的距离计算
NSStringDrawingUsesDeviceMetrics        //将文字以图像符号计算文本占用范围，而不是以字符计算。也即是以每一个字体所占用的空间来计算文本范围
NSStringDrawingTruncatesLastVisibleLine     //当文本不能适合的放进指定的边界之内，则自动在最后一行添加省略符号。如果NSStringDrawingUsesLineFragmentOrigin没有设置，则该选项不生效
```