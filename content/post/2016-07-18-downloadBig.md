---
layout: post
title: 大文件断点续传
date: 2016-07-18 17:56:00
categories: ["网络"]
tags: ["iOS"]
mathjax: true
---

# 定义

```objectivec
@interface FileDownloadManager ()<NSURLSessionDataDelegate>
/**
 *  下载任务
 */
@property (nonatomic, strong) NSURLSessionDataTask      *downloadTask;
/**
 *  session
 */
@property (nonatomic, strong) NSURLSession              *session;
/**
 *  下载对象流
 */
@property (nonatomic, strong) NSOutputStream            *stream;
/**
 *  文件总长度
 */
@property (nonatomic, assign) float                     totalLength;
/**
 *  下载中的数据模型
 */
@property (nonatomic, strong) FileDownloadModel         *myDownloadModel;
@end
```

# 创建session

```objectivec
- (NSURLSession *)session {
    if (!_session) {
        _session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[[NSOperationQueue alloc] init]];
    }
    return _session;
}
```

# 创建请求

```objectivec
- (void)initDownloadTast {
    [self initDownloadSream:@"下载文件的名字"];
    
    //创建请求
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString@"下载链接"]];
    //设置请求头
    //请求头的内容是已经下载的文件大小
    NSString *range = [NSString stringWithFormat:@"bytes=%zd-", [[[NSFileManager defaultManager] attributesOfItemAtPath:[[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject] stringByAppendingPathComponent:[NSString stringWithFormat:@"%@.zip", @"保存文件的名字"]] error:nil][NSFileSize] integerValue]];
    [request setValue:range forHTTPHeaderField:@"Range"];
    
    //创建一个data任务
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        self.downloadTask = [self.session dataTaskWithRequest:request];
        [self.downloadTask resume];
    });
}
```

# 创建下载流

```objectivec
- (void)initDownloadSream:(NSString *)fileName {
    if (!_stream) {
        _stream = [NSOutputStream outputStreamToFileAtPath:[[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject] stringByAppendingPathComponent:[NSString stringWithFormat:@"%@.zip", fileName]] append:YES];
    }
}
```

# NSURLSessionDataDelegate

```objectivec
/**
 * 接受到响应
**/
- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveResponse:(NSHTTPURLResponse *)response completionHandler:(void (^)(NSURLSessionResponseDisposition))completionHandler {
    //打开流
    [self.stream open];
    // 获取服务器请求， 返回未下载数据的长度，需要加上已经下载数据长度才会得到数据总长度
    self.totalLength = [response.allHeaderFields[@"Content-Length"] integerValue] + [[[NSFileManager defaultManager] attributesOfItemAtPath:[[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject] stringByAppendingPathComponent:[NSString stringWithFormat:@"%@.zip", @"下载文件名字"]] error:nil][NSFileSize] integerValue];
    completionHandler(NSURLSessionResponseAllow);
}
/**
 * 接受到服务器返回的数据（调用多次）
 **/
-(void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveData:(NSData *)data {
    //写入数据
    [self.stream write:data.bytes maxLength:data.length];
     
        dispatch_async(dispatch_get_main_queue(), ^{
              //在主线程上更新UI进度条
        });
}
/**
 * 请求完毕
 **/
-(void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error {
    [self.stream close];
    self.stream = nil;
    self.downloadTask = nil;
    if (!error) {
        //下载成功
        //处理下载下来的文件（解压或者转移等等）
        //更新UI记得在主线程
    }
}
```

> 当网络断开的时候会调用请求完毕这个代理，这时候 error 是有值的，查看其中的userInfo， 这个key @"NSLocalizedDescription",  对应的内容是@"网络连接已中断。" （注意有句号），可以在这里执行断网后的操作