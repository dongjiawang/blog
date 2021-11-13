---
layout: post
title: 判断 iOS 设备
date: 2017-11-22 17:10:00
categories: ["iOS"]
tags: ["iOS"] 
mathjax: true
---

需要引用头文件
```objectivec
#import <sys/utsname.h>
```


```objectivec
- (NSString*)deviceName {
    struct utsname systemInfo;

    uname(&systemInfo);

    NSString* code = [NSString stringWithCString:systemInfo.machine
                                        encoding:NSUTF8StringEncoding];

    static NSDictionary* deviceNamesByCode = nil;

    if (!deviceNamesByCode) {

        deviceNamesByCode = @{@"i386"      : @"Simulator",
                              @"x86_64"    : @"Simulator",
                              @"iPod1,1"   : @"iPod Touch",        
                              @"iPod2,1"   : @"iPod Touch",       
                              @"iPod3,1"   : @"iPod Touch",        
                              @"iPod4,1"   : @"iPod Touch",        
                              @"iPod7,1"   : @"iPod Touch",        
                              @"iPhone1,1" : @"iPhone",            
                              @"iPhone1,2" : @"iPhone",            
                              @"iPhone2,1" : @"iPhone",            
                              @"iPad1,1"   : @"iPad",              
                              @"iPad2,1"   : @"iPad 2",            
                              @"iPad3,1"   : @"iPad",              
                              @"iPhone3,1" : @"iPhone 4",          
                              @"iPhone3,3" : @"iPhone 4",          
                              @"iPhone4,1" : @"iPhone 4S",         
                              @"iPhone5,1" : @"iPhone 5",          
                              @"iPhone5,2" : @"iPhone 5",          
                              @"iPad3,4"   : @"iPad",              
                              @"iPad2,5"   : @"iPad Mini",         
                              @"iPhone5,3" : @"iPhone 5c",         
                              @"iPhone5,4" : @"iPhone 5c",         
                              @"iPhone6,1" : @"iPhone 5s",         
                              @"iPhone6,2" : @"iPhone 5s",         
                              @"iPhone7,1" : @"iPhone 6 Plus",     
                              @"iPhone7,2" : @"iPhone 6",          
                              @"iPhone8,1" : @"iPhone 6S",         
                              @"iPhone8,2" : @"iPhone 6S Plus",    
                              @"iPhone8,4" : @"iPhone SE",         
                              @"iPhone9,1" : @"iPhone 7",          
                              @"iPhone9,3" : @"iPhone 7",          
                              @"iPhone9,2" : @"iPhone 7 Plus",     
                              @"iPhone9,4" : @"iPhone 7 Plus",     
                              @"iPhone10,1": @"iPhone 8",          
                              @"iPhone10,4": @"iPhone 8",          
                              @"iPhone10,2": @"iPhone 8 Plus",     
                              @"iPhone10,5": @"iPhone 8 Plus",     
                              @"iPhone10,3": @"iPhone X",          
                              @"iPhone10,6": @"iPhone X",          

                              @"iPad4,1"   : @"iPad Air",          
                              @"iPad4,2"   : @"iPad Air",          
                              @"iPad4,4"   : @"iPad Mini",         
                              @"iPad4,5"   : @"iPad Mini",         
                              @"iPad4,7"   : @"iPad Mini",         
                              @"iPad6,7"   : @"iPad Pro (12.9\")", 
                              @"iPad6,8"   : @"iPad Pro (12.9\")", 
                              @"iPad6,3"   : @"iPad Pro (9.7\")",  
                              @"iPad6,4"   : @"iPad Pro (9.7\")"   
                              };
    }

    NSString* deviceName = [deviceNamesByCode objectForKey:code];

    if (!deviceName) {
        if ([code rangeOfString:@"iPod"].location != NSNotFound) {
            deviceName = @"iPod Touch";
        }
        else if([code rangeOfString:@"iPad"].location != NSNotFound) {
            deviceName = @"iPad";
        }
        else if([code rangeOfString:@"iPhone"].location != NSNotFound){
            deviceName = @"iPhone";
        }
        else {
            deviceName = @"Unknown";
        }
    }

    return deviceName;
}
```

**苹果发布新机后，这个列表还得再添加新的机型信息**