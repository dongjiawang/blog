---
layout: post
title: iOS webView 打开 TXT 文件乱码的问题
date: 2017-09-12 22:34:00
categories: ["iOS"]
tags: ["webView"]
mathjax: true
---

最近做资料文件下载下来并查看的时候，用 `WKWebView` 打开`office` 类型的文件的时候是没问题的，但是打开测试人员上传的一个 `TXT` 文件就出现了乱码问题，经过查看，应该是文件的编码问题，于是找了两种方式来解决出现的问题。

## 1、转成二进制文件
```objectivec
    NSString *lastName =[[_webUrlStr lastPathComponent] lowercaseString];
    //先判断是 TXT 文件
    if ([lastName containsString:@".txt"]) {
        
        NSData *data = [NSData dataWithContentsOfURL:[NSURL URLWithString:_webUrlStr]];
        // 加载二进制文件
        [self.webView loadData:data MIMEType:@"text/html" characterEncodingName:@"GBK" baseURL:nil];
    }
```

**官方文档对这个方法的解释:**

>*一个WKNavigation对象包含信息跟踪加载一个网页的进展。*
*A WKNavigation object contains information for tracking the loading progress of a webpage.*

>*导航web视图加载方法返回的对象,也是传递到导航委托方法来唯一地标识一个网页加载从开始到结束。它没有自己的方法或属性。*
*A navigation object is returned from the web view load methods and is also passed to the navigation delegate methods to uniquely identify a webpage load from start to finish. It has no method or properties of its own.*

## 2、对文件进行转码
```objectivec
   NSString *lastName =[[_webUrlStr lastPathComponent] lowercaseString];

    if ([lastName containsString:@".txt"]) {
        ///编码可以解决 .txt 中文显示乱码问题
        NSStringEncoding *useEncodeing = nil;
        //带编码头的如utf-8等，这里会识别出来
        NSString *body = [NSString stringWithContentsOfFile:_webUrlStr usedEncoding:useEncodeing error:nil];
        //识别不到，按GBK编码再解码一次.这里不能先按GB18030解码，否则会出现整个文档无换行bug。
        if (!body) {
            body = [NSString stringWithContentsOfFile:_webUrlStr encoding:0x80000632 error:nil];
        }
        //还是识别不到，按GB18030编码再解码一次.
        if (!body) {
            body = [NSString stringWithContentsOfFile:_webUrlStr encoding:0x80000631 error:nil];
        }
        
        //展现
        if (body) {
            NSString* responseStr = [NSString stringWithFormat:
                                     @"<HTML>"
                                     "<head>"
                                     "<title>Text View</title>"
                                     "</head>"
                                     "<BODY>"
                                     "<pre>"
                                     "%@"
                                     "</pre>"
                                     "</BODY>"
                                     "</HTML>",
                                     body];
            [self.webView loadHTMLString:responseStr baseURL:nil];
        }else {
            NSString *urlString = [[NSBundle mainBundle] pathForAuxiliaryExecutable:_webUrlStr];
            urlString = [urlString stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
            NSURL *requestUrl = [NSURL URLWithString:urlString];
            NSURLRequest *request = [NSURLRequest requestWithURL:requestUrl];
            [self.webView loadRequest:request];
        }
    }
```

**这个方法是参考网上提供的也是挺好用的**

_用 webview 加载的时候专门转了一下 HTML 文字，我编码出 body 的时候的确是带着文件中的换行符，但是展示出来就没有换行，所以我又自己加了一层转 HTML_