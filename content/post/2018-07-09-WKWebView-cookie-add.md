---
layout: post

title: WKWebView添加Cookie总结

date: 2018-07-09 17:40:00

categories: ["iOS"]

tags: ["webView"]

mathjax: true
---

WKWebView是苹果在iOS8 开始推出的webView组件，用来替代UIWebView内存占用高的问题，虽然WKWebView的性能提升了很多，但是也有很多的坑是不可忽略的，比如对cookie的使用，就是存在的一个很大的坑。

### 1、添加到NSURLRequest中

```objectivec
//获取Cookie
- (NSString *)getCookie {
    NSMutableString *cookieStr = [[NSMutableString alloc] init];
    NSArray *array =  [[NSHTTPCookieStorage sharedHTTPCookieStorage] cookiesForURL:[NSURL URLWithString:@"https://www.baidu.com"]];
    if ([array count] > 0) {
        for (NSHTTPCookie *cookie in array) {
            [cookieStr appendFormat:@"%@=%@;",cookie.name,cookie.value];
        }
        //删除最后一个分号 “；”
        [cookieStr deleteCharactersInRange:NSMakeRange(cookieString.length - 1, 1)];
    }
    return cookieStr;
}
`



​```objective-c
// 添加request的请求头
NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:requestUrl]];
NSString *cookie = [self getCookie];
[request addValue:cookie forHTTPHeaderField:@"Cookie"];
```

> 我平时用的时候这个方式经常添加失效，建议使用第二种方式添加，如果是iOS11以上的系统，尽量用第三种方式

### 2、使用JS注入的方式添加cookie



创建JS字符串

```objectivec
- (NSString *)cookieJSString {
    NSMutableString *script = [NSMutableString string];
    [script appendString:@"var cookieNames = document.cookie.split('; ').map(function(cookie) { return cookie.split('=')[0] } );\n"];
    for (NSHTTPCookie *cookie in [[NSHTTPCookieStorage sharedHTTPCookieStorage] cookies]) {

        if ([cookie.value rangeOfString:@"'"].location != NSNotFound) {
            continue;
        }

        [script appendFormat:@"if (cookieNames.indexOf('%@') == -1) { document.cookie='%@'; };\n", cookie.name, cookie.da_javascriptString];
    }
    return script;
}
```



添加JS字符串到`WKWebView`的`configuration`中,

创建[WKUserScript](https://developer.apple.com/documentation/webkit/wkuserscript?language=objc)

```objectivec
WKUserScript * cookieScript = [[WKUserScript alloc] initWithSource:[self cookieString] injectionTime:WKUserScriptInjectionTimeAtDocumentStart forMainFrameOnly:NO];

[self.wkwebView.configuration.userContentController addUserScript:cookieScript];
```

### 3,使用WKHTTPCookieStore 管理cookie

在iOS11中新增了 [WKHTTPCookieStore](https://developer.apple.com/documentation/webkit/wkhttpcookiestore?language=objc)管理cookie，通过这个API可以添加、删除、查询WKWebView的Cookie，甚至监听Cookie的变化。



先使用`NSHTTPCookieStorage`获取已经存在的cookie。

```objectivec
NSArray *cookies = [[NSHTTPCookieStorage sharedHTTPCookieStorage] cookies];
```

获取到WKWebView的`httpCookieStore`

```objectivec
WKHTTPCookieStore *cookieStroe = self.wkwebView.configuration.websiteDataStore.httpCookieStore;
```

```objectivec
// 添加cookie
for (NSHTTPCookie *cookie in cookies) {
    [cookieStroe setCookie:cookie completionHandler:^{
        if ([[cookies lastObject] isEqual:cookie]) {
            return;
        }
    }];
}
```

> WKHTTPCookieStore 添加cookie是通过block执行添加之后的操作



**通过WKHTTPCookieStore添加cookie经常在第一次加载的时候没有添加成功， 需要在web开始加载内容的 时候确认一下**

```objectivec
- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler {
    if (@available(iOS 11.0, *)) {
        WKHTTPCookieStore *cookieStore = webView.configuration.websiteDataStore.httpCookieStore;
        [cookieStore getAllCookies:^(NSArray<NSHTTPCookie *> * _Nonnull allCookies) {
            if (allCookies.count == 0 && ![webView.URL.absoluteString isEqualToString:@"about:blank"]) {
                //如果没有添加成功cookie，在这里让web重新进行loadRequest请求
                [self.wkwebView loadRequest:self.request];
            }
        }];
    }

    decisionHandler(WKNavigationActionPolicyAllow);
}
```



> 这三种方式基本上能满足大部分的对于添加cookie的需求，如果APP对cookie的操作比较多的话，建议还是使用UIWebView吧，毕竟WKWebView的坑太多了，cookie的管理知识其中一个😂😂😂

> 最后建议观看WWDC，这里能增长很多iOS开发的知识。这个视频是WWDC中介绍WKWebView的，里面讲解了cookie的管理等等，好好观看可以跳过很多坑

[Customized Loading in WKWebView](https://developer.apple.com/wwdc17/220)