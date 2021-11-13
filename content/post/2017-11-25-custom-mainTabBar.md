---
layout: post
title:  自定义 Tabbar 控制器中间按钮
date: 2017-11-25 09:30:00
categories: ["iOS"]
tags: ["iOS"] 
mathjax: true
---

<img src="http://upload-images.jianshu.io/upload_images/2417618-be8464f84b578c61.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="示例" style="zoom:30%;" />

**自定义主控制器**

自定义 `MainTabBarViewController` 继承于 `UITabBarController`。

这个控制器引用 `UITabBarControllerDelegate`,通过这个代理的方法获取到当前点击的 `tabbarItem`。

```objectivec
self.delegate = self;
```

**添加子控制器**

```objectivec
- (void)addChildViewControllers {
 	// 根据需求在这里添加子控制器
    HomeViewController *home = [[HomeViewController alloc] init];
    [self addTabItem:home image:nil selectedImage:nil title:@" 首页"];
    
    // 在中间的位置添加一个空的控制器，这样做是为了让底部的 tabbar 排版更好看，加号按钮刚好在中间的这个 item 位置
    UIViewController *ctrl = [[UIViewController alloc] init];
    [self addTabItem:ctrl image:nil selectedImage:nil title:@" "];
    
    MineViewController *mine = [[MineViewController alloc] init];
    [self addTabItem:mine image:nil selectedImage:nil title:@"我的"];
}
```

添加 tabbar

```objectivec
- (void)addTabItem:(UIViewController *)controller image:(UIImage *)image selectedImage:(UIImage *)selectedImage title:(NSString *)title {
    controller.navigationItem.title = title ;
    controller.title = title; //同时设置tabBar和nav的title
    controller.tabBarItem.image = image;
    controller.tabBarItem.selectedImage = [selectedImage imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];;
    
    // 调整tabBarItem的图片 和 文字的位置
    controller.tabBarItem.imageInsets = UIEdgeInsetsMake(-5, 0, 5, 0);
    controller.tabBarItem.titlePositionAdjustment = UIOffsetMake(0, -5);
    
    [self addChildViewController:controller];
}
```

**自定义加号按钮**

```objectivec
-(void)addCenterButtonWithImage:(UIImage*)buttonImage selectedImage:(UIImage*)selectedImage {
    self.plusBtn = [UIButton buttonWithType:UIButtonTypeCustom];
    self.plusBtn.clipsToBounds = YES;
    self.plusBtn.layer.cornerRadius = 30;
    [self.plusBtn addTarget:self action:@selector(clickedPlusBtn:) forControlEvents:UIControlEventTouchUpInside];
    self.plusBtn.autoresizingMask = UIViewAutoresizingFlexibleRightMargin
                                    | UIViewAutoresizingFlexibleLeftMargin
                                    | UIViewAutoresizingFlexibleBottomMargin
                                    | UIViewAutoresizingFlexibleTopMargin;
    
    //  设定button大小为适应图片
    self.plusBtn.frame = CGRectMake(0.0, 0.0, 60, 60);
    [self.plusBtn setImage:buttonImage forState:UIControlStateNormal];
    [self.plusBtn setImage:selectedImage forState:UIControlStateSelected];    
    
    //  这个比较恶心  去掉选中button时候的阴影
    self.plusBtn.adjustsImageWhenHighlighted = NO;
    CGPoint center = self.tabBar.center;
    center.y = center.y - 30;
    self.plusBtn.center = center;
    
    //按钮就放在当前主控制器的 view 上
    [self.view addSubview:self.plusBtn];
}
```

按钮点击

```objectivec
-(void)clickedPlusBtn:(id)sender {
    // 点击加号需要的操作
    // 如果要是push 或者 present 新的界面，最好是用根控制器的 navigationControler
}
```

**拦截 tabbar 的点击**

```objectivec
//每次点击tabBarItem后触发这个方法
- (void)tabBarController:(UITabBarController *)tabBarController didSelectViewController:(UIViewController *)viewController{
    if (self.selectedIndex == 1) { // 因为加号按钮添加到了下标为 1 的 item 上面，所以如果点击到这个位置，就过滤掉这个 item 的响应
        return;
    }
    self.title  = viewController.title;
}

//点击中间的 tablebar 拦截
// 因为加号按钮添加到了下标为 1 的 item 上面，所以如果点击到这个位置，就过滤掉这个 item 的响应
- (BOOL)tabBarController:(UITabBarController *)tabBarController shouldSelectViewController:(UIViewController *)viewController {
    if (viewController == [tabBarController.viewControllers objectAtIndex:1]) {
        return NO;
    }
    return YES;
}
```

​	