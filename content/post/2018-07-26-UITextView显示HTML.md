---
layout: post

title: UITextView显示HTML

date: 2018-07-26 17:00:00

categories: ["iOS"]

tags: ["UITextView"]

mathjax: true
---

有时候需要显示一串HTML的字符串，用`UILabel`  `UITextView`的`attributedText `属性都可以展示，但是如果HTML中有链接，要实现点击的话建议还是使用`UITextView`

**代码示例**

```objectivec
NSData *data = [htmlString dataUsingEncoding:NSUnicodeStringEncoding];
NSDictionary *options = @{NSDocumentTypeDocumentAttribute: NSHTMLTextDocumentType};
NSAttributedString *html = [[NSAttributedString alloc]initWithData:data
                                                           options:options
                                                documentAttributes:nil
                                                             error:nil];
textView.attributedText = html;
```

还需要实现UITextView的代理

```objectivec
- (BOOL)textView:(UITextView *)textView shouldInteractWithURL:(NSURL *)URL inRange:(NSRange)characterRange {
    
    //在这里可以获取点击的URL如果不做处理就会跳转到Safari打开链接
    return YES;    
}
```

