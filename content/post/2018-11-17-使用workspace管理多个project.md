---
layout: post

title: 使用workspace管理多个project

date: 2018-11-17 23:00:00

categories: ["iOS"]

tags:

mathjax: true
---

Xcode可以使用workspace同时存在和管理多个project，也就是一个项目中同时存在多个xcodeproj。

如图：

<img src="https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224054.png" style="zoom:50%;" />

### 创建workspace

打开 `File->New-Workspace`

<img src="https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224119.png" style="zoom:50%;" />

这样就创建了一个空的的workspace，然后就可以添加project到其中了。

### 添加或创建project

打开 `File->New->Project`，就可以创建一个新的project。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224141.png)

`在创建的时候把project添加到workspace中`

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224205.png)

同样的方式可以继续创建更多的project。

### 多个project关联使用

以我的Demo中的`UIKitDynamic`的project为例，编译之后会有一个`libUIKitDynamic.a`的静态文件，主project中添加这个静态文件。

<img src="https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224229.png" style="zoom:50%;" />

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224250.png)



在`Header Search Paths`中添加project的路径。

可以直接将project的文件夹拖到这里。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224311.png)

下面就可以在`iOS-FoundationDemo`中引用`UIKitDynamic`的类了。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224337.png)

### 使用bundle引用管理多project中的xib文件

在多个project的时候，使用创建xib文件经常无法关联对应的target。

可以在每个project中使用bundle管理自己的xib，这样就可以正常的使用了。

####  创建目录文件夹作为bundle的保存路径

在项目的根目录中创建文件夹`ModuleBundles`。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224406.png)

####  在project中创建bundle

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224427.png)

将bundle的`Base SDK`改为`iOS`,默认是macOS。

将新建带有xib文件的class，在`targets`中记得选中这个`bundle`。这样编译的时候xib文件会添加下到bundle中。

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224516.png)

下面非常重要的一步，就是利用脚本，将每次编译的bundle文件放到之前在根目录下创建的`ModuleBundles`文件夹中。

使用下面的脚本

这个脚本的`INSTALL_DIR`需要改为你的工程名字

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224637.png)



```bash
FMK_NAME=${PROJECT_NAME}
INSTALL_DIR=${SRCROOT}/../../iOS-FoundationDemo/ModuleBundles/${PROJECT_NAME}

ditto "${BUILT_PRODUCTS_DIR}/${FMK_NAME}.bundle" "${INSTALL_DIR}/${FMK_NAME}.bundle"
```

先编译一次对应的project，这样就会有对应的bundle，然后add file到项目工程的ModuleBundles的group中。

<img src="https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0/img/20190527224658.png" style="zoom:50%;" />



这样就可以正常的使用xib文件了。



> 虽然每次添加bundle步骤麻烦点，但是用起来后还是很顺手的，之前尝试了几次其他的方式，但都大同小异，我觉着这种方式算是比较明了的。