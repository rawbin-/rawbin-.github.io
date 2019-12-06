---
layout: post
title: "Electron 脚本注入"
categories: [前端开发,Web开发,JavaScript,Electron]
tags: [JavaScript,客户端,桌面端,Electron]
---



### 收集到的方法

#### BrowserWindow 配置

- webPreferences.preload
  - String (可选) -在页面运行其他脚本之前预先加载指定的脚本 无论页面是否集成Node, 此脚本都可以访问所有Node API 脚本路径为文件的绝对路径。 当 node integration 关闭时, 预加载的脚本将从全局范围重新引入node的全局引用标志 [参考示例](https://electronjs.org/docs/api/process#event-loaded).

#### webContents

- 方法
  - `executeJavaScript`
  - `insertCSS`

- 事件

  - `did-finish-load`

  - `dom-ready`

    



### 遇到的一些坑(好好看控制台，错误信息有可能在最上面，滚动到看不见了)

- `preload`脚本语法

  - 脚本本身只能用`commonjs`来写

  - 不支持`import`

  - 也不支持`require` `ES6` module，只能用`module.exports`

    ```js
    // preload.js
    // const rendererAPI = require('./RendererAPI').default
    // rendererAPI()
    // export default () => {
    // SyntaxError: Unexpected token 'export'
    
    // import RendererAPI from './RendererAPI'
    // // RendererAPI()
    // import RendererAPI from './RendererAPI'
    // SyntaxError: Cannot use import statement outside a module
    
    const rendererAPI = require('./RendererAPI')
    rendererAPI()
    ```

  - 报错备忘 注意路径问题(一定要绝对路径)，要用打包后的地址 `preload:path.join(app.getAppPath(),'renderer-preload.js')`

    ```
    //module.exports = xxx
    Uncaught TypeError: Cannot assign to read only property 'exports' of object '#<Object>'
    
    
    
    // const rendererAPI = require('./RendererAPI').default
    // rendererAPI()
    // export default () => {
    // SyntaxError: Unexpected token 'export'
    
    // import RendererAPI from './RendererAPI'
    // SyntaxError: Cannot use import statement outside a module
    
    
    // cannot load preload.js Unexpected Identifier 
    
     Unable to create basic Accelerated OpenGL renderer.
      Unable to create basic Accelerated OpenGL renderer.
      Core Image is now using the software OpenGL renderer. This will be slow.
    ```

    

- executeJavaScript`时机

  - 判断跟`dom-ready`,	`load`等的关系





### 参考资料

0. [BrowserWindow关于preload的说明](https://electronjs.org/docs/api/browser-window)
1. [sandbox中preload的说明](https://electronjs.org/docs/api/sandbox-option)
2. [webContents文档](https://electronjs.org/docs/api/web-contents)
3. [在Electron中最快速预加载脚本](https://www.cnblogs.com/lovesong/p/11161293.html)
4. [Electron webview完全指南]([http://www.ayqy.net/blog/electron-webview%E5%AE%8C%E5%85%A8%E6%8C%87%E5%8D%97/](http://www.ayqy.net/blog/electron-webview完全指南/))
5. [#217 Electron 深度实践总结](https://changkun.us/archives/2017/03/217/)
6. [electron 提供SDK接口注入到远端页面使用](https://blog.csdn.net/qbian/article/details/79360979)
7. [Electron 爬坑记](https://www.jianshu.com/p/6c08573c4eb8)
8. [electron程序，如何在主进程远程页面中注入js及css？](https://newsn.net/say/electron-insert-code.html)
9. [Electron 预加载远程页面提升用户体验](https://www.wyr.me/post/607)
10. [electron 研究笔记](https://www.it610.com/article/5223775.htm)
11. [electron初探问题总结](https://www.cnblogs.com/wonyun/p/10991984.html)
12. [我眼中的 Electron](https://segmentfault.com/a/1190000008205112)