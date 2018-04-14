---
layout: post
title: "浏览器原理 源码分析"
categories: [Web开发,前端开发]
tags: [浏览器,浏览器原理,源码分析]
---

### 1 事件循环

0.  [JavaScript单线程和浏览器事件循环简述](http://www.cnblogs.com/whitewolf/p/javascript-single-thread-and-browser-event-loop.html)
1.  [什么是浏览器的事件循环（Event Loop）？](https://segmentfault.com/a/1190000010622146)
2.  [Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)
3.  [从setTimeout说事件循环模型](http://web.jobbole.com/83883/)
4.  [The JavaScript Event Loop: Explained](https://blog.carbonfive.com/2013/10/27/the-javascript-event-loop-explained/)
5.  [JavaScript单线程异步的背后——事件循环机制](http://blog.csdn.net/fen747042796/article/details/72353238)
6.  [JavaScript：彻底理解同步、异步和事件循环(Event Loop)](https://segmentfault.com/a/1190000004322358)
7.  [从Chrome源码看浏览器的事件机制](https://zhuanlan.zhihu.com/p/25095179)
8.  [chromium源代码结构  ](http://wanglimin2004.blog.163.com/blog/static/115488498201381034354534/)
9.  [理解WebKit和Chromium: 消息循环(Message Loop)](http://blog.csdn.net/milado_nju/article/details/8539795)
10.  [Chromium线程模型、消息循环](http://blog.csdn.net/wy5761/article/details/44095089)
11.  [Chromium：线程模型，消息循环](http://blog.csdn.net/qq295445028/article/details/8028891)
12.  [Chromium：线程模型，消息循环](http://hyshucom.iteye.com/blog/1694645)

### 2 Chrome 源码分析

0.  [Google Chrome源码分析](http://www.ha97.com/tag/chrome)
1.  [Chrome源码剖析 【序】 && 【一】](http://www.cnblogs.com/duguguiyu/archive/2008/10/02/1303095.html)
2.  [Chrome源码剖析【二】Chrome的进程间通信](http://www.cnblogs.com/duguguiyu/archive/2008/10/04/1303695.html)
3.  [Chrome源码剖析【三】Chrome的进程模型](http://www.cnblogs.com/duguguiyu/archive/2008/10/12/1308876.html)
4.   [Chrome源码剖析 【四】Chrome的UI绘制](http://www.cnblogs.com/duguguiyu/archive/2008/10/24/1318363.html)
5.  [Chrome源码剖析 【五】Chrome的插件模型](http://www.cnblogs.com/duguguiyu/archive/2008/11/05/1326777.html)

### 3 参考资料

---
0. [理解WebKit和Chromium](http://blog.csdn.net/milado_nju/article/category/1060500)
1. [学习Google Chrome源码](http://blog.csdn.net/dc_726/article/details/7625365)
2. [从Chrome源码看JS Object的实现](http://www.renfed.com/2017/04/04/chrome-object/)
3. [从Chrome源码看JS Array的实现](http://www.renfed.com/2017/04/16/chrome-js-array/)
4. [从Chrome源码看浏览器如何构建DOM树](https://zhuanlan.zhihu.com/p/24911872)
5. [从Chrome源码看浏览器的事件机制](http://www.renfed.com/2017/02/05/browser-event/)
6. [从Chrome源码看浏览器如何构建DOM树](http://www.renfed.com/2017/01/30/chrome-build-dom/)
7. [从Chrome源码看浏览器如何计算CSS](https://zhuanlan.zhihu.com/p/25380611)
8. [从Chrome源码看浏览器如何layout布局](http://www.renfed.com/2017/02/26/chrome-layout/)
9. [Effective前端6：避免页面卡顿](http://www.renfed.com/2017/02/09/avoid-jank/)
10. [览器内核分类][0]
11. [[科普文] 关于浏览器内核的一些小知识，明明白白选浏览器！][1]
12. [浏览器的JavaScript引擎][3]
13. [前端必读：浏览器内部工作原理][4]
14. [浏览器内部工作原理][5]
15. [浏览器渲染原理及可能出现的bug][6]
16. [[ 浏览器原理 ] Repaint 、Reflow 的基本认识和优化][7]
17. [浏览器的工作原理：新式网络浏览器幕后揭秘][8]
18. [从输入网址到显示页面：浏览器工作原理拆解分析][9]
19. [浏览器的渲染原理简介][10]
20. [[译]Google Chrome中的高性能网络][11]
21. [How Rendering Work (in WebKit and Blink)][12]
22. [浏览器详谈及其内部工作机制 —— web开发必读][13]
23. [how browser work](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)



[0]: http://blog.csdn.net/beyondhaven/article/details/6753834 "览器内核分类"
[1]: http://www.iplaysoft.com/browsers-engine.html "[科普文] 关于浏览器内核的一些小知识，明明白白选浏览器！"
[3]: http://javascript.ruanyifeng.com/bom/engine.html "浏览器的JavaScript引擎"
[4]: http://kb.cnblogs.com/page/129756/ "前端必读：浏览器内部工作原理"
[5]: http://www.admin10000.com/document/1471.html "浏览器内部工作原理"
[6]: http://blog.csdn.net/zzzmmmkkk/article/details/12869207 "浏览器渲染原理及可能出现的bug"
[7]: http://segmentfault.com/a/1190000002629708 "[ 浏览器原理 ] Repaint 、Reflow 的基本认识和优化"
[8]: http://www.kuqin.com/web/20121209/333935.html "浏览器的工作原理：新式网络浏览器幕后揭秘"
[9]: http://developer.51cto.com/art/201007/209634_all.htm "从输入网址到显示页面：浏览器工作原理拆解分析"
[10]: http://kb.cnblogs.com/page/178445/ "浏览器的渲染原理简介"
[11]: http://tech.uc.cn/?p=2092 "[译]Google Chrome中的高性能网络"
[12]: http://tech.uc.cn/?p=2763 "How Rendering Work (in WebKit and Blink)"
[13]: http://www.cnblogs.com/moltboy/archive/2013/05/05/3061872.html "浏览器详谈及其内部工作机制 —— web开发必读"