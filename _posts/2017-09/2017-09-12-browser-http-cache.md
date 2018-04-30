---
layout: post
title: "浏览器HTTP缓存与不缓存问题详解"
categories: [Web开发,前端开发,HTTP]
tags: [HTTP,缓存,浏览器缓存,页面缓存]
---







### 参考资料

- [RFC 2616 HTTP Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)
- [RFC 7234 HTTP 1.1 Caching](https://tools.ietf.org/html/rfc7234#section-5.4)
- [大公司里怎样开发和部署前端代码？](https://www.zhihu.com/question/20790576)
- [前端工程精粹（一）：静态资源版本更新与缓存](http://www.infoq.com/cn/articles/front-end-engineering-and-performance-optimization-part1)
- [HTTP 缓存](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn)
- [浏览器缓存原理](https://www.cnblogs.com/wangpenghui522/p/5498427.html)
- [浏览器HTTP协议缓存机制详解](https://my.oschina.net/leejun2005/blog/369148)
- [从Chrome源码看HTTP](https://zhuanlan.zhihu.com/p/33846206)
- [缓存（二）——浏览器缓存机制：强缓存、协商缓存](https://github.com/amandakelake/blog/issues/41)
- [你应该知道的前端——缓存](https://juejin.im/post/5ae081aaf265da0b767d263a)
- [前端必须要懂的浏览器缓存机制](https://juejin.im/entry/5ae36365f265da0b886d28c2?utm_source=gold_browser_extension)
- [前端必须要懂的浏览器缓存机制](https://github.com/zuopf769/notebook/blob/master/fe/%E5%89%8D%E7%AB%AF%E5%BF%85%E9%A1%BB%E8%A6%81%E6%87%82%E7%9A%84%E6%B5%8F%E8%A7%88%E5%99%A8%E7%BC%93%E5%AD%98%E6%9C%BA%E5%88%B6/README.md)
- [浏览器缓存知识小结及应用](http://www.cnblogs.com/lyzg/p/5125934.html)
- [http爆炸重点学习——缓存](http://www.yzmspirit.com/2017/03/28/http%E7%88%86%E7%82%B8%E9%87%8D%E7%82%B9%E5%AD%A6%E4%B9%A0%E2%80%94%E2%80%94%E7%BC%93%E5%AD%98/)
- [浏览器缓存机制剖析](https://juejin.im/post/58eacff90ce4630058668257)
- [关于浏览器缓存我知道多少](https://juejin.im/post/59f9b68f51882578da0d313b)
- [关于浏览器缓存我知道多少](https://github.com/axuebin/articles/issues/10)
- [浏览器端的九种缓存机制介绍](http://www.techweb.com.cn/network/system/2016-01-05/2252395.shtml)