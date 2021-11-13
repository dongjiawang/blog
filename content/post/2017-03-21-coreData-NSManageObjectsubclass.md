---
layout: post
title: Xcode8 使用 CoreData 创建 NSManageObject subclass
date: 2017-03-21 15:00:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

升级 Xcode8 以上后，在使用 coreData 的时候发现新建的文件里找不到 *NSManageObject subclass* 了，如图，
![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926121238.png)

解决这个问题很简单。

首先，选中你的 *xcdatamodeld* 文件
![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926121248.png)

点击上方 *Editor*，选择 *Create NSManagedObject Subclass*选项
![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926121320.png)

然后就可以按照以前的方式进行 *NSManageObject subclass* 创建了，但是创建出来的实体默认语言是 *swift*，需要在 *xcdatamodeld* 文件中设置语言，如图
![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926121328.png)

