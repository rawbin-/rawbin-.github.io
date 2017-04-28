---
layout: post
title: "Web性能优化规则参考"
categories: [Web开发,前端开发]
tags: [性能优化,性能调优]
---



### 减少HTTP请求次数

+ 一次HTTP请求包含DNS解析，TCP连接建立（三次握手，HTTPS会更多），数据传输慢启动，数据传输，服务端处理等，少一次请求省很多时间
+ 合并一切可以合并的资源
  + 合并图片（CSS Sprite），极个别图片可以使用Base64 DataURL方式内联在CSS中
  + 合并JavaScript（使用Browserify，Webpack，Gulp，Grunt，rollup等进行打包）
  + 合并CSS（同JavaScript处理）
+ 避免无效请求（图片，JavaScript，CSS等404）
+ 避免页面跳转或者重定向
+ 遵循DRY原则，将公共资源公用比如jQuery,React等
+ 启用缓存，添加Expires或者Cache-Control信息头，配合使用HTTP和浏览器缓存，缓存一切可以缓存的资源
+ 使用Bigpipe 共享HTTP连接

### 减少每次HTTP请求数据传输量或时间

+ 减少Cookie个数，减少Cookie内容；精确设置Cookie属性，不需要的时候就不用传
+ 压缩相应的资源内容，同时服务端启用压缩比如gzip
  + 压缩图片（Google Guetzli再压缩30%）
  + 压缩JavaScript代码（配合打包工具）
  + 压缩CSS（配合打包工具）
+ 使用CDN（Content Delivery Network）
+ 遵循DRY原则，避免冗余重复相似代码



### 浏览器端优化

#### 加载优化

+ 尽早指定页面字符集，让浏览器能更早确定编码
+ 避免内联的样式、脚本和图片，是资源可以被缓存和重用


+ 将样式表放在</HEAD>前面，使用LINK 而不是@import，减少白屏时间
+ 将JavaScript代码放在</BODY>前面，减少脚本对并行下载的阻塞（脚本下载时其他资源不下载）
+ 异步加载独立脚本，<SCRIPT>加上async 或者 defered属性
+ 通过JavaScript代码来控制异步脚本加载（脚本Eval，动态加载等）

#### 渲染优化

+ 减少重排和重绘次数和范围
+ 一次性修改样式，修改不立即渲染的DOM节点
+ 让DOM脱离文档流
+ 减少DOM数量和深度

#### JavaScript优化

+ 使用事件代理
+ 缓存接口数据
+ 使用Web Worker处理后台计算任务
+ 使用Service Workder处理后台网络任务
+ 避免使用JavaScript动画
+ 缓存DOM节点，避免重复查询
+ 缓存DOM操作，使用批量操作代替直接操作
+ 避免频繁的I/O操作，比如Cookie操作，localStorage，IndexedDB等操作
+ 缩短访问作用域链

####　CSS 优化

+ 避免使用CSS表达式
+ 使用CSS动画代替JavaScript动画
+ 大字体，阴影，渐变，背景大小等耗性能

### 服务器端优化

+ 启用压缩比如gzip，减少传输量
+ 使用ETag
+ 缓存请求结果
+ 优化SQL语句，减少请求响应时间



### 参考资料

1. 《Web性能权威指南》,High Performance Browser Networking
2. 《高性能网站建设指南》
3. 《高性能网站建设进阶指南-Web开发者性能优化最佳实践》
4. 《HTTP权威指南》
5. 《高性能JavaScript》