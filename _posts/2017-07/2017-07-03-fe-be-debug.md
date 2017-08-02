---
layout: post
title: "前后端联调实践总结"
categories: [Web开发,前端开发]
tags: [前端开发,后端开发,调试,移动端调试]
---

[TOC]

## 联调的认知和感触

### 认知（讨论）
+ 为什么要联调？
+ 联调都干些什么？

### 联调的问题
>   联调就是后端不好好写单元测试与集成测试，让前端发请求调用以达到测试的目的；前端不好好写Mock和测试，让后端输出数据以达到测试的目的。

### 更正确地认识

+ 联调是前后端一起见证靠谱的测试结果
+ 给需求方提供一个正确的需求验证环境
+ 尽早暴露前后端实现的问题

## 联调必备技能

### DNS与HOSTS
+ DNS（Domain Name System）的认识
+ DNS提供将域名解析为IP
+ 域名只是便于记忆，真正起作用的还是IP
+ GFW作用，HOST翻墙
+ 域名解析顺序
+ 浏览器缓存>浏览器代理>HOST文件>系统DNS缓存
+ 本地DNS服务器>上级DNS>...>顶级DNS

### HOST配置的姿势

+ 直接修改HOSTS文件
+ 使用Chrome应用 [HostAdmin App](https://chrome.google.com/webstore/search/hostadmin%20app?hl=zh-CN)，也有Firefox插件[HostAdmin](https://addons.mozilla.org/zh-CN/firefox/search/?q=hostadmin&appver=54.0&platform=mac)
+ 使用HOST管理工具 [SwitchHosts](https://github.com/oldj/SwitchHosts)
+ 使用Charles/Fiddler HOST配置
+ 使用FEKit/YKit 代理配置 [Jerry Proxy](http://ued.qunar.com/ykit/proxy.html)

### 更改HOST不即时生效问题
#### 原因
+ 浏览器有HOST缓存
+ 浏览器复用了原有HOST的Socket连接

#### 解决办法
+ 重启浏览器或者打开新的浏览器
+ 使用Charles/Fiddler等代理，避免此问题
+ 使用Chrome工具，强行重置Socket连接

### 代理服务配置
+ Chrome浏览器代理[SwitchySharp](https://chrome.google.com/webstore/detail/proxy-switchysharp/dpplabbmogkhghncfbfdeeokoefdjegm?hl=zh-CN) 或者 [SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega)
+ 系统代理使用Charles/Fiddler 抓取请求包

### 代理服务HTTPS抓包问题
+ 打不开页面？ 需要根据Charles Help菜单中规则添加Charles根证书
+ 某些包解不开？需要在Charles代理设置里面配置具体的解包域名
+ 线上证书不安全？解包后再封的包跟原来的有些区别，需要单独打开对应页面进行信任

## 特定场景的调试姿势

### PC/Touch页面

+ 配置前后端环境
+ 配置HOST
+ 直接使用浏览器及浏览器模拟器调试
+ 如果只是前端更改，在不影响数据的情况下，可以使用线上页面调试

### App页面

#### 真机调试

+ 手机上安装Beta包，使用数据线连上电脑
+ 允许调试或者开启Inspect
+ 通过`chrome://inspect` 或者Safari的开发者工具进行调试

#### 模拟器调试
+ Xcode模拟器
+ 搞一个可以在模拟器里面跑的App
+ 将App拖入模拟器，然后打开，用Safari开发者工具调试



### 微信调试

+ [TBS Studio](http://bbs.mb.qq.com/thread-1416936-1-1.html) Android系统下基于Webview的应用调试（腾讯家的）
+ [微信开发者工具](https://mp.weixin.qq.com/debug/wxadoc/dev/devtools/devtools.html) 微信jsAPI相关调试
+ [weinre](https://www.npmjs.com/package/weinre) 通用的调试工具


## 如何更有效地联调
### 明确前后端的约定
+ 根据需求一起约定交互的内容
+ 包括但不限于如下内容
+ 接口名称、请求路径、请求方式
   + 返回字段结构、对应字段名和字段类型以及字段边界值
   + 异常情况数据约定
+ 一份逼真的样例数据
+ 前端可以根据这个数据造出各种数据


### 后端对约定的履行
+ 根据约定实现相应的需求
+ 使用[Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=zh-CN) 进行接口测试
+ 使用浏览器进行接口jsonp功能测试


### 前端对约定的履行
+ 根据约定实现相应的需求
+ Mock数据
  + 使用FEKit Mock数据
  + 使用YKit Mock数据
  + 使用Charles/Fiddler 代理接口响应
  + 任意其他Mock，代理，转发工具获取Mock数据
+ 通过变换Mock数据，进行功能测试

### 真正的联调

+ 前端完成自测
+ 后端完成自测
+ 一起验证需求的实现

### 友情建议
+ 远离关键路径，合理调配时间——别拖后腿
+ 把握全局进度，提高整体效率——别浪费时间
+ 积极沟通交流，主动推进联调——别甩锅
