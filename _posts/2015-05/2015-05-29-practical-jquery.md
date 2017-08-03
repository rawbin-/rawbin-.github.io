---
layout: post
title: "jQuery实战详解"
categories: [开发技术]
tags: [jQuery源码,jQuery]
---
## 1 事件绑定 bind,live,delegate,on 区别
+ bind 一直存在，直接绑定元素，不支持动态增加的元素，不支持委托；
+ live jquery1.3新增 支持将事件委托到document元素，支持动态增加的元素的事件绑定；
+ live jquery1.4修改 支持live 方法指定上下文对象，解决事件传播链太长问题；
+ delegate jquery1.4.2新增 支持事件委托，将事件委托到父元素，解决事件传播链太长问题；
+ on jquery1.7新增 支持事件代理，同时将以上几个的内部实现也调整为on调用 1.7+推荐使用


### 1.1 参考
2. [jQuery 2.0.3 源码分析 事件绑定 - bind/live/delegate/on][3]
3. [浅谈Jquery中的bind(),live(),delegate(),on()绑定事件方式][4]
4. [jQuery的.bind()、.live()和.delegate()之间区别][5]




## 2 参考资料
0. [jQuery源代码查看工具(jQuery source viewer)][0]
0. [[原创] jQuery1.6.1源码分析系列（停止更新）][1]
1. [jQuery源码分析系列][2]

[0]: http://www.css88.com/tool/jQuerySourceViewer/ "jQuery源代码查看工具(jQuery source viewer)"
[1]: http://www.cnblogs.com/nuysoft/archive/2011/11/14/2248023.html "[原创] jQuery1.6.1源码分析系列（停止更新）"
[2]: http://www.cnblogs.com/aaronjs/p/3279314.html "jQuery源码分析系列"
[3]: http://www.cnblogs.com/aaronjs/p/3440647.html "jQuery 2.0.3 源码分析 事件绑定 - bind/live/delegate/on"
[4]: http://www.cnblogs.com/xilipu31/p/4105794.html "浅谈Jquery中的bind(),live(),delegate(),on()绑定事件方式"
[5]: http://kb.cnblogs.com/page/94469/ "jQuery的.bind()、.live()和.delegate()之间区别"