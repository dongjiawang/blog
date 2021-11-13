---
layout: post
title: iOS 手势基本操作
date: 2016-07-07 00:15:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

## 1、UIGestureRecognizer 介绍

手势识别在 iOS 中非常重要，他极大地提高了移动设备的使用便捷性。
iOS 系统在 3.2 以后，他提供了一些常用的手势（UIGestureRecognizer 的子类），开发者可以直接使用他们进行手势操作。

```objectivec
UIPanGestureRecognizer（拖动）
UIPinchGestureRecognizer（捏合）
UIRotationGestureRecognizer（旋转）
UITapGestureRecognizer（点按）
UILongPressGestureRecognizer（长按）
UISwipeGestureRecognizer（轻扫）
```

另外，可以通过继承 UIGestureRecognizer 类，实现自定义手势（手势识别器类）。
PS：自定义手势时，需要 #import ，一般需实现如下方法：

```objectivec
-(void)reset;
-(void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event;
-(void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event;
-(void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event;
-(void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event;
//以上方法在分类 UIGestureRecognizer (UIGestureRecognizerProtected) 中声明，更多方法声明请自行查看
```
UIGestureRecognizer 的继承关系如下：

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926120229.jpg)

## 2、手势状态

在六种手势识别中，只有一种手势是离散型手势，他就是 UITapGestureRecognizer。

离散型手势的特点就是：一旦识别就无法取消，而且只会调用一次手势操作事件（初始化手势时指定的回调方法）。

换句话说其他五种手势是连续型手势，而连续型手势的特点就是：会多次调用手势操作事件，而且在连续手势识别后可以取消手势。从下图可以看出两者调用操作事件的次数是不同的：

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926115908.jpg)

手势状态枚举如下：

```objectivec
typedef NS_ENUM(NSInteger, UIGestureRecognizerState){
// 尚未识别是何种手势操作（但可能已经触发了触摸事件），默认状态
UIGestureRecognizerStatePossible,   
// 手势已经开始，此时已经被识别，但是这个过程中可能发生变化，手势操作尚未完成
UIGestureRecognizerStateBegan,  
// 手势状态发生转变    
UIGestureRecognizerStateChanged, 
// 手势识别操作完成（此时已经松开手指）   
UIGestureRecognizerStateEnded,
// 手势被取消，恢复到默认状态      
UIGestureRecognizerStateCancelled,  
// 手势识别失败，恢复到默认状态
UIGestureRecognizerStateFailed,  
// 手势识别完成，同UIGestureRecognizerStateEnded
UIGestureRecognizerStateRecognized = UIGestureRecognizerStateEnded 
};

```
- 对于离散型手势 UITapGestureRecgnizer 要么被识别，要么失败，点按（假设点按次数设置为1，并且没有添加长按手势）下去一次不松开则此时什么也不会发生，松开手指立即识别并调用操作事件，并且状态为3（已完成）。

- 但是连续型手势要复杂一些，就拿旋转手势来说，如果两个手指点下去不做任何操作，此时并不能识别手势（因为我们还没旋转）但是其实已经触发了触摸开始事件，
此时处于状态0；如果此时旋转会被识别，也就会调用对应的操作事件，同时状态变成1（手势开始），但是状态1只有一瞬间；紧接着状态变为2（因为我们的旋
转需要持续一会），并且重复调用操作事件（如果在事件中打印状态会重复打印2）；松开手指，此时状态变为3，并调用1次操作事件。

## 3、使用手势的步骤

使用手势很简单，分为三步：

>创建手势识别器对象实例。创建时，指定一个回调方法，当手势开始，改变、或结束时，执行回调方法。
设置手势识别器对象实例的相关属性（可选部分）添加到需要识别的 View 中。
每个手势只对应一个 View，当屏幕触摸在 View 的边界内时，如果手势和预定的一样，那就会执行回调方法。

PS：一个手势只能对应一个 View，但是一个 View 可以有多个手势。建议在真机上测试这些手势，模拟器操作不太方便，可能导致认为手势失效的情况。（模拟器测试捏合和旋转手势时，按住 option 键，再用触摸板或鼠标操作）







[参考文章](http://www.cnblogs.com/huangjianwu/p/4675648.html)

