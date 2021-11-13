---
layout: post
title: UICollectionView 添加类似 tableHeaderView
date: 2017-03-18 17:38:00
categories: ["iOS"]
tags: ["UICollectionView"]
mathjax: true
---

新项目的首页有个需求，就是在 collectionView 的顶部添加可以滚动的 banner 和几个点击按钮，而且 collectionView 的显示还是有多个 section，所以 collectionView 自己的注册 headView 的方式肯定是不能满足需求了。
于是想通过 collectionView 的内缩方法，使 collectionView 的显示内容向下移动需要的距离

```objectivec
// 上面 headView 的高度
CGFloat headViewH = self.myCollectionView.frame.size.width * 0.4;
// 将 headerView 的 X 值设置为负值，为 headerView 的高度
UIView *headView = [UIView alloc] initWithFrame:CGRectMake(0, -headViewH, self.collectionView.frame.size.width, headViewH)];
[self.collectionView addSubview:headView];
// 内缩 collectionView 的显示内容
self.collectionView.contentInset = UIEdgeInsetsMake(headViewH, 0, 0, 0);
```

这样的效果也不影响 collectionView 的使用，而且简单容易理解，具体效果如下图
![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926121213.gif)