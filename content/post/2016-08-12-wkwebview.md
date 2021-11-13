---
layout: post
title: WKWebView的使用
date: 2016-08-12 16:35:00
categories: ["iOS"]
tags: ["webView"]
mathjax: true
---


WKWebView是iOS8开始增加的浏览视图

#新特性
>性能高，稳定性好，占用的内存比较小
支持JS交互
支持HTML5 新特性
可以添加进度条
支持内建手势
据说高达60fps的刷新频率

#使用
**1、导入webkit库**
```objectivec
#import <WebKit/WebKit.h>
```
**2、遵守协议**
> WKNavigationDelegate
>该代理提供的方法，可以用来追踪加载过程（页面开始加载、加载完成、加载失败）、决定是否执行跳转。

>WKUIDelegate
这个代理的方法基本是与界面弹出提示框相关的，针对于web界面的三种提示框（警告框、确认框、输入框）分别对应三种代理方法。
还有就是创建新的webview

**3、初始化**
```objectivec
// 默认初始化
- (instancetype)initWithFrame:(CGRect)frame;
// 根据对webview的相关配置，进行初始化
- (instancetype)initWithFrame:(CGRect)frame configuration:(WKWebViewConfiguration *)configuration NS_DESIGNATED_INITIALIZER;
```
WKWebView的**AllowsBackForwardNavigationGestures**属性是否可以左划返回
>加载网页或者HTML的方式跟UIWebView的相同的

**4、WKNavigationDelegate**
追踪加载过程
```objectivec
// 页面开始加载时调用
- (void)webView:(WKWebView *)webView didStartProvisionalNavigation:(WKNavigation *)navigation;
// 当内容开始返回时调用
- (void)webView:(WKWebView *)webView didCommitNavigation:(WKNavigation *)navigation;
// 页面加载完成之后调用
- (void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)navigation;
// 页面加载失败时调用
- (void)webView:(WKWebView *)webView didFailProvisionalNavigation:(WKNavigation *)navigation;
```
页面跳转
```objectivec
// 接收到服务器跳转请求之后再执行
- (void)webView:(WKWebView *)webView didReceiveServerRedirectForProvisionalNavigation:(WKNavigation *)navigation;
// 在收到响应后，决定是否跳转
- (void)webView:(WKWebView *)webView decidePolicyForNavigationResponse:(WKNavigationResponse *)navigationResponse decisionHandler:(void (^)(WKNavigationResponsePolicy))decisionHandler;
// 在发送请求之前，决定是否跳转
- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler;
```
**5、WKUIDelegate**
```objectivec
// 创建一个新的WebView
- (WKWebView *)webView:(WKWebView *)webView createWebViewWithConfiguration:(WKWebViewConfiguration *)configuration forNavigationAction:(WKNavigationAction *)navigationAction windowFeatures:(WKWindowFeatures *)windowFeatures;
```
与JS交互
```objectivec
//显示一个JS的Alert（与JS交互）
- (void)webView:(WKWebView *)webView runJavaScriptAlertPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(void))completionHandler;
//弹出一个输入框（与JS交互的）
- (void)webView:(WKWebView *)webView runJavaScriptTextInputPanelWithPrompt:(NSString *)prompt defaultText:(nullable NSString *)defaultText initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(NSString * __nullable result))completionHandler;
//显示一个确认框（JS的）
- (void)webView:(WKWebView *)webView runJavaScriptConfirmPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(BOOL result))completionHandler;
//WebVeiw关闭（9.0中的新方法）
- (void)webViewDidClose:(WKWebView *)webView NS_AVAILABLE(10_11, 9_0);
```
**6、WKScriptMessageHandler**
>这个协议中包含一个必须实现的方法，这个方法是提高App与web端交互的关键，它可以直接将接收到的JS脚本转为OC或Swift对象。

```objectivec
// 从web界面中接收到一个脚本时调用
- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message;
```

**7、容易遇到的问题**
➤点击其中的链接没反应
>如果一些HTML网页的标签页的跳转方式为

```objectivec
<a href = "xxx" target = "_black">
```
那么点击链接就不会加载
"_black" 的方式是开一个新的页面,就像safari中弹出一个新的页面显示一样 。
解决方法就是重新load，实现下面的代理方式

```objectivec
- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler { 
 if (navigationAction.targetFrame == nil) {
    //重新加载一个新页面
     [webView loadRequest:navigationAction.request]; 
  } 
  decisionHandler(WKNavigationActionPolicyAllow);
}
```
>或者调用Safari打开链接