---
layout: post
title: iOS 获取UILabel上最后一个字符串的位置。获取文字长度和高度，自动换行
date: 2016-01-06 16:50:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---


```objectivec
//行的高度。
-(CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
   NewNotificationCell *cell = (NewNotificationCell *)[self tableView:tableView cellForRowAtIndexPath:indexPath];
    cell.myLabel.text = [self.dataArray objectAtIndex:indexPath.row];
    labelSize = [cell.myLabel.text sizeWithFont:[UIFont fontWithName:@"Arial" size:15] constrainedToSize:CGSizeMake(cell.myLabel.frame.size.width, 1000) lineBreakMode:UILineBreakModeWordWrap];
    cell.myLabel.lineBreakMode = UILineBreakModeWordWrap;
    cell.myLabel.numberOfLines = 0;
    [cell.myLabel setFont:[UIFont fontWithName:@"Arial" size:15]];
    cell.myLabel.frame = CGRectMake(0, 0, labelSize.width, labelSize.height);

    return labelSize.height+30;
}
```

```objectivec
//自动换行。
    cell.myLabel.text = [self.dataArray objectAtIndex:indexPath.row];
    labelSize = [cell.myLabel.text sizeWithFont:[UIFont fontWithName:@"Arial" size:15] constrainedToSize:CGSizeMake(cell.myLabel.frame.size.width, 1000) lineBreakMode:UILineBreakModeWordWrap];
    cell.myLabel.lineBreakMode = UILineBreakModeWordWrap;
    cell.myLabel.numberOfLines = 0;
    [cell.myLabel setFont:[UIFont fontWithName:@"Arial" size:15]];
    cell.myLabel.frame = CGRectMake(0, 0, labelSize.width, labelSize.height);

    //获取文字长度和高度。
    CGSize fontSize =[cell.myLabel.text sizeWithFont:cell.myLabel.font
                                    forWidth:cell.myLabel.frame.size.width
                               lineBreakMode:UILineBreakModeWordWrap];
    NSLog(@"文字长度=%f",fontSize.width);

    //获取UILabel上最后一个字符串的位置。
    CGPoint lastPoint;
    CGSize sz = [cell.myLabel.text sizeWithFont:cell.myLabel.font constrainedToSize:CGSizeMake(MAXFLOAT, 40)];

    CGSize linesSz = [cell.myLabel.text sizeWithFont:cell.myLabel.font constrainedToSize:CGSizeMake(cell.myLabel.frame.size.width, MAXFLOAT) lineBreakMode:UILineBreakModeWordWrap];
    if(sz.width <= linesSz.width) //判断是否折行
    {
        lastPoint = CGPointMake(cell.myLabel.frame.origin.x + sz.width, cell.myLabel.frame.origin.y);
    }
    else
    {
        lastPoint = CGPointMake(cell.myLabel.frame.origin.x + (int)sz.width % (int)linesSz.width,linesSz.height - sz.height);
    }
    NSLog(@"====%f",lastPoint.x);


    [cell.myButton setTitle:@"查看" forState:UIControlStateNormal];
    [cell.myButton setBackgroundImage:[UIImage imageNamed:@"button.png"] forState:UIControlStateNormal];
    if ([cell.myButton.titleLabel.text isEqualToString:@"查看"]) {
        cell.myButton.frame = CGRectMake(lastPoint.x+5, labelSize.height-19, 30, 20);
    }
    [cell.timeButton setTitle:@"今天" forState:UIControlStateNormal];
    cell.timeButton.frame = CGRectMake(260, labelSize.height+8, 50, 20);
    return cell;
}
```