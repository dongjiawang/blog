---
layout: post
title: UITableView 滚动到底部 闪一下的问题
date: 2017-07-31 17:10:00
categories: ["iOS"]
tags: ["UITableView"]
mathjax: true
---

最近做一个聊天对话的界面
使用 UItableView 做消息的界面，收到新消息的时候 tableivew 始终保持在最底部
一开始使用的是：

```objectivec
- (void)updateMessageTableView {
    dispatch_async(dispatch_get_main_queue(), ^{
        [self.messageTableView reloadData];
        NSIndexPath *indexPath = [NSIndexPath indexPathForRow:_messageArray.count - 1 inSection:0];
        // 判断是否可以滚动到最后一条消息
        if (indexPath.row < [self.messageTableView numberOfRowsInSection:0]) {
            [self.messageTableView scrollToRowAtIndexPath:indexPath atScrollPosition:UITableViewScrollPositionBottom animated:YES];
        }
    });
}
```

刷新 tableview 之后，调用 tableview 的 `scrollToRowAtIndexPath` 的方法，是它滚到自己的最后一条数据，但是这个时候就会先刷新数据，然后闪一下，再滚动到最下面，就算不使用动画效果，依旧会闪一下。

后来换一种思路去做滚动到最底部，比如获取最新数据，然后 reloadData，这个时候就已经获得了 tableview 的 contentSize，然后调用 UITableView 的 `setContentOffset`，参数就是 tableview 的 contentSize ，结果还是闪一下。

后来发现其实是因为我用的 `FDTemplateLayoutCell` 这个第三方框架，所以 cell 的高度是动态的，所以就会出现闪一下的情况，如果我给 UITableView 一个 `estimatedRowHeight` 初始化一个预估算高度（这是 iOS8之后才有的），就基本不会出现闪一下情况，或者实现下面这个 tableview 的 delegate
```objectivec
- (CGFloat)tableView:(UITableView *)tableView estimatedHeightForRowAtIndexPath:(NSIndexPath *)indexPath {
    return UITableViewAutomaticDimension;
}
```