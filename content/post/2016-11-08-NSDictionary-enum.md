---
layout: post
title: 遍历字典 NSDictionary
date: 2016-11-08 11:00:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

遍历字典内容的方式一般有两种 `enumerateKeysAndObjectsUsingBlock` 、`enumerateObjectsWithOptions`

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926120752.png)

`enumerateObjectsWithOptions` 可以设置遍历的类型：

> NSEnumerationConcurrent = (1UL << 0), // 并发模式
> NSEnumerationReverse = (1UL << 1), //倒序模式

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926120741.png)

```objectivec
   NSDictionary *demoDic = @{@"a":@"0",
                              @"b":@"1",
                              @"c":@"2",
                              @"d":@"3",
                              @"e":@"4",
                              @"f":@"5",
                              @"g":@"6"
                              };
```

```objectivec                            
    [demoDic enumerateKeysAndObjectsUsingBlock:^(id  _Nonnull key, id  _Nonnull obj, BOOL * _Nonnull stop) {
        NSLog(@"key: %@, value: %@", key, obj);
        // do something
    }];
```

**对于耗时且顺序无关的遍历，使用并发版本**

>遍历执行 block 会分配在多核cpu上执行。充分发挥多核心 CPU 的效率。

```objectivec  
    [demoDic enumerateKeysAndObjectsWithOptions:NSEnumerationConcurrent usingBlock:^(id  _Nonnull key, id  _Nonnull obj, BOOL * _Nonnull stop) {
            NSLog(@"key: %@, value: %@", key, obj);
             // do something
        }];
```

