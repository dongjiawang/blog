---
layout: post
title: UITextField的placeholder自定义
date: 2017-12-17 12:00:00
categories: ["iOS"]
tags: ["UITextField"] 
mathjax: true
---

系统的 `UITextField` 的 `placeholder`，一般只有默认的样式，但是 UI 设计的时候往往会出现占位文字颜色，位置的等等不同的需求，那么就要修改 `placeholder` 的样式了。

### placeholder字体和颜色

使用 `NSMutableParagraphStyle` 自定义样式

根据字体计算文字高度，使占位文字垂直居中

```objectivec
searchField.font = [UIFont systemFontOfSize:16.0];

NSMutableParagraphStyle *style = [searchField.font.defaultTextAttributes[NSParagraphStyleAttributeName] mutableCopy];
// 通过字体计算文字的高度，保证文字显示出来的时候是垂直居中
style.minimumLineHeight = searchField.font.lineHeight - (searchField.font.font.lineHeight - [UIFont systemFontOfSize:12.0].lineHeight) / 2.0;
searchField.attributedPlaceholder = [[NSAttributedString alloc] initWithString:@"输入您想要搜索的内容"
                                                                     attributes:@{
                                                                                  NSForegroundColorAttributeName: [UIColor whiteColor],
                                                                                  NSFontAttributeName : [UIFont systemFontOfSize:14.0],
                                                                                  NSParagraphStyleAttributeName : style
                                                                                  }
                                      
                                      ];
```

### 左右垂直居中

重写`UITextField`的 `drawPlaceholderInRect:(CGRect)rect`的方法

```objectivec
- (void)drawPlaceholderInRect:(CGRect)rect {

	//  self.font.pointSize + 5 ,防止placeholderRect 的高度刚刚好等于文字的高度，显示出来后，文字底部有种被切掉一边的效果
	
    CGRect placeholderRect = CGRectMake(rect.origin.x+10, (rect.size.height- self.font.pointSize - 2)/2, rect.size.width, self.font.pointSize + 5);//设置距离
    
    
    NSMutableParagraphStyle *style = [[NSMutableParagraphStyle alloc] init];
    style.lineBreakMode = NSLineBreakByTruncatingTail;
    style.alignment = NSTextAlignmentCenter;
    NSDictionary *attr = [NSDictionary dictionaryWithObjectsAndKeys:style,NSParagraphStyleAttributeName, self.font, NSFontAttributeName, [UIColor whiteColor], NSForegroundColorAttributeName, nil];
    
    [self.placeholder drawInRect:placeholderRect withAttributes:attr];
}
```