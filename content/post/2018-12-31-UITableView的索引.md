---
layout: post

title: UITableView的索引

date: 2018-12-31 22:00:00

categories: ["iOS"]

tags: ["UITableView"]

mathjax: true
---



苹果提供了`UILocalizedIndexedCollation`来配合`UITableView`进行索引管理。

`UILocalizedIndexedCollation`提供了返回汉字拼音的首字母、返回包含`A～Z`字母的数组和按照字母进行排序的功能。

[UILocalizedIndexedCollation](https://developer.apple.com/documentation/uikit/uilocalizedindexedcollation?language=objc)的官方文档说明。

#### 下面是我们项目中的代码的demo

1. 定义

```objectivec
/**
索引单例
*/
@property (nonatomic, strong) UILocalizedIndexedCollation *localizedCollation;

/**
每个section中的i数组
*/
@property (nonatomic, strong) NSMutableArray *sectionArray;
```

2. 设置显示效果

```objectivec
// 文字颜色
self.tableView.sectionIndexColor = [UIColor darkTextColor];
//背景颜色
self.tableView.sectionIndexBackgroundColor = [UIColor clearColor];

```

3. 创建并排序索引数据

```objectivec
// UserGroup 是我们项目中的对象，包含着一个name的属性
for (UserGroup *group in groups) {
        //根据name的字符串的拼音首字母,找到这个首字母对应的下标index
        NSInteger section = [self.localizedCollation sectionForObject:group collationStringSelector:@selector(name)];
        //根据index取出二维数组中的一维数组数组元素
        NSMutableArray *subArr = tempSectionArray[section];
        //将这个对象加入到一维数组数组中
        [subArr addObject:group];
    }
    
    //遍历二维数组，取出每一个一维数组，在对数组中的对象按照字母进行下排序。
    for (NSMutableArray *arr in tempSectionArray) {
        NSArray *sortArr = [self.localizedCollation sortedArrayFromArray:arr collationStringSelector:@selector(name)];
        [arr removeAllObjects];
        [arr addObjectsFromArray:sortArr];
    }
```

4. 筛选空白数据

```objectivec
// 遍历section中的数据，把空白的section，在索引中移除，这样显示效果比较好
NSMutableArray *tempTitleArray = [NSMutableArray array];
[tempSectionArray enumerateObjectsUsingBlock:^(NSArray *array, NSUInteger idx, BOOL * _Nonnull stop) {
    if (array.count == 0) {
        [tempTitleArray addObject:array];
    }
    else {
        [self.tableDataArray addObject:[self.localizedCollation sectionTitles][idx]];
    }
}];
    
[tempSectionArray removeObjectsInArray:tempTitleArray];

self.sectionArray = tempSectionArray;
```

可以在索引的开头添加搜索或者其他自定义的文字

```objectivec
[self.tableDataArray insertObject:@"推荐" atIndex:0];
// 或者添加系统提供的一个搜索标志
[self.tableDataArray insertObject:UITableViewIndexSearch atIndex:0];
```

5. UITableView的代理

```objectivec
- (CGFloat)tableView:(UITableView *)tableView heightForHeaderInSection:(NSInteger)section {
    return [self.sectionArray[section] count] == 0 ? 0 : 30;
}

- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    return 44;
}

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return self.tableDataArray.count;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {    
    return [self.sectionArray[section] count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    static NSString *cellid = @"tableViewCell";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:cellid];
    
    if (cell == nil) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:cellid];
        cell.selectionStyle = UITableViewCellSelectionStyleNone;
    }
    
    UserGroup *group = [self.sectionArray[indexPath.section] objectAtIndex:indexPath.row];
    cell.textLabel.text = group.name;
    
    return cell;
}
```

6. UITableView的索引代理

```objectivec
// 返回索引的数据
- (NSArray<NSString *> *)sectionIndexTitlesForTableView:(UITableView *)tableView {
    return self.tableDataArray;
}

// 返回每个section的title
- (NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section {
    return self.tableDataArray[section];
}

//点击索引后跳转到对应的section
- (NSInteger)tableView:(UITableView *)tableView sectionForSectionIndexTitle:(NSString *)title atIndex:(NSInteger)index {
    return index;
}

```



如图就是做出的效果

![](https://raw.githubusercontent.com/dongjiawang/BlogImage/master/img/20190527222605.png)

在右侧显示一条首字母的索引。