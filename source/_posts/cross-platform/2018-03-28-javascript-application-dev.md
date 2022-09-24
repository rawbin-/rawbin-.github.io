---
layout: post
title: "JavaScript 桌面客户端开发大合集"
categories: [前端开发,Web开发,JavaScript]
tags: [JavaScript,客户端,桌面端]
---

[TOC]

### 相关平台库

#### electron [ref](https://github.com/electron/electron)



#### node-webkit => NW.js [ref](https://github.com/nwjs/nw.js)



#### hex(多年未更新) [ref](https://github.com/netease-youdao/hex)



### 相关工具

#### NW.js

- 启动安装器
  - windows
    - `NSIS`
    - `Inno Setup`
    - `WinRAR`
  - MacOS
    - `nw-builder`
    - `appdmg`
  - Linux
    - `nw-builder`
- 调试工具
  - `Node Debug`
  - `Node Inspector`
  - `React Inspector`

#### Electron

- 启动安装器
  - windows
    - `Grunt-Electron-Installer`
    - `Electron-installer-squirrel-windows`
    - `electron-packager`
    - `electron-builder`
  - MacOS
    - `electron-builder`
  - Linux
    - `grunt-build-atom-shell`
    - `electron-packager`
    - `electron-builder`
- 调试工具
  - Devtron
    - `console`出来后用`require('devtron').install()`
  - Spectron
- `npm install -g asar` 将源代码转成归档文件
- `npm install -g rcedit` 资源编辑
- 自动更新 `Squirrel`
- 测试工具 `cucumber`
- [electron-forge](https://electronjs.org/docs/all#electron-forge)

### 参考文档

- [Electron VS NW.js](https://electronjs.org/docs/development/atom-shell-vs-node-webkit)
- [Electron 与 Vue 的性能优化](https://www.cnblogs.com/Jccc/p/6509525.html)
- [用JS开发跨平台桌面应用，从原理到实践](https://segmentfault.com/a/1190000019426512)