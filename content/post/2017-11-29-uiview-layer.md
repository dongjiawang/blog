---
layout: post
title:  UIView 的 layer
date: 2017-11-29 23:30:00
categories: ["iOS"]
tags: ["UIView"] 
mathjax: true
---


iOS 中能看得见的控件基本都是 UIView，比如按钮、文本、输入框、图片等等。

UIView 之所以能显示在屏幕上，因为它内部有一个图层：`CALayer`

```objectivec
@property(nonatomic,readonly,retain) CALayer *layer;
```

当 UIView 显示在屏幕上的时候，会调用 `drawRect:` 方法进行绘图，会将所有的内容绘制在自己的图层上，绘制完毕后，系统会将图层拷贝到屏幕上，于是 UIView 就显示了出来。

通过下面的方法可以获取到 view 的图层。

```objectivec 
CALayer *layer = self.view.layer;
```

一个 UIView 只有一个 layer 属性，但是 layer 是可以叠加的，多个 layer 叠加在一起就构成了一个组合图像。

### 添加图层

一个图层可以有多个子图层。在绘制屏幕的时候，子图层可以被排列后固定在一起。

比如游戏图层，一个绘制背景，一个绘制角色，一个绘制地图。

可以为每个图层准备一个专门的 UIView，然后用另外一个 UIView 来整合图层：

```objectivec
// 整合的 UIView 类
UIView *gameView = [[UIView alloc]initWithFrame:[[UIScreen mainScreen] applicationFrame]];  
// 背景图层
UIView *backgroundView = [[UIView alloc] initWithFrame:[[UIScreen mainScreen] applicationFrame]];  
// 角色图层
UIView *roleView= [[UIView alloc] initWithFrame:CGRectMake(0.0, 0.0, 100.0, 200.0)];  
// 地图图层
UIView *mapView = [[UIView alloc] initWithFrame:CGRectMake(200.0, 0.0, 100.0, 100.0)];  

//通过CALayer 类的addSublayer 方法，可以将3个UIView 类的图层全都与 gameView 对象链接在一起 
CALayer* gameLayer = gameView.layer;  
[gameLayer addSublayer:backgroundView.layer];  
[gameLayer addSublayer:roleView.layer];  
[gameLayer addSublayer:mapView.layer]; 
```

当 gameView 对象显示在屏幕上的时候，3个子图层被合并在一起绘制出来。每个类单独绘制他自己的图层，但当游戏图层被显示出来的时候，3个图层就全都融合在一起了。

子图层也可以添加自己的子图层，并且可以构建一个完整的图层层次结构。

```objectivec
//mapView图层中再构加入一个图层，用来显示剩余里程数
UILabel *lastDistance = [[UILabel alloc] initWithFrame:CGRectMake(0.0, 0.0, 20.0, 20.0)];  
[mapView.layer addSublayer:lastDistance.layer]; 
```

通过设置图层的position属性，你还可以不用改变图层的大小就对其位置进行调整。这个属性接受一个CGPoint 结构体，来定位图层在屏幕上的偏移位置。与frame 属性不同，position 属性指定的是图层的重点，而不是左上角：

```objectivec
CGPoint lastDistancePosition = CGPointMake(100.0, 100.0);  
lastDistance.layer.position = lastDistancePosition;
```


可以使用 `addSublayer`讲图层添加到其他图层的顶层，也可以使用 `insertSublayer`将图层插入到现有的图层之间。

使用 `avove` 或者 `below` 参数，可以将图层插入到指定图层的上面或者下面：

```objectivec
[gamelayer insertSublayer:mapView.layer below:backgroundView.layer];  

[gamelayer insertSublayer:mapView.layer above:roleView.layer]; 
```

使用 `removeFromSuperlayer` 方法可以将图层从父图层上移除。

使用 `replaceSublayer` 可以替换某个图层

```objectivec
[gamelayer replaceSublayer:backgroundView.layer with:newBackgroundView.layer ]; 
```

layer 同样有 hidden 属性，可以保留在父图层的同时，将它隐藏。

### 图层绘制

在更新一个图层时，变化不是立刻被绘制在屏幕上的。这样就可以对图层做很多写操作而不会被展示给用户，直到所有的操作全部结束为止。当图层准备好可以进行重画时，就调用图层的 `setNeedsDisplay`  方法：

```objectivec
[gamelayer setNeedsDisplsy];
```

有时候不需要重绘整个图层，只需要重新绘制某一部分，就可以使用 `setNeedsDisplayInRect:`:

```objectivec
CGRect mapViewFrame = CGRectMake(150.0, 150.0, 75.0, 75.0);  
[gameLayer setNeedsDisplayInRect:mapViewFrame];
```

可以使用 `transform` 或者 `affineTransform` 属性对 layer 进行变换

```objectivec
roleview.layer.transform = CATransform3DMakeScale(-1.0, -1.0, 1.0);  
CGAffineTransform transform = CGAffineTransformMakeRotation(45.0);  
backgroundView.layer.affineTransform = transform ; 
```
### 动画

结合 `Core Animation` 可以创建出很多的动画效果。

例如转场动画：

```objectivec
CATransition* animation = [[CATransition alloc]init];  
 animation.duration =1.0;  
 animation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseIn];  
 animation.type = kCATransitionPush;  
 animation.subtype = kCATransitionFromRight;
```

`CABasicAnimation` 旋转动画

```objectivec
CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"transform"];  
animation.toValue = [NSValue valueWithCATransform3D:CATransform3DMakeRotation(3.1415, 0, 0, 1.0)];  
animation.duration = 3.0;  
animation.cumulative = YES;  
animation.repeatCount = 2; 
```

创建好之后，你可以直接将动画或者转场应用到一个图层上

```objectivec
[mapView.layer addAnimation:animation forKey:@"animation"];  
```

`Quartz Core` 的渲染能力可以像三维一样对二维图像进行任意操纵。一个图像可以在 `x-y-z` 三维轴上进行任意角度旋转、缩放和扭曲。

变换是在单独的图层上执行的，因此多个变换可以在一个图层表面上同时进行。

`Quartz Core` 框架用 `CATransform3D` 对象来执行变换。这个对象作用于视图的图层，根据期望的三维设置对图层进行弯折或者其他操作。

对一个图层进行 3D 旋转：

```objectivec
CATransform3D myTransform;  
myTransform = CATransform3DMakeRotation(angle,x,y,z);  
```

>`CATransform3DMakeRotation` 函数创建了一个变换，对一个图形进行旋转，旋转角度`angle`单位为弧度，轴为 `x-y-z`。`x-y-z`的值定义了轴上在各个方向上的度量(介于-1和+1之间)。在一个轴上赋予值，就会指示变换绕该轴进行旋转。

要将一个图层绕水平轴转动（垂直转动）45度，可以使用下面的代码：

```objectivec
myTransform = CATransform3DMakeRotation(0.78, 1.0, 0.0, 0.0);
//0.78 是角度45度换成弧度得到的  
```

要在水平方向上转动同样的角度，可以换成 y 轴指定一个值：

```objectivec
myTransform = CATransform3DMakeRotation(0.78, 0.0, 1.0, 0.0); 
```

CALayer 对象提供了一个 transform属性，可以用来将变换附加到图层之上。

```objectivec
roleView.layer.transform = myTransform; 
```