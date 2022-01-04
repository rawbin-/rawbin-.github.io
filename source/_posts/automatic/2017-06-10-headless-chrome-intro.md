---
layout: post
title: "Web自动化之Headless Chrome概览"
categories: [Web开发,前端开发,JavaScript,自动化]
tags: [JavaScript,Xvfb, PhantomJS, Selenium, Headless Chrome]
---

[TOC]

### 1 Web自动化

这里所说的Web自动化是所有跟页面相关的自动化，比如页面爬取，数据抓取，页面内容检测，页面功能测试，页面加载性能测试，页面回归测试等等，当前主要由如下几种解决方式：

#### 1.1 文本数据获取

这就是各种`request`库或者类似`curl`这样的工具做的事情，拿到的是页面的文本，然后进行XML文档解析，获得相应的内容。但如果页面有一些动态执行的内容，比如JavaScript生成跳转URL，这样就搞不定了，需要有JS引擎的工具来搞，比如再配合JS引擎自行实现的工具，或者直接使用浏览器内核来搞的工具。

#### 1.2 按照浏览器的方式来执行

这就是各种使用浏览器内核来实现的工具，比如基于`Webkit`的`PhantomJS`, 基于`Gecko`的`SlimerJS`, 经过包装的`CasperJS`兼容`PhantomJS`和`SlimerJS`，以及基于`v8`JS引擎和`.net`平台的`trifleJS`等。这些工具更新迭代跟不上Web标准的发展，新的Web标准无法得到良好的支持。

#### 1.3 通过协议直接操作真实浏览器

这就是`Selenium`的实现方式，通过`WebDriver`协议进行通信，浏览器和浏览器驱动都实现了C/S结构的`WebDriver`协议,`Selenium`通过浏览器的驱动包，来发送操作请求，浏览器根据请求响应相应的操作。这样实现有一个问题是始终有一个真实浏览器的界面在渲染和变化，一方面效率低下，另一方面在服务端环境无法运行。于是就衍生出了使用`Xvfb(virtual framebuffer X Server)` 来配合实现在服务器环境下的运行，基本解决了问题。但每一种浏览器的特性支持都依赖于相应的驱动的实现和支持情况。

#### 1.4 无界面浏览器

前面的实现方式都或多或少有自己的问题或者局限，如果浏览器本身支持在服务端环境运行，这将是一个近乎完美的解决思路。`Chrome` 59开始支持无界面模式，Firefox 55支持Linux版的无界面模式，Firefox 56支持全平台的无界面模式，这样一来，使用方不用被动等待浏览器的`WebDriver`的实现。

### 2 Headless Chrome相关的对比

Headless 是一种在无图形界面环境运行的一种能力，比如高性能的服务器端环境，参考[Healess Software](https://en.wikipedia.org/wiki/Headless_software)。

#### 2.1 PhantomJS的问题
+ 使用老版本的Webkit，不支持新的Web标准，跟真实浏览器存在差异
+ 开发者维护难度大，[主要维护者不干了](https://groups.google.com/forum/)，起因是Chrome 将支持 Headless模式
+ 要解决的问题多，1800+
+ 内存占用高，并发有问题，不稳定，有些奇奇怪怪的bug

#### 2.2 Selenium的问题
+ 使用[WebDriver](https://www.w3.org/TR/webdriver/)接口和浏览器驱动来操作真实浏览器，带界面渲染，速度慢
+ 需要有图形界面，显示设备的支持
+ 需要使用[Xvfb](https://www.x.org/releases/X11R7.7/doc/man/man1/Xvfb.1.xhtml)（virtual framebuffer X Server）
  + X是架构无关的远程图形界面和输入设备兼容的系统，C/S架构
  + 在没有物理输入和显示设备的机器上运行
  + 这里可以简单理解为虚拟显示器或者显示器模拟器
+ 也支持Headless实现，实现跟Web标准有差异
+ 通过[ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/)(WebDriver的Chrome实现)的来操作Chrome，需要依赖ChromeDriver的更新
  + 可以在[ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/downloads)[下载](https://sites.google.com/a/chromium.org/chromedriver/downloads)查看支持的Chrome版本


#### 2.3 Headless Chrome带来的优势

+ 资源占用少（相对于PhantomJS）
+ 服务端环境(命令行，无图形界面)
+ 支持现代Web标准和技术(ES6,ServiceWorkers,WebGL...)
+ 行为符合Web规范、版本能自动更新
+ Chronium/Chrome团队维护


### 3 Headless Chrome 参考资料

+ [Headless Chrome ReadMe](https://chromium.googlesource.com/chromium/src/+/lkgr/headless/README.md)
+ [Getting Started with Headless Chrome](https://developers.google.cn/web/updates/2017/04/headless-chrome)
+ [Chrome DevTools Protocol](https://github.com/ChromeDevTools/devtools-protocol)
+ [Chrome DevTools Protocol Viewer](https://chromedevtools.github.io/devtools-protocol/)



