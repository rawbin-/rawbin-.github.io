---
layout: post
title: "前端视角切入原生之快速开发——鸿蒙、Android、iOS、Flutter"
cagegories: [Web开发,前端开发,移动端]
tags: [原生,Native,鸿蒙,harmonyos,安卓,Android,苹果,iOS,Flutter]
---



# 本次切入侧重点

## 开发调试

## 页面布局，屏幕设备适配

## 路由、页面切换

## 模块化、生命周期

## 网络资源请求

## 本地数据存储

## 上线流程



# Flutter

##  开发调试

- 开发工具 [参考](https://flutter.dev/docs/development/tools)
- 调试工具 [参考](https://flutter.dev/docs/development/tools/devtools/overview)



## 页面布局，屏幕设备适配

- 系统不支持自动适配，需要自己封装，比如有类似于`flexible`的 `screenutil` 封装。
- 通用组件库 [参考](https://flutter.dev/docs/development/ui/widgets-intro)
- [dart ui](https://api.flutter.dev/flutter/dart-ui/dart-ui-library.html)
- [material ui](https://api.flutter.dev/flutter/material/material-library.html)



## 路由、页面切换

- [路由配置](https://api.flutter.dev/flutter/material/MaterialApp/routes.html) 
- [路由构建](https://api.flutter.dev/flutter/material/MaterialPageRoute-class.html)
- [路由跳转](https://api.flutter.dev/flutter/widgets/Navigator-class.html)

## 模块化、生命周期

## 网络资源请求

- [http](https://pub.dev/packages/http)
- [dart:io httpClient](https://api.flutter.dev/flutter/dart-io/HttpClient-class.html)

## 本地数据存储

- 简单本地数据存储 [shared_preference](https://pub.dev/packages/shared_preferences)

## 上线流程



# 鸿蒙

## 开发调试

- 到 [这里](https://developer.harmonyos.com/cn/develop) 下载开发工具`DevEco-Studio`，这里才有Mac版本，学堂里面只有Windows版本，
- 开发文档 在[这里](https://developer.harmonyos.com/cn/documentation) 
- 调试 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/data_visualization-0000001064877812), JS开发用console打日志，Java开发用HiLog打日志



## 页面布局、屏幕设备适配

### 用JS开发

- 屏幕宽度720px，[参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/ui-js-building-ui-layout-intro-0000000000513453)
- 使用媒体查询进行适配 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-references/js-components-common-mediaquery-0000000000625942)
- 通用组件 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-references/js-apis-overview-0000001056361791)
- JS UI 说明 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/ui-js-overview-0000000000500376)



### 用Java开发

- Java UI 说明 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/ui-java-overview-0000000000500404)
- Java UI Demo [参考](https://developer.huawei.com/consumer/cn/codelabsPortal/carddetails/HarmonyOS-JAVA-Components)



## 路由、页面切换

### 用JS开发

- 使用router路由模块 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/ui-js-building-ui-routes-0000000000513445)



### 用Java开发

- 页面间跳转 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/ability-page-concept-0000000000033573)
- 页面部件间跳转 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/ability-page-switching-0000000000037999)





## 模块化

### 用JS开发

- 使用[自定义组件](https://developer.harmonyos.com/cn/docs/documentation/doc-references/js-components-custom-concepts-0000000000611781)
- 组件相关属性 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-references/js-common-events-0000001051151132)
- 组件间通信 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-references/js-framework-syntax-js-0000000000611432)



### 用Java开发

- 页面和页面组件 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/ability-ability-overview-0000000000029852)
- 自定义组件 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/ui-java-custom-component-layout-overview-0000001139508543)



## 生命周期

### 用JS开发

- 组件和App生命周期 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-references/js-framework-syntax-js-0000000000611432)



### 用Java开发

- 页面及页面组件生命周期 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/ability-page-lifecycle-0000000000029840)



## 网络资源请求

###  用JS开发

- 使用fetch模块 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-references/js-apis-network-data-request-0000000000626077)



## 本地数据存储

### 用JS开发

- 使用storage模块 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-references/js-apis-data-storage-0000000000626080)



### 用Java开发

- 简单存储 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/database-preference-overview-0000000000030086), ，及相关的数据库和文件存储



## 上线流程

### 用JS开发

- 编译构建配置 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/build_overview-0000001055075201)
- 应用发布流程 [参考](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/publish_app-0000001053223745)