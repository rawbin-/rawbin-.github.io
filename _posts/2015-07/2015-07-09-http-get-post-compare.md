--- 
layout: post
title: "HTTP GET POST 对比 区别"
categories: [计算机基础,计算机体系结构,计算机网络,HTTP]
tags: [HTTP,GET,POST]

---


### GET和POST对比

#### 相同点
+ 都是HTTP的请求方法，都能携带数据

#### 不同点
+ 职责    
在HTTP的定义中，GET被用来做只读信息请求，POST用来写数据（这是HTTP协议对协议实现的建议），后来的REST风格也是基于这样的建议来表达的。     
但这样的建议不是一个强制的要求，所以现有HTTP的使用也是各式各样，使用GET同时做读写数据也行，完全是协议使用者的习惯。

+ 表现形式     
GET方式的请求会将参数拼接在URL中，所以能很容易的在浏览器地址栏中看到请求的数据。而POST数据则完全是放在HTTP的请求体中，无法直观的看到。有说法是因为如此，两者存在安全性的差别，其实不然；同样的请求，只要数据不加密，他们的安全性是一样的，都是不安全的。随便用一个浏览器调试工具，或者网络抓包工具都可以看到完整的请求数据；差别在于多一步操作而已，所以无关乎安全，仅仅是一个能直接看到，一个直接看不到，表现形式不同而已。

+ 可重现性      
如上，GET请求的参数会被拼接在URL中，所以你可以直接将这样的请求拷贝到任何其他地方，比如给小伙伴测试，或者将这个地址收藏起来。POST请求则不行，你需要专门的工具或者写专门的代码来做这事，才能复现相同的请求。

+ 传输数据大小    
还是如上，由于GET参数会被拼接到URL中，对于浏览器来说，解析这个URL会有压力，所以会有长度限制（隐含的数据大小限制）；同时，服务器也有这样的麻烦。这不是HTTP协议本身对GET方法的限制，而是具体的应用的限制，如浏览器，服务端软件等。

+ 浏览器缓存和历史记录     
再次如上，由于GET参数会被拼接到URL中，浏览器会把这整个被拼接的URL当做是一个页面，并对请求结果做缓存，以减少重复解析的开销。顺势，如果浏览器请求一个页面，自然会放到历史记录里面，所以缓存和历史记录在POST请求中都没有。



### 参考资料
0. [HTTP 方法：GET 对比 POST](http://www.w3school.com.cn/tags/html_ref_httpmethods.asp)
0. [浅谈HTTP中Get与Post的区别](http://www.cnblogs.com/hyddd/archive/2009/03/31/1426026.html)
0. [GET和POST有什么区别？](http://kb.cnblogs.com/page/188928/)
0. [也谈 GET 和 POST 的区别](http://www.cnblogs.com/ldp615/archive/2012/07/27/http-get-post.html)
0. [详解HTTP中GET和POST的区别](http://www.jellythink.com/archives/806)
0. [GET和POST有什么区别？及为什么网上的多数答案都是错的。](http://www.cnblogs.com/nankezhishi/archive/2012/06/09/getandpost.html)
0. [HTTP POST GET 本质区别详解](http://blog.csdn.net/gideal_wang/article/details/4316691)
0. [不再以讹传讹，GET和POST的真正区别](http://www.nowamagic.net/librarys/veda/detail/1919)
0. [关于GET和POST请求](http://www.cnblogs.com/xyang/archive/2012/07/06/2579383.html)
0. [HTTP协议及其POST与GET操作差异 & C#中如何使用POST、GET等](http://www.cnblogs.com/skynet/archive/2010/05/18/1738301.html)