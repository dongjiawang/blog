---
layout: post
title: UITableView 的 selectRowAtIndexPath 被 UITapGestureRecognizer 的问题
date: 2016-12-27 16:38:00
categories: ["iOS"]
tags: ["UITableView"]
mathjax: true
---

今天在控制器的 view 上添加了一个 UITapGestureRecognizer 手势来处理一些功能

```objectivec
UITapGestureRecognizer *tap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(endTextfieldEditing)];
    tap.delegate = self;
    [self.view addGestureRecognizer:tap];
```

但是这时候点击 tableView 的 cell 的时候不会跳转了，是点击手势截获了tableView 的 touch 事件
![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926121138.gif)
在 UIGestureRecognizerDelegate 的文档中发现了

```objectivec
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch
```

这个方法。
于是重写这个手势代理方法
​```objectivec
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch {
    // 若为UITableViewCellContentView，则不截获Touch事件
    if ([NSStringFromClass([touch.view class]) isEqualToString:@"UITableViewCellContentView"]) {
        return NO;
    }
    return  YES;
    }
```
![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926121152.gif)

这次就解决了 tableview 的 touch 被拦截的问题。