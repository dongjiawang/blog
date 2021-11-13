---
layout: post
title: 自定义UIView动画效果
date: 2016-01-06 18:25:00
categories: ["iOS"]
tags: ["动画"]
mathjax: true
---


最普通动画:
```objectivec
//开始动画
[UIView beginAnimations:nil context:nil];
//设定动画持续时间
[UIView setAnimationDuration:2];
//动画的内容
frame.origin.x += 150;
[img setFrame:frame];
//动画结束
[UIView commitAnimations];
```

连续动画:一个接一个地显示一系列的图像 
```objectivec
NSArray *myImages = [NSArray arrayWithObjects:
[UIImage imageNamed:@"myImage1.png"],
[UIImage imageNamed:@"myImage2.png"],
[UIImage imageNamed:@"myImage3.png"],
[UIImage imageNamed:@"myImage4.gif"], nil];

UIImageView *myAnimatedView = [UIImageView alloc];
[myAnimatedView initWithFrame:[self bounds]];
myAnimatedView.animationImages = myImages; //animationImages属性返回一个存放动画图片的数组
myAnimatedView.animationDuration = 0.25; //浏览整个图片一次所用的时间
myAnimatedView.animationRepeatCount = 0; // 0 = loops forever 动画重复次数
[myAnimatedView startAnimating];
[self addSubview:myAnimatedView];
[myAnimatedView release];

CATransition Public API动画:
CATransition *animation = [CATransition animation];
//动画时间
    animation.duration = 0.5f;
//先慢后快
    animation.timingFunction = UIViewAnimationCurveEaseInOut;
animation.fillMode = kCAFillModeForwards;
//animation.removedOnCompletion = NO;
```

各种动画效果
```objectivec
/*
kCAFillModeRemoved 这个是默认值,也就是说当动画开始前和动画结束后,动画对layer都没有影响,动画结束后,layer会恢复到之前的状态

kCAFillModeForwards 当动画结束后,layer会一直保持着动画最后的状态
kCAFillModeBackwards 这个和kCAFillModeForwards是相对的,就是在动画开始前,你只要将动画加入了一个layer,layer便立即进入动画的初始状态并等待动画开始.你可以这样设定测试代码,将一个动画加入一个layer的时候延迟5秒执行.然后就会发现在动画没有开始的时候,只要动画被加入了layer,layer便处于动画初始状态
kCAFillModeBoth 理解了上面两个,这个就很好理解了,这个其实就是上面两个的合成.动画加入后开始之前,layer便处于动画初始状态,动画结束后layer保持动画最后的状态.

*/
/*
kCATransitionFromRight;
kCATransitionFromLeft;
kCATransitionFromTop;
kCATransitionFromBottom;
*/
//各种组合
animation.type = kCATransitionPush;
animation.subtype = kCATransitionFromRight;

[self.view.layer addAnimation:animation forKey:@"animation"];

CATransition Private API动画:
animation.type可以设定为以下效果
动画效果汇总:
/*
suckEffect（三角）

rippleEffect（水波抖动）

pageCurl（上翻页）

pageUnCurl（下翻页）

oglFlip（上下翻转）

cameraIris/cameraIrisHollowOpen/cameraIrisHollowClose  （镜头快门，这一组动画是有效果，只是很难看，不建议使用

而以下为则黑名单：

spewEffect: 新版面在屏幕下方中间位置被释放出来覆盖旧版面.

- genieEffect: 旧版面在屏幕左下方或右下方被吸走, 显示出下面的新版面 (阿拉丁灯神?).

- unGenieEffect: 新版面在屏幕左下方或右下方被释放出来覆盖旧版面.

- twist: 版面以水平方向像龙卷风式转出来.

- tubey: 版面垂直附有弹性的转出来.

- swirl: 旧版面360度旋转并淡出, 显示出新版面.

- charminUltra: 旧版面淡出并显示新版面.

- zoomyIn: 新版面由小放大走到前面, 旧版面放大由前面消失.

- zoomyOut: 新版面屏幕外面缩放出现, 旧版面缩小消失.

- oglApplicationSuspend: 像按"home" 按钮的效果.
*/

UIView Animations 动画:
[UIView beginAnimations:@"animationID" context:nil];
[UIView setAnimationDuration:0.5f];
[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
[UIView setAnimationRepeatAutoreverses:NO];
//以下四种效果
/*
[UIView setAnimationTransition:UIViewAnimationTransitionFlipFromLeft forView:self.view cache:YES];//oglFlip, fromLeft
[UIView setAnimationTransition:UIViewAnimationTransitionFlipFromRight forView:self.view cache:YES];//oglFlip, fromRight
[UIView setAnimationTransition:UIViewAnimationTransitionCurlUp forView:self.view cache:YES];
[UIView setAnimationTransition:UIViewAnimationTransitionCurlDown forView:self.view cache:YES];
*/

[self.view exchangeSubviewAtIndex:1 withSubviewAtIndex:0];
[UIView commitAnimations];
IOS4.0新方法:
方法: +(void)animateWithDuration:(NSTimeInterval)duration animations:(void (^)(void))animations;
+ (void)animateWithDuration:(NSTimeInterval)duration animations:(void (^)(void))animations completion:(void (^)(BOOL finished))completion; //多一个动画结束后可以执行的操作.
//下边是嵌套使用,先变大再消失的动画效果.
[UIView animateWithDuration:1.25 animations:^{
CGAffineTransform newTransform = CGAffineTransformMakeScale(1.2, 1.2);
[firstImageView setTransform:newTransform];
[secondImageView setTransform:newTransform];}
completion:^(BOOL finished){
[UIView animateWithDuration:1.2 animations:^{
[firstImageView setAlpha:0];
[secondImageView setAlpha:0];} completion:^(BOOL finished){
[firstImageView removeFromSuperview];
[secondImageView removeFromSuperview]; }];
}];
```