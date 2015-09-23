---
layout: post
title: "XMPP HTTP bind ie8 session cookie"
categories: [Web开发,前端开发]
tags: [XMPP, HTTP-BIND, IE8, Cookie, Session]

---

## 问题描述
### 


##相关知识
### XMPP
XMPP(Extensible Messaging and Prensence Protocal是一个网络即时通讯协议，它是基于TCP/IP来传输XML格式的文本。消息的XML内容中用一些特定的标志表示了消息的属性（从哪儿来，发哪儿去，谁发的，内容是什么等）。

### 浏览器安全模型


[第三方Cookie的限制](https://code.google.com/p/browsersec/wiki/Part2#Third-party_cookie_rules)

[IE内容分区模型](https://code.google.com/p/browsersec/wiki/Part3#Microsoft_Internet_Explorer_zone_model)

### P3P
P3P（Platform for Privacy Preference）是一个在线隐身保护的一个W3C标准，大致内容是对互联网访问时涉及到的隐私的一些约定：
#### 隐私信息的收集
+ 收集哪些信息，
+ 为什么收集这些信息
+ 收集的这些信息会给谁用，
+ 收集的这些信息会被保留多久

#### 隐私信息的使用
+ 对信息进行了十几个分类，比如个人指纹信息，计算机的操作系统和版本信息，地理位置信息等
+ 对信息的用途进行十来个分类，比如当前应用，管理，开发等
+ 对信息的使用者进行了分类，比如站点本身，公共使用，物流等。
+ 对信息保留的时限进行了分类，如会话期间，无限期保留等。

有了上面的约定，我们就可以在客户端（浏览器端）和站点（服务端）对隐私信息的收集和使用做一个限制。将哪些信息可以被用来做什么做一个明确的限制。当然这是建立在服务端受信的基础上的，服务端声明它可以遵守这些声明和约定，客户端用户相信这个声明，但具体服务端怎么做，就要看情况了。






### 参考资料
0. [XMPP 官网](http://xmpp.org/)
0. [XMPP Wiki](http://wiki.xmpp.org/web/Main_Page)
0. [XMPP WikiPedia](https://en.wikipedia.org/wiki/XMPP)
0. [XMPP协议实现原理介绍](http://www.cnblogs.com/hanyonglu/archive/2012/03/04/2378956.html)
1. [XMPP协议分析-原理篇](http://blog.csdn.net/xutaozero21/article/details/4873439)
2. [RFC 6120 XMPP Core](http://tools.ietf.org/html/rfc6120)
3. [RFC 3920 XMPP Core](http://tools.ietf.org/html/rfc3920)
0. [XMPP协议及实践](http://blog.csdn.net/xn4545945/article/details/38966557)
0. [XMPP协议原理介绍](http://blog.csdn.net/wbw1985/article/details/5502272)
0. [使用XMPP构建一个Web通知工具](http://www.ibm.com/developerworks/cn/xml/tutorials/x-realtimeXMPPtut/index.html)
0. [实现可扩展消息传递和到场协议（XMPP）](http://www.ibm.com/developerworks/cn/xml/x-xmppintro/#major1)
0. [](http://stackoverflow.com/questions/2952489/ie8-blocking-javascript-cookies)
0. [Solved: browser cookies vanish in IE8 when opening a new window to a different sub-domain](http://rothmanshore.com/2012/10/04/solved-browser-cookies-vanish-in-ie8-when-opening-a-new-window-to-a-different-sub-domain/)
0. [Beware Cookie Sharing in Cross-Zone Scenarios](http://blogs.msdn.com/b/ieinternals/archive/2011/03/10/internet-explorer-beware-cookie-sharing-in-cross-zone-scenarios.aspx)
0. [IE8 losing session cookies in popup windows](http://stackoverflow.com/questions/1324181/ie8-losing-session-cookies-in-popup-windows)
0. [Seven Things You Should Known About IE 8](http://blog.httpwatch.com/2009/04/07/seven-things-you-should-known-about-ie-8/)
0. [IE8 and Reliability](http://blogs.msdn.com/b/ie/archive/2008/07/28/ie8-and-reliability.aspx)
0. [IE8 and Loosely-Coupled IE (LCIE)](http://blogs.msdn.com/b/ie/archive/2008/03/11/ie8-and-loosely-coupled-ie-lcie.aspx)
0. [How to get Internet Explorer to use cookies inside a frame to a third party site.](http://www.softwareprojects.com/resources/programming/t-how-to-get-internet-explorer-to-use-cookies-inside-1612.html)
0. [Session lost in Iframe](https://kiranpatils.wordpress.com/2010/04/13/session-lost-in-iframe/)
0. [Cookie blocked/not saved in IFRAME in Internet Explorer](http://stackoverflow.com/questions/389456/cookie-blocked-not-saved-in-iframe-in-internet-explorer)

0. [W3C P3P](http://www.w3.org/P3P/)
0. [Web Privacy with P3P](http://www.p3pbook.com/)
0. [The P3P Implementation Guide](http://p3ptoolbox.org/guide/)
0. [第三方cookie丢失解决方案-P3P](http://blog.csdn.net/lovingprince/article/details/5984449)
0. [通过P3P头实现跨域设置cookie](http://www.phpddt.com/php/p3p-header.html)
0. [PHP - 利用P3P实现跨域 ](http://sjolzy.cn/PHP-Using-P3P-to-achieve-cross-domain.html)
0. [关于p3p 简洁策略,以及浏览器的支持情况.](http://www.cnblogs.com/_franky/archive/2011/03/16/1985954.html)
0. [Cookie, iframe 与 P3P 的那点事儿](http://dbanotes.net/web/cookie_p3p.html)
0. [实现跨域cookie共享(转载)](http://www.cnblogs.com/showker/archive/2010/01/21/1653332.html)
0. [淘宝如何跨域获取Cookie分析](http://developer.51cto.com/art/201104/255729.htm)
0. [关于跨域获取cookie问题的解决](http://www.cnblogs.com/whoamme/p/3598889.html)
0. [使用 P3P 规范让 IE 跨域接受第三方 cookie](http://www.lovelucy.info/ie-accept-third-party-cookie.html)
0. [用P3P header解决iframe跨域访问cookie](http://blog.csdn.net/wonder4/article/details/2125804)

0. [Browser Security Handbook](https://code.google.com/p/browsersec/wiki/Main)
0. [Same-origin policy limitations](https://code.google.com/p/browsersec/wiki/Part2)
0. [《Web Site Privacy With P3P》]()

