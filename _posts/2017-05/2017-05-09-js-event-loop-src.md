---
layout: post
title: "JavaScript 事件循环源码分析"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,事件循环,event-loop,源码]
---



### NodeJS中的异步实现

+ [`libuv` 设计文档](http://docs.libuv.org/en/v1.x/design.html)
+ [`libuv` API](http://docs.libuv.org/en/v1.x/api.html)

###   `libuv`中的事件阶段

+ 执行到点的定时器
+ 执行`pending`回调
+ 执行`idle` 操作
+ 执行`prepare`操作
+ 监听`I/O`请求
+ 执行`check`操作
+ 执行`close`回调



### 参考资料

1.  [libevent github](https://github.com/libevent/libevent)
2.  [libevent org](http://libevent.org/)
3.  [libuv github](https://github.com/libuv/libuv)
4.  [libuv org](http://libuv.org/)

