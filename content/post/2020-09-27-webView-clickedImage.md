---
layout: post

title: 网页图片添加点击方法获取图片
 
date: 2020-09-27 17:00:00

categories: ["iOS"]

tags:  ["webView"]

mathjax: true

---

点击网页中到图片，获取到这张图片后进行放大展示到效果。

实现这个效果，首先需要点击图片的时候获取到图片的地址，如果网页中的`js`没有点击方法，就需要在网页加载完成后通过注入`js`代码的方式为每个图片增加`click`方法，并且点击的时候把`src`地址传递出来。

需要在`webView`的两处回调增加代码。

首先是`webViewDidFinishLoad` 中注入`js`

```objectivec
// img标签添加点击事件，预览图片
NSString *imageStrings = @"function assignImageClickAction(){var imgs=document.querySelectorAll('img');var length=imgs.length;for(var i=0;i<length;i++){img=imgs[i];img.onclick=function(){event.stopPropagation();window.location.href='image-preview:'+this.src}}}";
[webView evaluateJavaScript:imageStrings completionHandler:nil];
```

然后是在`shouldStartLoadWithRequest`中获取点击方法

```objectivec
- (BOOL)webView:(WKWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(WKNavigationType)navigationType {
    if ([request.URL.scheme isEqualToString:@"image-preview"]) {
        NSString* path = [request.URL.absoluteString substringFromIndex:[@"image-preview:" length]];
        
        // path 就是获取到的图片的地址，可以通过这个地址拿到图片去用其他的控件进行展示图片
        
        return YES;
    }
    return NO;
}
```