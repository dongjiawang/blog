---
layout: post
title: UICollectionView的常用方法和属性
date: 2016-11-06 21:35:00
categories: ["iOS"]
tags: ["UICollectionView"]
mathjax: true
---


简单的创建和使用 collectionview 请看我的另一片文章，[UICollectionView的简单使用](https://dongjiawang.github.io/2015/12/30/2015-12-30-iOS-CollectionView-basic/)

下面是 UICollectionView 的常用方法和属性，或许可以通过详细阅读开发者文档从而做出来更炫的效果。


```objectivec
//通过一个布局策略初识化CollectionView
- (instancetype)initWithFrame:(CGRect)frame collectionViewLayout:(UICollectionViewLayout *)layout;

//获取和设置collection的layout
@property (nonatomic, strong) UICollectionViewLayout *collectionViewLayout;

//数据源和代理
@property (nonatomic, weak, nullable) id <UICollectionViewDelegate> delegate;
@property (nonatomic, weak, nullable) id <UICollectionViewDataSource> dataSource;

//从一个class或者xib文件进行cell(item)的注册
- (void)registerClass:(nullable Class)cellClass forCellWithReuseIdentifier:(NSString *)identifier;
- (void)registerNib:(nullable UINib *)nib forCellWithReuseIdentifier:(NSString *)identifier;

//下面两个方法与上面相似，这里注册的是头视图或者尾视图的类
//其中第二个参数是设置 头视图或者尾视图 系统为我们定义好了这两个字符串
//UIKIT_EXTERN NSString *const UICollectionElementKindSectionHeader NS_AVAILABLE_IOS(6_0);
//UIKIT_EXTERN NSString *const UICollectionElementKindSectionFooter NS_AVAILABLE_IOS(6_0);
- (void)registerClass:(nullable Class)viewClass forSupplementaryViewOfKind:(NSString *)elementKind withReuseIdentifier:(NSString *)identifier;
- (void)registerNib:(nullable UINib *)nib forSupplementaryViewOfKind:(NSString *)kind withReuseIdentifier:(NSString *)identifier;

//这两个方法是从复用池中取出cell或者头尾视图
- (__kindof UICollectionViewCell *)dequeueReusableCellWithReuseIdentifier:(NSString *)identifier forIndexPath:(NSIndexPath *)indexPath;
- (__kindof UICollectionReusableView *)dequeueReusableSupplementaryViewOfKind:(NSString *)elementKind withReuseIdentifier:(NSString *)identifier forIndexPath:(NSIndexPath *)indexPath;

//设置是否允许选中 默认yes
@property (nonatomic) BOOL allowsSelection;

//设置是否允许多选 默认no
@property (nonatomic) BOOL allowsMultipleSelection;

//获取所有选中的item的位置信息
- (nullable NSArray<NSIndexPath *> *)indexPathsForSelectedItems; 

//设置选中某一item，并使视图滑动到相应位置，scrollPosition是滑动位置的相关参数，如下：
/*
typedef NS_OPTIONS(NSUInteger, UICollectionViewScrollPosition) {
    //无
    UICollectionViewScrollPositionNone                 = 0,
    //垂直布局时使用的 对应上中下
    UICollectionViewScrollPositionTop                  = 1 << 0,
    UICollectionViewScrollPositionCenteredVertically   = 1 << 1,
    UICollectionViewScrollPositionBottom               = 1 << 2,
    //水平布局时使用的  对应左中右
    UICollectionViewScrollPositionLeft                 = 1 << 3,
    UICollectionViewScrollPositionCenteredHorizontally = 1 << 4,
    UICollectionViewScrollPositionRight                = 1 << 5
};
*/
- (void)selectItemAtIndexPath:(nullable NSIndexPath *)indexPath animated:(BOOL)animated scrollPosition:(UICollectionViewScrollPosition)scrollPosition;

//将某一item取消选中
- (void)deselectItemAtIndexPath:(NSIndexPath *)indexPath animated:(BOOL)animated;

//重新加载数据
- (void)reloadData;

//下面这两个方法，可以重新设置collection的布局，后面的方法多了一个布局完成后的回调，iOS7后可以用
//使用这两个方法可以产生非常炫酷的动画效果
- (void)setCollectionViewLayout:(UICollectionViewLayout *)layout animated:(BOOL)animated;
- (void)setCollectionViewLayout:(UICollectionViewLayout *)layout animated:(BOOL)animated completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(7_0);

//下面这些方法更加强大，我们可以对布局更改后的动画进行设置
//这个方法传入一个布局策略layout，系统会开始进行布局渲染，返回一个UICollectionViewTransitionLayout对象
//这个UICollectionViewTransitionLayout对象管理动画的相关属性，我们可以进行设置
- (UICollectionViewTransitionLayout *)startInteractiveTransitionToCollectionViewLayout:(UICollectionViewLayout *)layout completion:(nullable UICollectionViewLayoutInteractiveTransitionCompletion)completion NS_AVAILABLE_IOS(7_0);
//准备好动画设置后，我们需要调用下面的方法进行布局动画的展示，之后会调用上面方法的block回调
- (void)finishInteractiveTransition NS_AVAILABLE_IOS(7_0);
//调用这个方法取消上面的布局动画设置，之后也会进行上面方法的block回调
- (void)cancelInteractiveTransition NS_AVAILABLE_IOS(7_0);

//获取分区数
- (NSInteger)numberOfSections;

//获取某一分区的item数
- (NSInteger)numberOfItemsInSection:(NSInteger)section;

//下面两个方法获取item或者头尾视图的layout属性，这个UICollectionViewLayoutAttributes对象
//存放着布局的相关数据，可以用来做完全自定义布局，后面博客会介绍
- (nullable UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath;
- (nullable UICollectionViewLayoutAttributes *)layoutAttributesForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath;

//获取某一点所在的indexpath位置
- (nullable NSIndexPath *)indexPathForItemAtPoint:(CGPoint)point;

//获取某个cell所在的indexPath
- (nullable NSIndexPath *)indexPathForCell:(UICollectionViewCell *)cell;

//根据indexPath获取cell
- (nullable UICollectionViewCell *)cellForItemAtIndexPath:(NSIndexPath *)indexPath;

//获取所有可见cell的数组
- (NSArray<__kindof UICollectionViewCell *> *)visibleCells;

//获取所有可见cell的位置数组
- (NSArray<NSIndexPath *> *)indexPathsForVisibleItems;

//下面三个方法是iOS9中新添加的方法，用于获取头尾视图
- (UICollectionReusableView *)supplementaryViewForElementKind:(NSString *)elementKind atIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(9_0);
- (NSArray<UICollectionReusableView *> *)visibleSupplementaryViewsOfKind:(NSString *)elementKind NS_AVAILABLE_IOS(9_0);
- (NSArray<NSIndexPath *> *)indexPathsForVisibleSupplementaryElementsOfKind:(NSString *)elementKind NS_AVAILABLE_IOS(9_0);

//使视图滑动到某一位置，可以带动画效果
- (void)scrollToItemAtIndexPath:(NSIndexPath *)indexPath atScrollPosition:(UICollectionViewScrollPosition)scrollPosition animated:(BOOL)animated;

//用于动态添加，删除，移动某些分区获取items
- (void)insertSections:(NSIndexSet *)sections;
- (void)deleteSections:(NSIndexSet *)sections;
- (void)reloadSections:(NSIndexSet *)sections;
- (void)moveSection:(NSInteger)section toSection:(NSInteger)newSection;

- (void)insertItemsAtIndexPaths:(NSArray<NSIndexPath *> *)indexPaths;
- (void)deleteItemsAtIndexPaths:(NSArray<NSIndexPath *> *)indexPaths;
- (void)reloadItemsAtIndexPaths:(NSArray<NSIndexPath *> *)indexPaths;
- (void)moveItemAtIndexPath:(NSIndexPath *)indexPath toIndexPath:(NSIndexPath *)newIndexPath;
```