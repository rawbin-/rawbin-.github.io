---
layout: post
title: "浏览器原理 源码分析"
categories: [Web开发,前端开发]
tags: [浏览器,浏览器原理,源码分析]
---




### 参考资料
---
0. [从Chrome源码看浏览器如何构建DOM树](https://zhuanlan.zhihu.com/p/24911872)
1. [览器内核分类][0]
2. [[科普文] 关于浏览器内核的一些小知识，明明白白选浏览器！][1]
3. [浏览器的JavaScript引擎][3]
4. [前端必读：浏览器内部工作原理][4]
5. [浏览器内部工作原理][5]
6. [浏览器渲染原理及可能出现的bug][6]
7. [[ 浏览器原理 ] Repaint 、Reflow 的基本认识和优化][7]
8. [浏览器的工作原理：新式网络浏览器幕后揭秘][8]
9. [从输入网址到显示页面：浏览器工作原理拆解分析][9]
10. [浏览器的渲染原理简介][10]
11. [[译]Google Chrome中的高性能网络][11]
12. [How Rendering Work (in WebKit and Blink)][12]
13. [浏览器详谈及其内部工作机制 —— web开发必读][13]
14. [how browser work](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)



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