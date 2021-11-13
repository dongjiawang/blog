---
layout: post

title: 录屏功能
 
date: 2020-01-19 17:00:00

categories: ["iOS"]

tags: 

mathjax: true

---

苹果提供了`ReplayKit`对屏幕进行录制，支持音频、视频，还可以对视频进行剪辑、分享。

需要引用头文件`<ReplayKit/ReplayKit.h>`,通过`RPScreenRecorder`控制录屏。

## RPScreenRecorder

```objectivec
[[RPScreenRecorder sharedRecorder] startRecordingWithHandler:^(NSError * _Nullable error) {
        
}];
    
[[RPScreenRecorder sharedRecorder] setMicrophoneEnabled:YES];
```

> 开始录制，会请求摄像头和麦克风权限，如果用户拒绝，则无法进行录制。
>
> 录制内容无法直接查看，需要通过`RPPreviewViewController`才能查看、分享和保存到相册。

```objectivec
[[RPScreenRecorder sharedRecorder] stopRecordingWithHandler:^(RPPreviewViewController * _Nullable previewViewController, NSError * _Nullable error) {
        
 }];
```

也可以自己处理数据流：

```objectivec
[RPScreenRecorder sharedRecorder].delegate = self;// 设置代理
[RPScreenRecorder sharedRecorder].microphoneEnabled = YES;// 麦克风
[RPScreenRecorder sharedRecorder].cameraEnabled = YES; // 摄像头
```



```objectivec
[[RPScreenRecorder sharedRecorder] startCaptureWithHandler:^(CMSampleBufferRef  _Nonnull sampleBuffer, RPSampleBufferType bufferType, NSError * _Nullable error) {
        previewViewController.previewControllerDelegate = self;
} completionHandler:^(NSError * _Nullable error) {
        
}];

/* 接收到的 buffer类型，可以使用 AVAssetWriter  进行数据写入
API_AVAILABLE(ios(10.0),tvos(10.0))
typedef NS_ENUM(NSInteger, RPSampleBufferType) {
    RPSampleBufferTypeVideo = 1,
    RPSampleBufferTypeAudioApp,
    RPSampleBufferTypeAudioMic,
};
*/
```



**RPScreenRecorderDelegate**

停止的时候也可以通过代理方法使用 `RPPreviewViewController`

```objectivec
- (void)screenRecorder:(RPScreenRecorder *)screenRecorder didStopRecordingWithError:(NSError *)error previewViewController:(nullable RPPreviewViewController *)previewViewController API_DEPRECATED("No longer supported", ios(9.0, 10.0), tvos(10.0,10.0));
- (void)screenRecorder:(RPScreenRecorder *)screenRecorder didStopRecordingWithPreviewViewController:(nullable RPPreviewViewController *)previewViewController error:(nullable NSError *)error API_AVAILABLE(ios(11.0), tvos(11.0));
```

需要设置 `RPPreviewViewController`的代理。

```objectivec
previewViewController.previewControllerDelegate = self;
```

**RPPreviewViewController**

```objectivec
- (void)previewController:(RPPreviewViewController *)previewController didFinishWithActivityTypes:(NSSet<NSString *> *)activityTypes {
    if ([activityTypes containsObject: UIActivityTypeSaveToCameraRoll]) {
        NSLog(@"保存到相册");
    }
}
// 根据activityType 判断选择的操作
/*
UIKIT_EXTERN UIActivityType const UIActivityTypePostToFacebook     API_AVAILABLE(ios(6.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypePostToTwitter      API_AVAILABLE(ios(6.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypePostToWeibo        API_AVAILABLE(ios(6.0)) __TVOS_PROHIBITED;    // SinaWeibo
UIKIT_EXTERN UIActivityType const UIActivityTypeMessage            API_AVAILABLE(ios(6.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypeMail               API_AVAILABLE(ios(6.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypePrint              API_AVAILABLE(ios(6.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypeCopyToPasteboard   API_AVAILABLE(ios(6.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypeAssignToContact    API_AVAILABLE(ios(6.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypeSaveToCameraRoll   API_AVAILABLE(ios(6.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypeAddToReadingList   API_AVAILABLE(ios(7.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypePostToFlickr       API_AVAILABLE(ios(7.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypePostToVimeo        API_AVAILABLE(ios(7.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypePostToTencentWeibo API_AVAILABLE(ios(7.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypeAirDrop            API_AVAILABLE(ios(7.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypeOpenInIBooks       API_AVAILABLE(ios(9.0)) __TVOS_PROHIBITED;
UIKIT_EXTERN UIActivityType const UIActivityTypeMarkupAsPDF        API_AVAILABLE(ios(11.0)) __TVOS_PROHIBITED;
*
```





## extension方式录制视频

通过Xcode的`file -> new -> target `创建`extension`

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20200119152644.png)

如果录屏或直播屏幕前需要用户输入一些内容或其他操作，需要同时添加 `UI Extension`

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20200119153225.png)

创建完成后就出现`Extension`的代码

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20200119154545.png)

具体的录制功能在`SampleHandler`中实现,由于iOS沙盒机制的存在，`extension`的数据并不能直接被App使用，可以使用`AppGroup`进行数据共享。从而将录制的视频保存到App中。

**RPSystemBroadcastPickerView 按钮**

需要通过`replaykit`提供的`RPSystemBroadcastPickerView` 启动录屏，把`RPSystemBroadcastPickerView`放到需要的view上，通过点击调起`Extension`。

```objectivec
RPSystemBroadcastPickerView *broadcastPickerView = [[RPSystemBroadcastPickerView alloc] initWithFrame:CGRectMake(0, 0, 100, 100)];
broadcastPickerView.preferredExtension = kBroadcastExtensionBundleId;  // extension的id
[self.view addSubview:broadcastPickerView];
broadcastPickerView.center = self.view.center;
```

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20200119163751.jpeg)

**`SampleHandler`中的视频写入操作**

```objectivec
@property (nonatomic, strong) AVAssetWriter *assetWriter;
@property (nonatomic, strong) AVAssetWriterInput *assetWriterInput;
@property (nonatomic, strong) AVAssetWriterInput *assetAudioInput;
@property (nonatomic, strong) NSString *videoOutPath; // 当前沙盒保存视频的路径

@property (nonatomic, assign) BOOL  isFirstSample; // 是否第一帧数据
```

```objectivec
- (void)broadcastStartedWithSetupInfo:(NSDictionary<NSString *,NSObject *> *)setupInfo {
	// 视频开始录制时调用
  // info中可以是从 UI 中传递过来的参数
}
```

```objectivec
 // 定义 AVAssetWriter
NSNumber *width= [NSNumber numberWithFloat:[[UIScreen mainScreen] bounds].size.width];
NSNumber *height = [NSNumber numberWithFloat:[[UIScreen mainScreen] bounds].size.height];
// 视频参数

NSDictionary *compressionProperties =
    @{AVVideoProfileLevelKey         : AVVideoProfileLevelH264HighAutoLevel,
      AVVideoH264EntropyModeKey      : AVVideoH264EntropyModeCABAC,
      AVVideoAverageBitRateKey       : @(1920 * 1080 * 11.4),
      AVVideoMaxKeyFrameIntervalKey  : @30,
      AVVideoAllowFrameReorderingKey : @NO};

NSDictionary *videoSettings =
    @{
        AVVideoCompressionPropertiesKey : compressionProperties,
        AVVideoCodecKey                 : AVVideoCodecTypeH264,
        AVVideoWidthKey                 : @(width),
        AVVideoHeightKey                : @(height)
    };
// 音频参数
AudioChannelLayout acl;
    bzero( &acl, sizeof(acl));
    acl.mChannelLayoutTag = kAudioChannelLayoutTag_Mono;

NSDictionary *audioOutputSettings = [NSDictionary dictionaryWithObjectsAndKeys:
                                         [NSNumber numberWithInt: kAudioFormatMPEG4AAC ], AVFormatIDKey,
                                         [NSNumber numberWithInt: 1 ], AVNumberOfChannelsKey,
                                         [NSNumber numberWithFloat: 44100.0 ], AVSampleRateKey,
                                         [NSNumber numberWithInt: 64000 ], AVEncoderBitRateKey,
                                         [NSData dataWithBytes: &acl length: sizeof( acl ) ], AVChannelLayoutKey,
                                         nil];
// 定义 writer
self.assetWriterInput = [AVAssetWriterInput assetWriterInputWithMediaType:AVMediaTypeVideo outputSettings:videoSettings];
self.assetWriterInput.expectsMediaDataInRealTime = YES;

self.assetAudioInput = [AVAssetWriterInput assetWriterInputWithMediaType:AVMediaTypeAudio outputSettings:audioOutputSettings];
self.assetAudioInput.expectsMediaDataInRealTime = YES;

self.assetWriter = [AVAssetWriter assetWriterWithURL:[NSURL fileURLWithPath:self.videoOutPath] fileType:AVFileTypeMPEG4 error:nil];    
[self.assetWriter addInput:self.assetWriterInput];    
[self.assetWriter addInput:self.assetAudioInput];
[self.assetWriter setMovieTimeScale:60];
//	开始写入视频
[self.assetWriter startWriting];
```

```objectivec
// 暂停和继续录屏
- (void)broadcastPaused {
    // User has requested to pause the broadcast. Samples will stop being delivered.
    
}

- (void)broadcastResumed {
    // User has requested to resume the broadcast. Samples delivery will resume.
    
}
```

```objectivec
// 收到录屏的 buffer
- (void)processSampleBuffer:(CMSampleBufferRef)sampleBuffer withType:(RPSampleBufferType)sampleBufferType {
    
    switch (sampleBufferType) {
        case RPSampleBufferTypeVideo:
            // Handle video sample buffer for app audio
        {
          // 如果是第一帧数据，判断数据类型，如果不是 video，则废弃，否则会出现视频开头是黑屏
            if (self.isFirstSample) {
                
                [self.assetWriter startSessionAtSourceTime:CMSampleBufferGetPresentationTimeStamp(sampleBuffer)];
                
                self.isFirstSample = NO;
            }
            
            if (CMSampleBufferIsValid(sampleBuffer) && self.assetWriterInput.readyForMoreMediaData) {
                [self.assetWriterInput appendSampleBuffer:sampleBuffer];
            }
        }
            break;
        case RPSampleBufferTypeAudioApp:
            // Handle audio sample buffer for app audio
            
            break;
        case RPSampleBufferTypeAudioMic:
            // Handle audio sample buffer for mic audio
        {
            if (CMSampleBufferIsValid(sampleBuffer) && self.assetAudioInput.readyForMoreMediaData) {
                [self.assetAudioInput appendSampleBuffer:sampleBuffer];
            }
        }
            break;
        default:
            break;
    }
}

```

```objectivec
// 完成录屏
- (void)broadcastFinished {
    // User has requested to finish the broadcast.
    // 定义一个标志，标记 assetWriter 是否完成了写入
  	// 也可以使用 dispatchgroup
    __block BOOL finish = NO;    
    [self.assetWriterInput markAsFinished];
    [self.assetAudioInput markAsFinished];
    [self.assetWriter finishWritingWithCompletionHandler:^{
        self.assetWriterInput = nil;
        self.assetAudioInput = nil;
        self.assetWriter = nil;
        
        // [self moveVideo]; // 把视频数据保存到appgroup共享
        // [self sendLocalNotification]; // 发送 一个本地通知
        
        finish = YES;
    }];
    
    while (finish == NO) {
    }
}
```

**AppGroup共享数据**

```objectivec
- (void)moveVideo {
    NSURL *groupURL = [[NSFileManager defaultManager] containerURLForSecurityApplicationGroupIdentifier:@"定义的 appgroup 的 id"];
    NSURL *fileURL = [groupURL URLByAppendingPathComponent:@"record.mp4"];
    [[NSFileManager defaultManager] removeItemAtURL:fileURL error:nil];
    
    [[NSFileManager defaultManager] moveItemAtPath:self.videoOutPath toPath:[fileURL path] error:nil];    
}
```

**通过通知回调到app**

```objectivec
//  identifier 是通知到id，在app的代码中，正常监听这个id 的通知就行
- (void)sendNotificationForMessageWithIdentifier:(nullable NSString *)identifier userInfo:(NSDictionary *)info {
    CFNotificationCenterRef const center = CFNotificationCenterGetDarwinNotifyCenter();
    CFDictionaryRef userInfo = (__bridge CFDictionaryRef)info;
    BOOL const deliverImmediately = YES;
    CFStringRef identifierRef = (__bridge CFStringRef)identifier;
    CFNotificationCenterPostNotification(center, identifierRef, NULL, userInfo, deliverImmediately);
}
```

**接收录制完成的通知**

定义`CFNotification`回调

```objectivec
void NotificationCallback(CFNotificationCenterRef center,
                                void * observer,
                                CFStringRef name,
                                void const * object,
                                CFDictionaryRef userInfo) {
    NSString *identifier = (__bridge NSString *)name;
    NSObject *sender = (__bridge NSObject *)observer;
    
    NSDictionary *notionUserInfo = @{@"identifier":identifier};
    [[NSNotificationCenter defaultCenter] postNotificationName:@"ScreenNotificationName"
                                                        object:sender
                                                      userInfo:notionUserInfo];
}
```

注册通知监听

```objectivec
CFNotificationCenterRef const center = CFNotificationCenterGetDarwinNotifyCenter();
    CFStringRef str = (__bridge CFStringRef)identifier;
    CFNotificationCenterAddObserver(center,
                                    (__bridge const void *)(self),
                                    NotificationCallback,
                                    str,
                                    NULL,
                                    CFNotificationSuspensionBehaviorDeliverImmediately);
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(broadcastInfo:) name:@"ScreenNotificationName" object:nil];
```

收到通知后

```objectivec
- (void)broadcastInfo:(NSNotification *)notion {
    NSDictionary *userInfo = notion.userInfo;
    NSString *identifier = userInfo[@"identifier"];
  // 从 appgroup 中获取保存的视频地址
    NSURL *groupURL = [[NSFileManager defaultManager] containerURLForSecurityApplicationGroupIdentifier:appGroupId];
    NSURL *fileURL = [groupURL URLByAppendingPathComponent:@"record.mp4"];
}
```



## 注意

> 系统提供录制进程的内存空间约为50M，超过50M进程将会被停止
>
> 采集到数据结构中的yuv的缓存空间，不能占用
>
> 使用 AppDelegate 的代理判断是否锁屏