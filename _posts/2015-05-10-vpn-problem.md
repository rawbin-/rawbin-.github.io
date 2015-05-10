---
layout: post
title: "VPN 619 端口关闭问题解决"
date: 2015/5/10 10:48:25 
categories: VPN
---

### 原因总结
+ VPN连接限制，换一个备用的VPN试试
+ Windows相关服务没开启
+ Windows防火墙限制
+ 路由器防火墙限制


### 我的解决办法
1、在计算机管理->服务中开启Windows服务
Telephony 
Remote Access Connection Manager 
Remote Access Auto Connection Manager 
Remote Procedure Call (RPC) Locator
Network Connections

2、在路由器管理界面->安全设置里面关闭SPI防火墙


### 参考文档
0. [VPN错误解决办法一][2]
1. [VPN错误解决办法二][3]
2. [VPN 619解决办法][1]

[1]: http://jingyan.baidu.com/article/49711c61418c45fa441b7cde.html "VPN 619  解决办法"
[2]: http://jingyan.baidu.com/article/49711c61501a6afa451b7c4c.html "VPN错误解决办法一"
[3]: http://jingyan.baidu.com/article/a378c9609e3952b329283074.html "VPN错误解决办法二"