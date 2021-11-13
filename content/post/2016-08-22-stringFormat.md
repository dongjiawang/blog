---
layout: post
title: NSString使用stringWithFormat的拼接
date: 2016-08-22 10:27:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

**保留2位小数点**
```objectivec
//.2代表小数点后面保留2位(2代表保留的数量)
NSString *string = [NSString stringWithFormat:@"%.2f",M_PI];
//输出结果是: 3.14
```

**用0补全的方法**
```objectivec
NSInteger count = 5;
//02代表:如果count不足2位 用0在最前面补全(2代表总输出的个数)
NSString *string = [NSString stringWithFormat:@"%02zd",count];
//输出结果是: 05
```

**字符串中有特殊符号%**
```objectivec
NSInteger count = 50;
//%是一个特殊符号 如果在NSString中用到%需要如下写法
NSString *string = [NSString stringWithFormat:@"%zd%%",count];
```
**字符串中有特殊符号 ”**
```objectivec
NSInteger count = 50;
//"是一个特殊符号, 如果在NSString中用到"需要用\进行转义
NSString *string = [NSString stringWithFormat:@"%zd\"",count];
```

[更多技巧参考公众号](http://mp.weixin.qq.com/s?__biz=MzA3NzM0NzkxMQ==&mid=2655357941&idx=2&sn=369151803180639190ffee35003ef20d&scene=1&srcid=08221ricKJeB7jqg5G98vtG7#rd)