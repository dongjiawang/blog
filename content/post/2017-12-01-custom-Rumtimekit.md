---
layout: post
title:  封装常用的 Runtime 方法
date: 2017-12-01 22:30:00
categories: ["iOS"]
tags: ["Runtime"] 
mathjax: true
---

可以下载我写好的代码
[RuntimeKit](https://github.com/dongjiawang/RuntimeKit)

```objectivec
/**
 获取类名

 @param class 类
 @return 类名
 */
+ (NSString *)fetchClassName:(Class)class {
    const char *className = class_getName(class);
    return [NSString stringWithUTF8String:className];
}
```

---

```objectivec
/**
 获取成员变量

 @param class class
 @return NSArray
 */
+ (NSArray *)fetchIvarList:(Class)class {
    unsigned int count = 0;

    Ivar *ivarList = class_copyIvarList(class, &count);
    NSMutableArray *mutableArray = [NSMutableArray arrayWithCapacity:count];
    for (unsigned int i = 0; i < count; i++) {
        NSMutableDictionary *dict = [NSMutableDictionary dictionaryWithCapacity:2];
        const char *ivarName = ivar_getName(ivarList[i]);
        const char *ivarType = ivar_getTypeEncoding(ivarList[i]);
        dict[@"type"] = [NSString stringWithUTF8String:ivarType];
        dict[@"ivarName"] = [NSString stringWithUTF8String:ivarName];
        
        [mutableArray addObject:dict];
    }
    free(ivarList);
    return [NSArray arrayWithArray:mutableArray];
}
```

---

```objectivec
/**
 获取成员属性

 @param class  class
 @return NSArray
 */
+ (NSArray *)fetchPropertyList:(Class)class {
    unsigned int count = 0;
    objc_property_t *propertyList = class_copyPropertyList(class, &count);
    NSMutableArray *mutableArray = [NSMutableArray arrayWithCapacity:count];
    for (unsigned int i = 0; i < count; i++) {
        const char *propertyName = property_getName(propertyList[i]);
        [mutableArray addObject:[NSString stringWithUTF8String:propertyName]];
    }
    free(propertyList);
    return [NSArray arrayWithArray:mutableArray];
}
```

---

```objectivec
/**
 获取实例方法

 @param class  class
 @return NSArray
 */
+ (NSArray *)fetchMetodList:(Class)class {
    unsigned int count = 0;
    Method *methodList = class_copyMethodList(class, &count);
    
    NSMutableArray *mutableArray = [NSMutableArray arrayWithCapacity:count];
    
    for (unsigned int i = 0; i < count; i++) {
        Method method = methodList[i];
        SEL methodName = method_getName(method);
        [mutableArray addObject:NSStringFromSelector(methodName)];
    }
    free(methodList);
    return [NSArray arrayWithArray:mutableArray];
}
```

---

```objectivec
/**
 获取协议列表

 @param class class
 @return NSArray
 */
+ (NSArray *)fetchProtocolList:(Class)class {
    unsigned int count = 0;
    __unsafe_unretained Protocol **protocolList = class_copyProtocolList(class, &count);
    
    NSMutableArray *mutableArray = [NSMutableArray arrayWithCapacity:count];
    for (unsigned int i = 0; i < count; i++) {
        Protocol *protocol = protocolList[i];
        const char *protocolName = protocol_getName(protocol);
        [mutableArray addObject:[NSString stringWithUTF8String:protocolName]];
    }
    free(protocolList);
    return [NSArray arrayWithArray:mutableArray];
}
```

---

```objectivec
/**
 动态添加方法

 @param class  class
 @param methodSel 方法名
 @param methodSelImp 对应方法的实现方法名称
 */
+ (void)addMethod:(Class)class method:(SEL)methodSel method:(SEL)methodSelImp {
    Method method = class_getInstanceMethod(class, methodSelImp);
    IMP methodIMP = method_getImplementation(method);
    const char *types = method_getTypeEncoding(method);
    class_addMethod(class, methodSel, methodIMP, types);
    
}
```

----

```objectivec
/**
 交换方法

 @param class class
 @param method1 方法1
 @param method2 方法2
 */
+ (void)methodSwap:(Class)class firstMethod:(SEL)method1 secondMethod:(SEL)method2 {
    Method first = class_getInstanceMethod(class, method1);
    Method second = class_getInstanceMethod(class, method2);
    method_exchangeImplementations(first, second);
}
```