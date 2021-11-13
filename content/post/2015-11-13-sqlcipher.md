---
layout: post
title: 使用SQLCipher加密数据库
date: 2015-11-13 16:59:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

Xcode中集成了免费的sqlite，但是不提供加密的模块，突然有一天，蛋疼的客户要求把数据进行加密，于是乎就寻找使用简单并且可以把数据迁移过度到加密数据库的框架。
SQLCipher是第三方的开源框架，实现对sqlite的加密，
[官网链接](http://sqlcipher.net/) 
下面开始下载并导入框架。
*（使用命令行下载）*

使用SQLCipher需要3个文件：`sqlcipher` ，`openssl-xcode`，`openssl-1.0.0e`

**下载 openssl-xcode**
>cd~/Documents/code/SQLCipherApp
>git clone https://github.com/sqlcipher/openssl-xcode.git

**下载 sqlcipher**
>cd~/Documents/code/SQLCipherApp
>git clone https://github.com/sqlcipher/sqlcipher.git

**下载 openssl-1.0.0e**
>curl -o openssl-1.0.0e.tar.gz http://www.openssl.org/source/openssl-1.0.0e.tar.gz
//解压
tar xzf openssl-1.0.0e.tar.gz

把这三个目录拷贝到工程目录中

**配置Xcode**

1、打开Xcode 的设置页，进入`locations ->source trees`  ，点击+号添加项目 ，`settingname` 和 `display name` 均设为  `OPENSSL_SRC`  `path`设置为你工程目录下openssl-1.0.0e的所在路径。比如我的路径是：/Users/henry/Documents/工程公用/SQLCipherApp/openssl-1.0.0e

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926114614.png)

 2、添加项目的引用 ，将文件里的 `openssl.xcodeproj` 和 `sqlcipher.xcodeproj` （分别在openssl-xcode文件和sqlcipher文件下）添加到你的主工程下，建立引用
 3、配置编译库，进入项目的工程 `TARGETS`，进入 `build phases －>target dependencies` ,添加图中的两个项目

![](https://cdn.jsdelivr.net/gh/dongjiawang/BlogImage@1.0.0.2/img/20200926114718.png)

如果在编译时提示：
`No architectures to compile for (ARCHS=armv6,armv7, VALID_ARCHS=armv7 armv7s`
则将在Bulid Settings选项下面的 `Architectures` 和 `Valid Architectures` 里面都改成一样（例如：都填写 armv6 armv7），问题解决。 对于警告 ：
`warning: implicit declaration of function 'sqlite3_key' is invalid in C99` 只需要将Bulid Settings选项下的 `C Language Dialect` 改为： `C89[-std-c89]` 就可以，即使用c89标准或者去掉项目中的arm64。

[部分代码](http://www.cnblogs.com/jw-blog/p/4962613.html)