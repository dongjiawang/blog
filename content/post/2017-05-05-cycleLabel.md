---
layout: post
title: 循环滚动展示 label
date: 2017-05-05 17:30:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

业务需要做了一个上下滚动展示的文字广告位，类似轮播图的效果
![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926121819.gif)

[Github 上的 Demo](https://github.com/dongjiawang/CycleLabel/tree/master)

用两个`UILabel`循环滚动，在`UIView`动画执行完成后，设置 `frame`，并且再次调用`UIView`的动画
没有用`NSTimer`简化了很多防止内存泄露的地方
**贴出一部分代码（没有加注释，逻辑一般看看能明白）**


```objectivec
- (void)startAnimation {
    _stopAnimate = NO;
    if (![self isCurrentViewControllerVisible:[self viewController]]) { // 处于非当前页面
        // 防止上一次动画没有完成，会一直加载两次动画，所以延迟一下执行
        [self performSelector:@selector(startAnimation) withObject:nil afterDelay:0.1];
        
    }else{
        
        __weak typeof(self) weakSelf = self;
        [UIView animateWithDuration:1.0 delay:_timeInterVal options:0 animations:^{
            if (!_wichOne) {
                _label_0.frame = CGRectMake(0, -self.frame.size.height, _label_0.frame.size.width, _label_0.frame.size.height);
                _label_1.frame = CGRectMake(0, 0, _label_1.frame.size.width, _label_1.frame.size.height);
            }
            else {
                _label_0.frame = CGRectMake(0, 0, _label_0.frame.size.width, _label_0.frame.size.height);
                _label_1.frame = CGRectMake(0, -self.frame.size.height, _label_1.frame.size.width, _label_1.frame.size.height);
            }
        } completion:^(BOOL finished) {
            if (!_stopAnimate) {
                _wichOne = !_wichOne;
                _showCount++;
                if (_showCount > _textArray.count - 1) {
                    _showCount = 0;
                }
                
                if (_label_0.frame.origin.y == -self.frame.size.height) {
                    _label_0.frame = CGRectMake(0, self.frame.size.height, _label_0.frame.size.width, _label_0.frame.size.height);
                    _label_0.text = _textArray[_showCount];
                    _label_0.tag = _showCount;
                }
                if (_label_1.frame.origin.y == -self.frame.size.height) {
                    _label_1.frame = CGRectMake(0, self.frame.size.height, _label_1.frame.size.width, _label_1.frame.size.height);
                    _label_1.text = _textArray[_showCount];
                    _label_1.tag = _showCount;
                }
                [weakSelf startAnimation];
            }
        }];
    }
}
```