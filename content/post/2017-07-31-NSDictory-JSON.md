---
layout: post
title: NSDictionary 转成 json
date: 2017-07-31 15:30:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

iOS 自带框架中有将字典转成标准 json 的方法
比如我有一个字典
```objectivec
{
    from = dongjiawang;
    "img_url" = "http://content.uat.qixuetang.cn//img/user/1/1/132--911404.";
    messagetime = "1.50149e+12";
    msg = "测试对话";
    "nick_name" = " test001";
    type = text;
}
```

用自带 json 库转成标准 json 串
```objectivec
NSData *jsonData = [NSJSONSerialization dataWithJSONObject:dict options:0 error:nil];
NSString *jsonString = [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding];
```

