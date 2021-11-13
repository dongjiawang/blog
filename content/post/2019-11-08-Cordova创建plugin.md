---
layout: post

title: Cordova创建plugin
 
date: 2019-11-08 12:00:00

categories: ["Cordova"]

tags: 

mathjax: true

---

创建`plugin`需要用到`plugman`这个工具，可以使用`npm`安装

```bash
npm install -g plugman
```

### 创建plugin模版

假设有一个名为`CordovaDemo`的工程，进入工程根目录，使用`plugman`创建名为`pluginDemo`的插件，版本号是 `1.0`， `ID`是` cordova-plugin-demo`

```bash
plugman create --name pluginDemo --plugin_id cordova-plugin-demo --plugin_version 1.0
```

进入插件目录，配置插件

```bash
cd pluginDemo

#添加iOS平台
plugman platform add --platform_name ios
#添加安卓平台
plugman platform add --platform_name android
```

### 修改plugin.xml

刚刚创建的插件的plugin是这样的，下面就只说iOS平台了，安卓类似

```xml
<?xml version='1.0' encoding='utf-8'?>
<plugin id="cordova-plugin-demo" version="1.0" 
    xmlns="http://apache.org/cordova/ns/plugins/1.0" 
    xmlns:android="http://schemas.android.com/apk/res/android">
    <name>pluginDemo</name>
    <js-module name="pluginDemo" src="www/pluginDemo.js">
        <clobbers target="cordova.plugins.pluginDemo" />
    </js-module>
    <platform name="ios">
        <config-file parent="/*" target="config.xml">
            <feature name="pluginDemo">
                <param name="ios-package" value="pluginDemo" />
            </feature>
        </config-file>
      	<!-- 引用的iOS文件 -->
        <source-file src="src/ios/pluginDemo.m" />
    </platform>
</plugin>
```

`source-file`这里要把自定义的iOS的插件的代码都引用起来，否则会找不到文件(包括头文件)

例如下图，是我的项目都代码

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/QQ20191108-111239@2x.png)



**[《Cordova中使用cocoapods管理iOS的第三方库》](https://blog.dongjiawang.top/2019/10/30/2019-10-30-Cordova中使用cocoapods管理iOS的第三方库/)**

参考这篇文章，管理iOS的cocoapods库

**[《自定义cordova插件》](https://blog.dongjiawang.top/2018/11/23/2018-11-23-自定义cordova插件/)**

参考这篇文章，写原生代码

把原生代码引用到**xml**文件中后，`Cordova`的`js`文件就能调用到

创建`package.json`文件，用

```bash
npm init	
```

`package.json`的内容就用插件的信息就行

### 安装插件到项目

安装Cordova提供的插件的时候使用的是插件的ID

```bash
cordova plugin add cordova-plugin-device
```

安装自定义的本地插件，就可以使用`本地路径`安装，如下

```bash
 cordova plugin add /Users/admin/Desktop/CordovaDemo/plugins/pluginDemo
```

安装成功后，`Cordova`的`plugin`目录会自动添加自定义的插件，就可以正常使用了。