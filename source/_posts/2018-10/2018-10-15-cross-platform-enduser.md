---
layout: post
title: "2020跨平台跨端解决方案梳理"
categories: 
- [前端开发,Web开发,JavaScript,代码规范]
tags: 
- [跨平台,跨端,解决方案,Web跨平台,Web跨端]
---



# 解决方案汇总

## Hybrid（H5+原生混合）

### Cordova 前身是PhoneGap

- 跨平台 Android，iOS，Windows

#### 相关资源

- 文档地址： https://cordova.apache.org/
- github地址：https://github.com/apache/cordova

### uni-app DCloud

是一个使用 [Vue.js](https://vuejs.org/) 开发所有前端应用的框架，开发者编写一套代码，可发布到iOS、Android、H5、以及各种小程序（微信/支付宝/百度/头条/QQ/钉钉/淘宝）、快应用等多个平台。

#### 相关资源

- 文档地址： https://uniapp.dcloud.net.cn/
- github地址：https://github.com/dcloudio/uni-app

### AppCan

- 跨平台 Android,iOS

### Inoic

### Titanium

### Xamarin

- 跨平台 Android, iPhone, iPad, Mac, and Windows
- 开发语言 C#



## X Native

### Weex

阿里出品，Vue官方支持的跨平台开发框架，对标React Native

#### 相关资源

- 文档地址：https://weex.apache.org/zh/
- github地址：https://github.com/alibaba/weex



### React Native

Facebook出品的，跨平台开发框架，基于React，一码多端的成熟产品。

#### 相关资源

- 文档地址： https://reactnative.dev/
- github地址：https://github.com/facebook/react-native

## Taro
Taro 是一个开放式跨端跨框架解决方案，支持使用 React/Vue/Nerv 等框架来开发微信/京东/百度/支付宝/字节跳动/ QQ 小程序/H5 等应用。现如今市面上端的形态多种多样，Web、React Native、微信小程序等各种端大行其道，当业务要求同时在不同的端都要求有所表现的时候，针对不同的端去编写多套代码的成本显然非常高，这时候只编写一套代码就能够适配到多端的能力就显得极为需要。
### 相关资源

- 文档地址：https://taro.aotu.io/
- github地址：https://github.com/NervJS/taro

## Chameleon

**web、微信小程序、支付宝小程序、百度小程序、[android(weex)](https://github.com/chameleon-team/chameleon-sdk-android)、[ios(weex)](https://github.com/chameleon-team/chameleon-sdk-ios)、qq 小程序、[字节跳动小程序](https://cml.js.org/doc/example/tt_miniapp.html)、[快应用](https://cml.js.org/doc/example/quickapp_miniapp.html)、持续更新中**

### 相关资源

- 文档地址：https://cml.js.org/

- github地址：https://github.com/didi/chameleon

## Flutter

侧重跨端，一套代码适配Android和iOS和Web端

### 相关资源

- 文档地址： https://flutter.dev/docs
- github地址：https://github.com/flutter/flutter

## 小程序

依赖所在平台本身进行跨平台，小程序依赖于微信的跨平台能力，又衍生出一堆框架

### wepy

来源腾讯，Vue风格的框架，支持ES2015+的特性，以及各方面的开发增强

#### 相关资源

- 文档地址：https://wepyjs.gitee.io/wepy-docs/
- github地址：https://github.com/Tencent/wepy

### mpvue

来源美团，使用Vue开发小程序的前端框架

#### 相关资源

- 文档地址： http://mpvue.com/
- github地址：https://github.com/Meituan-Dianping/mpvue

### Chameleon

**web、微信小程序、支付宝小程序、百度小程序、[android(weex)](https://github.com/chameleon-team/chameleon-sdk-android)、[ios(weex)](https://github.com/chameleon-team/chameleon-sdk-ios)、qq 小程序、[字节跳动小程序](https://cml.js.org/doc/example/tt_miniapp.html)、[快应用](https://cml.js.org/doc/example/quickapp_miniapp.html)、持续更新中**

#### 相关资源

- 文档地址：https://cml.js.org/
- github地址：https://github.com/didi/chameleon

### mpx

#### 相关资源

- 文档地址：https://mpxjs.cn/
- github地址：https://github.com/didi/mpx

### Taro

Taro 是一个开放式跨端跨框架解决方案，支持使用 React/Vue/Nerv 等框架来开发微信/京东/百度/支付宝/字节跳动/ QQ 小程序/H5 等应用。现如今市面上端的形态多种多样，Web、React Native、微信小程序等各种端大行其道，当业务要求同时在不同的端都要求有所表现的时候，针对不同的端去编写多套代码的成本显然非常高，这时候只编写一套代码就能够适配到多端的能力就显得极为需要。

#### 相关资源

- 文档地址：https://taro.aotu.io/
- github地址：https://github.com/NervJS/taro

## Fusion

## Electron

- 跨平台 Mac, Windows, Linux
- 开发语言 JavaScript

## therecipe/qt

- 跨平台 Windows / macOS / Linux / FreeBSD / Android / iOS / Sailfish OS / Raspberry Pi / AsteroidOS / Ubuntu Touch / JavaScript / WebAssembly

## Picasso

美团的，暂时没开源

### 相关资源

- 介绍文档：https://tech.meituan.com/2018/06/21/picasso-the-future.html

## 快应用

是国内主流手机厂商指定你给的轻量级应用标准，对标微信小程序，用JavaScript开发，原生控件渲染

## 其他汇总

Google(Dart)：
[flutter](https://github.com/flutter/flutter)

FaceBook(React)：
[react](https://github.com/facebook/react)
[react-native](https://github.com/facebook/react-native)

阿里巴巴(Vue、Rax)：
[incubator-weex](https://github.com/apache/incubator-weex)

腾讯(React、Vue)：
https://github.com/Tencent/Hippy
https://github.com/Tencent/omi

京东(React)：
https://github.com/NervJS/taro

滴滴(类似Vue)：
[chameleon](https://github.com/didi/chameleon)

dcloud(Vue)：
[uni-app](https://github.com/dcloudio/uni-app)

EEUI(Vue)：
https://github.com/kuaifan/eeui

ionic(React、Vue、Angular)：
[ionic](https://ionicframework.com/)

cordova(HTML5)：
[cordova](https://cordova.apache.org/)

微软(.NET)：
[xamarin](https://docs.microsoft.com/zh-cn/xamarin/)

陌陌(Lua语言)：
https://github.com/momotech/MLN