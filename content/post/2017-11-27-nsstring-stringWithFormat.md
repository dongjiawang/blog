
---
layout: post
title:  NSString使用stringWithFormat拼接
date: 2017-11-27 12:30:00
categories: ["iOS"]
tags: ["NSString"] 
mathjax: true
---


**字符串中有特殊符号 ”**

```objectivec
NSInteger count = 50;
//"是一个特殊符号, 如果在NSString中用到"需要用\进行转义
NSString *string = [NSString stringWithFormat:@"%zd\"",count];
//输出结果是: 50"
NSLog(@"%@", string);
```

**字符串中有特殊符号 %**

```objectivec
NSInteger count = 50;
//%是一个特殊符号 如果在NSString中用到%需要如下写法
NSString *string = [NSString stringWithFormat:@"%zd%%",count];
//输出结果是: 50%
NSLog(@"%@", string);
```

**用 0 补全的方法**

```objectivec
NSInteger count = 5;
//02代表:如果count不足2位 用0在最前面补全(2代表总输出的个数)
NSString *string = [NSString stringWithFormat:@"%02zd",count];
//输出结果是: 05
NSLog(@"%@", string);
```

**保留 2 位小数点**

```objectivec
//2代表保留的数量
NSString *string = [NSString stringWithFormat:@"%.2f",M_PI];
//输出结果是: 3.14
NSLog(@"%@", string);
```