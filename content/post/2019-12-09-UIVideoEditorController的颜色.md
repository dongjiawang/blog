---
layout: post

title: UIVideoEditorController的颜色
 
date: 2019-12-09 12:00:00

categories: ["iOS"]

tags: 

mathjax: true

---

这篇博客介绍了[《UIVideoEditorController》](https://blog.dongjiawang.top/2019/12/06/2019-12-06-UIVideoEditorController/)的简单使用，但是系统的的颜色都是白色高斯模糊，放到一些APP的主题颜色中并不好看，所以需要修改一下控制器的导航栏和toolbar的颜色。

创建一个继承`UIVideoEditorController`的控制器，然后修改这个控制器的导航栏颜色，遍历控制器的`view`的`subviews`，找到`toolbar`，修改`toolbar`的属性。

```objectivec
- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    
    
    self.navigationBar.translucent = NO;
    self.navigationBar.barTintColor = [UIColor appThemeColor];
    self.navigationBar.tintColor = [UIColor whiteColor];
    self.navigationBar.titleTextAttributes = @{NSForegroundColorAttributeName : [UIColor whiteColor]};
    
    [self changeBottomToolBarColor:self.view];
}
```



```objectivec
- (void)changeBottomToolBarColor:(UIView *)view {
    for (UIView *subView in view.subviews) {
        if ([NSStringFromClass([subView class]) isEqualToString:@"UIToolbar"]) {
            UIToolbar *toolbar = (UIToolbar *)subView;
            toolbar.barTintColor = [UIColor appThemeColor];
            toolbar.tintColor = [UIColor whiteColor];
        }
        [self changeBottomToolBarColor:subView];
    }
}
```

效果如下图

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/IMG_C145ED458A94-1.jpeg)





