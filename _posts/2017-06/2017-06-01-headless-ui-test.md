---
layout: post
title: "Headless UI 自动化测试实践"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript, Selenium, Headless Chrome]
---

[TOC]

### 介绍

[Headless](https://en.wikipedia.org/wiki/Headless_software) Chrome是Chrome的无界面模式，提供不带浏览器界面的页面加载，信息抽取，页面内容截图等

操作Chrome使用 Chrome DevTools Protocol，它是Chrome DevTool使用的协议，可以允许通过API来操作Chromium、Chrome和其他基于Blink（基于Webkit）引擎的浏览器，Chrome DevTool团队维护这个API。

#### 现在使用Chrome DevTools Protocol的项目 https://github.com/ChromeDevTools/awesome-chrome-devtools：

+ ChromeDriver 官方的Selenium/WebDriver的Chrome 实现

+ 编辑器扩展

  + VS Code
  + Sublime Web Inspector
  + WebStorm && Jetbrains Chrome Extension

+ 开发工具扩展

  + Chrome React Perf
  + React Developer Tools
  + VueJS Developer Tools
  + Redux DevTools
  + ...

  ​

  #### 优势

  + 资源占用少（相对于PhantomJS）
  + 服务端环境(命令行)
  + Web自动化
  + 支持现代Web标准和技术(ES6,ServiceWorkers,WebGL...)
  + 行为符合Web规范、版本能自动更新
  + Chronium/Chrome团队维护

### Web自动化的需求

- 自动化页面加载、爬取、执行
  - 数据抓取
  - 页面检测
- 自动化页面测试
  - 功能测试
  - 回归测试



### 举步维艰的PhantomJS

#### [PhantomJS](http://phantomjs.org/)做什么

+ [配合各种测试框架（Jasmine、QUnit、Mocha、WebDriver等）进行功能测试](http://phantomjs.org/headless-testing.html)
+ [可编程页面内容截图和图片获取](http://phantomjs.org/screen-capture.html)
+ [访问和操作页面元素，比如获取内容，填写表单等](http://phantomjs.org/page-automation.html)
+ [网络访问数据监控，类似于浏览器调试工具的网络功能](http://phantomjs.org/network-monitoring.html)



#### PhantomJS 问题

+ 使用老版本的Webkit，不支持新的Web标准，跟真实浏览器存在差异

+ 开发者维护难度大，[主要维护者不干了](https://groups.google.com/forum/#!topic/phantomjs/9aI5d-LDuNE)，起因是Chrome 将支持 Headless模式

+ 要解决的问题多，1800+

+ 内存占用高，并发有问题，不稳定，有些奇奇怪怪的bug

  ​

### 效率低下的Selenium

+ 使用[WebDriver](https://www.w3.org/TR/webdriver/)接口和浏览器驱动来操作真实浏览器，速度慢
+ 需要有图形界面，显示设备的支持
+ 需要使用[Xvfb](https://www.x.org/releases/X11R7.7/doc/man/man1/Xvfb.1.xhtml)（virtual framebuffer X Server）
  + X 是架构无关的远程图形界面和输入设备兼容的系统，C/S架构
  + 在没有物理输入和显示设备的机器上运行
  + 这里可以简单理解为 虚拟显示器或者显示器模拟器
+ 也支持Headless实现，实现跟Web标准有差异
+ 通过[ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/)(WebDriver的Chrome实现)的来操作Chrome，需要依赖ChromeDriver的更新
  + 可以在[ChromeDriver下载](https://sites.google.com/a/chromium.org/chromedriver/downloads)查看支持的Chrome版本
  + 目前支持Chrome v56-58，而Headless Chrome在Chrome 59才支持



### [其他类似的实现](https://github.com/dhamaniasad/HeadlessBrowsers)

+ PhantomJS 基于Webkit
+ SlimerJS 基于Gecko
+ CasperJS 驱动PhantomJS 和 SlimerJS
+ trifleJS 使用V8和.Net WebBrowser实现的无界面 IE
+ 其他自行实现类浏览器功能的库



### 相关开发项目

+ [Chrome DevTools Protocol 文档](https://chromedevtools.github.io/devtools-protocol/)


+ [chrome-remote-interface](https://github.com/cyrus-and/chrome-remote-interface/) 
  + 是Chrome DevTools Protocol的NodeJS接口实现
  + 目前支持Google Chrome，Microsoft Edge，Node.js 6.3.0+,Safari(iOS)



### 参考资料

1. [Headless Chrome ReadMe](https://chromium.googlesource.com/chromium/src/+/lkgr/headless/README.md)
2. [Getting Started with Headless Chrome](https://developers.google.cn/web/updates/2017/04/headless-chrome)
3. [相关工具 awesome-chrome-devtools](https://github.com/ChromeDevTools/awesome-chrome-devtools)
4. [技能树升级——Chrome Headless模式](https://segmentfault.com/a/1190000009071883)
5. [Headless Chrome入门](https://baijiahao.baidu.com/po/feed/share?wfr=spider&for=pc&context=%7B%22sourceFrom%22%3A%22bjh%22%2C%22nid%22%3A%22news_3637406010040831860%22%7D)
6. [初探Headless Chrome](https://zhuanlan.zhihu.com/p/27100187)
7. [Lighthouse](https://developers.google.cn/web/tools/lighthouse/)
8. [Showcase Chrome Debugging Protocol Clients](https://developer.chrome.com/devtools/docs/debugging-clients)
9. [依赖chrome-remote-interface的项目](https://www.npmjs.com/browse/depended/chrome-remote-interface)
10. [ChromeDevToolsProtocol](https://github.com/ChromeDevTools/devtools-protocol)
11. [chrome devtool protocol](https://chromedevtools.github.io/devtools-protocol/)
12. [Chrome DevTools Protocol wiki](https://sourceforge.net/p/chromedevtools/wiki/ChromeDevToolsProtocol/)
13. [chrome-remote-interface](https://github.com/cyrus-and/chrome-remote-interface/)
14. [WebKitProtocol](https://sourceforge.net/p/chromedevtools/wiki/WebKitProtocol/)
15. [5 Reasons I Chose Selenium over PhantomJS](http://www.chrisle.me/2013/08/5-reasons-i-chose-selenium-over-phantomjs/)
16. [Running Headless Selenium with Chrome](http://www.chrisle.me/2013/08/running-headless-selenium-with-chrome/)
17. [Headless Chrome Architecture](https://docs.google.com/document/d/11zIkKkLBocofGgoTeeyibB2TZ_k7nR78v7kNelCatUE/edit#heading=h.1mcyhpdqtjob)
18. [vagrant](https://www.vagrantup.com/)
19. [Vagrant 基础全面解析](http://www.cnblogs.com/kelsen/p/6247005.html)
20. [实战Chrome Headless数据抓取](http://www.aichengxu.com/wangluo/24649358.htm)
21. [使用 Headless Chrome 进行页面渲染](https://zhuanlan.zhihu.com/p/26810049)
22. [Google 工程师带你入门 Headless Chrome](http://qkxue.net/info/216803/Google-Headless-Chrome)
23. [FEX Chrome 远程调试协议分析与实战](http://fex.baidu.com/blog/2014/06/remote-debugging-protocol/)
24. [【翻译】怎么在 macOS 上安装和使用 Headless Chrome](http://www.jianshu.com/p/8b1a9d465ae4)
25. [How to install and use Headless Chrome on OSX](https://objectpartners.com/2017/04/13/how-to-install-and-use-headless-chrome-on-osx/)
26. [Chromium 命令行参数说明文档](http://www.chromium.org/developers/how-tos/run-chromium-with-flags)
27. [Chromium 命令行参数列表](http://peter.sh/experiments/chromium-command-line-switches/)
28. [Chrome 命令行参数开关源码](https://cs.chromium.org/chromium/src/chrome/common/chrome_switches.cc?q=chrome_switches.cc&sq=package:chromium&type=cs)
29. [Headless Browser Testing With Xvfb](http://tobyho.com/2015/01/09/headless-browser-testing-xvfb/)
30. [Headless Browser Testing with Chrome and Firefox](http://fgimian.github.io/blog/2014/04/06/headless-browser-testing-with-chrome-and-firefox/)
31. [X Window System](https://en.wikipedia.org/wiki/X_Window_System)
32. [HTMLUnitDriver & PhantomJS for Selenium Headless Testing](http://www.guru99.com/selenium-with-htmlunit-driver-phantomjs.html)
33. [RUNNING SELENIUM WITH THE NEW HEADLESS CHROME](https://intoli.com/blog/running-selenium-with-headless-chrome/)