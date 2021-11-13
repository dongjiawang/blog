---
layout: post
title: 配置搬瓦工
date: 2017-10-27 17:30:00
categories: ["工具"]
tags:  
mathjax: true
---



新买了一个 `搬瓦工vps`， 练习了下配置流程，在这里记录一下

打开 vps 的控制界面，现在 stop 服务器，安装`CentOS 6+`的系统，我这里打开的时候已经是默认的`centOS-6-x86-bbr`,因为下一步就是[开启 Google BBR 加速](http://www.banwagongzw.com/kvm-bbr.html) （我买的是 KVM 架构）

## 安装 Shadowsocks 服务

搬瓦工提供了一键安装 Shadowsocks 的服务，在控制界面，选择 `Shadowsocks server`的选项，然后选择 `Install Shadowsocks Server`, 坐等安装完成。
安装完成后点击 `Go Back`，就会看到自己的 Shadowsocks 的配置，加密协议默认是 `aes-256-cfb`, 端口默认是`443`, 还有一串随机生成的密码。
如果是自己一个人使用，那么就直接在本地的客户端中填写这些配置就好了。

## 配置多用户的Shadowsocks

#### 通过 ssh 连接上 vps

Mac 下终端中输入 
```
ssh root@*自己的ip地址* -p *vps端口*
```
然后输入 vps 密码。

#### 创建配置文件
在终端中 输入 `vi /etc/shadowsocks.json`
插入以下内容

```
{
 "server":"my_server_ip",  #填入你的IP地址
 "local_address": "127.0.0.1",
 "local_port":1080,
  "port_password": {
      "8381": "自定义密码",    #端口号，密码
      "8382": "自定义密码",
      "8383": "自定义密码"      #最后一个用户后面不用逗号
 },
 "timeout":300,
 "method":"aes-256-cfb",
 "fast_open": false
}
```
退出 `vi`

重启 Shadowsocks 服务
`ssserver -c /etc/shadowsocks.json -d restart`

设置开机启动 Shadowsocks

在终端 `vi /etc/rc.local`
把里面最后的带有`ssserver`的默认代码删除掉，
把 `ssserver -c /etc/shadowsocks.json -d start` 加进去
保存，退出 `vi`
到此就配置完了，可以多用户使用了。

## 优化 Shadowsocks

继续用 ssh 连接上 vps

在终端输入 `vi /etc/sysctl.d/local.conf`,创建配置文件
插入以下内容

```
# max open files
fs.file-max = 1024000
# max read buffer
net.core.rmem_max = 67108864
# max write buffer
net.core.wmem_max = 67108864
# default read buffer
net.core.rmem_default = 65536
# default write buffer
net.core.wmem_default = 65536
# max processor input queue
net.core.netdev_max_backlog = 4096
# max backlog
net.core.somaxconn = 4096

# resist SYN flood attacks
net.ipv4.tcp_syncookies = 1
# reuse timewait sockets when safe
net.ipv4.tcp_tw_reuse = 1
# turn off fast timewait sockets recycling
net.ipv4.tcp_tw_recycle = 0
# short FIN timeout
net.ipv4.tcp_fin_timeout = 30
# short keepalive time
net.ipv4.tcp_keepalive_time = 1200
# outbound port range
net.ipv4.ip_local_port_range = 10000 65000
# max SYN backlog
net.ipv4.tcp_max_syn_backlog = 4096
# max timewait sockets held by system simultaneously
net.ipv4.tcp_max_tw_buckets = 5000
# TCP receive buffer
net.ipv4.tcp_rmem = 4096 87380 67108864
# TCP write buffer
net.ipv4.tcp_wmem = 4096 65536 67108864
# turn on path MTU discovery
net.ipv4.tcp_mtu_probing = 1

# for high-latency network
net.ipv4.tcp_congestion_control = hybla
# forward ivp4
net.ipv4.ip_forward = 1
```
保存并退出

终端中输入 `sysctl --system`,配置生效