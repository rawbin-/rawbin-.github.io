---
layout: post
title: "开始使用PhantomJS"
categories: [Web开发,前端开发]
tags: [phantomjs,nodejs,javascript]
---



###  初步使用

[PhantomJS](http://phantomjs.org/) 可以很方便的用来做浏览器模拟测试，网页截图等，它是一个命令行工具，执行JavaScript文件。

在使用的过程中，发现如下的问题:

+ 生成的page对象，只能打开一个页面，需要打开另一个页面的话需要另外create一个page对象，同时page对象的使用不恰当的话，命令行不会报错的；
+ 同时在evaluate中，使用console.log是无法打印出来东西的；
+ 在PhantomJS的交互中，老出错定义一个page对象都出错；
+ 整个PhantomJS执行的脚本中，无法共享NodeJS的模块；
+ 这样就很不爽了，需要异步操作的库，比如Promise呢？



### 寻求出路

上面的问题的解决办法：

+ 每打开一个页面，就先新建一个page对象
+ 这个可以通过page.onConsoleMessage来获取 页面内部的console信息，类似的有onAlert，onPompt，onConfirm
+ 这个没法玩
+ 寻找其他的替代品

于是就有了能在NodeJS平台中跑的phantom，[这里](https://github.com/alexscheelmeyer/node-phantom)有简单的区别说明,用法基本都差不多是PhantomJS的用法。

+ [phantomjs-node](https://github.com/amir20/phantomjs-node),
+ [node-phantom](https://github.com/alexscheelmeyer/node-phantom)



####  代码比较

一开始使用比较两个页面html的方式来判断页面差异，最后发现如下问题：

+ 页面中会有不少请求的url带上了时间戳，导致diff差异大；
+ 同时页面中有JS动态生成的内容，执行的时间差异会导致内容不一样；
+ 页面差异粒度太细，玩不下去了





### 参考资料

1. [PhantomJS](http://phantomjs.org/)
2. [PhantomJS 教程](http://javascript.ruanyifeng.com/tool/phantomjs.html) 
3. [phantomjs-node](https://github.com/amir20/phantomjs-node)
4. [node-phantom](https://github.com/alexscheelmeyer/node-phantom)
5. [phantomjs入门学习笔记](http://my.oschina.net/rasine/blog/335997?fromerr=kafyAzGK)
6. [phantomjs使用说明](http://www.zhouhua.info/2014/03/19/phantomjs/)
7. [利用nodejs+phantomjs+casperjs采集淘宝商品的价格](http://www.cnblogs.com/xinzhyu/p/4214669.html)
8. [鼓捣phantomjs，做ajax网站的信息采集](http://www.cnblogs.com/zeusro/p/4185196.html)
9. [NodeJS + PhantomJS 抓取页面信息以及截图](http://www.cnblogs.com/justany/p/3279717.html)
10. [Resemble](http://huddle.github.io/Resemble.js/)
11. [Resemble.js github](https://github.com/Huddle/Resemble.js) 
12. [CasperJS](http://casperjs.org/index.html)
13. [SlimerJS](http://slimerjs.org/)
14. [SpookyJS](https://github.com/SpookyJS/SpookyJS)
15. [PhantomCSS](https://github.com/Huddle/PhantomCSS)
16. [Headless Testing](http://phantomjs.org/headless-testing.html)
17. [Related Projects](http://phantomjs.org/related-projects.html)
18. [Moving from PhantomJS to node-webkit](http://twolfson.com/2014-11-22-moving-from-phantomjs-to-node-webkit)
19. [从PhantomJS迁移到node-webkit：自动化测试框架简单比较](http://ourjs.com/detail/54792b570dad0fbb6d000005)
20. [CSS Regression Testing](http://tldr.huddle.com/blog/css-testing/)
21. [CSS Testing with PhantomCSS, PhantomJS, CasperJS and Grunt](https://www.phase2technology.com/blog/css-testing-with-phantomcss-phantomjs-casperjs-and-grunt/)
22. [CSS回归测试与Resemble.js](http://drupalchina.cn/node/3157)
23. [浏览器自动化测试初探 - 使用phantomjs与casperjs](http://imweb.io/topic/55e46d8d771670e207a16bdc)
24. [image-diff](https://github.com/uber/image-diff)
25. [blink-diff](https://github.com/yahoo/blink-diff)