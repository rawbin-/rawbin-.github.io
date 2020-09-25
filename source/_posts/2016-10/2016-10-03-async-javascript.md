---
layout: post
title: "异步JavaScript实践 NodejS事件循环"
categories: [前端开发,Web开发,JavaScript]
tags: [异步,JavaScript,NodeJS]
---











### 1 参考资料

0. [不要混淆nodejs和浏览器中的event loop](https://juejin.im/post/5b0524f8518825428a2631ee?utm_source=gold_browser_extension)
1. [一次弄懂Event Loop（彻底解决此类面试问题）](https://juejin.im/post/5c3d8956e51d4511dc72c200?utm_source=gold_browser_extension)
2. [Node.js的事件驱动模型](http://www.edwardesire.com/2015/05/09/nodejs-event-model/)
3. [Nodejs的事件驱动模型](http://www.tuicool.com/articles/6FjM3ai)
4. [被误解的 Node.js](http://www.ibm.com/developerworks/cn/web/1201_wangqf_nodejs/)
5. [JavaScript 事件模型 事件处理机制](http://blog.csdn.net/chenmoquan/article/details/10162477)
6. [Node.js机制及原理理解初步](http://blog.csdn.net/leftfist/article/details/41891407)
7. [Node.js应用实战和工作原理解析](http://www.csdn.net/article/a/2016-07-12/3358)
8. [深入浅出Node.js（四）：Node.js的事件机制](http://www.infoq.com/cn/articles/tyq-nodejs-event/)
9. [Nodejs源码的阅读-事件循环的建立](http://blog.csdn.net/sxyizhiren/article/details/18950839)
10. [Nodejs源码的阅读-事件循环的过程](http://blog.csdn.net/sxyizhiren/article/details/18954273)
11. [Promise的前世今生和妙用技巧](http://www.cnblogs.com/whitewolf/p/promise-best-practice.html)