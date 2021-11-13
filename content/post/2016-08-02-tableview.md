---
layout: post
title: UITableView侧滑删除
date: 2016-08-02 14:34:00
categories: ["iOS"]
tags: ["UITableView"]
mathjax: true
---


UITableView的代理方法中已经集成了侧滑删除的功能，只要实现以下的方法就能增加侧滑删除
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

```objectivec
//修改编辑按钮文字
- (NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath {
    return @"删除";
}
```

```objectivec
//设置进入编辑状态时，Cell不会缩进
- (BOOL)tableView: (UITableView *)tableView shouldIndentWhileEditingRowAtIndexPath:(NSIndexPath *)indexPath {
    return NO;
}
```

```objectivec
//点击删除
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath {
  //在这里实现删除操作

  //删除数据，和删除动画
   [self.myDataArr removeObjectAtIndex:deleteRow];
   [tableView deleteRowsAtIndexPaths:[NSArray arrayWithObject:[NSIndexPath indexPathForRow:deleteRow inSection:0]] withRowAnimation:UITableViewRowAnimationTop];
}
```

>注意：一定是先删除了数据，再执行删除的动画或者其他操作，否则会出现崩溃