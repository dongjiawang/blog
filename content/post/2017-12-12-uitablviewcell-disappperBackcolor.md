---
layout: post
title: 点击Cell时自定义控件背景色消失的问题
date: 2017-12-12 12:00:00
categories: ["iOS"]
tags: ["UITableView"] 
mathjax: true
---

在 `UITableVIewCell` 上添加的 `UIView`，并设置了背景色，然后点击 cell 的时候，因为 cell 的 `selectionStyle` 的原因，这个自定义的 `UIView` 的背景色被设置成了 `[r:0 b:0 g:0 a:0]`。

是因为选择 cell 的时候，调用了父类的 `setSelected`，会把 UIView 背景 disappear。

所以需要重写 cell 的下面两个方法

```objectivec
- (void)setHighlighted:(BOOL)highlighted animated:(BOOL)animated {
    UIColor *backgroundColor = self.myView.backgroundColor;
    [super setHighlighted:highlighted animated:animated];
    self.myView.backgroundColor = backgroundColor;
}

- (void)setSelected:(BOOL)selected animated:(BOOL)animated {
    UIColor *backgroundColor = self.myView.backgroundColor;
    [super setSelected:selected animated:animated];
    self.myView.backgroundColor = backgroundColor;
}
```