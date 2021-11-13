---
layout: post
title: 计算时间间隔(年月日)
date: 2015-12-28 13:55:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

```objectivec
//创建时间格式
    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
    [dateFormatter setDateFormat:@"yyyy-MM-dd HH:mm"];
    NSDate *startDate = [dateFormatter dateFromString:@"2015-12-20 14:00"];
    NSDate *endDate = [dateFormatter dateFromString:@"2015-12-21 14:00"];
    NSTimeInterval time = [endDate timeIntervalSinceDate:startDate];
```

```objectivec
//显示时间间隔
int minute,second,hour,day;
    second=time%60;
    minute = (time/60)%60;
    hour = (time/3600)%24;
    day = (time/3600/24);
    
    NSString *timeStr;
    if (day == 0) {
         timeStr = [NSString stringWithFormat:@"%d:%d:%d", hour, minute, second];
    } else {
         timeStr = [NSString stringWithFormat:@"%d 天 %d:%d:%d", day, hour, minute, second];
    }
```