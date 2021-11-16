---
layout: post
title: "前端组件化那些事"
cagegories: [Web开发,前端开发,移动端]
tags: [WebComponent,React,Vue,小程序,组件化]
---

# 组件化的发展过程



## 基于命名空间的多入口文件组件

- jquery插件，一个js 一个css





## 基于模块的多入口文件组件

- amd js，css





## 单JavaScript入口组件



## WebComponent

- 自定义元素
- HTML模板
- Shadow DOM
- HTML import
- [lit from polymer](https://lit.dev/docs/getting-started/)





# React 组件化

## 组件实现方式

- React.createClass
- ES6 Class
- 无状态函数
- Hooks



## 组件生命周期





## 组件复用

- mixin（早期特性）
- Decorator
- HOC——是一个以组件为参数的函数
  - displayName
  - 用途
    - 操作props
    - 通过ref访问组件实例
    - 组件状态提升
    - 用其他元素包裹组件
  - 实现方式
    - 属性代理
    - 反向继承
      - 渲染劫持
      - 控制state



## 组件间通信

- props
- 回调函数
- 自定义事件
- Context
- EventBus



## 组件调试

- React Developer Tools

- React Context DevTool

- Redux DevTools

- why-did-you-update

- ReactPerf

  

## 组件性能优化

- 纯函数
- PureRender（shouldComponentUpdate）
- shouldComponentUpdate
- react-addon-perf



## 组件测试

- react-addons-test-util

- Enzyme

- Jest

  - 浅渲染
  - 全渲染
    - JSDOM
    - Cheerio
    - Karma

- 

  



# Vue 组件化 





# 小程序组件化 