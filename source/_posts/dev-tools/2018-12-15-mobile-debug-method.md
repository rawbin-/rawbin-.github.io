---
layout: post
title: "移动端页面真机或模拟器调试方法分场景攻坚"
sticky: 90
categories: [Web开发,前端开发]
tags: [移动端,页面,调试,Android,iOS,HarmonyOS,安卓,苹果,鸿蒙]
---



#  前言

废话不多说，磨刀不误砍柴工，欲善其事，先利其器~



# 调试场景案例详解

## 内网穿透

具体是什么就不介绍了，简单说就是让外网能访问内网

### 为什么要内网穿透？

开发调试过程，要让外网访问内网，比如跟外网服务联调，微信相关开发、公众号，支付宝开发，其他平台第三方对接等等，如何让外网的服务能访问到内网的接口，甚至是本地开发的接口，就很有价值了。

解决联调域名的问题，解决端口映射的问题，接口https协议要求的问题。

### 如何实现一个

下面列举了很多工具，这里以ngrok为例

#### 启动步骤

- 到[ngrok 官网](https://ngrok.com/download),下载 ngrok 可执行文件

- 到可执行文件的目录执行`./ngrok http 8080`  启动成功就可以得到对应的一个域名

  ```
  Web Interface                 http://127.0.0.1:4040
  Forwarding                    http://02e2-114-113-63-81.ngrok.io -> http://localhost:8080
  Forwarding                    https://02e2-114-113-63-81.ngrok.io -> http://localhost:8080
  ```

- 外网访问`https://02e2-114-113-63-81.ngrok.io` 就可以访问到本机的8080端口的服务

- 比如我们启动了weinre或者chii服务，这个时候就可以访问 `https://02e2-114-113-63-81.ngrok.io/target.js` 访问到本机的8080端口，同时解决了要调试的移动端页面是https 而本机8080启动的是http服务导致无法访问的问题。



### 如何自己实现一个

- 可以自己搭建一个ngrok服务器
- 可以通过一个外网可访问的内网域名，并反向代理到自己的机子对应的服务上，比如咱们测试环境的14机器就可以



## HTTPS相关调试

### 安装相关证书

- 用什么代理就装什么的证书，用Charles就装Charles的，用whistle就装whistle的证书
- 如果运气好装不上，就换设备换系统，换不了就想办法换成HTTP的，避免这个证书的问题
- Android 7以上系统证书安装需要费点劲，先试试能行就行，不行就想其他办法，比如Root手机类似于iOS的越狱
- 鸿蒙目前没有明确的办法，adb可能好使



## 资源映射或者数据mock

- 将特定规则的内容映射到本地devServer 解决需要频繁部署的问题，使用Charles的Map Remote可以搞定或者whistle的匹配规则，原理类似；当然也可以映射到其他机器，比如A环境的接口映射到B环境都行
- 将特定规则的资源从本地获取（比如数据mock，本地资源替换等等），这个就是Charles的Map Local功能，对于whistle来说还是规则匹配，也可以使用[类似的插件](https://github.com/rawbin-/webpack-api-mock-plugin)
- 使用场景
  - 端上的网络代理到本机的代理服务上（比如Charles或者whistle），本机代理服务通过各种规则映射到不同的地方
  - 常见场景
    - SPA资源通过Map Remote打到本地devServer上，避免了需要侵入环境加调试代码的问题，同时可以减少部署
    - 线上资源映射到本地，比如CDN资源需要变更，本地直接测试，不需要多次发布
    - 开发联调数据mock



# 常用工具

###  数据代理类

- [Charles](https://www.charlesproxy.com/)
- [whistle](https://github.com/avwo/whistle)
- [Fiddler](https://www.telerik.com/fiddler)
- 各种proxy等等
  - 比如ykit 中的jerryproxy
  - [anyproxy](https://github.com/alibaba/anyproxy)

### 调试辅助综合类

- [weinre](http://people.apache.org/~pmuellr/weinre/docs/latest/)
- 腾讯 [vConsole](https://github.com/Tencent/vConsole)
- 微软 [vorlonjs](http://www.vorlonjs.com/)
- [Eruda](https://github.com/liriliri/eruda)
- [spy-debugger](https://github.com/wuchangming/spy-debugger)
- [chii](https://github.com/liriliri/chii) 
- 各种浏览器自带调试工具等等



###  内网穿透

- 操作常用的有 向日葵，TeamViewer，花生壳，蒲公英等等
- 开发常用
  - ngrok系列
    - [ngrok官方](https://ngrok.com/)
    - [ittun](http://www.ittun.com/)
    - [Sunny-Ngrok](https://www.ngrok.cc/)
    - [小米球-ngrok](http://ngrok.ciqiuwl.cn/)
    - [TunnelNat](http://free.tunnelnat.com/)
  - [natapp](https://natapp.cn/)
- 其他可用的
  - [lanproxy](https://github.com/ffay/lanproxy)
  - [goproxy](https://github.com/snail007/goproxy)



# 连数据线真机（模拟器）调试(需要app可调试包)

## Android 端 webview调试

- App代码中要打开webview调试

  ```
  WebView.setWebContentsDebuggingEnabled(true);　
  ```

- Android设备 设置=>打开手机开发者模式(不同手机不一样)

- Android设备 设置=>打开USB调试

- 使用可传送数据的数据线（市面上有些数据线被阉割仅可以充电，无法传数据，自然也无法调试）链接需要调试的设备（手机，Pad等），并允许调试

- 电脑端Chrome浏览器使用`chrome://inspect` 查看可被调试的应用页面

  

## iOS端App webview调试

- iOS 设备设置=>Safari浏览器=>高级=> 打开 网页检查器
- 电脑端Safari浏览器 偏好设置=>高级=> 显示开发菜单
- 使用可传送数据的数据线（市面上有些数据线被阉割仅可以充电，无法传数据，自然也无法调试）链接需要调试的设备（手机，Pad等）
- 电脑端Safari浏览器 开发菜单=>找到对应的设备=>用于开发，并在iOS设备上信任此电脑端
- 电脑端Safari浏览器 开发菜单=>找到对应设备下的对应页面，使用Safari进行调试
- 也可以使用其他工具使用Chrome DevTools进行调试 [ios-webkit-debug-proxy](https://github.com/google/ios-webkit-debug-proxy), 用什么工具都一样，只要满足对应协议提供相应数据就行



## 鸿蒙 Webview调试

- 暂时还没有找到~



## 腾讯X5系Android Webview调试

- 使用[TBS Studio](https://x5.tencent.com/tbs/guide/debug/download.html) 进行调试，[参考文档](https://x5.tencent.com/tbs/guide/debug.html) 
- 下载安装，按步骤要求操作就行（连接手机需要使用能传输数据的数据线）



## 其他强制调试型

### Android 

- 使用 [Xposed](https://repo.xposed.info/module/de.robv.android.xposed.installer) 和 [WebViewDebugHook](https://github.com/feix760/WebViewDebugHook)



### iOS

- 类似 [GlobalWebInspect](https://github.com/ChiChou/GlobalWebInspect)



## 鸿蒙 

- 当前无解



# 通用的H5调试工具

## alert——永远的一哥

- 懂的都懂不多说



## console——一家独大

- 配合各种工具使用



## weinre 系列

- weinre 早期的思想
- spy-debugger 集成weinre 并做功能增强，比如加了自动注入，以及一些相关增强
- chii 使用了较新的Web Inspect或者说ChromeDevTools，更好使





