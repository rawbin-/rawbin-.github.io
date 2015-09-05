---
layout: post
title: "HTTP cookie session详解和对比"
categories: [计算机基础,计算机体系结构,计算机网络,HTTP]
tags: [HTTP, cookie, session]

---




> Web服务器对应的客户端（浏览器）与服务器之间的数据交换的间歇性较大（即数据传输具有突发性，瞬时性），并且网络访问之间是相对独立的（关联性较低）。    
> 请求时建连接、请求完释放连接，以尽快将资源释放出来。    
> Keep-Alive解决每次访问同一资源都建立TCP连接的问题。    
> 无状态是指服务器不知道客户端是什么状态。    
> HTTP无状态的特性严重阻碍了这些交互式应用程序的实现。    
> 两种用于保持HTTP状态的技术就应运而生了，一个是Cookie，而另一个则是Session。    





## 参考资料
0. [http协议无状态含义](http://blog.csdn.net/bingjing12345/article/details/9819731)
1. [http 之Session 和 Cookie](http://www.cnblogs.com/xuxm2007/archive/2011/12/05/2276705.html)
1. [浅析http协议、cookies和session机制、浏览器缓存](http://my.oschina.net/u/267858/blog/472052)
2. [Session/Cookie机制详解](http://blog.csdn.net/fangaoxin/article/details/6952954)
3. [对Session和Cookie的理解](http://www.blogjava.net/cheneyfree/archive/2007/05/26/120168.html)
4. [Cookie和Session的区别](http://www.admin10000.com/document/1034.html)
5. [Cookie和Session的比较](http://blog.163.com/lgh_2002/blog/static/44017526200711333625932/)
6. [Cookie/Session机制详解](http://qiusuoge.com/10576.html)
7. [理解Cookie和Session机制](http://my.oschina.net/xianggao/blog/395675)
7. [RFC 2109](http://www.rfc-editor.org/pdfrfc/rfc2109.txt.pdf)
8. [RFC 2965](http://www.rfc-editor.org/pdfrfc/rfc2965.txt.pdf)