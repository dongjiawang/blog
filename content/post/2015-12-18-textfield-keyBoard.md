---
layout: post
title: 防止输入时键盘覆盖掉输入框
date: 2015-12-18 16:11:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---


**添加监听者**

```objectivec
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(keyboardwasChange:) name:UIKeyboardWillChangeFrameNotification object:nil];
```

**监听键盘之后的操作**

可以根据自己的需要调整高度的大小

```objectivec
- (void)keyboardwasChange:(NSNotification *)info {
    CGRect keyFrame = [info.userInfo[UIKeyboardFrameEndUserInfoKey] CGRectValue];

    CGFloat tY = keyFrame.origin.y - self.view.frame.size.height - 64;

    self.view.transform = CGAffineTransformMakeTranslation(0, tY);
}
```