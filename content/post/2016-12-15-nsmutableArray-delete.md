---
layout: post
title: 删除可变数组元素
date: 2016-12-15 12:07:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

平时使用 `NSMutableArray` 中经常用到遍历删除数组元素的问题。
一般第一个想法是使用一下 `forin` 就解决了，但是老司机都会知道使用 `forin` 做删除操作的时候会 **crash**。
报错的原因是：
> 当数组在枚举的时候被修改了，因为数组规定在`forin`遍历的时候不能修改数组元素。
> 但是有一种特殊情况，就是在删除数组最后一个元素的时候可以使用`forin`,因为到最后一个元素的时候`forin`枚举已经结束了，这时候删除元素不会影响到`forin`工作。

```objectivec
NSMutableArray *nameArray = @[@"zhangsan", @"lisi", @"wangwu", @"zhanliu"];
```

**使用倒序`forin` 删除元素**
```objectivec
    //创建逆序遍历
    NSEnumerator *enume = [nameArray reverseObjectEnumerator]; 
    for (NSString *name in enumerator) {  
        if ([name isEqualToString:@"lisi"]) {  
            [array removeObject:name];  
        }  
    } 
```

**使用 for 循环进行遍历删除**
	遍历整个数组，找到对应的元素，然后执行删除操作
```objectivec
	for (int i = 0; i < count; ++i) {
		NSString *name = nameArray[i];
		if ([name isEqualToString:@"lisi"]) {
			nameArray removeObject:name];
		}
	}
```


**还有一种方式是定义一个副本数组，对这个副本数组进行遍历，在原数组中进行删除操作**

```objectivec
	NSMutableArray *copyNameArray = [NSMutableArray arrayWithArray:nameArray];
	for (NSString *name in copyNameArray) {
		if ([name isEqualToString:@"lisi"]) {
			[nameArray removeObject:name];
		}
	}
```
