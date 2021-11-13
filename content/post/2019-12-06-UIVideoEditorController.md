---
layout: post

title: UIVideoEditorController
 
date: 2019-12-06 12:00:00

categories: ["iOS"]

tags: 

mathjax: true

---

在使用`UIImagePickerController`拍摄视频的时候如果设置`allowsEditing`为`YES`，在拍摄完成后可以对视频进行裁剪。

如果需要裁剪相册中的视频，可以直接使用系统提供的`UIVideoEditorController`。

在使用的时候可以先调用这个类方法判断视频是否可以裁剪

```objectivec
+ (BOOL)canEditVideoAtPath:(NSString *)videoPath;
```

`UIVideoEditorController`提供下面三个属性

```objectivec
// 视频文件的路径
@property(nonatomic, copy)     NSString                              *videoPath;
// 裁剪出视频的时长，默认10分钟
@property(nonatomic)           NSTimeInterval                        videoMaximumDuration; // default value is 10 minutes. set to 0 to specify no maximum duration.
// 导出视频的清晰度
@property(nonatomic)           UIImagePickerControllerQualityType    videoQuality;    
```

`UIVideoEditorController`的代理回调



需要引用` <UIVideoEditorControllerDelegate, UINavigationControllerDelegate>`

```objectivec
// 编辑并保存成功
- (void)videoEditorController:(UIVideoEditorController *)editor didSaveEditedVideoToPath:(NSString *)editedVideoPath; // edited video is saved to a path in app's temporary directory
// 编辑失败
- (void)videoEditorController:(UIVideoEditorController *)editor didFailWithError:(NSError *)error;
// 取消编辑
- (void)videoEditorControllerDidCancel:(UIVideoEditorController *)editor;
```

效果如下图

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/IMG_1A99528EA3F1-3.jpeg)



