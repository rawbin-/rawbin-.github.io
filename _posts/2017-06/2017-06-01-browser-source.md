---
layout: post
title: "浏览器原理 源码分析"
categories: [Web开发,前端开发]
tags: [浏览器,浏览器原理,源码分析]
---




### 1 参考资料
---
0. [从Chrome源码看JS Object的实现](http://www.renfed.com/2017/04/04/chrome-object/)
1. [从Chrome源码看JS Array的实现](http://www.renfed.com/2017/04/16/chrome-js-array/)
2. [从Chrome源码看浏览器如何构建DOM树](https://zhuanlan.zhihu.com/p/24911872)
3. [从Chrome源码看浏览器的事件机制](http://www.renfed.com/2017/02/05/browser-event/)
4. [从Chrome源码看浏览器如何构建DOM树](http://www.renfed.com/2017/01/30/chrome-build-dom/)
5. [从Chrome源码看浏览器如何计算CSS](https://zhuanlan.zhihu.com/p/25380611)
6. [从Chrome源码看浏览器如何layout布局](http://www.renfed.com/2017/02/26/chrome-layout/)
7. [Effective前端6：避免页面卡顿](http://www.renfed.com/2017/02/09/avoid-jank/)
8. [览器内核分类][0]
9. [[科普文] 关于浏览器内核的一些小知识，明明白白选浏览器！][1]
10. [浏览器的JavaScript引擎][3]
11. [前端必读：浏览器内部工作原理][4]
12. [浏览器内部工作原理][5]
13. [浏览器渲染原理及可能出现的bug][6]
14. [[ 浏览器原理 ] Repaint 、Reflow 的基本认识和优化][7]
15. [浏览器的工作原理：新式网络浏览器幕后揭秘][8]
16. [从输入网址到显示页面：浏览器工作原理拆解分析][9]
17. [浏览器的渲染原理简介][10]
18. [[译]Google Chrome中的高性能网络][11]
19. [How Rendering Work (in WebKit and Blink)][12]
20. [浏览器详谈及其内部工作机制 —— web开发必读][13]
21. [how browser work](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)



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