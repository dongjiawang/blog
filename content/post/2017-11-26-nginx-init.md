---
layout: post
title:  搭建本地 nginx 推流服务器
date: 2017-11-26 14:30:00
categories: ["iOS"]
tags: ["nginx"] 
mathjax: true
---

### 安装nginx+rtmp

使用 homebrew 安装，先 clone nginx 到本地

```bash
brew tap homebrew/nginx
```
执行安装命令

```bash
brew install nginx-full --with-rtmp-module
```

安装完成后使用 nginx 命令，检查是否安装成功

```bash
nginx
```

在浏览器里打开http://localhost:8080 ，如果出现下图, 则表示安装成功

![nginx 启动成功](http://upload-images.jianshu.io/upload_images/2417618-a6767ff4ed1b6630.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果终端显示 下面这些 

```
nginx: [emerg] bind() to 0.0.0.0:8080 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:1935 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8080 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:1935 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8080 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:1935 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8080 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:1935 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8080 failed (48: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:1935 failed (48: Address already in use)
nginx: [emerg] still could not bind()
```

则表示端口被占用了
使用命令`lsof -i tcp:8080`
查看端口PID

![端口被占用](http://upload-images.jianshu.io/upload_images/2417618-7ee10542f523557a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`kill 掉 15484`

然后重新启动
`nginx`

打开http://localhost:8080验证 。

### 配置nginx和ramp

查看 nginx 的安装位置

```bash
brew info nginx-full
```

![安装位置](http://upload-images.jianshu.io/upload_images/2417618-774263325a4fb84a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开这个路径下的 `nginx.conf`，

在http节点后面加上`rtmp`配置

```bash
rtmp {
   server {
        listen 1935; 
        application rtmplive { 
              live on; 
             record off; 
        } 
    }
}
```

重启 nginx

```bash
/usr/local/Cellar/nginx-full/1.10.1/bin/nginx -s reload
```

>其中的1.10.1要换成你自己安装的nginx版本号, 查看版本号用 `nginx -v` 命令即可

### 使用 ffmpeg 推流
安装 ffmpeg

```bash
brew install ffmpeg
```

安装支持 rtmp 协议的播放器

[VLC](http://www.videolan.org) 或者 [IINA](https://lhc70000.github.io/iina)

我在文稿中放了一个本地 rtmplive.mp4 视频，将这个视频推流到服务器

```bash
ffmpeg -re -i /Users/sunlin/Documents/rtmplive.mp4 -vcodec libx264 -acodec aac -strict -2 -f flv rtmp://localhost:1935/rtmplive/room
```

打开播放器，在播放 URL 的输入框输入 `rtmp://localhost:1935/rtmplive/room`

就能播放这个推流的视频了。

----
----
我们的 APP 推流使用的是阿里云视频的推流服务，具体的使用看官方的文档就很清晰了

[阿里云视频](https://help.aliyun.com/document_detail/45263.html?spm=5176.doc45263.6.757.fpDMXQ)