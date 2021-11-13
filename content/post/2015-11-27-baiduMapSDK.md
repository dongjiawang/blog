---
layout: post
title: 使用百度地图进行定位和路线规划
date: 2015-11-27 16:19:00
categories: ["libSDK"]
tags: ["iOS"]
mathjax: true
---


先去百度地图开发者中心申请APPkey：[百度开发者中心](http://developer.baidu.com/map/index.php?title=iossdk)    ，下载百度地图的SDK。

导入SDK的framework文件，`BaiduMapAPI_Base.framework` 是基础包，是必须要导入的，根据自己的需求导入需要的百度SDK的framework，新版的SDK已经可以选择下载哪些framework，减小了包的大小。

引入所需的系统库，在Xcode工程中引入

```objectivec
CoreLocation.framework

QuartzCore.framework

OpenGLES.framework

SystemConfiguration.framework

CoreGraphics.framework

Security.framework

libsqlite3.0.tbd（xcode7以前为 libsqlite3.0.dylib）

CoreTelephony.framework

libstdc++.6.0.9.tbd（xcode7以前为libstdc++.6.0.9.dylib）
```

根据需求引入头文件，我们只要定位，搜索和路线绘制，所以不必要全部引入。


```objectivec
#import <BaiduMapAPI_Base/BMKBaseComponent.h>//引入base相关所有的头文件
 
#import <BaiduMapAPI_Map/BMKMapComponent.h>//引入地图功能所有的头文件
 
#import <BaiduMapAPI_Search/BMKSearchComponent.h>//引入检索功能所有的头文件
 
#import <BaiduMapAPI_Cloud/BMKCloudSearchComponent.h>//引入云检索功能所有的头文件
 
#import <BaiduMapAPI_Location/BMKLocationComponent.h>//引入定位功能所有的头文件
 
#import <BaiduMapAPI_Utils/BMKUtilsComponent.h>//引入计算工具所有的头文件
 
#import <BaiduMapAPI_Radar/BMKRadarComponent.h>//引入周边雷达功能所有的头文件
 
#import < BaiduMapAPI_Map/BMKMapView.h>//只引入所需的单个头文件
```

**下面开始进行代码的实践：**

首先引用代理方法：

```objectivec
BMKMapViewDelegate//基础代理
BMKRouteSearchDelegate//搜索代理
BMKLocationServiceDelegate//定位代理
BMKGeoCodeSearchDelegate//地理反编码代理
```

定义声明文件：

```objectivec
@interface RouteSearchDemoViewController : BaseViewCtrl<BMKMapViewDelegate, BMKRouteSearchDelegate,BMKLocationServiceDelegate,BMKGeoCodeSearchDelegate> {
    IBOutlet BMKMapView* _mapView;//地图view
    IBOutlet UITextField* _startCityText;//开始的城市
    IBOutlet UITextField* _startAddrText;//开始的位置
    IBOutlet UITextField* _endCityText;//终点城市
    IBOutlet UITextField* _endAddrText;//终点位置
    BMKRouteSearch* _routesearch;//搜索
    BMKLocationService *locService;//定位
}

-(IBAction)onClickBusSearch;//公交路线
-(IBAction)onClickDriveSearch;//开车路线
-(IBAction)onClickWalkSearch;//不行路线
- (IBAction)textFiledReturnEditing:(id)sender;

@property(nonatomic,retain)MapAlertView *mapview;//一个遮盖view
@end
```

使用的是类中使用了XIB文件，所以根据自己的需要进行控件的拖放、位置及大小设置。

在实现文件中添加一个图片出的函数，有些路线的绘制和图标的变化需要用到（百度的Demo中就有这个函数）

```objectivec
@implementation UIImage(InternalMethod)

- (UIImage*)imageRotatedByDegrees:(CGFloat)degrees {
    
    CGFloat width = CGImageGetWidth(self.CGImage);
    CGFloat height = CGImageGetHeight(self.CGImage);
    
    CGSize rotatedSize;
    
    rotatedSize.width = width;
    rotatedSize.height = height;
    
    UIGraphicsBeginImageContext(rotatedSize);
    CGContextRef bitmap = UIGraphicsGetCurrentContext();
    CGContextTranslateCTM(bitmap, rotatedSize.width/2, rotatedSize.height/2);
    CGContextRotateCTM(bitmap, degrees * M_PI / 180);
    CGContextRotateCTM(bitmap, M_PI);
    CGContextScaleCTM(bitmap, -1.0, 1.0);
    CGContextDrawImage(bitmap, CGRectMake(-rotatedSize.width/2, -rotatedSize.height/2, rotatedSize.width, rotatedSize.height), self.CGImage);
    UIImage* newImage = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
    return newImage;
}

@end
```

**下面就到了地图的真正实施阶段：**

封装一个获取资源文件的函数，从百度的bundle中获取图片等等：

```objectivec
- (void)viewDidLoad {
    [super viewDidLoad];
    //适配ios7
    if( ([[[UIDevice currentDevice] systemVersion] doubleValue]>=7.0))
    {
        self.navigationController.navigationBar.translucent = NO;
    }
    _routesearch = [[BMKRouteSearch alloc] init];
     _routesearch.delegate = self;
    [_mapView setZoomEnabled:YES];
    [_mapView setZoomLevel:13];//级别，3-19
    _mapView.showMapScaleBar = YES;//比例尺
    _mapView.mapScaleBarPosition = CGPointMake(10,_mapView.frame.size.height-45);//比例尺的位置
    _mapView.showsUserLocation=YES;//显示当前设备的位置
    _mapView.userTrackingMode = BMKUserTrackingModeFollow;//定位跟随模式
    _mapView.delegate = self;
    [_mapView setMapType:BMKMapTypeStandard];//地图的样式(标准地图)
    
    //定位
    locService = [[BMKLocationService alloc] init];
    locService.delegate = self;
    //启动LocationService
    [locService startUserLocationService];
}
```

开始定位，并且地理反编码，获取到位置信息：

```objectivec
/**
 *  更新定位信息
 *
 *  @param userLocation 获取到的位置数据
 */
-(void)didUpdateBMKUserLocation:(BMKUserLocation *)userLocation {
    
    CLLocationCoordinate2D coordinate = userLocation.location.coordinate;
    // 缩放
    BMKCoordinateSpan span = BMKCoordinateSpanMake(0.1, 0.1);
    BMKCoordinateRegion region = BMKCoordinateRegionMake(coordinate, span);
    [_mapView setRegion:region];
    
    BMKReverseGeoCodeOption *option = [[BMKReverseGeoCodeOption alloc] init];
    option.reverseGeoPoint = CLLocationCoordinate2DMake(userLocation.location.coordinate.latitude, userLocation.location.coordinate.longitude);
    BMKGeoCodeSearch *geoCode = [[BMKGeoCodeSearch alloc] init];
    geoCode.delegate = self;
    [geoCode reverseGeoCode:option];
    [option release];
    [geoCode release];
}
/**
 *  反编码地理位置返回代理
 *
 *  @param result   返回结果
 */
-(void)onGetReverseGeoCodeResult:(BMKGeoCodeSearch *)searcher result:(BMKReverseGeoCodeResult *)result errorCode:(BMKSearchErrorCode)error {
    if (result) {
        _startCityText.text = result.addressDetail.city;;
        _startAddrText.text = result.addressDetail.streetName;
        //已经获取到经纬度，可以停止定位服务
        [locService stopUserLocationService];
    }
}
```

经过上面的两个函数，已经可以获取到所在的城市跟位置地址，具体更加详细的信息，可以在addressDetail中看到，一般就是所在的区，街道，街道号码等。

在view上自己添加一个按钮，当点击的时候弹出一个自定义alertview，上面有三个按钮，分别是公交，行车，步行，并且添加一个手势，点击按钮之外的地方，remove或者隐藏alertview。

点击需要进行的导航线路绘制，有对应的搜索类：

```objectivec
-(IBAction)onClickBusSearch {
    if ([_startCityText.text isEqualToString:@""]) {
         [self AddStatusLabelWithText:@"正在获取位置"];
        return;
    }
    [self.mapview removeFromSuperview];
    BMKPlanNode* start = [[[BMKPlanNode alloc]init] autorelease];
    start.name = _startAddrText.text;
    BMKPlanNode* end = [[[BMKPlanNode alloc]init] autorelease];
    end.name = _endAddrText.text;
    
    //公交
    BMKTransitRoutePlanOption *transitRouteSearchOption = [[BMKTransitRoutePlanOption alloc]init];
    transitRouteSearchOption.city = _startCityText.text;
    transitRouteSearchOption.from = start;
    transitRouteSearchOption.to = end;
    BOOL flag = [_routesearch transitSearch:transitRouteSearchOption];
    
    [transitRouteSearchOption release];
    if(flag)
    {
        NSLog(@"bus检索发送成功");
    }
    else
    {
        NSLog(@"bus检索发送失败");
    }
}

-(IBAction)onClickDriveSearch {
    if ([_startCityText.text isEqualToString:@""]) {
        [self AddStatusLabelWithText:@"正在获取位置"];
        return;
    }
    [self.mapview removeFromSuperview];
    BMKPlanNode* start = [[[BMKPlanNode alloc]init] autorelease];
    start.name = _startAddrText.text;
    BMKPlanNode* end = [[[BMKPlanNode alloc]init] autorelease];
    end.name = _endAddrText.text;
    
    /// 驾车查询基础信息类
    BMKDrivingRoutePlanOption *drivingRouteSearchOption = [[BMKDrivingRoutePlanOption alloc]init];
    drivingRouteSearchOption.from = start;
    drivingRouteSearchOption.to = end;
    BOOL flag = [_routesearch drivingSearch:drivingRouteSearchOption];
    [drivingRouteSearchOption release];
    if(flag)
    {
        NSLog(@"car检索发送成功");
    }
    else
    {
        NSLog(@"car检索发送失败");
    }
}

-(IBAction)onClickWalkSearch
{
    if ([_startCityText.text isEqualToString:@""]) {
    [self AddStatusLabelWithText:@"正在获取位置"];
    return;
}
    [self.mapview removeFromSuperview];
    BMKPlanNode* start = [[[BMKPlanNode alloc]init] autorelease];
    start.name = _startAddrText.text;
    BMKPlanNode* end = [[[BMKPlanNode alloc]init] autorelease];
    end.name = _endAddrText.text;

    /// 步行查询基础信息类
    BMKWalkingRoutePlanOption *walkingRouteSearchOption = [[BMKWalkingRoutePlanOption alloc]init];
    walkingRouteSearchOption.from = start;
    walkingRouteSearchOption.to = end;
    BOOL flag = [_routesearch walkingSearch:walkingRouteSearchOption];
    [walkingRouteSearchOption release];
    if(flag)
    {
        NSLog(@"walk检索发送成功");
    }
    else
    {
        NSLog(@"walk检索发送失败");
    }
}
```

每个start的城市和地址都可以设置，其中公交线路的start城市和transitRouteSearchOption的城市是必须要有的，否则会检索失败或者检索不到线路；

发送检索成功后会回调对应的绘制线路代理方法：

```objectivec
- (void)onGetTransitRouteResult:(BMKRouteSearch*)searcher result:(BMKTransitRouteResult*)result errorCode:(BMKSearchErrorCode)error {

    MapExplainViewController *exp=[[[MapExplainViewController alloc]init]autorelease];
    exp.array=result.routes;
    [self.navigationController pushViewController:exp animated:YES];
    NSArray* array = [NSArray arrayWithArray:_mapView.annotations];
    [_mapView removeAnnotations:array];
    array = [NSArray arrayWithArray:_mapView.overlays];
    [_mapView removeOverlays:array];
    if (error == BMK_SEARCH_NO_ERROR) {
        BMKTransitRouteLine* plan = (BMKTransitRouteLine*)[result.routes objectAtIndex:0];
        // 计算路线方案中的路段数目
        int size = [plan.steps count];
        int planPointCounts = 0;
        for (int i = 0; i < size; i++) {
            BMKTransitStep* transitStep = [plan.steps objectAtIndex:i];
            if(i==0){
                RouteAnnotation* item = [[RouteAnnotation alloc]init];
                item.coordinate = plan.starting.location;
                item.title = @"起点";
                item.type = 0;
                [_mapView addAnnotation:item]; // 添加起点标注
                [item release];                
            }
            else if(i==size-1){
                RouteAnnotation* item = [[RouteAnnotation alloc]init];
                item.coordinate = plan.terminal.location;
                item.title = @"终点";
                item.type = 1;
                [_mapView addAnnotation:item]; // 添加起点标注
                [item release];
            }
            RouteAnnotation* item = [[RouteAnnotation alloc]init];
            item.coordinate = transitStep.entrace.location;
            item.title = transitStep.instruction;
            item.type = 3;
            [_mapView addAnnotation:item];
            [item release];
            
            //轨迹点总数累计
            planPointCounts += transitStep.pointsCount;
        }
        
        //轨迹点
        BMKMapPoint * temppoints = new BMKMapPoint[planPointCounts];
        int i = 0;
        for (int j = 0; j < size; j++) {
            BMKTransitStep* transitStep = [plan.steps objectAtIndex:j];
            int k=0;
            for(k=0;k<transitStep.pointsCount;k++) {
                temppoints[i].x = transitStep.points[k].x;
                temppoints[i].y = transitStep.points[k].y;
                i++;
            }            
        }
        // 通过points构建BMKPolyline
        BMKPolyline* polyLine = [BMKPolyline polylineWithPoints:temppoints count:planPointCounts];
        [_mapView addOverlay:polyLine]; // 添加路线overlay
        delete []temppoints;
    }
}

//驾车的路线绘制
- (void)onGetDrivingRouteResult:(BMKRouteSearch*)searcher result:(BMKDrivingRouteResult*)result errorCode:(BMKSearchErrorCode)error {

    MapExplainViewController *exp=[[[MapExplainViewController alloc]init]autorelease];
    exp.array=result.routes;
    [self.navigationController pushViewController:exp animated:YES];
    NSArray* array = [NSArray arrayWithArray:_mapView.annotations];
    [_mapView removeAnnotations:array];
    array = [NSArray arrayWithArray:_mapView.overlays];
    [_mapView removeOverlays:array];
    if (error == BMK_SEARCH_NO_ERROR) {
        BMKDrivingRouteLine* plan = (BMKDrivingRouteLine*)[result.routes objectAtIndex:0];
        // 计算路线方案中的路段数目
        int size = [plan.steps count];
        int planPointCounts = 0;
        for (int i = 0; i < size; i++) {
            BMKDrivingStep* transitStep = [plan.steps objectAtIndex:i];
            if(i==0){
                RouteAnnotation* item = [[RouteAnnotation alloc]init];
                item.coordinate = plan.starting.location;
                item.title = @"起点";
                item.type = 0;
                [_mapView addAnnotation:item]; // 添加起点标注
                [item release];
            }
            else if(i==size-1){
                RouteAnnotation* item = [[RouteAnnotation alloc]init];
                item.coordinate = plan.terminal.location;
                item.title = @"终点";
                item.type = 1;
                [_mapView addAnnotation:item]; // 添加起点标注
                [item release];
            }
            //添加annotation节点
            RouteAnnotation* item = [[RouteAnnotation alloc]init];
            item.coordinate = transitStep.entrace.location;
            item.title = transitStep.entraceInstruction;
            item.degree = transitStep.direction * 30;
            item.type = 4;
            [_mapView addAnnotation:item];
            [item release];
            //轨迹点总数累计
            planPointCounts += transitStep.pointsCount;
        }
        // 添加途经点
        if (plan.wayPoints) {
            for (BMKPlanNode* tempNode in plan.wayPoints) {
                RouteAnnotation* item = [[RouteAnnotation alloc]init];
                item = [[RouteAnnotation alloc]init];
                item.coordinate = tempNode.pt;
                item.type = 5;
                item.title = tempNode.name;
                [_mapView addAnnotation:item];
                [item release];
            }
        }
        //轨迹点
        BMKMapPoint * temppoints = new BMKMapPoint[planPointCounts];
        int i = 0;
        for (int j = 0; j < size; j++) {
            BMKDrivingStep* transitStep = [plan.steps objectAtIndex:j];
            int k=0;
            for(k=0;k<transitStep.pointsCount;k++) {
                temppoints[i].x = transitStep.points[k].x;
                temppoints[i].y = transitStep.points[k].y;
                i++;
            }            
        }
        // 通过points构建BMKPolyline
        BMKPolyline* polyLine = [BMKPolyline polylineWithPoints:temppoints count:planPointCounts];
        [_mapView addOverlay:polyLine]; // 添加路线overlay
        delete []temppoints;
    }
}

//步行的路线绘制
- (void)onGetWalkingRouteResult:(BMKRouteSearch*)searcher result:(BMKWalkingRouteResult*)result errorCode:(BMKSearchErrorCode)error {
    MapExplainViewController *exp=[[[MapExplainViewController alloc]init]
                                   autorelease];
    exp.array=result.routes;
    [self.navigationController pushViewController:exp animated:YES];
    NSArray* array = [NSArray arrayWithArray:_mapView.annotations];
    [_mapView removeAnnotations:array];
    array = [NSArray arrayWithArray:_mapView.overlays];
    [_mapView removeOverlays:array];
    if (error == BMK_SEARCH_NO_ERROR) {
        BMKWalkingRouteLine* plan = (BMKWalkingRouteLine*)[result.routes objectAtIndex:0];
        int size = [plan.steps count];
        int planPointCounts = 0;
        for (int i = 0; i < size; i++) {
            BMKWalkingStep* transitStep = [plan.steps objectAtIndex:i];
            if(i==0){
                RouteAnnotation* item = [[RouteAnnotation alloc]init];
                item.coordinate = plan.starting.location;
                item.title = @"起点";
                item.type = 0;
                [_mapView addAnnotation:item]; // 添加起点标注
                [item release];
                
            }
            else if(i==size-1){
                RouteAnnotation* item = [[RouteAnnotation alloc]init];
                item.coordinate = plan.terminal.location;
                item.title = @"终点";
                item.type = 1;
                [_mapView addAnnotation:item]; // 添加起点标注
                [item release];
            }
            //添加annotation节点
            RouteAnnotation* item = [[RouteAnnotation alloc]init];
            item.coordinate = transitStep.entrace.location;
            item.title = transitStep.entraceInstruction;
            item.degree = transitStep.direction * 30;
            item.type = 4;
            [_mapView addAnnotation:item];
            [item release];
            //轨迹点总数累计
            planPointCounts += transitStep.pointsCount;
        }
        
        //轨迹点
        BMKMapPoint * temppoints = new BMKMapPoint[planPointCounts];
        int i = 0;
        for (int j = 0; j < size; j++) {
            BMKWalkingStep* transitStep = [plan.steps objectAtIndex:j];
            int k=0;
            for(k=0;k<transitStep.pointsCount;k++) {
                temppoints[i].x = transitStep.points[k].x;
                temppoints[i].y = transitStep.points[k].y;
                i++;
            }
            
        }
        // 通过points构建BMKPolyline
        BMKPolyline* polyLine = [BMKPolyline polylineWithPoints:temppoints count:planPointCounts];
        [_mapView addOverlay:polyLine]; // 添加路线overlay
        delete []temppoints;
    }
}
```

搜索的回调代理成功后，需要在mapview 上绘制线条：

```objectivec
///<0:起点 1：终点 2：公交 3：地铁 4:驾乘 5:途经点
- (BMKAnnotationView*)getRouteAnnotationView:(BMKMapView *)mapview viewForAnnotation:(RouteAnnotation*)routeAnnotation {
    BMKAnnotationView* view = nil;
    switch (routeAnnotation.type) {
        case 0:
        {
            view = [mapview dequeueReusableAnnotationViewWithIdentifier:@"start_node"];
            if (view == nil) {
                view = [[[BMKAnnotationView alloc]initWithAnnotation:routeAnnotation reuseIdentifier:@"start_node"] autorelease];
                view.image = [UIImage imageWithContentsOfFile:[self getMyBundlePath1:@"images/icon_nav_start.png"]];
                view.centerOffset = CGPointMake(0, -(view.frame.size.height * 0.5));
                view.canShowCallout = TRUE;
            }
            view.annotation = routeAnnotation;
        }
            break;
        case 1:
        {
            view = [mapview dequeueReusableAnnotationViewWithIdentifier:@"end_node"];
            if (view == nil) {
                view = [[[BMKAnnotationView alloc]initWithAnnotation:routeAnnotation reuseIdentifier:@"end_node"] autorelease];
                view.image = [UIImage imageWithContentsOfFile:[self getMyBundlePath1:@"images/icon_nav_end.png"]];
                view.centerOffset = CGPointMake(0, -(view.frame.size.height * 0.5));
                view.canShowCallout = TRUE;
            }
            view.annotation = routeAnnotation;
        }
            break;
        case 2:
        {
            view = [mapview dequeueReusableAnnotationViewWithIdentifier:@"bus_node"];
            if (view == nil) {
                view = [[[BMKAnnotationView alloc]initWithAnnotation:routeAnnotation reuseIdentifier:@"bus_node"] autorelease];
                view.image = [UIImage imageWithContentsOfFile:[self getMyBundlePath1:@"images/icon_nav_bus.png"]];
                view.canShowCallout = TRUE;
            }
            view.annotation = routeAnnotation;
        }
            break;
        case 3:
        {
            view = [mapview dequeueReusableAnnotationViewWithIdentifier:@"rail_node"];
            if (view == nil) {
                view = [[[BMKAnnotationView alloc]initWithAnnotation:routeAnnotation reuseIdentifier:@"rail_node"] autorelease];
                view.image = [UIImage imageWithContentsOfFile:[self getMyBundlePath1:@"images/icon_nav_rail.png"]];
                view.canShowCallout = TRUE;
            }
            view.annotation = routeAnnotation;
        }
            break;
        case 4:
        {
            view = [mapview dequeueReusableAnnotationViewWithIdentifier:@"route_node"];
            if (view == nil) {
                view = [[[BMKAnnotationView alloc]initWithAnnotation:routeAnnotation reuseIdentifier:@"route_node"] autorelease];
                view.canShowCallout = TRUE;
            } 
            else {
                [view setNeedsDisplay];
            }
            
            UIImage* image = [UIImage imageWithContentsOfFile:[self getMyBundlePath1:@"images/icon_direction.png"]];
            view.image = [image imageRotatedByDegrees:routeAnnotation.degree];
            view.annotation = routeAnnotation;
            
        }
            break;
        case 5:
        {
            view = [mapview dequeueReusableAnnotationViewWithIdentifier:@"waypoint_node"];
            if (view == nil) {
                view = [[[BMKAnnotationView alloc]initWithAnnotation:routeAnnotation reuseIdentifier:@"waypoint_node"] autorelease];
                view.canShowCallout = TRUE;
            } 
            else {
                [view setNeedsDisplay];
            }
            
            UIImage* image = [UIImage imageWithContentsOfFile:[self getMyBundlePath1:@"images/icon_nav_waypoint.png"]];
            view.image = [image imageRotatedByDegrees:routeAnnotation.degree];
            view.annotation = routeAnnotation;
        }
            break;
        default:
            break;
    }
    
    return view;
}

- (BMKAnnotationView *)mapView:(BMKMapView *)view viewForAnnotation:(id <BMKAnnotation>)annotation {
    if ([annotation isKindOfClass:[RouteAnnotation class]]) {
        return [self getRouteAnnotationView:view viewForAnnotation:(RouteAnnotation*)annotation];
    }
    return nil;
}

//加载线路
- (BMKOverlayView*)mapView:(BMKMapView *)map viewForOverlay:(id<BMKOverlay>)overlay {
    if ([overlay isKindOfClass:[BMKPolyline class]]) {
        BMKPolylineView* polylineView = [[[BMKPolylineView alloc] initWithOverlay:overlay] autorelease];
        polylineView.fillColor = [[UIColor cyanColor] colorWithAlphaComponent:1];
        polylineView.strokeColor = [[UIColor blueColor] colorWithAlphaComponent:0.7];
        polylineView.lineWidth = 3.0;
        return polylineView;
    }
    return nil;
}
```

经过以上方法，基本上在mapview上就可以看到线路导航图，如果不行，可以那些错误枚举去查看原因，毕竟百度是中国的，所以SDK里的注释都是中文，对于英文渣渣的我来说，简直太幸福了。

> 还有一个地方使用了百度定位来获取天气信息，比系统的地理反编码快很多，很快就获取到了所在位置的城市。

> 同样的天气api使用的是百度天气，返回的数据里直接就有对应天气的图片的url，虽然图片很丑，但是总比没有的强，下面是百度天气的api地址： 

```objectivec
-(void)WeatherRequest {
     [self ShowWaitView:@"正在加载中。。。"];
    //[GlobalData GetInstance].GB_CityString 是自己获取到城市
    NSString *str = [[GlobalData GetInstance].GB_CityString URLEncoding];
    //用定位的城市请求 ak是自己申请的百度密钥
    NSString *strurl=[NSString stringWithFormat:@"http://api.map.baidu.com/telematics/v3/weather?location=%@&output=json&ak=C3d2845360d091a5e8f42f605b7472ea",str];
    ASIFormDataRequest *weatherRequest = [[ASIFormDataRequest alloc] initWithURL:[NSURL URLWithString:strurl]];
    
    weatherRequest.delegate = self;
    [weatherRequest startAsynchronous];
}
```