---
layout: post

title: Flutter的BottomNavigationBar不显示的问题

date: 2019-08-07 14:00:00

categories: ["flutter"]

tags: ["flutter"]

mathjax: true
---

在学习Flutter开发的时候发现当设置`BottomNavigationBar`的数量超过3个后，底部的导航是白色，不仔细观察的话跟没有显示出来一样。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/WX20190807-135637@2x.png)

官方文档的说明是：

> BottomNavigationBarType.shifting, the default when there are four or more items. All items are rendered in white and the navigation bar’s
> background color is the same as the
> BottomNavigationBarItem.backgroundColor of the selected item. In this
> case it’s assumed that each item will have a different background
> color and that background color will contrast well with white.

有道翻译：

>BottomNavigationBarType。移位，当有四个或多个项时的默认值。所有项目都呈现为白色和导航栏的
>背景颜色与。相同
>BottomNavigationBarItem。所选项目的背景颜色。在这个
>假设每个项目都有不同的背景
>颜色和背景颜色会与白色形成很好的对比。

#### 解决办法：

给 `BottomNavigationBar`添加`type`属性。

```dart
type:BottomNavigationBarType.fixed,  
```

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/WX20190807-135702@2x.png)