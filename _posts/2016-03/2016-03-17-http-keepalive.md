---
layout: post
title: "http keep-alive 和 tcp keep-alive"
categories: [HTTP,TCP]
tags: [http,tcp,keep-alive]
---



HTTP的keep-alive表示需要在同一个TCP连接中进行多次请求（比如说几个图片），更少的TCP连接创建和关闭。

TCP的keep-alive表示检测当前TCP连接状态的配置，看看当前连接还活着没有。

两者同名，但没有任何关系。不存在说HTTP的keep-alive就是使用底层TCP的keep-alive的说法。因为没有HTTP，TCP照样使用keep-alive。

### 参考资料

1. [HTTP长连接和短连接原理浅析](http://www.codeceo.com/article/http-long-connect.html)
2. [ 误人子弟的网络，谈谈HTTP协议中的短轮询、长轮询、长连接和短连接](http://www.mamicode.com/info-detail-1279085.html) 
3. [keep-alive wiki](https://en.wikipedia.org/wiki/Keepalive)
4. [HTTP persistent connection](https://en.wikipedia.org/wiki/HTTP_persistent_connection)
5. [HTTP Keep-Alive是什么？如何工作？](http://www.nowamagic.net/academy/detail/23350305)
6. [HTTP的长连接和短连接](http://www.cnblogs.com/cswuyg/p/3653263.html)
7. [HTTP长连接与短连接](http://www.cnblogs.com/sunada2005/p/3304593.html)
8. [TCP 保活](http://www.vants.org/?post=162)
9. [TCP连接 保持 保活 ](http://blog.csdn.net/yangruibao/article/details/8776515)
10. [随手记之TCP Keepalive笔记](http://www.blogjava.net/yongboy/archive/2015/04/14/424413.html) 
11. [TCP Keepalive HOWTOTCP Keepalive HOWTO](http://www.tldp.org/HOWTO/html_single/TCP-Keepalive-HOWTO/)
12. [RFC 1122 tcp keep-alive](https://tools.ietf.org/html/rfc1122#section-4.2.3.6)
13. [Relation between HTTP Keep Alive duration and TCP timeout duration](http://stackoverflow.com/questions/2735883/relation-between-http-keep-alive-duration-and-tcp-timeout-duration)