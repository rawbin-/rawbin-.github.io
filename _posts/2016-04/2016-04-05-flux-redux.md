---
layout: post
title: "Flux Redux 参考资料"
categories: ["前端开发","Web开发"]
tags: ["Flux","Redux","React"]
---

### Flux
#### 组织结构
- constant
  需要在各个模块中都使用的静态条目，比如操作的名称，状态的名称等
- Component
  各种拿数据进行渲染的视图函数，最普通的ReactClass，相关DOM的清理
- Action 
  定义操作函数，引入并调用Dispatcher的方法触发操作任务
- Dispatcher
  操作分发功能的定义模块，接收操作执行回调
- Store
  数据的操作中心，使用Dispatcher监听并处理数据和状态的变更，
- App
  渲染主应用，初始应用信息
- Api
  各种获取和发送数据的操作，不多可以直接放Store

#### 基本流程 
- 用户访问界面（View）
- 用户操作View发出Action
- Dispacher收到Action，调用Store回调进行相应的数据更新
- Store更新数据后，触发View的change事件
- View执行change操作，更新用户界面
- 整体是View调用Action，Action调用Dispacher，Dispacher调用Store，Store更新View状态，View重新渲染

#### 实现方式
- View
  - 绑定操作，在操作中执行对应的Action 
  - 注册事件，在Store触发改变的时候更新状态
- Action
  - 调用Dispacher，执行对应的操作分发
- Dispacher
  - 注册相应的操作分发操作
  - 调用Store的数据处理，并触发Store的更新行为
- Store
  - 实现Event Emitter
  - 进行数据更新
  - 触发View的更新行为	



### 参考资料

####  入门理解

1. [怎样理顺react，flux，redux这些概念的关系，开发中有必要使用它们吗?](http://gold.xitu.io/entry/576cb79a2e958a0078d08b67)
2. [怎样理顺react，flux，redux这些概念的关系，开发中有必要使用它们吗?](https://www.zhihu.com/question/47686258)
3. [浅谈 React、Flux 与 Redux](http://www.tuicool.com/articles/3AFJNbj)
4. [浅谈 React、Flux 与 Redux](http://imweb.io/topic/57711e37f0a5487b05f325b5?utm_source=tuicool&utm_medium=referral)
5. [还在纠结 Flux 或 Relay，或许 Redux 更适合你](https://segmentfault.com/a/119000000309989)
6. [还在纠结 Flux 或 Relay，或许 Redux 更适合你](http://ruby-china.org/topics/26944)
7. [如何理解 Facebook 的 flux 应用架构？](https://www.zhihu.com/question/33864532?sort=created)
8. [Flux用过了，Redux也用过了，还是觉得不顺手？要不要自己造一个？](http://www.open-open.com/news/view/1877fc8)
9. [react 有哪些最佳实践?](https://www.zhihu.com/question/36516604)

1. [React中文](http://reactjs.cn/)
2. [React中文版](http://wiki.jikexueyuan.com/project/react/)
3. [React 官网](https://facebook.github.io/react/)
4. [Flux应用架构](http://reactjs.cn/react/docs/flux-overview.html)
5. [Flux架构入门](http://www.ruanyifeng.com/blog/2016/01/flux.html)
6. [[如何理解 Facebook 的 flux 应用架构？](https://www.zhihu.com/question/33864532?sort=created)](https://www.zhihu.com/question/33864532?sort=created)
7. [[flux github](https://github.com/facebook/flux)](https://github.com/facebook/flux)
8. [Redux 介绍](https://segmentfault.com/a/1190000003503338?_ea=323420)
9. [redux官网](http://redux.js.org/)
10. [Redux核心概念](http://www.jianshu.com/p/3334467e4b32)
11. [深入浅出Redux](http://www.w3ctech.com/topic/1561)
12. [使用Redux管理你的React应用](http://www.cnblogs.com/matthewsun/p/4773646.html)
13. [谈一谈我对 React Flux 架构的理解](http://www.cocoachina.com/webapp/20150928/13600.html)
14. [Redux中文文档](http://cn.redux.js.org/docs/introduction/Motivation.html)
15. [深入理解React、Redux](http://www.jianshu.com/p/0e42799be566)
16. [Redux管理你的React应用](http://www.cnblogs.com/Leo_wl/p/4780750.html)
17. [解读Redux工作原理](https://segmentfault.com/a/1190000004236064?utm_source=Weibo)
18. [redux github](https://github.com/reactjs/redux)
19. [react-redux github](https://github.com/reactjs/react-redux)
20. [NB的React](https://github.com/enaqx/awesome-react)

