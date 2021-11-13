---
layout: post
title:  自定义 UITableView 侧滑菜单
date: 2017-12-12 11:30:00
categories: ["iOS"]
tags: ["UITableView"] 
mathjax: true
---

之前写过一篇 [UITableView侧滑删除](https://dongjiawang.github.io/2016/08/02/2016-08-02-tableview/) ，这个只是添加一个删除功能，但是现在很多需求就是侧滑出来一个编辑菜单，于是就需要用另一种方式添加侧滑的功能了。
其实系统方法支持添加侧滑菜单，虽然 UI 简单点，基本上能满足需求。

```objectivec
//先要设Cell可编辑
- (BOOL)tableView:(UITableView *)tableView canEditRowAtIndexPath:(NSIndexPath *)indexPath {
        return YES;
}
```

```objectivec
//定义编辑样式
- (UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath {
    return UITableViewCellEditingStyleDelete;
}
```

**下面就是跟单独添加一个删除不一样的地方了**

```objectivec
// 返回一个菜单数组
- (NSArray<UITableViewRowAction *> *)tableView:(UITableView *)tableView editActionsForRowAtIndexPath:(NSIndexPath *)indexPath {
    NSMutableArray  *actionArray = [NSMutableArray array];
    // 添加删除操作
    UITableViewRowAction *deleteRowAction = [UITableViewRowAction rowActionWithStyle:UITableViewRowActionStyleDestructive title:@"删除" handler:^(UITableViewRowAction *action, NSIndexPath *indexPath) {

        // 进行删除操作
    }];
    
    // 添加编辑操作
    UITableViewRowAction *editRowAction = [UITableViewRowAction rowActionWithStyle:UITableViewRowActionStyleNormal title:@"编辑" handler:^(UITableViewRowAction *action, NSIndexPath *indexPath) {
        // 编辑操作
    }];
    // 自定义这个菜单的背景色
    editRowAction.backgroundColor = [UIColor lightGrayColor];
    
    [actionArray addObject:deleteRowAction];
    [actionArray addObject:editRowAction];
    
    return actionArray;
}
```

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/2417618-590ddd8c207d0644.gif)
