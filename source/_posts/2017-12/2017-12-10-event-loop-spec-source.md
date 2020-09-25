---
layout: post
title: "事件循环规范和源码分析"
categories: [前端开发,Web开发,JavaScript]
tags: [Javascript,事件循环,Event Loop,浏览器]
---



### W3C HTML 标准事件循环部分的部分翻译

事件循环是用来协调事件，用户操作，脚本，渲染，网络等各个部分的机制，有两种事件循环，一种是浏览上下文事件循环，另一种是Web Worker的事件循环。

每个用户代理只是有一个浏览上下文事件循环，并且单位相关类似域浏览上下文至多有一个浏览上下文事件循环。

每个用户代理总是至少有一个浏览上下文，如果事件循环的浏览上下文消失了，那么事件循环也会消失，浏览上下文总是有一个事件循环来处理它的活动。

事件循环都有一个或多个任务队列，任务队列是任务的有序列表。

每个任务都有特定的任务来源，来自特定任务源和指向特定事件循环的任务必须总是被添加到同一个任务队列，不同任务来源的任务可能被放入不同的任务队列。

每个事件循环都有一个微任务队列，微任务是相对于任务来说的，微任务有自己专门的微任务队列，微任务可分为两种独立回调微任务和复合微任务，HTML规范中只涉及到独立回调微任务。





### 参考资料

0. [W3C HTML 5.2 Event Loop](https://www.w3.org/TR/html52/webappapis.html#event-loops)
1. [whatwg Event Loop](https://html.spec.whatwg.org/multipage/webappapis.html#event-loops)
2. [whatwg HTML 文档中文翻译](https://whatwg-cn.github.io/html/)
3. [一次弄懂Event Loop（彻底解决此类面试问题）](https://juejin.im/post/5c3d8956e51d4511dc72c200?utm_source=gold_browser_extension)
4. [ECMAScript 2018](http://www.ecma-international.org/ecma-262/9.0/index.html)
5. [从Chrome源码看事件循环](https://juejin.im/post/5bdec7d551882516ce4e4d10)
6. [理解Webkit和Chronium](https://blog.csdn.net/milado_nju/)