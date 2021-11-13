---
layout: post

title: 优化tableview加载网络图片

date: 2018-09-24 21:40:00

categories: ["iOS"]

tags: ["UITableView"]

mathjax: true
---

最近在优化公司的`TableView`的代码，有个界面需要加载一个在线的图片列表，在快速滑动的时候，会异步下载多个图片，容易造成内存警告，于是就准备在滑动的时候停止加载网络图片，`停止滑动`的时候再请求网络图片。

**在控制器中实现下面的方法:**

```objectivec
// scrollView的代理方法，表示减速结束，这时候让cell加载图片

-(void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView{

    [self loadShowCells];

}

// 自定义cell的setmodel方法,这时候是load图片

-(void)loadShowCells {

   NSArray * array = [self.tableView indexPathsForVisibleRows];
   for (NSIndexPath *indexPath in array) {        

   UITableViewCell * cell = [self.tableView cellForRowAtIndexPath:indexPath];  
   [cell setPhotoModel:self.tableDataArray[indexPath.row] isLoadImg:YES];
   }

}

```

```objectivec

// UITableview的代理，这时候只赋值给cell，显示需要的内容，但是不加载网络图片

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"cellID"];

   if (cell == nil) {

       cell = [[UITableViewCell alloc]initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier:@"cellID"];

}
    
   [cell setPhotoModel:self.tableDataArray[indexPath.row] isLoadImg:NO];

   
   return cell;

}

```

**在cell中判断是否加载网络图片**

```objectivec

- (void)setPhotoModel:(PictureModel *)model isLoadImg:(BOOL)isload {
   if (isload) {
       [self.imgView sd_setImageWithURL:model.imgUrl placeholderImage:[UIImage imageNamed:@"default"]];

   }
   else {
        self.imgView.image = [UIImage imageNamed:@"default"];
    }

}

```

> 这样在tableview滑动的时候cell是不会加载网络图片的，只有在滑动停止，滚动开始减速的时候，会让当前显示的cell加载网络图片