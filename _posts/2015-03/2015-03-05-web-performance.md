---
layout: post
title: "前端性能优化"
categories: [开发技术]
tags: [前端,性能优化,HTTP请求]
---

## 1 基本过程
先了解打开一个Web页面的过程：

+ 用户输入网址，浏览器接收到用户输入的地址（一般为域名）
+ 浏览器解析输入，获取域名并通过DNS查询，获取该域名的IP
  查询顺序是浏览器缓存，主机缓存，路由器缓存，ISP（互联网服务提供商）DNS缓存，根域名服务器，顶级域名服务器
+ 浏览器发送HTTP请求到目标主机，获取相应的资源（还有可能被重定向）
  请求Header中会包含浏览器支持的编码方式，接受的内容，有cookie的话还会发送cookie
+ -------请求发送-----
+ 服务器处理浏览器请求，返回相应的资源内容
+ -------响应接收-----
+ 浏览器接收到资源内容，根据响应Header信息解析资源内容
  解析过程可能是解码字节流，解压gzip，解码资源文件，如果编码不明确，还要做编码嗅探
+ 根据解码的文件内容，请求其他相关的资源，比如说JavaScript，CSS，图片等，由回到输入网址的过程
+ 浏览器将拿到的资源进行解析，渲染并显示在浏览器窗口中



## 2 过程级优化
+ 减少HTTP请求次数（网络绝大部分的时间80%花在了传输上）
  每一趟都跑的很累，少跑一趟能省不少事，这个是大头；同样多的活，跑的趟数少了，每趟干背的东西得多
  + 合并资源（图片，CSS，JS分别合并）
  + 合并图片到CSS文件：使用编码的方式将图片编码到CSS中
  + 尽量使用AJAX GET代替POST
+ 减少传输数据量
  跑的趟数是少了，但每趟干的活多了，也不是一个很好的事情，要是每趟背的东西也能少，就更好了。
  + 将合并的静态资源文件进行压缩，服务端启用gzip等压缩
  + 减小cookie内容
    像个未断奶的孩子一样，来回都得背着，背个小孩子总比背个大孩子要轻松
  + 将静态内容和动态内容分开，将需要cookie的内容和不需要cookie的内容分开
  + 避免重复脚本内容，导致的多次下载
+ 启用缓存
  客户端可以共享的资源就没有必要再次请求了（同时也减少了次数），即使需要请求，也是询问资源是否变化，少背东西总能快些
+ 避免无效请求
  大老远跑一趟不容易，白跑总不合适，家里老婆等着拿东西下锅呢
  + 杜绝404（图片，CSS，JS等）
  + 避免页面跳转或重定向  
+ 避免跑远路
  + 使用CDN

## 3 环节级优化
+ 减少DNS查询
  这里只能是在页面中少引用不同域名下的资源，比如打开一个页面从其他十个不同主机下载资源，不过这种慢是第一次，后面会好些
+ 加速浏览器解析
  + 尽早指定页面字符集
  + 将样式表放在页面的前面
  + 避免加载无用内容（CSS，JS）
  + 第一次先加载必要资源，再延迟加载后面才会用到的资源（这次就要用的资源）
  + 加载完成后，提前加载现在还没用到，后面可能用到的资源（下次才会用的资源）
  + 避免使用CSS表达式
  + 用<link>代替@import
  + 避免使用Filter
  + 使用媒体查询按需加载图片
  + 使用CSS动画代替JS动画
  + ​
  + 使用Webworkers做计算量大的操作
  + 将脚本放在页面的后面
  + 减少DOM元素的数量，减小单个DOM大小
  + 减少DOM访问次数
  + 缓存DOM操作，避免重复查询
  + 避免频繁的IO操作，如cookie，localstorage等操作


## 4 参考资料
0. [在浏览器中输入Google.com并且按下回车之后发生了什么？](http://kb.cnblogs.com/page/516964/)
1. [从输入网址到显示页面：浏览器工作原理拆解分析][20]
2. [Web前端优化最佳实践及工具集锦](http://www.csdn.net/article/2013-09-23/2817020-web-performance-optimization)
3. [大话WEB前端性能优化基本套路](https://blog.thankbabe.com/2017/07/05/fore-end-optimize/)
4. [前端性能优化实践][2]
5. [大型网站--前端性能优化和规范][8]
6. [博客园-毫秒必争，前端网页性能最佳实践][4]
7. [JOBBOLE-毫秒必争，前端网页性能最佳实践][5]
8. [关于Yahoo十四条军规与前端性能优化][10]
9. [网站前端性能优化总结][9]
10. [前端性能优化----yahoo前端性能团队总结的35条黄金定律][13]
11. [Web前端性能优化小结][14]
12. [【译】编写高性能JavaScript][11]
13. [前端性能优化最佳实践][12]
14. [7 天打造前端性能监控系统][15]
15. [Web性能监控自动化探索之路–初识WebPageTest][19]
16. [Web前端性能优化全攻略][7]
17. [网站性能优化工具大全][16]
18. [Web前端优化最佳实践及工具集锦][23]
19. [Web网站性能测试工具][17]
20. [WEB前端性能分析--工具篇][0]
21. [Web前端性能分析--实践篇][1]
22. [知乎-前端性能优化][3]
23. [DNS查询原理][18]
24. [DNS解析过程原理【深入浅出详解】][21]
25. [DNS原理及其解析过程【精彩剖析】][22]


[0]: http://blog.csdn.net/five3/article/details/7686376 "WEB前端性能分析--工具篇"
[1]: http://blog.csdn.net/five3/article/details/7688298 "Web前端性能分析--实践篇"
[2]: http://my.oschina.net/felumanman/blog/266096 "前端性能优化实践"
[3]: http://www.zhihu.com/topic/19583739 "知乎-前端性能优化"
[4]: http://www.cnblogs.com/developersupport/p/webpage-performance-best-practices.html "毫秒必争，前端网页性能最佳实践"
[5]: http://blog.jobbole.com/48746/ "毫秒必争，前端网页性能最佳实践"
[6]: http://www.w3cfuns.com/article-1283-1.html "前端性能优化的14个规则，学会就偷着乐"
[7]: http://www.poluoluo.com/jzxy/200912/75133.html "Web前端性能优化全攻略"
[8]: http://www.cnblogs.com/and/p/3390676.html "大型网站--前端性能优化和规范"
[9]: http://www.open-open.com/news/view/9902b7 "网站前端性能优化总结"
[10]: http://segmentfault.com/a/1190000000656717 "关于Yahoo十四条军规与前端性能优化"
[11]: http://www.alloyteam.com/2012/11/performance-writing-efficient-javascript/ "【译】编写高性能JavaScript"
[12]: http://www.iteye.com/magazines/116-Web-Front-Performance-Best-Practice "前端性能优化最佳实践"
[13]: http://www.cnblogs.com/lei2007/archive/2013/08/16/3262897.html "前端性能优化----yahoo前端性能团队总结的35条黄金定律"
[14]: http://www.cnblogs.com/mofish/archive/2010/10/12/1849041.html "Web前端性能优化小结"
[15]: http://fex.baidu.com/blog/2014/05/build-performance-monitor-in-7-days/ "7 天打造前端性能监控系统"
[16]: http://www.qianduan.net/website-performance-optimization-tool.html "网站性能优化工具大全"
[17]: http://www.williamlong.info/archives/1291.html "Web网站性能测试工具"
[18]: http://www.cnblogs.com/yd1227/archive/2010/02/10/1667333.html "DNS查询原理"
[19]: http://www.51testing.com/html/15/n-1360715.html "Web性能监控自动化探索之路–初识WebPageTest"
[20]: http://developer.51cto.com/art/201007/209634_all.htm "从输入网址到显示页面：浏览器工作原理拆解分析"
[21]: http://www.ecdoer.com/post/dns.html "DNS解析过程原理【深入浅出详解】"
[22]: http://369369.blog.51cto.com/319630/812889 "DNS原理及其解析过程【精彩剖析】"
[23]: http://www.csdn.net/article/2013-09-23/2817020-web-performance-optimization "Web前端优化最佳实践及工具集锦"