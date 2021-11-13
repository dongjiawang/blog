---
layout: post
title: 获取相册视频并保存到指定路径
date: 2017-11-20 17:40:00
categories: ["iOS"]
tags:  ["Photos"] 
mathjax: true
---

需要用到两个系统框架

> Photos/Photos.h 用来获取系统的相册信息

> AVFoundation/AVFoundation.h 用来获取视频信息


**首先获取相册中的所有视频**

```objectivec
NSMutableArray *videoArray = [NSMutableArray array];
PHFetchOptions *option = [[PHFetchOptions alloc] init];
    option.sortDescriptors = @[[NSSortDescriptor sortDescriptorWithKey:@"creationDate" ascending:NO]];
    PHFetchResult *result = [PHAsset fetchAssetsWithMediaType:PHAssetMediaTypeVideo options:option];
    
    [result enumerateObjectsUsingBlock:^(PHAsset *  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        //这个obj即PHAsset对象,判断 obj 的类型，如果是 video，就添加到自定义的数组中记录
        if (obj.mediaType == PHAssetMediaTypeVideo) {
          [videoArray addObject:obj];
        }
    }];
```

**获取视频对应的** AVAsset

```objectivec
- (void)requestVideo:(PHAsset *)PHAsset compeletion:(void (^)(AVAsset *))compeletion {
    PHVideoRequestOptions *options = [[PHVideoRequestOptions alloc] init];
    options.version = PHImageRequestOptionsVersionCurrent;
    options.deliveryMode = PHVideoRequestOptionsDeliveryModeAutomatic;
    [[PHImageManager defaultManager] requestAVAssetForVideo:PHAsset options:options resultHandler:^(AVAsset * _Nullable asset, AVAudioMix * _Nullable audioMix, NSDictionary * _Nullable info) {
        compeletion(asset);
    }];
}
```

**利用AVAsset获取视频的信息**

```objectivec
NSString *videoPath = @"";// 需要保存到路径
AVAssetExportSession *session = [AVAssetExportSession exportSessionWithAsset:asset presetName:AVAssetExportPresetHighestQuality];
        session.outputFileType = AVFileTypeMPEG4;// 输出的视频格式
        session.outputURL = [NSURL fileURLWithPath:videoPath]; // 这个就是你可以导出的文件路径了。
        
        [session exportAsynchronouslyWithCompletionHandler:^{
				保存成功后的回调        
        }];
```



