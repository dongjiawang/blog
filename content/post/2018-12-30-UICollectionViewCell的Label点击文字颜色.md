---
layout: post

title: UICollectionViewCell的Label点击文字颜色

date: 2018-12-30 19:40:00

categories: ["iOS"]

tags: ["UICollectionView"]

mathjax: true
---

在做有`UICollectionViewCell`的界面，cell上有一个`UILabel`，需要在选中cell的时候改变label的文字颜色。

在cell的`- (void)setSelected:(BOOL)selected ;`中设置了颜色的变化，但是出来的效果并不是设置的颜色，而是变成了cell的背景色。

经过尝试，发现设置`UILabel`的`highlightedTextColor`这个属性为想要选中后的文字颜色就可以了。

例如：

```objectivec
self.textLabel.textColor = [UIColor whiteColor];
self.textLabel.highlightedTextColor = [UIColor redColor];
```

