---
layout: post
title: 通知的添加和移除
date: 2015-12-18 11:09:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---


我们都知道 `viewWillAppear:` 方法是在控制器的view将要显示的时候调用的，而 `viewWillDisappear:` 方法是在控制器的view将要隐藏的时候调用。很多时候我们根据自身需要将相关代码逻辑添加到这两个方法中。

现在随着手势操作的流行，很多人在页面中添加了左滑返回的功能，但是我们还是习惯性的把通知的移除放在了 `viewWillDisappear` 中，这样就会出现一个问题，如果我左滑滑到一半又滑回去了，这时候页面的通知已经移除了，整体的功能就会受到影响。

**解决方法：**

1、将注册通知的方法放到 `viewDidLoad` 中；只要页面有变化就会调用这个方法，页面的通知监听就会一直存在。

2、将移除通知的代码放到 `dealloc` 方法中，dealloc方法是在控制器销毁之时调用的。这个时候移除通知而不是在 `viewWillDisappear` 方法中移除可以有效避免上述的问题。既然控制器都销毁了，那么还留着相关的通知干嘛？该移除的移除。

上面的两种解决方案，要说哪种最优，那肯定非第二种莫属了。

在这里可以举个例子。假如我们现在有这么个场景：在控制器的view上有个label，在label上添加一个手势（一般手势都是在创建完label之后添加的），假设我在 `viewWillDisappear:` 方法中移除该手势，则此时用户侧滑返回之时又取消侧滑返回，那么原先在label上的手势却再也添加不回来了（这里label一般在viewDidLoad方法中创建），因为viewDidLoad方法不会再次调用。而第二种方法却可以有效避免这种情况，这样就可以避免我这种“手贱”的任意捣鼓了，是不是很完美呢？

 **注意项：**
>用 [NSNotificationCenter defaultCenter] 发出的通知在控制器里面接收不用理会，控制器销毁的时候会自动释放不会野指针，但是在view里面接收通知的时候要在dealloc里面移除，否则会野指针。

**总结：**

    1、iOS7新增加了导航控制器侧滑手势，当触发侧滑返回时，会调用系统的viewWillDisappear:方法，取消侧滑返回时又会调用viewWillAppear:方法。
    
    2、在做手势和通知等一系列操作之时尽量在dealloc方法中执行，添加通知尽量在viewDidLoad等一次性方法中执行。
    
    3、在viewWillAppear:、viewWillDisappear:、viewDidAppear:、viewDidDisappear:等类似于这种会多次调用的系统方法中添加代码时，一定要多考虑业务逻辑，以免出现不必要的麻烦。