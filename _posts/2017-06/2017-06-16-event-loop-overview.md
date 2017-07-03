---
layout: post
title: "深入理解JavaScript事件循环之结构概览"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,事件循环]
---

### 引言

从2007年大学课程接触JavaScript以来，到现在（2017年）已经整整十个年头了；期间处理过当时主流的浏览器的兼容，比如IE6、7、8，Opera，Firefox，Safari（那会Chrome用的还很少，少数geek同学用），一半的兼容处理靠的是搜索引擎然后甚至是复制粘贴；十年时间浏览器市场也经历了重大的变迁，Chrome、Firefox、Safari以及微软痛定思痛换了壳还换了引擎的Edge成了几大主流。JavaScript规范也从近十年前的小修小补到目前把这十年的积淀都甩了出来，从Java脚本语言（不少人这么认为吧，甚至还有书这么写的，这里纠正一下，Java和JavaScript曾经有过一些勾搭，但关系不是这样的，或者说这俩没关系）到能自己撑起一片天，现在不理解一些JavaScript核心实现机制了，都不好意思说自己是搞前端的了。

翻了五六十本（这里没写错，就是五六十本）关于JavaScript的书，也没有找到一个能帮助更好理解事件循环的地方，看到的绝大多数也就是“有一个循环，有一个事件队列，循环不断地取队列里面的事件来执行”，网上也基本上千篇一律是这个说法，对于浏览器里面的事件循环，我们看完上面的说法得到的感觉是“哦，对，原来是这样啊，新技能Get”。然而放到NodeJS里面，这种说法又说不过去了。直到有一天翻到《深入浅出Node.js》，对事件循环有了更清晰的认识，逐渐摒弃阮一峰老师[什么是 Event Loop？](http://www.ruanyifeng.com/blog/2013/10/event_loop.html)以及后来所谓的修正版[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)，这些还不是我想要的内容，因为没法解释`process.nextTick`,`setImmediate`,`setTimeout`这几个的区别。偶然的机会在NodeJS官网翻到了[The Node.js Event Loop, Timers, and `process.nextTick()`](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)，使用多个多不同优先级的事件队列来处理，对NodeJS的理解有了一个空前的感觉，这是这么多年来看到的这些资料里面没有过的。然后这也是更加清晰的阐述了原理，也没法解释应用中的类似Promise的异步与这些异步函数之间的优先级的问题。是时候好好捋一捋这里面的东东了。

这里准备搞的是关于整个疑问的分析思路和事件循环的源码分析，抛开源码讲原理无异于耍流氓；仗着源码胡讲原理更是害己害人，所以小生这里也是诚惶诚恐、战战兢兢、如履薄冰~~



HTML 规范中 定时器和事件循环

ECMAScript 规范 job，job queue





### 参考资料

+ 《深入浅出Node.js》

+ [Promise的队列与setTimeout的队列有何关联？](https://www.zhihu.com/question/36972010/answer/71338002)

+ [The Node.js Event Loop, Timers, and `process.nextTick()`](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)

+ [前端基础进阶（十二）：深入核心，详解事件循环机制](http://www.jianshu.com/p/12b9f73c5a4f)

+ [事件循环-深入理解Node.js：核心思想与源码分析](https://yjhjstz.gitbooks.io/deep-into-node/content/chapter5/chapter5-1.html)

+ [JavaScript：彻底理解同步、异步和事件循环(Event Loop)](https://segmentfault.com/a/1190000004322358)

+ [Event loop in JavaScript](https://acemood.github.io/2016/02/01/event-loop-in-javascript/)

+ [Promise介绍--异步篇](https://segmentfault.com/a/1190000007936922)

+ [理解Promise简单实现的背后原理](http://bupt-hjm.github.io/2017/03/23/study-promise/)

+ [libuv.org](http://libuv.org/)

  ​