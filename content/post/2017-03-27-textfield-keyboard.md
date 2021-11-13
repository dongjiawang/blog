---
layout: post
title: 关闭 textField、textView 的键盘联想和拼写检查
date: 2017-03-27 16:00:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

```objectivec
self.textField.autocorrectionType = UITextAutocorrectionTypeNo; // 关闭键盘联想
self.textField.spellCheckingType = UITextSpellCheckingTypeNo;// 禁用拼写检查

```

**对应的设置选项：**

```objectivec
typedef NS_ENUM(NSInteger, UITextAutocorrectionType) {
    UITextAutocorrectionTypeDefault, //默认
    UITextAutocorrectionTypeNo, //不自动纠错
    UITextAutocorrectionTypeYes, //自动纠错
};
```


```objectivec
typedef NS_ENUM(NSInteger, UITextSpellCheckingType) {
    UITextSpellCheckingTypeDefault, // 默认
    UITextSpellCheckingTypeNo, // 禁用联想
    UITextSpellCheckingTypeYes, // 使用联想
} NS_ENUM_AVAILABLE_IOS(5_0);
```