---
layout: post
title: "window open ajax 弹窗 阻止 广告"
categories: [开发技术,JavaScript]
tags: [JavaScript,Ajax,广告]

---

### 浏览器阻止弹窗
浏览器阻止恶意的弹窗（一般是广告弹窗），同时也将我们程序中想要的弹窗也干掉了。     

现有阻止的规则是非用户操作的弹窗，包括程序自动弹窗和用户操作长时间后的弹窗（不同的浏览器表现不一样）。[参考][1]

### 解决办法
+ 直接绑定用户操作事件打开窗口
+ 在AJAX请求之前打开空窗口，在返回后再用返回的URL刷新窗口
+ 使用同步AJAX请求并在回调中打开窗口（可能逃不出时间限制）
+ 如果只想弹广告，直接监听页面点击，滚动事件，弹窗无惧


### 参考资料
[1]: http://lingyi.red/window-open-and-the-browser-to-block-pop-up-window/ "window.open() 与浏览器阻止弹出窗口"

0. [window.open() 与浏览器阻止弹出窗口](http://www.html-js.com/article/2410)
0. [window.open() 与浏览器阻止弹出窗口](http://lingyi.red/window-open-and-the-browser-to-block-pop-up-window/)
0. [弹窗解决最终奥义，人类再再也无法阻止弹窗了！](http://levi.yii.so/archives/3453)
0. [如何绕过浏览器的弹窗拦截机制](http://www.adminwang.com/blog/82.html)
0. [你还敢使用window.open弹广告吗?](http://www.cnblogs.com/birdshome/archive/2007/12/22/window-open.html)
0. [解决window.open() 详解 ](http://my.oschina.net/jgy/blog/122438)
0. [window.open被浏览器拦截的解决方案](http://www.mamicode.com/info-detail-495157.html)
0. [解决弹出的窗口window.open会被浏览器阻止的问题（自定义open方法）](http://www.cnblogs.com/linyechengwei/archive/2011/03/23/1992434.html)
