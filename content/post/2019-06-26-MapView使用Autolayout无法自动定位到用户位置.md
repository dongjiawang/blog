---
layout: post

title: MapView使用Autolayout无法自动定位到用户位置

date: 2019-06-26 12:00:00

categories: ["iOS"]

tags: ["iOS"]

mathjax: true
---

一般在设置了`MKMapView`的`frame`的时候，会自动跳转到当前用户对位置进行显示。

有时候需要用的`Masonry`进行布局，这时候发现虽然已经获取到了用户的位置，但是`mapView`并没有跳到用户的位置进行显示。

解决这个问题，可以配合`CLLocationManager`的回调获取到用户的位置，然后把位置传给`mapView`，更新显示内容。

如下代码示例：

```objectivec
#pragma mark ----定位代理----
- (void)locationManager:(CLLocationManager *)manager didUpdateLocations:(NSArray<CLLocation *> *)locations {
  // 获取到的location
    CLLocation *location = locations.firstObject;    
    CLLocationCoordinate2D coordinate = [location coordinate];
    // 停止地理位置更新
    [manager stopUpdatingLocation];
  // 数值越小显示的越精确
    MKCoordinateSpan span = MKCoordinateSpanMake(0.05, 0.05);
    MKCoordinateRegion region = MKCoordinateRegionMake(coordinate, span);
  // 更新mapView 的显示
    [self.mapView setRegion:region animated:YES];
}
```



