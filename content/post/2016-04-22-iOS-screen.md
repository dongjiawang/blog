---
layout: post
title: iOS 横竖屏幕
date: 2016-04-22 10:06:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---


首先，确保APP支持屏幕旋转，具体怎么支持，去网上查询一下

然后，在对应的类中进行如下操作


```objectivec
#pragma mark 转屏方法重写
-(UIInterfaceOrientationMask)supportedInterfaceOrientations {    
    return [self.viewControllers.lastObject supportedInterfaceOrientations];
}
- (UIInterfaceOrientation)preferredInterfaceOrientationForPresentation {    
  return [self.viewControllers.lastObject preferredInterfaceOrientationForPresentation];
}
-(BOOL)shouldAutorotate { 
     return self.visibleViewController.shouldAutorotate;
}
```


最后，在你不想转屏切换的ViewController上重写以下方法：

```objectivec
#pragma mark 转屏方法 不允许转屏
-(UIInterfaceOrientationMask)supportedInterfaceOrientations {  
      return UIInterfaceOrientationMaskPortrait ;
}
- (BOOL)shouldAutorotate{    
  return NO;
}
-(UIInterfaceOrientation)preferredInterfaceOrientationForPresentation {
    return UIInterfaceOrientationPortrait;
}
- (BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation {
    return NO;
}
```



在你想转屏切换的ViewController上可以照这样重写（允许左右横屏以及竖屏）：

```objectivec
- (BOOL)shouldAutorotate {    
    return YES;
}
- (UIInterfaceOrientationMask)supportedInterfaceOrientations {
    return UIInterfaceOrientationMaskAll;
}
- (UIInterfaceOrientation)preferredInterfaceOrientationForPresentation {    
    return UIInterfaceOrientationPortrait;
}
- (BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)interfaceOrientation {
    return YES;
}
```


另外，在ViewController中对于转屏事件可以参见下面的方法进行捕获：

```objectivec
- (void)viewWillTransitionToSize:(CGSize)size withTransitionCoordinator:(id<UIViewControllerTransitionCoordinator>)coordinator {

    [super viewWillTransitionToSize:size withTransitionCoordinator:coordinator];    

    [coordinator animateAlongsideTransition:^(id<UIViewControllerTransitionCoordinatorContext> context) {

        //计算旋转之后的宽度并赋值
        CGSize screen = [UIScreen mainScreen].bounds.size;
        //界面处理逻辑
        self.lineChartView.frame = CGRectMake(0, 30, screen.width, 200.0);
        //动画播放完成之后
        if(screen.width > screen.height ){
            NSLog(@"横屏");
        }else{
            NSLog(@"竖屏");
        }

    } completion:^(id<UIViewControllerTransitionCoordinatorContext> context) {

        NSLog(@"动画播放完之后处理");    

    }];
}
```

区分当前屏幕是否为横竖屏的状态，其实通过判断当前屏幕的宽高来决定是不是横屏或者竖屏：


**竖屏时：** 宽<高

**横屏时：** 宽>高