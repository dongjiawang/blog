---
layout: post

title: MJRefresh增加震动反馈

date: 2019-08-05 23:00:00

categories: ["iOS"]

tags: ["iOS"]

mathjax: true
---

发现有的app在下拉刷新的时候有一下震动反馈，感觉用户体验很棒，所以想在现有的项目中也增加一个这样的效果。但是项目中使用的 [MJRefresh](https://github.com/CoderMJLee/MJRefresh) 并没有提供这样的接口，自己重新实现下拉刷新也不现实。

既然如此就需要手动去监听下拉的状态改变。

`MJRefresh`中刷新控件的基类`MJRefreshComponent`有一个`state`属性，是一个枚举：

```objectivec
/** 刷新控件的状态 */
typedef NS_ENUM(NSInteger, MJRefreshState) {
    /** 普通闲置状态 */
    MJRefreshStateIdle = 1,
    /** 松开就可以进行刷新的状态 */
    MJRefreshStatePulling,
    /** 正在刷新中的状态 */
    MJRefreshStateRefreshing,
    /** 即将刷新的状态 */
    MJRefreshStateWillRefresh,
    /** 所有数据加载完毕，没有更多的数据了 */
    MJRefreshStateNoMoreData
};
```

这个就属性就是刷新控件的状态值，可以使用`KVO`的方式在列表中监听控件状态的变化，从而增加震动反馈。

```objectivec
// 增加KVO监听
[_tableView.mj_header addObserver:self forKeyPath:@"state" options:NSKeyValueObservingOptionNew context:nil];

[_tableView.mj_footer addObserver:self forKeyPath:@"state" options:NSKeyValueObservingOptionNew context:nil];
```

实现监听方法

```objectivec
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSString *,id> *)change context:(void *)context {
    
    if ([object isEqual:self.tableView.mj_header] && self.tableView.mj_header.state == MJRefreshStatePulling) {
        [self feedbackGenerator];
    }
    else if ([object isEqual:self.tableView.mj_footer] && self.tableView.mj_footer.state == MJRefreshStatePulling) {
        [self feedbackGenerator];
    }
}
```

震动反馈

```objectivec
- (void)feedbackGenerator {
    UIImpactFeedbackGenerator *generator = [[UIImpactFeedbackGenerator alloc] initWithStyle:UIImpactFeedbackStyleMedium];
    [generator prepare];
    [generator impactOccurred];
}
```

