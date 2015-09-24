---
layout: post
title: "XMPP HTTP bind ie8 session cookie"
categories: [Web开发,前端开发]
tags: [XMPP, HTTP-BIND, IE8, Cookie, Session, P3P]

---

## 问题描述

### 场景
一个简单的单点登录的场景，客户端需要访问`app.test.com`这个域，而app这个域要求客户端经过`auth.test.com`的认证，那么就需要向auth域发送一个认证请求，并在认证通过后设置认证相关信息的cookie，这个cookie需要是app域的，因为在访问app域的时候需要带上。浏览器的隐私策略可能在访问app这个域的时候，不会带上刚刚设置的这个认证信息的cookie[参考](https://code.google.com/p/browsersec/wiki/Part2#Third-party_cookie_rules), 甚至可以不执行这个认证cookie的设置（这个可以在浏览器的隐私策略中配置）。

### 原因分析
当然，这个问题主要是针对IE啦，谁让微软浏览器出那么早呢。同时目前还没看到通过编程的方式自动降低IE隐私策略的方法，即使有也难以登堂，因为用户不能同意。同时由于浏览器安全的要求，注定这样的方法不可能存在。

先来看看IE几级隐私策略（从高到地，从严格到宽松，IE11，不同版本可能不一样）：
+ 阻止所有Cookie
    + 阻止来自所有网站的所有Cookie
    + 该计算机上已有的Cookie不能被网站读取
+ 高
    + 阻止来自没有精简隐私策略的网站的所有Cookie
    + 阻止没有经你明确同意就保存你的联系信息的第三方Cookie
+ 中高
    + 阻止没有精简策略的第三方Cookie
    + 阻止没有经你明确同意就保存你的联系信息的第三方Cookie
    + 阻止没有经你默许就保存你的联系信息的第三方Cookie
+ 中
    + 阻止没有精简策略的第三方Cookie
    + 阻止没有经你明确同意就保存你的联系信息的第三方Cookie
    + 限制没有经你默许就保存你的联系信息的第三方Cookie
+ 低                
    + 阻止没有精简策略的第三方Cookie
    + 限制没有经你默许就保存你的联系信息的第三方Cookie
+ 授权所有Cookie
    + 保存来自任何网站的Cookie
    + 该计算机上已有的Cookie可被创建他们的网站获取    

从上面可以看出，`第三方Cookie`总是打击对象，而我们的认证信息Cookie就属于第三方。为了避免受打击，我们需要一些办法来保护我们的认证信息免受打击。

### 解决办法

#### 惹不起躲得起
这是一个简单粗暴的解决办法，  
    
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

