---
layout: post
title: "webpack-前端工程化模块化工具之webpack"
categories: [前端开发,Web开发,开发工具]
tags: [模块化,工程化,JavaScript,webpack,打包工具]
---

### 1 简介

#### 1.1 基本思想

+ webpack 的核心是js，它将所有能引用的资源都转换成js来处理，通过loader来适配所有的资源加载，通过plugin来处理其他相应的代码转换之外的事情。


#### 1.2 学习曲线

+ 1.x 说明了webpack的总体思想和功能块，更加完善和全面；但2.x的文档分类更加明晰，更容易学习
+ [这里是一个完整的实例](http://webpack.github.io/docs/tutorials/getting-started/)。
+ 如果没有接触过webpack ，可以直接从2.x上手
+ 如果有过1.x的实践，可以通过2.x的文档重新学习和巩固，并注意 [1.x到2.x之间的区别](https://webpack.js.org/guides/migrating/)
+ [涉及到的概念](https://webpack.js.org/concepts/)
+ [一些应用实例](http://webpack.github.io/docs/examples.html)

#### 1.3 关键配置

+ [参考文档](https://webpack.js.org/configuration/)

#### 1.4 重要概念

##### 1.4.1 loader

+ 代码和数据的预处理器或转换器，是一个输入为转换前资源内容输出为转换后资源内容的功能块（函数），[webpack1.x的用法实例](http://webpack.github.io/docs/usage.html)
+ 可以链式调用，最后一个loader需要输出JavaScript，用在其他位置的可以输出任意格式
+ [loaders介绍](http://webpack.github.io/docs/loaders.html)
+ [loaders使用](http://webpack.github.io/docs/using-loaders.html)
+ [如何写一个loader](http://webpack.github.io/docs/how-to-write-a-loader.html)

##### 1.4.2 plugin

+ 打包过程数据额外的处理器或功能增强，不一定有输出，也可以有额外的输出。
+ 订阅打包过程中的各阶段的事件的回调处理
+ [插件介绍](http://webpack.github.io/docs/plugins.html)
+ [插件使用](http://webpack.github.io/docs/using-plugins.html)
+ [如何写一个plugin](http://webpack.github.io/docs/how-to-write-a-plugin.html)

### 2 参考资料

0. [Webpack 中文指南](http://webpackdoc.com/reference.html)
1. [webpack文档索引](http://webpack.github.io/)
2. [webpack 1.x 文档](http://webpack.github.io/docs/)
3. [webpack 2.x 文档](https://webpack.js.org/)
4. [webpack入门看这篇就够了](http://www.jianshu.com/p/42e11515c10f)
5. [webpack参考资料](http://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83/2015/09/10/webpack-reference/)


