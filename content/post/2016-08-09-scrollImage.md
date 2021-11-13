---
layout: post
title: 可以放大、缩小、滑动的imageView
date: 2016-08-09 10:54:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

## 示例图片##
![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926120348.gif)

## 代码##
```objectivec
//底部srollView
@property (nonatomic, strong) UIScrollView                    *imgScroll;
//显示图片
@property (nonatomic, strong) UIImageView                     *myImageView;
```

```objectivec
-(UIScrollView *)imgScroll {
    if (!_imgScroll) {
        _imgScroll = [[UIScrollView alloc] initWithFrame:self.view.bounds];
        _imgScroll.delegate = self;
        //底部不要透明，否则图片缩小的时候会看到被遮住的界面
        _imgScroll.backgroundColor = [UIColor whiteColor];
        //最大级别
        _imgScroll.maximumZoomScale = 5;
        //初始化缩放级别
        self.imgScroll.zoomScale = 1;
        [self.view addSubview:_imgScroll];
    }
    return _imgScroll;
}
```

```objectivec
-(UIImageView *)myImageView {
    if (!_myImageView) {
        _myImageView = [[UIImageView alloc] initWithFrame:self.imgScroll.bounds];
        _myImageView.backgroundColor = [UIColor clearColor];
        _myImageView.image = [UIImage imageNamed:@"123.png"];
        [self.imgScroll addSubview:_myImageView];
    }
    return _myImageView;
}
```

```objectivec
- (void)openImage:(UIImage *)image {
     // 重置UIImageView的Frame，让图片居中显示
        self.myImageView.frame = CGRectMake(0, 0, self.imgScroll.width, self.imgScroll.width * image.size.height/image.size.width);
        [self scrollViewDidZoom:self.imgScroll];
        //设置scrollView的缩小比例
        CGSize maxSize = self.imgScroll.frame.size;
        CGFloat widthRatio = maxSize.width/localImage.size.width;
        CGFloat heightRatio = maxSize.height/localImage.size.height;
        CGFloat initialZoom = (widthRatio > heightRatio) ? heightRatio : widthRatio;
        self.imgScroll.minimumZoomScale = initialZoom;
```

```objectivec
// 设置UIScrollView中要缩放的视图
- (UIView *)viewForZoomingInScrollView:(UIScrollView *)scrollView {
    return self.myImageView;
}
```

```objectivec
// 让UIImageView在UIScrollView缩放后居中显示
- (void)scrollViewDidZoom:(UIScrollView *)scrollView {
    CGFloat offsetX = (scrollView.bounds.size.width > scrollView.contentSize.width)?
    (scrollView.bounds.size.width - scrollView.contentSize.width) * 0.5 : 0.0;
    CGFloat offsetY = (scrollView.bounds.size.height > scrollView.contentSize.height)?
    (scrollView.bounds.size.height - scrollView.contentSize.height) * 0.5 : 0.0;
    self.myImageView.center = CGPointMake(scrollView.contentSize.width * 0.5 + offsetX,
                            scrollView.contentSize.height * 0.5 + offsetY);
}
```