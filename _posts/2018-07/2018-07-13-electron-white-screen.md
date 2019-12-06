---
layout: post
title: "Electron 白屏问题"
categories: [前端开发,Web开发,JavaScript]
tags: [JavaScript,客户端,桌面端,Electron]
---





### 一些优化方式

#### 一直白屏

- 升级`electron`版本 

- 安装依赖问题 

- 删除 `.electron-vue/webpack.renderer.config.js` 中关于白名单的配置，让所有资源都打包起来（包会变大）

  ```
  externals: [
      ...Object.keys(dependencies || {}).filter(d => !whiteListedModules.includes(d))
    ],
  ```

  

#### 短暂白屏 

- 先不显示窗口，可以显示了再显示
  - `new BrowserWindow` 的时候，设置`show:false`, 然后在 `window` 的`ready-to-show` 事件中显示 







### 参考资料

0. [解决electron-vue build 后static/下资源无法加载问题](https://blog.csdn.net/weixin_43103477/article/details/82259381)
1. [Electron-vue静态资源路径](https://simulatedgreg.gitbooks.io/electron-vue/content/cn/using-static-assets.html?q=)
2. [electron入坑指南](https://www.cnblogs.com/xueyoucd/p/8006610.html)
3. [electron的log调试手段，可用于白屏调试](https://newsn.net/say/electron-dev-logging.html)
4. [Electron踩坑指南不完全篇](https://www.jianshu.com/p/21c9b5e5d709)
5. [Electron-builder打包后，exe运行白屏的问题](https://www.imqianduan.com/electron/159.html)
6. [npm run build 打出来的包，安装后，什么都不显示，白屏？](https://github.com/SimulatedGREG/electron-vue/issues/430)
7. [electron-vue的正确build姿势](https://segmentfault.com/a/1190000013473230)
8. [electron常见问题汇总](https://blog.csdn.net/qq_35432904/article/details/88608707)