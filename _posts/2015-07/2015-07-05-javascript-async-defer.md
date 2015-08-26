---
layout: post
title: "HTML script defer async 区别 对比"
categories: [开发技术]
tags: [JavaScript,Web前端,前端基础]

---


## I. 外引用 script脚本加载过程
### A. 普通script 引用（不带defer，async）

浏览器从头到尾按顺序解析，并占用式(阻塞解析)下载脚本，并执行，完成之后继续往下进行。

### B. 只带defer的script引用

浏览器启用新的线程或进程异步下载（不阻塞解析），并在下载完成后不立即执行，等页面解析完成后再按解析的顺序执行。
这样浏览器解析的脚本的顺序跟脚本最终执行的顺序是一致的。

时间点：(firefox chrome)
   inline脚本>defer脚本->DOMContentloaded->onload


### C. 只带async的script引用

浏览器启用新的线程或进程异步下载（不阻塞解析），下载完成后立即执行。
这意味着文件的执行和代码的先后不是强关系，可能因为网络的原因或者文件大小等因素导致代码执行顺序跟浏览器解析到的代码的顺序不一致。

时间点:(firefox chrome)
    inline脚本->DOMContentloaded->async脚本->->onload

### D. 同时带defer和async的script 引用

+ defer 是html4.x就存在的属性，浏览器支持广泛
+ async 是html5.x才存在的属性，较老旧的浏览器可能不支持（ie8）
+ 同时存在时，优先考虑async模式，如果不支持再考虑defer


## II. 行内script加载过程
分块解析<script>块，在一个块内，先进行变量声明和函数声明的预解析，然后再按顺序解析。


### 参考资料
0. [defer和async的区别](http://segmentfault.com/q/1010000000640869)
1. [script的defer和async](http://www.cnblogs.com/henryhappier/archive/2013/02/22/2921478.html)
2. [引用JavaScript文件时的两个属性defer和async](http://www.oseye.net/user/kevin/blog/53)
3. [script的defer和async](http://www.kuqin.com/webpagedesign/20120208/317938.html)
4. [Asynchronous and deferred JavaScript execution explained](http://peter.sh/experiments/asynchronous-and-deferred-javascript-execution-explained/)
5. [Asynchronous script execution and GPU Acceleration by default](http://peter.sh/2010/09/last-week-asynchronous-script-execution-and-gpu-acceleration-by-default/)
6. [w3c html 4.01 script](http://www.w3.org/TR/html401/interact/scripts.html)
7. [w3c html 5.01 script](http://www.w3.org/TR/html51/semantics.html#scripting-3)
8. [一个defer async的测试](http://segmentfault.com/a/1190000002435922)