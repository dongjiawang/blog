---
layout: post

title: UITextView自动滚动到底部

date: 2018-07-11 16:40:00

categories: ["iOS"]

tags: ["UITextView"]

mathjax: true
---



给`UITextView`设置文字的时候，文字很长的话，默认是显示第一行。如果需要显示最后一行，需要手动设置`textView`滚动到最后。

**首先设置textView的属性**

```objectivec
self.textView.layoutManager.allowsNonContiguousLayout = NO;
```

**赋值文字后执行**

```objectivec
[self.textView scrollRangeToVisible:NSMakeRange(self.textView.text.length, 1)];  
```



> 一定要设置textView的layoutManager的allowsNonContiguousLayout，否则自动到底部的时候可能会闪一下。

