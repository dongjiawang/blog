
---
layout: post
title:  CoreAnimation 的简单使用
date: 2017-11-30 23:00:00
categories: ["iOS"]
tags: ["动画"] 
mathjax: true
---

`CoreAnimation` 的执行过程是在后台操作的，不会阻塞主线程。
`CoreAnimation` 是作用在 `CALayer` 上，不会修改 `UIView`。

## 核心动画与UIView动画的区别

>核心动画的一切都是假象，并不会真是改变layer的值。
UIView 真实改变属性才能有动画。

>核心动画用在不需要交互的地方。
UIView 用在需要交互的地方

****

## CALayer 基础属性

```objectivec
bounds 边境范围
position 中心点
zPosition z轴中心点
anchorPoint 锚点(默认锚点 是与中心点重合)
        锚点的值与位置: 最小值是（0，0），最大值是（1，1）,当视图改变的时候，是以锚点为基准改变的
								           锚点值 = 锚点在视图上的位置.x.y/视图的宽高
                		  默认锚点的值是（0.5，0.5）= 中心点
                                          (0，0) = 图层的左上角
                                          (0，1) = 图层的左下角
                                          (1，0) = 图层的右上角
                                          (1，1) = 图层的右下角                     
                                          
anchorPointZ Z轴锚点
transform 转换形态
frame NO. Animatable 坐标
hidden 隐藏
doubleSided 图层背面是否显示
geometryFlipped 翻转 颠倒
masksToBounds 裁切边境
contents 内容
opaque 不透明度
allowsEdgeAntialiasing 是否使用 变形后的抗锯齿
backgroundColor 背景颜色
borderWidth 边框宽
borderColor 边框颜色
opacity 不透明度
shadowColor 阴影颜色
shadowOpacity 阴影不透明度
rasterizationScale 防止Retina屏幕像素化
shadowOffset 阴影偏移量
shadowRadius 阴影的半径
```

## CAAnimation

### 1. 关键帧动画 CAKeyframeAnimation

关键帧动画，`CAKeyframeAnimation` 是CAPropertyAnimation的子类。
与 `CABasicAniamtion` 的区别是`CABasicAniamtion`是从`fromValue` 到`toValue` 两个值的变化，而 `CAKeyframeAnimation` 可以使用 `NSArray` 来保存这些数值。

==属性说明：==


**values：** NSArray 对象，里面的元素就是“关键帧”，动画会在指定的时间内，依次显示 values 数组中的每一个关键帧。

**path:** 可以设置`CGPathRef`、`CGMutablePathRef`，让图层按照轨迹移动，`path`只对`CALayer`的 `anchorPoint` 和 `point` 起作用。如果设置了`path`， 那么`values`将被忽略。

**keyTimes:** 关键帧指定对应的时间点，取值范围从0~1，keyTimes中的每一个时间值对应values中的每一帧，如果没有设置，时间是平分的。

`CABasicAniamtion` 可看做 只有两个关键帧的`CAKeyframeAnimation`。

### 2. 动画组 CAAnimationGroup

`CAAnimationGroup` 是 `CAAnimation` 的子类，可以保存一组动画对象，将 `CAAnimationGroup` 对象加入层之后，族中所有的动画对象可以同时并发运行。

==属性说明：==

**animations：** 保存一组动画对象的NSArray。

默认情况下，一组动画对象是同时运行的，也可以通过设置动画对象的 `beginTimer` 属性来更改动画的开始时间。


### 3.转场动画 CATransition

`CATransition`为层提供移出屏幕和移入屏幕的动画效果。UINavigationController就是通过CATransition实现了将控制器推入屏幕的动画效果。

==属性说明：==

**type：** 动画过渡类型

**subtype：**动画过渡方向

**startProgress：**动画起点

**endProgress：**动画终点


## 代码示例

### 1. CABasicAnimation 闪烁

![闪烁.gif](http://upload-images.jianshu.io/upload_images/2417618-80da35263a680863.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```objectivec

    CALayer *layer = [CALayer layer];
    layer.frame = CGRectMake(80, 100, 150, 150);
    layer.cornerRadius = 75;
    layer.backgroundColor =[UIColor redColor].CGColor;    
    [self.view.layer addSublayer:layer];
    
    CABasicAnimation *animation = [CABasicAnimation animation];
    animation.keyPath = @"opacity";
    animation.fromValue = @1;
    animation.toValue = @0;
    animation.duration = 1;
    animation.repeatCount = MAXFLOAT;
    animation.removedOnCompletion = NO;
    animation.fillMode = kCAFillModeBackwards;
    [layer addAnimation:animation forKey:nil];
```

### 2. CAKeyframeAnimation 路径移动

![移动.gif](http://upload-images.jianshu.io/upload_images/2417618-ca6b28e3eb090f75.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```objectivec
    CAKeyframeAnimation *animation = [CAKeyframeAnimation animation];
    animation.keyPath = @"position";
    UIBezierPath *path = [UIBezierPath bezierPathWithArcCenter:self.view.center radius:100 startAngle:0 endAngle:M_PI*2 clockwise:YES];
    animation.path = path.CGPath;
    animation.repeatCount = MAXFLOAT;
    animation.duration = 2;
    animation.removedOnCompletion = NO;
    animation.fillMode = kCAFillModeBoth;
    [layer addAnimation:animation forKey:nil];
```

### 3. CAKeyframeAnimation 抖动

![抖动.gif](http://upload-images.jianshu.io/upload_images/2417618-d46617f989d2fd1e.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```objectivec
    CALayer *layer = [CALayer layer];
    layer.frame = CGRectMake(80, 100, 150, 150);
    layer.backgroundColor =[UIColor redColor].CGColor;
    [self.view.layer addSublayer:layer];
    
    #define angleRadion(angle) (angle / 180.0 * M_PI)
    //抖动
    CAKeyframeAnimation*animation = [CAKeyframeAnimation animation];
    animation.keyPath = @"transform.rotation";
    animation.values = @[@(angleRadion(-5)),@(angleRadion(5)),@(angleRadion(-5))];
    animation.duration = 0.15;
    animation.repeatCount = MAXFLOAT;
    [layer addAnimation:animation forKey:nil];
```

### 4. CATransition 转场

![转场.gif](http://upload-images.jianshu.io/upload_images/2417618-e3256e1b3a4b0c7f.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```objectivec
    CALayer *layer = [CALayer layer];
    layer.frame = CGRectMake(80, 100, 150, 150);
    layer.backgroundColor =[UIColor redColor].CGColor;
    [self.view.layer addSublayer:layer];
    
    CATransition *animation = [CATransition animation];
    // 指定转场类型
    animation.type = @"pageCurl";
    // 设置转场的方向
    animation.subtype = kCATransitionFromLeft;
    // 设置动画的进度
    animation.startProgress = 0;
    animation.endProgress = 1;
    animation.duration = 1;
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
       [layer addAnimation:animation forKey:nil];
    });
```

### 5. CAGradientLayer 渐变颜色

![渐变.gif](http://upload-images.jianshu.io/upload_images/2417618-9b4edba883ac641c.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```objectivec
    CAGradientLayer *layer =[CAGradientLayer layer];
    layer.frame =self.view.frame;
    //设置渐变的颜色数组
    layer.colors =@[(id)[UIColor yellowColor].CGColor,
                    (id)[UIColor orangeColor].CGColor,
                    (id)[UIColor greenColor].CGColor,
                    (id)[UIColor blueColor].CGColor,
                    (id)[UIColor grayColor].CGColor,
                    (id)[UIColor redColor].CGColor];
    //设置透明度
    layer.opacity = 0.2;
    //设置起始点
    layer.startPoint  = CGPointMake(0, 0);
    //设置
    layer.locations =@[@0.1,@0.2,@0.3,@0.4,@0.5];
    [self.view.layer addSublayer:layer];
```

### 6. CAEmitterLayer 粒子发送器

![粒子.gif](http://upload-images.jianshu.io/upload_images/2417618-8b69c271e90daabb.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```objectivec
   CAEmitterLayer *emitterLayer =[CAEmitterLayer layer];
    emitterLayer.frame = CGRectMake(50, 300, 100, 400);
    // 设置粒子发送器 每秒钟发送的数量
    emitterLayer.birthRate = 1;
    //设置粒子的中心点
    emitterLayer.emitterPosition = self.view.center;
    emitterLayer.renderMode = kCAEmitterLayerBackToFront;
    
    //初始化粒子
    CAEmitterCell *cell = [CAEmitterCell emitterCell];
    cell.contents = (id)[UIImage imageNamed:@"粒子.png"].CGImage;
    //粒子的出生量
    cell.birthRate = 2;
    //粒子的存活时间，默认是0，单位是秒
    cell.lifetime = 5;
    cell.lifetimeRange = 11;
    //粒子发送的速度,默认是0
    cell.velocity = 100;
    cell.velocityRange = 10;
    //设置粒子发送的 方向
    cell.emissionLongitude = 30*M_PI/180;
    cell.emissionLatitude = M_PI/180;
    //设置粒子散发的范围 弧度
    cell.emissionRange = 180*M_PI/180;
    //设置粒子的加速度
    cell.yAcceleration = -100;
    
    // 把粒子的cell 放到粒子发送器上
    emitterLayer.emitterCells = @[cell];
    
    [self.view.layer addSublayer:emitterLayer];
```