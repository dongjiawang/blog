---
layout: post
title: 使用AFN提交头像
date: 2016-07-13 17:11:00
categories: ["网络"]
tags: ["iOS"]
mathjax: true
---


```objectivec
  //服务器地址
   NSString *url = [NSString stringWithFormat:@"%@/uploadfile",[GlobalData GetInstance].GB_ServerIP];
  //请求的字典信息（用户的id，文件的本地路径，文件类型），根据需求自定义
   NSDictionary *parametersDict = @{@"id":[Utility StringEncrpty:[GlobalData GetInstance].GB_UserID], @"attach":myFileName, @"type":@"H"};
  //创建请求服务器的请求
   NSMutableURLRequest *request = [[AFHTTPRequestSerializer serializer] multipartFormRequestWithMethod:@"POST"
                                                                                              URLString:url
                                                                                             parameters:parametersDict
                                                                              constructingBodyWithBlock:^(id<AFMultipartFormData>  _Nonnull formData) {
                                                                               //  把需要提交的图片转成二进制数据  
                                                                              [formData appendPartWithFileURL:[NSURL fileURLWithPath:myFileName] name:@"file" fileName:@"filename.jpg" mimeType:@"image/jpeg" error:nil];
                                                                              } error:nil];
    
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
    manager.requestSerializer = [AFHTTPRequestSerializer serializer];
    manager.responseSerializer = [AFHTTPResponseSerializer serializer];
    [manager.requestSerializer willChangeValueForKey:@"timeoutInterval"];
    manager.requestSerializer.timeoutInterval = 20;
    [manager.requestSerializer didChangeValueForKey:@"timeoutInterval"];
    
    
    NSURLSessionUploadTask *uploadTask = [manager
                                          uploadTaskWithStreamedRequest:request
                                          progress:nil
                                          completionHandler:^(NSURLResponse * _Nonnull response, id  _Nullable responseObject, NSError * _Nullable error) {
                                              //服务器返回的结果（跟后台定）
                                              //比如我们，返回的二进制中NSString，如果是0，就是提交失败；是 2 就是文件过大，其他的就是提交成功
                                              NSData *needData = [Utility decryptData:[NSData dataWithData:(NSData *)responseObject]];
                                              NSString *responseString = [[NSString alloc] initWithData:needData  encoding:NSUTF8StringEncoding];
                                              if([responseString isEqualToString:@"0"])
                                              {
                                                 //由于网络等原因本次请求操作失败
                                              }
                                              else if([responseString isEqualToString:@"2"])
                                              {
                                                  //文件过大，请重新上传
                                              }
                                              else {
                                                 //头像上传成功
                                              }
                                              
                                          }];
    
    [uploadTask resume];
```