---
layout: post
title: UICollectionView的简单使用和常用代理方法
date: 2015-12-30 15:34:00
categories: ["iOS"]
tags: ["UICollectionView"]
mathjax: true
---

`UICollectionView` 相对于 `UITableView` 有更加自由的布局，做出的界面可变性更大最近开始接触使用 `UICollectionView` ，整理了一下常用的代理方法。

首先需要先添加UICollectionView的代理：`UICollectionViewDelegate`   `UICollectionViewDataSource`  `UICollectionViewDelegateFlowLayout`

在 `viewdidLoad` 的时候注册 `UICollectionView` 的 `cell`

```objectivec
UICollectionViewFlowLayout *flowLayout=[[UICollectionViewFlowLayout alloc] init];
 [flowLayout setScrollDirection:UICollectionViewScrollDirectionVertical]; 
UICollectionView *collection = [[UICollectionView alloc] initWithFrame:CGRectMake(0, 0, self.width, self.height) collectionViewLayout:flowLayout]; 
[collection registerClass:[UICollectionViewCell class] forCellWithReuseIdentifier:@"CollectionCell"];
```

需要需要添加 `headerView` 或者 `footerView` 同样需要注册

```objectivec
[collection registerClass:[collectionHeaderView class] forSupplementaryViewOfKind:UICollectionElementKindSectionHeader withReuseIdentifier:@"HeaderView"];
//collectionHeaderView是自定义的view
```

想要定义View的UI就需要在它的代理方法中进行设置就行

```objectivec
//返回headerView的大小为宽320  高 100
-(CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout *)collectionViewLayout referenceSizeForHeaderInSection:(NSInteger)section {
    CGSize size = CGSizeMake(320, 100);

    return size;
}
```

定义需要显示的 `headerView` UI，因为 `UICOllectionView` 的 `headerView` 是复用的，所以需要像使用Cell一样在代理方法中自定义

```objectivec
//返回headerView
- (UICollectionReusableView *) collectionView:(UICollectionView *)collectionView viewForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath
{
    UICollectionReusableView *reusableview = nil;
    
        if (kind == UICollectionElementKindSectionHeader)
        {
                VoteTableHeaderView *myHeaderView = [collectionView dequeueReusableSupplementaryViewOfKind:kind withReuseIdentifier:@"HeaderView" forIndexPath:indexPath];
                   //在这里进行headerView的操作
            reusableview = myHeaderView;
    }
    
    return reusableview;
}
```

```objectivec
//返回section 的数量
-(NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView {
     return 1;
}
```

```objectivec
//返回对应section的item 的数量
-(NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section { 
    return [self.myDataArr count];
}
```

```objectivec
//创建和复用cell
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath
{
    //重用cell
    MineVoteTableViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"MIneVoteCell" forIndexPath:indexPath];
    //赋值给cell
    
    return cell;
}
```

```objectivec
//定义每个UICollectionViewCell 的大小
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout sizeForItemAtIndexPath:(NSIndexPath *)indexPath {
       return CGSizeMake(self.view.width/2, 150);
}
```

```objectivec
-(UIEdgeInsets)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout *)collectionViewLayout insetForSectionAtIndex:(NSInteger)section { 
      return UIEdgeInsetsMake(5, 5, 5, 5);
}
```

```objectivec
//每个section中不同的行之间的行间距
- (CGFloat)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout minimumLineSpacingForSectionAtIndex:(NSInteger)section {
   return 10;
}
```

```objectivec
//选择了某个cell
- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath {
     //在这里进行点击cell后的操作
}
```

```objectivec
//每个item之间的间距
- (CGFloat)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout minimumInteritemSpacingForSectionAtIndex:(NSInteger)section {
       return 10;
}
```