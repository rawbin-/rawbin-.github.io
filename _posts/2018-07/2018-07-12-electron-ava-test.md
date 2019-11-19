---
layout: post
title: "使用ava+spectron做Electron测试"
categories: [前端开发,Web开发,JavaScript,Electron]
tags: [JavaScript,客户端,桌面端,Electron,自动化测试]
---



### 引子

原有项目是`electron-vue`的模板，使用的是`mocha`和`babel 6`, `ava`需求是`babel 7` 升级了一波babel的插件，就可以玩起来了，基本基本大部分内容：

- `@babel/core` 替换 `babel-core`

- `@babel/preset-*` 替换 `babel-preset-*`

- 部分 `@babel/plugin-*` 替换 `babel-plugin-*`, 有的没有或者不影响，还是用原来的

- 修改 `.babelrc`, `preset` 和 `plugin` 前加`@babel/`

- 为了保留原有的其他`mocha` 相关内容，单独建自己的目录，并修改`package.json`

  - ```
    "e2e:ava": "npm run pack && nyc ava test/e2e/ava",
    ```



`ava`使用的优势：

- 异步支持的好, `Promise`, `Generator`, `Async`,`Observable` ,`Callback`都支持
- 用例并行执行，效率高
- 集成Babel 7，无伤 ES6+



### 启用ava

#### 直接用ava

- `npm i ava@latest -D && ava`  
  - `ava --help`  查看相应的命令行参数



#### 注意点

- `ava`目前只转换测试文件，有问题需要单独处理



### 趟了半天的坑

##### electron 2.0.4 + spectron 3.8.0 OK

##### electron 6.1.4 + spectron 8.0.0 不好使

##### electron 7.1.1 + spectron 9.0.0 不好使

原因是 没有开启`nodeIntegration`, 尴尬 [Node Integration](https://github.com/electron-userland/spectron#node-integration) , [低级问题自问自答](https://github.com/electron-userland/spectron/issues/482)

#### 解决办法

#### 通过 `new BrowserWindow` 的 `preload` 脚本加载, 通过环境变量来设置

```js
const preloadPath = path.join(process.cwd(), 'src/common/api/renderer-preload.js')
  console.log('preloadPath:', preloadPath)
  mainWindow = new BrowserWindow({
    height: 800,
    useContentSize: true,
    width: 1260,
    webPreferences: {
      preload: preloadPath,
      nodeIntegration: false
    }
})
```

```js
// renderer-preload.js
if (process.env.NODE_ENV === 'test') {
  window.electronRequire = require
}
```

```js
// Spenctron test case
test.beforeEach(async t => {
  const app = new Application({
    path: electron,
    args: ['dist/electron/main.js'],
    requireName: 'electronRequire',
    startTimeout: 10000,
    waitTimeout: 10000
  })
  await app.start()
 })
```

```
// package.json 设置环境变量
"e2e:ava": "npm run pack && cross-env NODE_ENV=test nyc ava test/e2e/ava/index.js",
```



#### 直接在 `new BrowserWindow` 的时候配上`webPreferences. nodeIntegration`

```js
mainWindow = new BrowserWindow({
    height: 800,
    useContentSize: true,
    width: 1260,
    webPreferences: {
      nodeIntegration: false
    }
})
```



### 主进程和渲染进程通信

用了`reply`函数，但函数参数中还是需要传 `channelId`, 这个函数与`event.sender.send`的区别在于, `send`总是发送到`main frame`, 而 `reply`是发送到任意（内部调用了`sendToFrame`）,我错误的理解为一个是带`channelId`参数的，一个是不带的，其实是都需要带的

- [IPCMainEvent Doc](https://electronjs.org/docs/api/structures/ipc-main-event)
- [ipcMain doc demo](https://electronjs.org/docs/api/ipc-main#sending-messages)



### 主进程通信`once`注册的并发问题

如果使用`once`注册，那么连续发送两次请求，得到的结果是一样的，需要根据请求，  `once` 注册不同的 `listener`



###  参考资料

0. [babel 7.x 和 webpack 4.x 配置vue项目](https://www.jianshu.com/p/e21d19875fbb)
1. [Upgrade  to Babel 7](https://babeljs.io/docs/en/v7-migration)
2. [ava code coverage](https://wecodetheweb.com/2016/04/19/effortless-unit-testing-with-ava/)