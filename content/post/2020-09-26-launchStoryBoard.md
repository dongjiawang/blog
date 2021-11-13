---
layout: post

title: 启动页白屏
 
date: 2020-09-26 17:00:00

categories: ["iOS"]

tags: 

mathjax: true

---

使用 `storyboard`作为启动页之后可能会出现加载图片失败的问题，也就是白屏。

#### 第一种

一般的解决方法是把启动页用的图片从`Image.xcassets`中移出来，放到项目的根目录下。这种方式可以解决大部分遇到的白屏问题。

但是还有可能在某些机器在某种情形下出现白屏问题。

#### 第二种

在查看沙盒文件的时候，发现在`library`中有启动页的缓存文件。路径大概如下：

> ~/Library/SplashBoard/Snapshots

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.3/img/20200926143233.png)

在以`bundleid`开头的文件夹下面有两张`ktx`格式的启动页缓存图。可以在`app`启动后，在子线程开启一次获取这个路径下缓存的图的操作，然后判断图片是否为纯色图，如果是就删除，等下次启动的时候重新生成。

简单写了些代码逻辑：

```objectivec
#pragma mark ----清理沙盒中的启动页缓存图片----

/// 建议在子线程中调用这个方法
- (void)cleanSplashBoardCache {
    NSString *filePath = [NSString stringWithFormat:@"%@/Library/SplashBoard/Snapshots", NSHomeDirectory()];
    for (UIImage *image in [self getSplashBoardImageCache:filePath]) {
        if ([self judgePureImage:image]) {
            if ([[NSFileManager defaultManager] fileExistsAtPath:filePath]) {
                NSError *error = nil;
                [[NSFileManager defaultManager] removeItemAtPath:filePath error:&error];
                if (error) {
                    NSLog(@"清除LaunchScreen缓存失败");
                } else {
                    NSLog(@"清除LaunchScreen缓存成功");
                }
            }
            
            break;
        }
    }
}

- (NSArray *)getSplashBoardImageCache:(NSString *)filePath {
    NSMutableArray *array = [NSMutableArray array];
    NSArray *snapshotsArray = [[NSFileManager defaultManager] contentsOfDirectoryAtPath:filePath error:nil];
    NSString *bundleID = [[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleIdentifier"];
    for (NSString *directoryName in snapshotsArray) {
        // 图片缓存在 bundleid 开头的文件夹中
        if ([directoryName hasPrefix:bundleID]) {
            filePath = [filePath stringByAppendingFormat:@"/%@", directoryName];
            break;;
        }
    }
    
    NSArray *imagePathArray = [[NSFileManager defaultManager] contentsOfDirectoryAtPath:filePath error:nil];
    for (NSString *imageName in imagePathArray) {
        NSString *imagePath = [filePath stringByAppendingFormat:@"/%@", imageName];
        // ktx 格式的图片
        if ([imagePath hasSuffix:@"ktx"]) {
            NSData *data = [NSData dataWithContentsOfFile:imagePath];
            if (data.length) {
                UIImage *image = [UIImage imageWithData:data];
                [array addObject:image];
            }
        }
    }
    
    return array;
}

- (BOOL)judgePureImage:(UIImage *)image {
    int bitmapInfo = kCGBitmapByteOrderDefault | kCGImageAlphaPremultipliedLast;
    //第一步：先把图片缩小，加快计算速度，但越小结果误差可能越大
    CGSize thumbSize = CGSizeMake(50,50);
    CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
    CGContextRef context = CGBitmapContextCreate(NULL,
                                                thumbSize.width,
                                                thumbSize.height,
                                                8,//bits per component
                                                thumbSize.width*4,
                                                colorSpace,
                                                bitmapInfo);
    CGRect drawRect = CGRectMake(0, 0, thumbSize.width, thumbSize.height);
    CGContextDrawImage(context, drawRect, image.CGImage);
    CGColorSpaceRelease(colorSpace);
    
    //第二步：取每个点的像素值
    unsigned char* data = CGBitmapContextGetData (context);
    
    if (data == NULL) return NO;
    
    BOOL isPureColorImage = NO;
    UIColor *tempColor;
    for (int x = 0; x < thumbSize.width; x++) {
        for (int y = 0; y < thumbSize.height; y++) {
            int offset = 4 * (x * y);
            int red = data[offset];
            int green = data[offset+1];
            int blue = data[offset+2];
            // 生成当前像素点到 color
            UIColor *pixelColor = RGBCOLOR(red, green, blue);
            // 判断是否有记录上一个的像素颜色
            if (tempColor) {
                // 对比两个像素颜色
                if (CGColorEqualToColor(pixelColor.CGColor, tempColor.CGColor)) {
                    // 颜色相同
                    isPureColorImage = YES;
                } else {
                    // 颜色不同
                    isPureColorImage = NO;
                    break;
                }
            }
            tempColor = RGBCOLOR(red, green, blue);
        }
    }
    CGContextRelease(context);
    
    return isPureColorImage;
}
```

但是还是会出现白屏的现象，并没有彻底解决这个问题。

#### 第四种

重启手机，万能的重启，这种方式需要用户去操作。会清理掉所有的缓存。