---
layout: post
title: "JavaScript 事件循环详解"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,事件循环,event-loop]
---



### 定时执行

+ setTimeout(定时器) clearTimeout
+ setInterval(定时器) clearInterval
+ setImmediate clearImmediate
  + 在本轮事件循环的所有I/O操作之后执行
  + 在下一轮事件循环的所有定时器操作之前执行
+ process.nextTick
  + 在所有`Immediate`和所有`I/O`操作之前执行
  + 原子执行，过程不能中断



### 事件循环

+ 每一个事件阶段都有一个 先进先出的回调队列
+ 当事件循环执行到每个阶段，会在允许情况下执行该阶段的所有操作，然后进入下一个阶段
  + 要么执行完所有在队列里面的回调
  + 要么执行完最大的回调个数限制的回调数
+ 事件循环中的每一个回调（操作）完成后，如果有`process.nextTick`设定的回调，要立即全部执行完（这个看起来是亲生的，相比setImmediate这个后娘养的来说），再进行下一个回调
+ 事件循环的每个阶段及其执行内容
  + `timers` 
    + 执行`setTimeout`和`setInterval`设定的回调；
    + 只能保证不会提前执行，有可能被其他任务拖延（还没轮到这个阶段，设定的时间已经过去）
  + `I/O callbacks` 
    + 执行除`timers`，`check`，`close callbacks`之外的所有回调
  + `idle`,` prepare` 
    + 内部使用的阶段
  + `poll` 
    + `timers`执行也在这里控制
    + 新的I/O事件的处理，此处可能会阻塞
    + 队列非空，执行当前阶段到最大数量限制或者队列置空
    + 队列为空
      + 如果`check`不为空，执行`check`中的全部内容；
      + 如果`check`为空，等待回调添加，然后立即执行
      + 检查`timers`中到点的回调，并执行`times`阶段
  + `check` 
    + `setImmediate`设定的回调（相比起来后娘养的）
    + 在poll完成时执行
  + `close callbacks` 
    + I/O关闭的回调，比如socket.on('close','...')



```
   ┌───────────────────────┐
┌─>│        timers         │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     I/O callbacks     │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     idle, prepare     │
│  └──────────┬────────────┘      ┌───────────────┐
│  ┌──────────┴────────────┐      │   incoming:   │
│  │         poll          │<─────┤  connections, │
│  └──────────┬────────────┘      │   data, etc.  │
│  ┌──────────┴────────────┐      └───────────────┘
│  │        check          │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
└──┤    close callbacks    │
   └───────────────────────┘
```





### 闲扯淡

`NodeJS`官方文档比较`setImmediate`个`process.nextTick`：

+ `process.nextTick`在当前阶段立即执行
+ `setImmediate`在下一轮事件循环的时候尽早执行

如果把`tick`理解为一次回调的话，就没有那一段说的那个问题了，`immediate`再快也要等到高优先级的搞完了才到，而`tick`可以理解为秒针动一次（一个回调） ，一次循环不能为一个`tick`



### 参考资料

1.  [whatwg event-loop](https://html.spec.whatwg.org/multipage/webappapis.html#event-loope441	W33)
2.  [NodeJS 官方Guide](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)
3.  [MDN JavaScript EventLoop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)
4.  [JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)
5.  [JavaScript 事件循环（译文JavaScript Event Loop）](https://segmentfault.com/a/1190000006811224)
6.  [Nodejs Event Loop](http://stackoverflow.com/questions/10680601/nodejs-event-loop)
7.  [神之门V8-----Event loop的舞池盛宴(1)](http://blog.csdn.net/u013700510/article/details/53396166)
8.  [神之门V8-----Event loop的舞池盛宴(2)](http://blog.csdn.net/u013700510/article/details/53401838)
9.  [神之门V8-----Event loop的舞池盛宴(3)](http://blog.csdn.net/u013700510/article/details/53413828)
10.  [Understanding the Node.js Event Loop](https://blog.risingstack.com/node-js-at-scale-understanding-node-js-event-loop/)
10.  [v8 posts](http://wingolog.org/tags/v8)

