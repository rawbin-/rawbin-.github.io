---
layout: post
title: "JavaScript异步编程理解"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,async]
---

### 引言
JavaScript除了最常用的回调方式之外，还有好几个语言特性为异步编程提供方便的支持。



###  Callbacks 回调方式

+ 时间相关
  +  setTimeout/ setInterval 
  +  nextTick 
  +  setImmediate
+ 事件监听
  + addEventListener/attachEvent
  + EventEmitter
+ 异步I/O
  + XMLHTTPRequest
  + 新版NodeJS中的 I/O 调用风格



回调的方式需要异步控制方知道操作内容（各种回调）,而操作控制方操作确实被动的。

从软件设计的角度来说，异步控制方只需要控制什么时候改变状态，给出一个状态表达即可。而对于操作控制方来说，只需要获取异步控制方的状态，来决定执行什么操作。（迪米特原则或者最少知识原则的要求）这样交互双方才能有效解耦。

Promise就是这样一种状态表达的实现。

同时回调函数在处理多步依赖异步流程的场景时，回调金字塔也是一个问题。

Promise能使依赖回调扁平化。



###  Promise 对将来状态的许诺

+ 链式调用方式，避免回调金字塔
+ 状态可预期(`pending`,`resolved`,`rejected`)
+ 调用方式一致，始终是异步调用
+ 满足条件，"立即"执行
+ 不关心接下来的操作，只改变对接对象的状态
+ 操作控制方有绝对的操作控制权来决定接下来的操作




###  Generators 生成器 (协程的实现)

+ 使用`*`和`yield`实现代码受控执行
+ 应用程序控制代码执行的调度和切换
+ 控制支点较原始，对控制的要求较高（有控制才执行）
+ 需要有包装才能实现业务流程
+ `thunk`函数，`co`模块能实现这样的包装



使用底层的生成器完成业务代码，需要生成器流程管理的包装；作为语言特性来说算是一个缺憾。



### async 函数（ES7）

+ 使用`async`和`await`实现流程管理
+ 包装好了的生成器函数，不需要额外的包装代码
+ 使用同步的形式编写异步代码
+ 返回`Promise`只关心状态控制





### 参考资料

1.  《深入浅出NodeJS》
2. 《JavaScript异步编程-设计快速响应的网络应用》
3. 《You Don't Know JS-Async & Performance》
4. [You Don't Know JS: Async & Performance](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20&%20performance/README.md#you-dont-know-js-async--performance)
5. 《ES6标准入门》
6. [ES6入门](http://es6.ruanyifeng.com/)