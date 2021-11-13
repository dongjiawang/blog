---
layout: post
title: 判断图片的类型
date: 2016-08-23 10:39:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

>从网络获取的图片URL. 举例如下:

```objectivec
   //假设这是一个网络获取的URL   
NSString *path = @"http://pic3.nipic.com/20090709/2893198_075124038_2.png";  
 // 判断是否为png 
  NSString *extensionName = path.pathExtension;    
if ([extensionName.lowercaseString isEqualToString:@"png"]) {
        //是png图片
    } else {
        //不是png图片   
 }
```
>其实图片数据的第一个字节是固定的,一种类型的图片第一个字节就是它的标识, 通过data来判断未知图片的类型

```objectivec
 //假设这是一个网络获取的URL
    NSString *path = @"http://pic.rpgsky.net/images/2016/07/26/3508cde5f0d29243c7d2ecbd6b9a30f1.png";
    NSData *data = [NSData dataWithContentsOfURL:[NSURL URLWithString:path]];
    //调用获取图片扩展名
    NSString *string = [self contentTypeForImageData:data];
    //输出结果为 png 
   NSLog(@"%@",string);
```

```objectivec
//通过图片Data数据第一个字节 来获取图片扩展名
- (NSString *)contentTypeForImageData:(NSData *)data {
    uint8_t c;
    [data getBytes:&c length:1];
    switch (c) {
        case 0xFF:
            return @"jpeg";
        case 0x89: 
           return @"png";
        case 0x47:
            return @"gif"; 
        case 0x49:
        case 0x4D:
            return @"tiff";               
        case 0x52:
     if ([data length] < 12) {
          return nil;
      }
     NSString *testString = [[NSString alloc] initWithData:[data subdataWithRange:NSMakeRange(0, 12)] encoding:NSASCIIStringEncoding];
     if ([testString hasPrefix:@"RIFF"] && [testString hasSuffix:@"WEBP"]) {
         return @"webp";
      }
     return nil;
    }
    return nil;
}
```