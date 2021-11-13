---
layout: post
title: UIScrollView的基本使用
date: 2015-12-30 15:56:00
categories: ["iOS"]
tags: ["UIScrollView"]
mathjax: true
---


```objectivec
    scrollView = [[UIScrollView alloc] initWithFrame:CGRectMake(0, 0, 320, 460)];
    scrollView.backgroundColor = [UIColor redColor];
    // 是否支持滑动最顶端
    scrollView.scrollsToTop = NO;
    scrollView.delegate = self;
    // 设置内容大小
    scrollView.contentSize = CGSizeMake(320, 460*10);
    // 是否反弹
    scrollView.bounces = NO;
    // 是否分页
    scrollView.pagingEnabled = YES;
    // 是否滚动
    scrollView.scrollEnabled = NO;
    //是否显示滚动条
    scrollView.showsHorizontalScrollIndicator = NO;
    // 设置indicator风格
    scrollView.indicatorStyle = UIScrollViewIndicatorStyleWhite;
    // 设置内容的边缘和Indicators边缘
    scrollView.contentInset = UIEdgeInsetsMake(0, 50, 50, 0);
    scrollView.scrollIndicatorInsets = UIEdgeInsetsMake(0, 50, 0, 0);
    // 提示用户,Indicators flash
    [scrollView flashScrollIndicators];
    // 是否同时运动,lock
    scrollView.directionalLockEnabled = YES;
```

```objectivec
// 返回一个放大或者缩小的视图
- (UIView *)viewForZoomingInScrollView:(UIScrollView *)scrollView {
     
}
// 开始放大或者缩小
- (void)scrollViewWillBeginZooming:(UIScrollView *)scrollView withView:
(UIView *)view {
     
}
 
// 缩放结束时
- (void)scrollViewDidEndZooming:(UIScrollView *)scrollView withView:(UIView *)view atScale:(float)scale  {
     
}
  
// 视图已经放大或缩小
- (void)scrollViewDidZoom:(UIScrollView *)scrollView {
    NSLog(@"scrollViewDidScrollToTop");
}
 
// 是否支持滑动至顶部
- (BOOL)scrollViewShouldScrollToTop:(UIScrollView *)scrollView  {
    return YES;
}
 
// 滑动到顶部时调用该方法
- (void)scrollViewDidScrollToTop:(UIScrollView *)scrollView {
    NSLog(@"scrollViewDidScrollToTop");
}
 
// scrollView 已经滑动
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    NSLog(@"scrollViewDidScroll");
}
 
// scrollView 开始拖动
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView {
    NSLog(@"scrollViewWillBeginDragging");
}
 
// scrollView 结束拖动
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate {
    NSLog(@"scrollViewDidEndDragging");
}
 
// scrollView 开始减速（以下两个方法注意与以上两个方法加以区别）
- (void)scrollViewWillBeginDecelerating:(UIScrollView *)scrollView {
    NSLog(@"scrollViewWillBeginDecelerating");
}
 
// scrollview 减速停止
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView {
   NSLog(@"scrollViewDidEndDecelerating"); 
}
```

**有时候需要做出翻页效果，使用代理方法得到现在所在的页面数**

```objectivec
-(void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView{
   CGPoint offset = scrollView.contentOffset; CGFloat currentPage = offset.x / (self.view.bounds.size.width); 
//计算当前的页码 
}
```

**或者使scrllView滚动到指定页**

```objectivec
//在停止拖动的时候计算偏移量

-(void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView{
      //计算当前的页码
     CGPoint offset = scrollView.contentOffset;
        pageController.currentPage = offset.x / (self.view.bounds.size.width); 
        //设置scrollview的显示为当前滑动到的页面      
        [scrollView setContentOffset:CGPointMake(self.view.bounds.size.width * (pageController.currentPage),               scroll.contentOffset.y) animated:YES];         
}
```