---
layout: post

title: 使用TFHpple解析HTML

date: 2018-10-27 17:40:00

categories: ["iOS"]

tags:

mathjax: true
---

这篇文章是对[《修改HTML富文本中的图片的大小》](https://dongjiawang.github.io/2018/09/01/2018-09-01-修改HTML富文本中的图片的大小/)的补充。

可能是我们的后台传过来的HTML字符串有不规范的地方，当有多张图片的时候，上面文章的正则只能解析出来第一张，所以就用到了本文中要说的[TFHpple](https://github.com/topfunky/hpple)来解析HTML。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224740.png)

根据`Github`的`README`，需要先把HTML字符串转为data类型，然后再转为`TFHpple`类型。

查询需要的节点，存到数组中，例如我们的需求是查询图片

```objectivec
NSArray *imgArray = [hpple searchWithXPathQuery:@"//img"];
```

这样就是返回了`img`标签的数组。

如果还需要获取`img`下的`src`链接,就需要遍历每个标签

```objectivec
for (TFHppleElement *tempElement in imgArray) {
        NSString *imgUrl = [tempElement objectForKey:@"src"];       
        NSLog(@"scr = %@", imgUrl);
    }
```

> 有个问题需要注意，解析需要的标签需要跟后台沟通好，一定要解析对应的标签节点