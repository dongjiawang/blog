---
layout: post
title: masonry 使用 UIView 的动画
date: 2017-04-14 17:00:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

在创建一个 `view` 的时候，给这个 `view` 一个从下弹出的动画，以前使用 frame 做布局的时候，很简单的就用 `UIViewAnimation` 就做出来了，可是现在APP 布局用的是 `masonry`，直接在设置好初始 `layout` 后就更新，发现并没有动画，而是直接就显示了出来，后来发现是需要 view 先更新第一次的约束，动画执行后在执行一个更新约束才能出现动画
代码如下（项目中的代码，没有做整理，理解理解思路就行）：
```objectivec
- (GoodDetailSelectNumView *)selectNumView {
    if (!_selectNumView) {
        _selectNumView = [[GoodDetailSelectNumView alloc] init];
        [self.view addSubview:_selectNumView];
        [_selectNumView mas_makeConstraints:^(MASConstraintMaker *make) {
            make.bottom.mas_equalTo(150);
            make.width.equalTo(self.view);
            make.height.mas_equalTo(150);
        }];
        // 注意需要先执行一次更新约束
        [self.view layoutIfNeeded];
        
        __weak typeof(self) weakSelf = self;
        _selectNumView.exitSelectNum = ^{
            [weakSelf removeSelectView];
        };
        [UIView animateWithDuration:0.3 animations:^{
            [_selectNumView mas_updateConstraints:^(MASConstraintMaker *make) {
                make.bottom.mas_equalTo(-50);
            }];
            // 注意需要再执行一次更新约束
            [self.view layoutIfNeeded];
        }];
    }
    return _selectNumView;
}
```

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926121352.gif)

```objectivec
- (void)removeSelectView {
    [UIView animateWithDuration:0.3 animations:^{
        [_selectNumView mas_updateConstraints:^(MASConstraintMaker *make) {
            make.bottom.mas_equalTo(150);
        }];
        [self.view layoutIfNeeded];
    } completion:^(BOOL finished) {
        [_selectNumView removeFromSuperview];
        _selectNumView = nil;
    }];
}
```