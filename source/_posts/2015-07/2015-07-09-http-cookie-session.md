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

## 1 事出有因

### 1.1 HTTP无状态
最初的互联网上只有带有超链接的文本文件，没有图片、视频、音频、动画等媒体。一次请求就返回一个文件的内容，再次请求要么还是这个文件的内容（刷新），要么返回新的文件的内容（点击超链接）。
在这种情况下，一次请求，建立TCP（HTTP的低层协议）连接后，一次交互，获取内容，自然而然。客户端和服务器端的交互都是一次性的，不需要知道双方是否交互过。    

这种情况就叫做HTTP的无状态，每次请求是独立的，不知道双方交互的状态。

### 1.2 HTTP得有状态
随着互联网的发展，多媒体也出现在了网页中。这时候打开一个页面，除了需要获取原本的文本内容（HTML源码）外，还需要解析并加载页面中的多媒体元素，如图片，音视频等。这样一次请求之后，还会发送加载页内其他内容的请求，还会有很多次交互。    
举一个例子，我们需要从某个地方拿东西，以前的东西呢，一趟一次性可以拿完，每次去打暗号，过安检等一系列操作完成之后拿东西走人。    
现在不一样了，东西一次性可能拿不完了，第一趟去拿出来一个清单和轻的东西，同样打暗号过安检（TCP三次握手），拿完走人（TCP四次挥手）。然后按照这个清单里面的列表，在一次一次的来拿清单中还没拿的东西。比如拿一个图片还好，大不了再来一次暗号，安检。然而拿重的东西就比较麻烦了，比如加载一个视频，一次拿一点，暗号安检,，再一次拿一点，暗号安检。。。。。如此往复。拿东西的人就疯了。    

搬东西的例子估计还好，毕竟都是后台自动完成交互的过程，如果涉及到用户操作，那么全世界都会疯了。比如，你打开淘宝，需要登陆；登入之后，点击个人中心，又需要登陆；点击地址管理，还需要登陆。。。。；就这样，你继续，不管你疯没疯，我反正快疯了。

一次暗号安检就是TCP的三次握手的过程，需要三次网络传输（Client-去-回-去-Server），HTTP的Keep-Alive(HTTP长连接)请求头就是解决拿同一个东西多次暗号安检的问题。

## 2 时代的要求
上面我们知道了HTTP必须知道当前情况才行，所以就出现了两个帮助HTTP记录状态的东东，一个工作在服务端（Session），一个工作在客户端（Cookie）。

### 2.1 Session
Session是服务端用来保存客户端状态信息的对象，是一个动态的概念，数据放在内存里，按需不持久化，也有放数据库的。 

#### 2.1.1 第一次
客户端向服务器端发送一个请求（打开并登陆淘宝）（会自动带上Cookie，这时候什么都没有）,服务端先检查请求Cookie中是否带有标志信息（一般为SESSION_ID），如果没有就把当前Session的ID（全局唯一标志）放入响应头，让客户端把这个放到Cookie里面。这个标志只是说明请求过。跟是否登陆并没有直接关系。还需要登录后，再记录这个SESSION_ID对应的客户端已经登陆，这样就能在已经登陆的情况下，不需要烦人的登陆了。

#### 2.1.2 非第一次
如果请求中Cookie中带有标志信息，则检查带来的是否与当前Session的ID一致，不一致自然是无效的。如果一致，再看看请求Cookie是否过期，是否是当前域等等。一系列的检查都通过后，就可以开开心心的返回内容了。要啥给啥，老熟人了。这会如果是已经登陆的状态，以后点击个人中心，地址管理等就不需要一次次的登陆了。 

这样服务端就能记录客户端的状态了。

### 2.2 Cookie
Cookie是客户端用户记录和存储与服务器的交互状态的机制，以前的存储方式基本是一个小的文本文件，放在每个浏览器各自的目录。现在Chrome用SQLite。各个版本的浏览器存储方式和存储路径页不一定一样，这里就不贴了。
不管怎样，只要客户端能将这些交互信息存起来，并在我们需要用的时候能拿得到就行。

#### 2.2.1 第一次
第一次请求的时候空手套白狼，请求Cookie中什么都没有，请求回来服务器响应中就带了信息了（Set-Cookie），客户端只能知道这个SESSION_ID，不记录登陆状态。以后每次请求就带上这些Cookie信息。

#### 2.2.2 非第一次
客户端每次请求带上Cookie信息，服务端会根据这个SESSION_ID，来判断当前客户端的交互状态，是否是合法的ID，是否已经登陆等等。

### 2.3 其他用法
上面说服务端可以在响应中向客户端存入信息到Cookie，Cookie的信息也会在请求中把Cookie带给服务端。 所以Cookie可以作为服务端和客户端数据传输的一种方式。    

### 2.4 一些限制

#### 2.4.1 客户端限制
上面我们说到了可以使用Cookie做数据传输。但是，但是，浏览器（客户端）对Cookie是有限制的，一方面是Cookie个数的限制（曾经有收IE6 20个，现代浏览器基本是50个），另一方面是单个Cookie内容大小的限制（一般为4K）。    
一方面可能回存在兼容性的问题，另一方面每次网络请求都会把这些信息带去带回，流量和性能也会受到影响。所以不建议使用较多的Cookie和使用Cookie传输较多的数据，具体可以按需权衡。

#### 2.4.2 安全限制
浏览器安全限制有一个同源策略，简单的说一个站点的资源URL的（协议，域名，端口）这个三元组组成的属性跟另一个站点的资源要完全一样，才认为这两个资源同源。同源的资源才被允许直接相互访问（不包括跨域方式）。

+ 没有会怎样？    
如果没有同源策略，不如你登陆了淘宝，登陆了工商银行等等。当你无意间打开一个恶意站点或者是被恶意代码注入的站点（比如被挂了网页木马），这段代码假设就是简单的加载一个JavaScript文件（当然是自动执行），然后读取你机子上的（你所有访问过的站点）Cookie，简单的通过隐藏Iframe表单提交的方式发送到指定的远程主机。这样拿到这些信息的人都可以像你一样用你的认证信息，看你的东西（你已经登陆）。

+ 限制了是什么样？    
简单的说，不同浏览器同时访问同一站点，Cookie不共享（废话，放的都不是同一个地方，用的不是同一个Cookie，可能内容相同）。    
统一浏览器的多个Tab对于同一站点来说是共享的，比如一个Tab登陆了，同时打开另一个Tab，查看其它的信息，同样是登陆状态。    
即使同一个浏览器，两个Tab不同站点，Cookie是不一样的（这也是废话）。    






## 3 参考资料
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