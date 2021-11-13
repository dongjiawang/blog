---
layout: post

title: iOS项目中集成Cordova

date: 2018-11-22 18:00:00

categories: ["Cordova"]

tags:

mathjax: true
---

公司想要在已有的APP中集成一个页面，准备使用[Cordova](https://github.com/apache/cordova-ios)。

关于Cordova的介绍可以看他们官方文档 [https://cordova.apache.org/docs/zh-cn/8.x/guide/platforms/ios/index.html](https://cordova.apache.org/docs/zh-cn/8.x/guide/platforms/ios/index.html)



## 安装Cordova

1. 安装Node.js

   安装Cordova需要先安装Node.js，可以去[Node.js官网](https://nodejs.org/en/)下载并安装。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527223315.png)

1. 安装git

   这个不用过多说明了，OSX自带git，用`git --version`，检查一下，如果没有，可以去[git官网](https://git-scm.com)下载安装。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527223346.png)

1. 安装Cordova

   使用`npm`安装

   ```bash
   $ npm install -g cordova
   ```

   然后就是等待安装结果就行。也可以使用`cnpm`，毕竟国内环境。

   不过我安装时候显示成功了，但是创建cordova工程的时候老是失败，就卸载了，使用yarn重新全局安装的。

   ```bash
   $ yarn global add cordova
   ```

## 创建Cordova项目

#### 1. 新建项目

切到需要目录，使用下面的命令创建Demo

```bash
$ cordova create hello com.example.hello HelloWorld
```

等待一会儿，就会创建成功。

> **第一个参数： hello**
>
>  项目的目录，Cordova会创建这个目录，将它的子目录以及各种资源(css,js,img)，遵循共同的web开发文件命名规范。这些资源将存储在设备上的本地文件系统，而不是远程服务。config.xml文件包含重要的需要生成和分发应用程序的元数据。
>
> **第二个参数： com.example.hello**
>
> 可选，APP的ID，默认值：io.cordova.hellocordova，省略这个时候，第三个参数也要省略，可以以后在`config.xml`中修改，但是建议写一个合适的值。
>
> **第三个参数：HelloWorld**
>
> APP的项目名称，默认是HelloCordova，建议写一个合适的值。

#### 2. 添加平台

所有后续命令在项目的目录中进行

```bash
$ cd hello	
```

在构建项目之前，需要指定一组平台。能够运行这些命令取决于电脑是否合理的安装SDK。

```bash
$ cordova platform add ios	
# $ cordova platform add amazon-fireos
# $ cordova platform add android
# $ cordova platform add blackberry10
# $ cordova platform add firefoxos
```

使用下面的命令可以查看cordova支持哪些平台

```bash
$ cordova platforms ls
```

使用下面的命令可以删除支持的平台

```bash
$ cordova platform rm blackberry10
$ cordova platform rm amazon-fireos
$ cordova platform rm android
```

#### 3. 构建应用

使用下面的命令构建项目

```bash
$ cordova build
```

或者构建指定平台

```bash
$ cordova build ios
# $ cordova platform add iosiOS_Cordova
```

## 运行项目

在finder中打开项目会在目录文件中发现如下路径：

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527223439.png)

是不是很熟悉，打开workspace，选择模拟器，就可以简单的跑起来，默认的效果如下：

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527223508.png)

这就简单的构建成功了一个应用。

## 添加插件

根据项目的需求，可以添加插件来调用对应的原生API。

可以使用下面命令搜索插件

```bash
$ cordova plugin search bar code
```

或者用浏览器搜索

[https://cordova.apache.org/plugins](https://cordova.apache.org/plugins/)

iOS常用的插件有：

```bash
# 设备信息
$ cordova plugin add cordova-plugin-device
# 网络
$ cordova plugin add cordova-plugin-network-information
# 电池
$ cordova plugin add cordova-plugin-battery-status
# 加速计
$ cordova plugin add cordova-plugin-device-motion
# 指南针
$ cordova plugin add cordova-plugin-device-orientation
# 定位
$ cordova plugin add cordova-plugin-geolocation
# 相机
$ cordova plugin add cordova-plugin-camera
# 媒体
$ cordova plugin add cordova-plugin-media-capture
# 文件
$ cordova plugin add cordova-plugin-file
$ cordova plugin add cordova-plugin-file-transfer
# 通讯录
$ cordova plugin add cordova-plugin-contacts
```

使用下面的命令查看已经安装的插件

```bash
$ cordova plugin ls
```

## 将cordova的本地文件添加到已有的项目中

### 1. 拷贝配置文件

找到刚才创建的cordova项目的目录

将`CordovaLib`、`cordova`、`www`、`platform_www`四个文件夹拷贝到工程项目的根路径

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527223539.png)



将`config.xml`文件拷贝到工程项目的文件夹内

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527223600.png)



### 2. 修改工程项目的参数

进入工程项目的`Build Phases`, 创建Run Script：

修改标题为`Copy www directory`

复制下面的脚本到shell中

```shell
NODEJS_PATH=/usr/local/bin; NVM_NODE_PATH=~/.nvm/versions/node/`nvm version 2>/dev/null`/bin; N_NODE_PATH=`find /usr/local/n/versions/node/* -maxdepth 0 -type d 2>/dev/null | tail -1`/bin; XCODE_NODE_PATH=`xcode-select --print-path`/usr/share/xcs/Node/bin; PATH=$NODEJS_PATH:$NVM_NODE_PATH:$N_NODE_PATH:$XCODE_NODE_PATH:$PATH && node cordova/lib/copy-www-build-step.js
```

如图：

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527223626.png)



在`Other Linker Flags`中添加`-ObjC -all_load`



### 3. 添加cordova本地文件的引用

`Add Files to`

添加 `CordovaLib.xcodeproj`

添加 `config.xml`

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/2417618-cbf183894661ec47.gif)




### 4. 添加静态文件的引用

先编译`CordovaLib`这个target，得到静态文件，然后在`Link Binary With Libraries`中添加引用`libCordova.a`

### 5. 类文件的引用

创建cocoa touch类，可以继承于`CDVViewController`

在需要使用到cordova的类中 `import`

```objectivec
#import <Cordova/CDVViewController.h>
#import <Cordova/CDVCommandDelegate.h>
#import <Cordova/CDVCommandQueue.h>

@interface CordovaViewController : CDVViewController

@end
```

### 完成

现在cordova已经添加到已有的工程项目中，运行的时候就可以看到工程中www文件夹下的`index.html`中信息了。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/2417618-9f0ec0a600f37f0d.gif)





