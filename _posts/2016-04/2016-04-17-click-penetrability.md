---
layout: post
title: "移动端点击穿透,滑动穿透问题"
categories: [Web开发,前端开发,JavaScript,移动开发]
tags: [点击穿透,滑动穿透,穿透]
---



### 弹层滚动穿透解决步骤：

+ 在touchmove中阻止事件传播已经在chrome 中不好使了 硬伤！
+ 各种js的效果都需要记住滚动位置再进行还原 low！
+ 直接在弹层打开之前给html body 设置overflow hidden，此时的位置是会被记住的，顺滑！
+ 使用router 才是王道，页内弹层带入浏览器历史，位置被保持，前进后退的都能使，自然！





### 参考资料

####  点击穿透

1. [移动页面点击穿透问题解决方案](http://blog.csdn.net/helloxiaoliang/article/details/51362107)
2. [彻底理解和解决移动WEB开发中CLICK点透问题](http://www.uedsc.com/through-the-click-point-in-the-development-of-web.html)
3. [【移动端兼容问题研究】javascript事件机制详解（涉及移动兼容）](http://www.cnblogs.com/yexiaochai/p/3462657.html)
4. [移动页面点击穿透问题解决方案](http://www.ayqy.net/blog/%E7%A7%BB%E5%8A%A8%E9%A1%B5%E9%9D%A2%E7%82%B9%E5%87%BB%E7%A9%BF%E9%80%8F%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/?utm_source=tuicool&utm_medium=referral)
5. [也来说说touch事件与点击穿透问题](https://segmentfault.com/a/1190000003848737)
6. [移动端的touch事件处理](http://www.tuicool.com/articles/uyE3Ar)
7. [移动页面【点击穿透】问题](http://blog.csdn.net/qq_29066959/article/details/50778076)
8. [解读移动端事件穿透、Zepto tap穿透bug、移动端touch穿透](http://www.w3cfuns.com/notes/16843/1decc0aa63c9180ee564ffcd748165e8)

#### 滚动穿透 滑动穿透

1. [移动页面滚动穿透解决方案（荐）](http://blog.csdn.net/qq_16559905/article/details/51333335)

2. [移动端滚动穿透问题完美解决方案](https://segmentfault.com/a/1190000005617307)

3. [移动页面滚动穿透如何解决](https://segmentfault.com/q/1010000002942948)
4. [JS移动客户端--触屏滑动事件]
4. [JS移动客户端--触屏滑动事件](http://www.cnblogs.com/mq0036/p/3934821.html)

5. [移动端滚动穿透问题完美解决方案](http://www.cnblogs.com/sohighthesky/p/mobile-modal-scroll.html)

6. [移动页面滚动穿透问题解决方案](http://www.cnblogs.com/GeniusLyzh/p/5808446.html)

7. [h5页面弹窗滚动穿透的思考](http://www.cnblogs.com/pingfan1990/p/4899931.html)
