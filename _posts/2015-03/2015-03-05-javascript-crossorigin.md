---
layout: post
title: "JavaScript 跨域问题 浏览器 同源策略"
categories: [开发技术]
tags: [跨域,同源策略,CORS,Same Origin Policy,浏览器安全]
---

[RFC6454](http://tools.ietf.org/html/rfc6454  "rfc6454")

网络访问部分：

一般来说，禁止一个域从另一个域读取数据，然而，有些资源是允许的。比如说，允许一个域执行、渲染、应用从其他域获取到的脚本、图片、样式；同样，一个域可以展示从其他域获取的内容，比如在frame中显示html文档。网络资源也可以选择让其他域来读取自己的信息，比如使用Cross-Origin Resource Sharing，这种情况下访问权是单个域获取的。

同源策略允许向另一个域发送数据。但是，在网络上发送任意格式的数据是危险的。正因为如此，用户代理限制文档用指定的协议发送信息，比如说不带用户自定义头的HTTP请求。

广义的同源策略，允许一个域向另一个域发送信息，禁止一个域从另一个域接收信息。禁止接收信息是为了防止恶意站点读取其他站点的机密信息，同时防止正当读取其他站点提供的信息(比如你正在解决的跨域问题^_^)。

在同源策略下，跨站发送信息同样是有危害的，因为它可以被用来做类似CSRF（跨站请求伪造）的攻击。同源策略不能像控制接收信息那样直接禁止发送信息，因为禁止发送信息同时也会禁止跨站超链接。不允许发送信息的话，就没有超链接，每个源只能链接到自己的站点，也就没有了互联网。

同源策略限制消息从一个域发送到另一个域。比如说同源策略允许域间的GET和POST方式的HTTP请求，却禁止域间的PUT和DELETE方式的请求。同时，域在发送请求到自己时可以自定义HTTP请求头，发送请求到其他域不能自定义请求头。

限制读取从其他域接收到的信息有点微妙。比如，HTML&lt;script&gt;元素可以执行从其他域获取的信息，这就意味着站点不应该依赖同源策略来保护类脚本解析形式（比如，JavaScript源代码，JSON 格式，JSONP服务等）的信息的机密性。


同源策略内容，对于A，B两个域：

+ 允许A域执行来自B域的资源
+ 限制A域对B域进行写操作
+ 禁止A域读取B域的资源

同源策略限制的是一个域加载的脚本去获取或操作另一个域上的文档属性。
同源策略的控制者是浏览器，浏览器可以控制不同域之间的资源的访问或相互操作，但不控制自己对不同域之间的资源的操作和访问。
在浏览器中，&lt;script&gt;、&lt;img&gt;、&lt;iframe&gt;、&lt;link&gt;等标签都可以加载跨域资源，而不受同源限制，但浏览器限制了JavaScript的权限使其不能读、写加载的内容。
比如打开test.com/index.html的首页，首页可能加载了a.com，b.net下的各种资源，比如说a.com/a.js，b.net/b.js。 浏览器会把跨域的外部资源全部给index.html，这种情况是允许的。
同源策略只对网页的HTML文档做了限制，对加载的其他静态资源如javascript、css、图片等仍然认为属于同源。


同源策略应用于XMLHttpRequest生成的HTTP请求，允许客户端JavaScript生成任意的HTTP请求到所属文档的Web服务器，但不允许脚本和其他Web服务器通信。

同源策略规则：

+ 脚本本身的来源与同源策略无关，相关的是脚本所嵌入的文档的来源。
  假设一个来自主机A的脚本被包含到（使用&lt;script&gt;标记的src属性）宿主B的一个Web页面中。这个脚本的来源是主机B，并且可以完整地访问包含它的文档的内容。如果脚本打开一个新窗口并载入来自主机B的另一个文档，脚本对这个文档的内容也具有完全的访问权限（同源）。如果脚本打开一个新窗口并载入来自主机C的文档（主机B和主机C不同源），则同源策略会阻止脚本访问这个文档。

不严格的同源策略：

+ document.domain
不同子域名之间的同源策略，如a.test.com与b.test.com之间的访问，可以使用Document对象的domain属性，这是一个可写的属性，可以将其设为test.com来实现子域名之间的不受同源策略约束的资源共享。

+ Cross-Origin Resource Sharing
是W3C提出的方式，用新定义的"Origin"请求头和"Access-Control-Allow-Origin"相应头来扩展HTTP协议。它允许服务器用头信息显式的列出源或使用通配符*来匹配所有的源并允许由任何地址请求文件。这样可以解除同源策略对XMLHttpRequest的限制。

+ 跨文档消息
  允许来自一个文档的脚本发消息到另一个文档里的脚本，而不管脚本的来源是否不同，调用Window对象的postMessage()方法，可以异步传递消息事件（可以用onmessage事件句处理程序函数来处理它）到窗口的文档里。一个文档里的脚本还是不能调用其他文档里的方法和读取属性，但他们可以通过这种消息传递技术来实现安全的通信。

### 参考资料
0. [Same Origin Policy-W3C][12]
1. [JavaScript的同源策略][3]
0. [Same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)
0. [Browser Security Handbook, part 2](https://code.google.com/p/browsersec/wiki/Part2)
1. 0. [Google 同源策略及跨域方法](https://code.google.com/p/browsersec/wiki/Part2)
0. [Same Origin Policy-0][11]
1. [Same Origin Policy-1][9]
2. [Same Origin Policy-2][10]
0. [JavaScript同源策略][7]
0. [同源策略][0]
0. [浅谈跨域][2]
2. [JavaScript同源策略][4]
2. [[CORS：跨域资源共享] W3C的CORS Specification][5]
3. [[CORS：跨域资源共享] 同源策略与JSONP][1]
4. [同源策略和跨域访问][6]
5. [深入浅出JSONP--解决ajax跨域问题][8]
6. [jQuery跨域请求，跨域Post提交数据的方法][13]
7. [简单描述JSON跟JSONP的区别以及实战][15]
7. [JavaScript系列----AJAX机制详解以及跨域通信][14]
8. [详解js跨域问题][16]
9. [优雅绝妙的Javascript跨域问题解决方案][17]
10. [跨域方法汇总][18]
11. [HTML5安全：CORS（跨域资源共享）简介][19]
12. [js中几种实用的跨域方法原理详解][20]
13. [Javascript跨域访问解决方案][21]




[0]: http://www.cnblogs.com/dsky/archive/2012/04/06/2434010.html "同源策略"
[1]: http://www.cnblogs.com/artech/p/cors-4-asp-net-web-api-01.html "[CORS：跨域资源共享] 同源策略与JSONP"
[2]: http://targetkiller.net/cross-domain/ "浅谈跨域"
[3]: https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy "JavaScript的同源策略"
[4]: http://blog.chinaunix.net/uid-20737871-id-4458460.html "JavaScript同源策略"
[5]: http://www.cnblogs.com/artech/p/cors-4-asp-net-web-api-02.html "[CORS：跨域资源共享] W3C的CORS Specification"
[6]: http://blog.csdn.net/shimiso/article/details/21830313 "同源策略和跨域访问"
[7]: http://www.zfanw.com/blog/javascript-same-origin-policy.html "JavaScript同源策略"
[8]: http://www.cnblogs.com/chopper/archive/2012/03/24/2403945.html "深入浅出JSONP--解决ajax跨域问题"
[9]: http://blogs.msdn.com/b/ieinternals/archive/2009/08/28/explaining-same-origin-policy-part-1-deny-read.aspx "Same Origin Policy Part 1: No Peeking"
[10]: http://blogs.msdn.com/b/ieinternals/archive/2012/04/03/explaining-same-origin-policy-part-2-limited-write.aspx "Same Origin Policy Part 2: Limited Write"
[11]: http://blogs.msdn.com/b/ieinternals/archive/2014/03/13/explaining-same-origin-policy-part-0-origins.aspx "Same Origin Policy Part 0: Origins"
[12]: http://www.w3.org/Security/wiki/Same_Origin_Policy "Same Origin Policy W3C"
[13]: http://www.tuicool.com/articles/7ve6ja "jQuery跨域请求，跨域Post提交数据的方法"
[14]: http://www.cnblogs.com/renlong0602/p/4414872.html "JavaScript系列----AJAX机制详解以及跨域通信"
[15]: http://www.qixing318.com/article/simply-describe-the-difference-between-json-with-json-as-well-as-the-actual-combat.html "简单描述JSON跟JSONP的区别以及实战"
[16]: http://segmentfault.com/a/1190000000718840 "详解js跨域问题"
[17]: http://blog.csdn.net/sfdev/article/details/5807045 "优雅绝妙的Javascript跨域问题解决方案"
[18]: http://www.raychase.net/2216 "跨域方法汇总"
[19]: http://blog.csdn.net/hfahe/article/details/7730944 " HTML5安全：CORS（跨域资源共享）简介"
[20]: http://www.cnblogs.com/2050/p/3191744.html "js中几种实用的跨域方法原理详解"
[21]: http://blog.csdn.net/sfdev/article/details/3887006 "Javascript跨域访问解决方案"
[22]: http://www.cnblogs.com/JChen666/p/3399951.html "前端解决跨域问题的8种方案（最新最全）"