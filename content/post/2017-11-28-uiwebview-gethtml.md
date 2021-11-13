---
layout: post
title:  UIWebView简单的获取HTML内容和修改页面信息
date: 2017-11-28 22:00:00
categories: ["iOS"]
tags: ["webView"] 
mathjax: true
---

通过 `stringByEvaluatingJavaScriptFromString` 这个方法我们可以在iOS中与UIWebView中的网页元素交互。

使用`stringByEvaluatingJavaScriptFromString`方法，需要等UIWebView中的 **页面加载完成之后** 去调用。


创建一个 UIWebView，设定加载的 URL 为 

```objectivec
NSURL *url =[[NSURL alloc] initWithString:@"http://www.google.com.hk/m?gl=CN&hl=zh_CN&source=ihp"];
```

**webview 的代理方法**

```objectivec
- (void)webViewDidFinishLoad:(UIWebView *)webView {    
    // 获取当前页面的url
    NSString *currentURL = [webView stringByEvaluatingJavaScriptFromString:@"document.location.href"];  
   
   // 获取页面title
    NSString *title = [webview stringByEvaluatingJavaScriptFromString:@"document.title"];
   //   修改界面元素的值
    NSString *js_result = [webView stringByEvaluatingJavaScriptFromString:@"document.getElementsByName('q')[0].value='iOS开发';"];   
   // 表单提交
    NSString *js_result2 = [webView stringByEvaluatingJavaScriptFromString:@"document.forms[0].submit(); "];  
    
}
```

这样就实现了在google搜索关键字：“iOS开发”。

上面的功能可以封装到一个 js 的函数中，然后将这个函数插入到页面上执行：

```objectivec
if ([title compare: @"Google"]＝=NSOrderedSame ) {  
    [webView stringByEvaluatingJavaScriptFromString:@"var script = document.createElement_x('script');"    
    // 创建一个script的标签，type为'text/javascript'
     "script.type = 'text/javascript';" 
     //在这个标签中插入一段字符串,这段字符串就是一个函数：mySearch，这个函数实现google自动搜索关键字的功能   
     "script.text = "function mySearch() { "    
     "var field = document.getElementsByName('q')[0];"    
     "field.value='iOS开发';"    
     "document.forms[0].submit();"    
     "}";"    
     "document.getElementsByTagName_r('head')[0].appendChild(script);"];     
      
      // 使用stringByEvaluatingJavaScriptFromString执行mySearch函数
    [webView stringByEvaluatingJavaScriptFromString:@"mySearch();"];    
}  
```

获取 HTML 的内容

```objectivec
获取固定ID内的html 内容

- (void)webViewDidFinishLoad:(UIWebView *)webView {    
        NSString *js = @"document.getElementById('lg').innerHTML";    
        NSString *pageSource = [webView stringByEvaluatingJavaScriptFromString:js];   

        NSLog(@"pagesource:%@", pageSource); 
}
```