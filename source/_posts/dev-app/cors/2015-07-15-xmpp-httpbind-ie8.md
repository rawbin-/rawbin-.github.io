---
layout: post
title: "XMPP HTTP bind ie8 session cookie"
categories: [Web开发,前端开发]
tags: [XMPP, HTTP-BIND, IE8, Cookie, Session, P3P]

---

## 1 问题描述

### 1.1 场景
一个简单的单点登录的场景，客户端需要访问`app.test.com`这个域，而app这个域要求客户端经过`auth.test.com`的认证，那么就需要向auth域发送一个认证请求，并在认证通过后设置认证相关信息的cookie，这个cookie需要是app域的，因为在访问app域的时候需要带上。浏览器的隐私策略可能在访问app这个域的时候，不会带上刚刚设置的这个认证信息的cookie[参考](https://code.google.com/p/browsersec/wiki/Part2#Third-party_cookie_rules), 甚至可以不执行这个认证cookie的设置（这个可以在浏览器的隐私策略中配置）。

当然问题类似但不限于这个，比如

+ [cookie带不上](http://rothmanshore.com/2012/10/04/solved-browser-cookies-vanish-in-ie8-when-opening-a-new-window-to-a-different-sub-domain/)
+ [又一个cookie带不上](http://stackoverflow.com/questions/1324181/ie8-losing-session-cookies-in-popup-windows)
+ [还有这个](http://stackoverflow.com/questions/179260/ie8-loses-cookies-when-opening-a-new-window-after-a-redirect)
+ [Iframe也出问题](https://kiranpatils.wordpress.com/2010/04/13/session-lost-in-iframe/) 
+ 等等

### 1.2 原因分析
当然，这个问题主要是针对IE啦，谁让微软浏览器出那么早呢。同时目前还没看到通过编程的方式自动降低IE隐私策略的方法，即使有也难以登堂，因为用户不能同意；同时我们也不能以编程的方式直接将咱们的域名加入浏览器的信任站点列表。由于浏览器安全的要求，注定这样的方法不可能存在。

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

从上面可以看出，`第三方Cookie`总是打击对象，而我们的认证信息Cookie就属于第三方。为了避免受打击，我们需要一些办法来保护我们的认证信息免受打击。第三方Cookie简言之就是非目的站点的Cookie，比如场景中我们访问app.test.com 那么auth.test.com 设置的Cookie就是第三方Cookie。

什么是“什么是第三方Cookie”参考：[这里](http://blog.csdn.net/zgmzyr/article/details/5458508)，[这里](http://blog.163.com/web_analyst/blog/static/20215206320122275419664/)，[这里](http://www.chinawebanalytics.cn/%E6%8D%8D%E5%8D%ABcookie%E2%80%94%E2%80%94%E6%B2%A1%E6%9C%89cookie%EF%BC%8C%E6%88%91%E4%BB%AC%E4%BB%80%E4%B9%88%E9%83%BD%E6%B2%A1%E6%9C%89%E4%BA%86/)。

### 1.3 解决办法

#### 1.3.1 惹不起躲得起

这是一个简单粗暴却行之有效的解决办法，使用cookie会有问题，那么我就不用cookie。比如在使用认证的时候原来是通过HTTP头Set-Cookie的方式保存认证信息到客户端，现在直接不用cookie，将原来要放入cookie的信息直接在请求（一般为跨域请求）中返回。这样我们就拿到认证信息了，再接下来的请求中直接以参数传递的形式带上，怎么着都不会存在这个第三方cookie的问题了。我们可以在dom或者本地存储等任何地方放起来，每次需要这个信息的时候再取出来放入参数中就行，避免了cookie的使用和频繁调用认证请求。

#### 1.3.2 直面惨淡人生

一切根源都是在浏览器中，浏览器的问题就是大家都要面对的问题，这样就会有很多成型的解决方案。传言早在IE6 横行霸道以前，聪明的先驱就已经提出了成熟的解决方案P3P（Platform for Privacy Preference），并且IE6是最早支持这个W3C标准的。这个自然了，既然IE6都支持了，那咱们现代浏览器还说啥。

P3P标准要求站点明确声明需要搜集哪些隐私信息和这些被搜集的隐私信息的用途，一般浏览器在解析时看到这个声明，就不会出现类似上面的问题了，浏览器就是好骗哈。
    
## 2 相关知识

### 2.1 XMPP
XMPP(Extensible Messaging and Prensence Protocal是一个网络即时通讯协议，它是基于TCP/IP来传输XML格式的文本。消息的XML内容中用一些特定的标志表示了消息的属性（从哪儿来，发哪儿去，谁发的，内容是什么等）。

### 2.2 浏览器安全模型
IE 的安全模型（[IE内容分区模型](https://code.google.com/p/browsersec/wiki/Part3#Microsoft_Internet_Explorer_zone_model)）会将内容区分对待，并可能根据内容的可信度（[IE的不同安全等级](http://blogs.msdn.com/b/ieinternals/archive/2011/03/10/internet-explorer-beware-cookie-sharing-in-cross-zone-scenarios.aspx)）区分对待其中的Cookie，并且在没有明确指定P3P信息的时候，默认阻止第三方cookie的发送（[第三方Cookie的限制](https://code.google.com/p/browsersec/wiki/Part2#Third-party_cookie_rules)
）。

### 2.3 P3P
P3P（Platform for Privacy Preference）是一个在线隐身保护的一个W3C标准，大致内容是对互联网访问时涉及到的隐私的一些约定：

#### 2.3.1 隐私信息的收集
+ 收集哪些信息，
+ 为什么收集这些信息
+ 收集的这些信息会给谁用，
+ 收集的这些信息会被保留多久

#### 2.3.2 隐私信息的使用
+ 对信息进行了十几个分类，比如个人指纹信息，计算机的操作系统和版本信息，地理位置信息等
+ 对信息的用途进行十来个分类，比如当前应用，管理，开发等
+ 对信息的使用者进行了分类，比如站点本身，公共使用，物流等。
+ 对信息保留的时限进行了分类，如会话期间，无限期保留等。

有了上面的约定，我们就可以在客户端（浏览器端）和站点（服务端）对隐私信息的收集和使用做一个限制。将哪些信息可以被用来做什么做一个明确的限制。当然这是建立在服务端受信的基础上的，服务端声明它可以遵守这些声明和约定，客户端用户相信这个声明，但具体服务端怎么做，就要看情况了。

#### 2.3.3 P3P策略配置
策略配置有两种方法，一种是通过配置文件的方法，这种方法会多两次请求。另外一种是直接添加HTTP Header的方式，这个简单高效，更适合我们的实验。


## 3 实例演练（使用P3P）

### 3.1 环境配置
修改本机host（屌丝必备技能哈）
127.0.0.1 auth.test.com
127.0.0.1 app.test.com

### 3.2 场景说明
两台机子，一台认证中心（auth.test.com），一台应用中心（app.test.com），访问应用中心的时候需要先向认证中心进行认证获取认证信息Cookie后，再拿这个认证信息去访问应用中心。我们就看看Cookie能不能带过去。

认证中心上有一个认证接口，做认证并设置响应的认证信息Cookie。

应用中心就一个页面，看看访问页面的时候能不能带Cookie过去。

### 3.3 其他说明
这个发不出Cookie的场景在我这难以直接重现，于是通过修改浏览器隐私设置的方式进行重现。
将IE浏览器的隐私策略级别设为“高”，这时能够获取到auth.test.com中的cookie，可以直接用fiddler等工具抓包，或者在浏览器的开发工具中的网络标签中查看。但在访问app.test.com的时候却无法把Cookie带过去。 

如果将IE浏览器的隐私策略级别设为“中高”及以下，可以在本地存在cookie的情况下，访问app.test.com时中带过去。


### 3.4 上代码

#### 3.4.1 auth.test.com

起一个认证的服务，由于80被app.test.com 占用，这里使用9000端口。 直接使用`node auth-server.js` 运行。

    //auth-server.js
    var http = require('http');
    var server = http.createServer(function(request,response){
        console.log(request.url)
        if(/^\/login.action.*/.test(request.url)){
            console.log('setting cookie...')
            //response.setHeader('Set-Cookie',['logined=true;path=/;domain=.test.com;HttpOnly;','sessionid=session_123456;path=/;domain=.test.com;HttpOnly;'])
            response.setHeader('Set-Cookie',['logined=true;path=/;domain=.test.com;','sessionid=session_123456;path=/;domain=.test.com;'])
        }
        //response.setHeader('P3P','CP=CAO PSA OUR')
        response.writeHead(200,{});
        response.end();
    }).listen(9000);


#### 3.4.2 app.test.com

放置一个页面，进行认证和访问。

    <!doctype html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Document</title>
        <script type="text/javascript" src="jquery-1.7.2.js"></script>
        <script type="text/javascript" src="jquery.cookie.js"></script>
    </head>
    <body>
        <script type="text/javascript">
            var authUrl = "http://auth.test.com:9000/login.action?callback=?"
            if(!$.cookie('logined')){
                $.ajax({
                    url:authUrl,
                    dataType:'jsonp'
                }).done(function(){
                    window.console && console.log('login.action successed')
                }).fail(function(){
                    window.console && console.log('login.action failed')
                }).always(function(){
                    window.location.reload();
                })
            }else{
                window.console && console.log('using the exists info');
            }
            window.console && console.log("login status:",$.cookie('logined'))
            window.console && console.log("session info:",$.cookie('sessionid'))
        </script>
    </body>
    </html>

### 3.5 玩起来

将上面的代码部署起来，浏览器访问类似这样的地址`http://app.test.com/tests/cross-origin-cookie/app.html`

#### 3.5.1 可以获取认证的cookie，却无法将cookie传递到app.test.com。
+ 将浏览器的隐私策略设置为“高”
+ 直接将上面的代码部署 

这样在IE8-11中再访问app.test.com 的这几个资源的时候，请求头中都不会存在认证cookie信息。

#### 3.5.2 可以获取认证的cookie，同时可以将cookie传递到app.test.com
+ 将浏览器的隐私策略设置为“高”
+ 直接将上面的代码部署
+ 去掉认证中心中关于P3P header的注释

这样在IE8-11中再访问app.test.com 的这几个资源的时候，请求头中都会存在认证cookie信息。


#### 3.5.3 一般怎么都能行
+ 将浏览器的隐私策略设置为“中高”及以下
+ 不管是否有P3P的Header

这样在IE8-11中再访问app.test.com 的这几个资源的时候，请求头中都会存在认证cookie信息。


## 4 总结
+ 我们可以通过编程的方式，使用P3P规范，满足浏览器的隐私策略要求，从而解决第三方cookie共享的问题。
+ 推荐使用P3P的方式，设置HttpOnly的cookie来共享认证信息，这样更加规范。
+ 不推荐使用传递信息的方式，这样有些反人类，每次开发都会写一段处理信息的代码，而不是简单地发一个请求。

## 5 引申
如果是完全不同的域，比如是auth.testauth.com,和app.testapp.com，这样主域不相同的话。需要在认证成功之后，同时调用app.testapp.com下的一个请求，并将cookie信息传过去。这样就可以通过app.testapp.com域下的请求设置本域的cookie，然后再请求app.testapp.com下的页面就ok。 这里也是需要设置P3P头的，这里的原则是，谁设置第三方的cookie，谁就设置这个P3P头就行了。

P3P的内容远比这里看到的丰富的多，详情参考[W3C P3P](http://www.w3.org/TR/P3P/)

### 5.1 参考资料
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
0. [W3C P3P](http://www.w3.org/TR/P3P/)
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
0. [cookie完全跨域 - 注意事项（header: P3P...）  ](http://kenchell.blog.163.com/blog/static/26088309201131343153313/)
0. [关于PHP的header("P3P: CP=CURa……")](http://www.cnblogs.com/ccdc/archive/2012/05/08/2489535.html)
0. [Browser Security Handbook](https://code.google.com/p/browsersec/wiki/Main)
0. [Same-origin policy limitations](https://code.google.com/p/browsersec/wiki/Part2)
0. [《Web Site Privacy With P3P》]()

