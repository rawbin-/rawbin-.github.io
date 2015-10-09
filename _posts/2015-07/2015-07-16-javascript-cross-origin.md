---
layout: post
title: "JavaScript跨域方法实例详解"
categories: [开发技术, JavaScript]
tags: [前端,跨域]

---

## 同源与跨域

### 什么是源
RFC6454 规定一个资源的源由资源的URI中的（协议，主机，端口）这一个三元组来确定（IE中没有把端口纳入源的属性）。比如 https://www.test.com/test-script.js, 这个资源的源为（https, www.test.com, 80）。

对于一个被执行的脚本来说，他的源属性是执行这个脚本的资源的源属性。比如一个页面加载了来自另一个域的脚本，这个脚本执行的时候向页面所在域发送请求被视为同源。

相对路径和无法明确源属性的协议（javascript:,data:,about:blank）的资源的源，取将这些资源载入的页面的源。

IE没有将端口作为同源的组成部分，原因是IE历史垄断的市场占有率导致的历史遗留问题，IE8尝试在原生的XMLHTTPRequest中使用端口作为同源的一部分（标准化），但效果不好，一些依照老IE特性开发的站点，为了保持兼容性，继续使用了原有的MSXML 方式的XMLHTTPRequest。所以在IE8-11的版本都没有考虑端口作为同源的判断条件。

### 怎样算同源
两个资源的组成源的属性（协议，主机，端口），完全一致，这两个资源才同源。这里如果两个资源的协议分别为http 和 https 被认为不同源。同时，即使两个资源的所属同一台服务器，但给出的域名分别为www.testA.com和www.testB.com，这两个资源也被认为是不同源。

### 同源策略
同源策略是浏览器的核心安全策略，目的是将来自不同源的资源进行隔离，并控制不同源资源间的通信，从而减少安全威胁，增强安全性。

#### 同源策略的规则
##### 不限制

互联网的核心思想是资源共享，资源的相互访问应该被允许。

+ 执行来自其他域的脚本（如使用`<script>`标签引用CDN的脚本,JSONP请求等）
+ 渲染来自其他域的图片（如使用`<img>`标签引用图片服务器的图片资源）
+ 应用来自其他域的样式（如使用`<link>`标签引用来自静态资源服务器的样式文件）
+ 嵌入来自其他域的资源（使用`<iframe>`,`<frame>`加载来自其他域的资源）
+ 重定向页面地址（Location对象地址的改变，使用`<a>`链接到其他资源）
+ 数据发送（使用`<form>`向其他域提交数据）
+ 多个子域的资源可以设置document.domain来改变所属域属性，来实现子域同域
+ window.name 属性是可写的，脚本可以随意设置，iframe的name属性，会作为iframe内部window对象的name属性初始值
+ 窗体层级嵌套 可以通过parent.parent...的方式来访问祖先窗口，不受跨域限制
+ postMessage接口跨域通信
+ `<video>`,`<audio>`,`<object>`,`<embed>`,`<applet>`,`@font-face`可以加载其他域的资源

##### 部分限制
+ 跨域发送请求不能自定义HTTP Header
+ 跨域发送请求不能使用PUT和DELETE方式，只能使用GET和POST
+ 脚本可以访问一个不同源窗口的整体，而不能访问窗口的内部信息
+ CORS可以改变跨域的情形Access-Control-Allow-Origin,同源策略不放宽，跨域请求正常工作（设置为例外），不包含用户名和密码，不包含cookie和token，，响应的cookie会被丢弃，如果需要这些信息，需要设置XMLHttpRequest的withCredentials=true
  
##### 完全限制
+ 限制本地文件系统读写
+ 限制FileUpload元素的value属性，不能修改，甚至不能读取路径。
+ 限制脚本对来自不同服务器的文档的读写（同源策略）
+ 限制本地存储localStorage和sessionStorage和IndexedDB
+ 限制XMLHTTPRequest请求的发送对象

### 跨域

同源策略将来自不同源的资源进行了隔离，略在阻止安全威胁的同时，也对正常的访问带来了不便，我们也需要在安全策略下根据应用的需要进行不同源资源间的通信。 比如和iframe或frame中的资源的通信，与新窗口的资源的通信，与不同域的服务器之间的通信等。

比如同源策略限制了不同源的资源之间的互操作，当页面在iframe中加载不同源的内容的时候，想要根据页面内容动态调整iframe的高度的时候，就需要特殊的跨域操作。

在应用系统中做单点登录的时候，在进行当前应用的认证的时候需要向认证中心进行认证，这个时候也需要特殊的操作。

## 跨域方法汇总

跨域的方法和浏览器安全问题都围绕着同源策略来展开，我们可以避开浏览器端的参与，从而规避同源策略带来的不便；同时我们也可以利用同源策略及其辅助接口开放的功能特性来实现跨越通信。

### 惹不起躲得起

如果可以的话，可以将Web应用部署在同一个域下，这样可以很好的回避跨域的问题。

### 使用反向代理

直接使用Web服务器apache，nginx等的反向代理的方式，将需要跨域的请求发送到当前域，在Web容器配置中做请求转发，像nginx这样的反向代理很擅长做这样的事情。

### 直面惨淡的人生

#### 动态不受限标签

使用脚本动态创建`<script>`,`<img>`,`<link>`,`<iframe>`,`<frame>`等标签，在加入文档DOM后，浏览器会自动加载并解析渲染响应的资源。

#### JSONP

JSONP原理其实是对`<script>`标签的一个利用，首先`<script>`标签加载资源是不受域限制的，然后浏览器会将`<script>`加载的内容当做脚本来执行。如果服务端返回的是类似于`callback({"data":[{"aa":1}]})`这样的内容，那么浏览器会将`{"data":[{"aa":1}]}`作为函数参数调用callback这个方法。这样就实现了从不同域加载数据。

#### Form提交

Form提交不受限制，客户端可以以GET和POST的方式向服务端提交数据。

#### document.domain

每一个窗体可以对当前窗体所属域进行微调，比如当前与名为`app.test.com`,则可以设置`document.domain`为`test.com`,也可以设置为`app.test.com`。通过将子域的`document.domain`属性均改为主域`test.com`,可以实现`test.com`下的任意子域`app.test.com`,`auth.test.com`,`img.test.com`等之间的通信。

#### window.name 

window.name 在加载不同的页面后还会存在，可以通过使用同一个window来加载需要通信的页面，通过共享window.name来进行数据通信。

#### CORS（Cross Origin Resource Sharing）

通过自定义的HTTP Header让浏览器和服务端进行通信，来决定请求或者响应是否有效。

#### P3P

P3P是处理Web应用中隐私数据的W3C标准,他可以通过添加HTTP 相应头的方式来通过浏览器对cookie的限制，解决第三方cookie传递的问题。

#### postMessage

这个是HTML5新增的页面间通信的接口，能够很好的解决iframe之间通信的问题。

#### WebSocket

这也是HTML5新增的浏览器和服务端通信的非HTTP通信的机制，它不受同源策略的限制，是解决跨域数据传输的解决方案。


## 不同角度看问题

### 本地页面间通信 VS Browser-Server通信
  
#### 本地页面间通信
+ 动态标签
+ postMessage
+ window.name
+ document.domain 

#### Browser-Server通信

##### GET
+ 动态标签
+ JSONP
+ Form提交
+ CORS 
+ P3P
+ WebSocket

##### POST
+ Form提交
+ CORS
+ P3P
+ WebSocket

### 单向通信 VS 双向通信

#### 单向通信
+ 动态标签
+ JSONP
+ Form提交
+ CORS 
+ P3P

#### 双向通信
+ window.name
+ document.domain
+ postMessage
+ WebSocket

### 前端单独处理 VS 需要后端配合

#### 前端单独处理
+ 动态标签
+ window.name
+ document.domain
+ postMessage

#### 需要后端配合
+ JSONP
+ Form提交
+ CORS
+ P3P
+ WebSocket

### 全版本浏览器 VS 现代浏览器

#### 全版本浏览器
+ 动态标签
+ JSONP
+ Form提交
+ window.name
+ document.domain
+ P3P

#### 现代浏览器   
+ CORS
+ postMessage
+ WebSocket

## 实例详解

## 安全相关



## 参考资料
0. [RFC 6454 The Web Origin Concept](http://tools.ietf.org/html/rfc6454)
0. [同源策略和跨域访问](http://blog.csdn.net/shimiso/article/details/21830313)
0. [W3C 同源策略](http://www.w3.org/Security/wiki/Same_Origin_Policy)
0. [JavaScript的同源策略](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)
0. [Same Origin Policy Part 0: Origins](http://blogs.msdn.com/b/ieinternals/archive/2014/03/13/explaining-same-origin-policy-part-0-origins.aspx)
0. [Same Origin Policy Part 1: No Peeking](http://blogs.msdn.com/b/ieinternals/archive/2009/08/28/explaining-same-origin-policy-part-1-deny-read.aspx)
0. [Same Origin Policy Part 2: Limited Write](http://blogs.msdn.com/b/ieinternals/archive/2012/04/03/explaining-same-origin-policy-part-2-limited-write.aspx)
0. [同源策略](http://www.cnblogs.com/dsky/archive/2012/04/06/2434010.html)
0. [同源策略理解](https://rawbin-.github.io/%E5%BC%80%E5%8F%91%E6%8A%80%E6%9C%AF/2015/03/05/javascript-crossorigin/)
0. [[CORS：跨域资源共享] W3C的CORS Specification](http://www.cnblogs.com/artech/p/cors-4-asp-net-web-api-02.html)
0. [JavaScript最全的10中跨域共享的方法](http://www.csdn.net/article/2011-01-27/290968)
0. [前端解决跨域问题的8种方案](http://www.cnblogs.com/JChen666/p/3399951.html)
0. [跨域方法汇总](http://www.udpwork.com/item/11695.html)
0. [跨域方法汇总](http://www.raychase.net/2216)
0. [父子页面跨域通信的方法](http://tid.tenpay.com/?p=4695)
0. [父子页面跨域解决办法](http://www.ttlsa.com/web/cross-domain-solutions/)
0. [优雅绝妙的Javascript跨域问题解决方案](http://blog.csdn.net/sfdev/article/details/5807045)
0. [JavaScript跨域访问解决方案](http://blog.csdn.net/sfdev/article/details/3887006)
0. [JS几种实用的跨域方法原理详解](http://www.cnblogs.com/2050/p/3191744.html)
0. [JavaScript跨域总结与解决办法](http://www.cnblogs.com/rainman/archive/2011/02/20/1959325.html)
0. [JavaScript跨域解决方法大全](http://blog.csdn.net/freshlover/article/details/40827207)
0. [近乎完美的简单JS跨域解决方法](http://rubel.iteye.com/blog/901182)
0. [Post方式跨域上传文件](http://blog.csdn.net/black_ox/article/details/20645957)
0. [AJAX机制详解及跨域通信](http://www.cnblogs.com/renlong0602/p/4414872.html)
0. [跨域数据传输方法](http://www.cnblogs.com/GodIsBoy/p/3563865.html?utm_source=tuicool)
0. [异步上传文件并获得返回值（完全跨域）](http://blog.csdn.net/lrz1011/article/details/7913992)
0. [异步上传文件并获得返回值（完全跨域）](http://my.oschina.net/whynotAZ/blog/206871#OSC_h2_1)
0. [同源策略以及cookie安全策略](http://blog.csdn.net/turkeyzhou/article/details/8818173)
0. [Google浏览器安全策略](https://code.google.com/p/browsersec/wiki/Main)
0. [同源策略详解及绕过](http://www.freebuf.com/articles/web/65468.html)