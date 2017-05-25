---
layout: post
title: "JavaScript跨域方法实例详解"
categories: [开发技术, JavaScript]
tags: [前端,跨域]

---

## 同源与跨域

### 基调

一般情况下，禁止一个域从另一个域读取数据，却可以使用某些从其他域拿到的资源。比如说，允许一个域执行、渲染、应用从其他域获取到的脚本、图片、样式；同样，一个域可以展示从其他域获取的内容，比如在frame中显示html文档。网络资源也可以选择性的让其他域来读取自己的信息，比如使用Cross-Origin Resource Sharing，这种情况下访问权是针对单个域授权的。

同源策略限制消息从一个域发送到另一个域。比如说同源策略允许域间的GET和POST方式的HTTP请求，却禁止域间的PUT和DELETE方式的请求。同时，域在发送请求到自己时可以自定义HTTP请求头，发送请求到其他域不能自定义请求头。

同源策略的控制者是浏览器，浏览器可以控制不同域之间的资源的访问或相互操作，但不控制自己对不同域之间的资源的操作和访问。

### 什么是源
RFC6454 规定一个资源的源由资源的URI中的（协议，主机，端口）这一个三元组来确定（IE中没有把端口纳入源的属性）。比如 https://www.test.com/test-script.js, 这个资源的源为（https, www.test.com, 80）。

对于一个被执行的脚本来说，他的源属性是执行这个脚本的资源的源属性。比如一个页面加载了来自另一个域的脚本，这个脚本执行的时候向页面所在域发送请求被视为同源。

相对路径和无法明确源属性的协议（javascript:,data:,about:blank）的资源的源，取将这些资源载入的页面的源。

IE没有将端口作为同源的组成部分，原因是IE历史垄断的市场占有率导致的历史遗留问题，IE8尝试在原生的XMLHTTPRequest中使用端口作为同源的一部分（标准化），但效果不好，一些依照老IE特性开发的站点，为了保持兼容性，继续使用了原有的MSXML 方式的XMLHTTPRequest。所以在IE8-11的版本都没有考虑端口作为同源的判断条件。

### 怎样算同源
两个资源的组成源的属性（协议，主机，端口），完全一致，这两个资源才同源。这里如果两个资源的协议分别为http 和 https 被认为不同源。同时，即使两个资源的所属同一台服务器，但给出的域名分别为www.testA.com和www.testB.com，这两个资源也被认为是不同源。

### 同源策略
同源策略是浏览器的核心安全策略，目的是将来自不同源的资源进行隔离，并控制不同源资源间的通信，从而减少安全威胁，增强安全性。

#### 同源策略的规则

##### 不限制

互联网的核心思想是资源共享，资源的相互访问应该被允许。

+ 执行来自其他域的脚本（如使用`<script>`标签引用CDN的脚本,JSONP请求等）
+ 渲染来自其他域的图片（如使用`<img>`标签引用图片服务器的图片资源）
+ 应用来自其他域的样式（如使用`<link>`标签引用来自静态资源服务器的样式文件）
+ 嵌入来自其他域的资源（使用`<iframe>`,`<frame>`加载来自其他域的资源）
+ 重定向页面地址（Location对象地址的改变，使用`<a>`链接到其他资源）
+ 数据发送（使用`<form>`向其他域提交数据）
+ 多个子域的资源可以设置document.domain来改变所属域属性，来实现子域同域
+ window.name 属性是可写的，脚本可以随意设置，iframe的name属性，会作为iframe内部window对象的name属性初始值
+ 窗体层级嵌套 可以通过parent.parent...的方式来访问祖先窗口，不受跨域限制
+ postMessage接口跨域通信
+ `<video>`,`<audio>`,`<object>`,`<embed>`,`<applet>`,`@font-face`可以加载其他域的资源

##### 部分限制
+ 跨域发送请求不能自定义HTTP Header
+ 跨域发送请求不能使用PUT和DELETE方式，只能使用GET和POST
+ 脚本可以访问一个不同源窗口的整体，而不能访问窗口的内部信息
+ CORS可以改变跨域的情形Access-Control-Allow-Origin,同源策略不放宽，跨域请求正常工作（设置为例外），不包含用户名和密码，不包含cookie和token，，响应的cookie会被丢弃，如果需要这些信息，需要设置XMLHttpRequest的withCredentials=true

##### 完全限制
+ 限制本地文件系统读写
+ 限制cookie的访问
+ 限制FileUpload元素的value属性，不能修改，甚至不能读取路径。
+ 限制脚本对来自不同服务器的文档的读写（同源策略）
+ 限制本地存储localStorage和sessionStorage和IndexedDB
+ 限制XMLHTTPRequest请求的发送对象

### 跨域

同源策略将来自不同源的资源进行了隔离，略在阻止安全威胁的同时，也对正常的访问带来了不便，我们也需要在安全策略下根据应用的需要进行不同源资源间的通信。 比如和iframe或frame中的资源的通信，与新窗口的资源的通信，与不同域的服务器之间的通信等。

比如同源策略限制了不同源的资源之间的互操作，当页面在iframe中加载不同源的内容的时候，想要根据页面内容动态调整iframe的高度的时候，就需要特殊的跨域操作。

在应用系统中做单点登录的时候，在进行当前应用的认证的时候需要向认证中心进行认证，这个时候也需要特殊的操作。

## 跨域方法汇总

跨域的方法和浏览器安全问题都围绕着同源策略来展开，我们可以避开浏览器端的参与，从而规避同源策略带来的不便；同时我们也可以利用同源策略及其辅助接口开放的功能特性来实现跨越通信。

### 惹不起躲得起

如果可以的话，可以将Web应用部署在同一个域下，这样可以很好的回避跨域的问题，我们常用的通过本域的后端接口包装，避免跨域的问题。

### 使用反向代理

直接使用Web服务器apache，nginx等的反向代理的方式，将需要跨域的请求发送到当前域，在Web容器配置中做请求转发，像nginx这样的反向代理很擅长做这样的事情。

### 直面惨淡的人生

#### 动态不受限标签

使用脚本动态创建`<script>`,`<img>`,`<link>`,`<iframe>`,`<frame>`等标签，在加入文档DOM后，浏览器会自动加载并解析渲染响应的资源。

#### JSONP

JSONP原理其实是对`<script>`标签的一个利用，首先`<script>`标签加载资源是不受域限制的，然后浏览器会将`<script>`加载的内容当做脚本来执行。如果服务端返回的是类似于`callback({"data":[{"aa":1}]})`这样的内容，那么浏览器会将`{"data":[{"aa":1}]}`作为函数参数调用callback这个方法。这样就实现了从不同域加载数据。

#### Form提交

Form提交不受限制，客户端可以以GET和POST的方式向服务端提交数据。

#### document.domain

每一个窗体可以对当前窗体所属域进行微调，比如当前与名为`app.test.com`,则可以设置`document.domain`为`test.com`,也可以设置为`app.test.com`。通过将子域的`document.domain`属性均改为主域`test.com`,可以实现`test.com`下的任意子域`app.test.com`,`auth.test.com`,`img.test.com`等之间的通信。

+ 修改为主域之后，子域的访问会带上父域的cookie，反之则不然
+ `.test.com` 和 `test.com` 效果一样,写成`test.com` 浏览器会理解为`.test.com`

#### window.name 

window.name 在加载不同的页面后还会存在，可以通过使用同一个window来加载需要通信的页面，通过共享window.name来进行数据通信。

#### CORS（Cross Origin Resource Sharing）

通过协商的的HTTP Header让浏览器和服务端进行通信，来决定请求或者响应是否有效。

+ 默认情况下，浏览器发送跨域请求不带认证信息（比如cookie,证书,代理认证信息等），withCredentials属性值为false
+ 跨域需要withCredentials=true，同时服务端允许Access-Control-Allow-Credentials:true，同时Access-Control-Allow-Origin 值不能为*

#### postMessage

这个是HTML5新增的页面间通信的接口，能够很好的解决iframe之间通信的问题。

### Fetch

#### 带认证信息信息跨域

+ 请求设置 `credentials:true`
+ 响应设置`Access-Control-Allow-Origin:http://origin.to.cross`,`Access-Control-Allow-Credentials:true`



#### 请求对象

- `mode`值为`same-origin`,`cors`,`no-cors`(默认),`navigate`,`websocket` 
- `credentials mode` 值为`omit`(默认),`same-origin`,`include`

#### 响应对象

+ 同域响应`type`值为 `basic`,`cors`，`default`（默认）,`error`
+ 跨域响应`type`值为`opaque`,`opaqueredirect`,`error`

#### P3P

P3P是处理Web应用中隐私数据的W3C标准,他可以通过添加HTTP 相应头的方式来通过浏览器对cookie的限制，解决第三方cookie传递的问题。

#### WebSocket

这也是HTML5新增的浏览器和服务端通信的非HTTP通信的机制，它不受同源策略的限制，是解决跨域数据传输的解决方案。

+ 在https的页面，无法发送ws://的请求，同http

## 不同角度看问题

### 本地页面间通信 VS Browser-Server通信

#### 本地页面间通信
+ 动态标签
+ postMessage
+ window.name
+ document.domain 

#### Browser-Server通信

##### GET
+ 动态标签
+ JSONP
+ Form提交
+ CORS 
+ P3P
+ WebSocket
+ fetch

##### POST
+ Form提交
+ CORS
+ P3P
+ WebSocket
+ fetch

### 单向通信 VS 双向通信

#### 单向通信
+ 动态标签
+ JSONP
+ Form提交
+ CORS 
+ P3P
+ fetch

#### 双向通信
+ window.name
+ document.domain
+ postMessage
+ WebSocket

### 前端单独处理 VS 需要后端配合

#### 前端单独处理
+ 动态标签
+ window.name
+ document.domain
+ postMessage
+ fetch

#### 需要后端配合
+ JSONP
+ Form提交
+ CORS
+ P3P
+ WebSocket

### 全版本浏览器 VS 现代浏览器

#### 全版本浏览器
+ 动态标签
+ JSONP
+ Form提交
+ window.name
+ document.domain
+ P3P

#### 现代浏览器   

- 动态标签
- JSONP
- Form提交
- window.name
- document.domain
- P3P

+ CORS
+ postMessage
+ WebSocket
+ fetch

## 实例详解

在本地玩起来，修改hosts文件增加如下的几个域名绑定作为测试:   

    127.0.0.1 source.test.com
    127.0.0.1 target.test.com
    127.0.0.1 source.test.org
    127.0.0.1 target.test.org

本文所有实例都可以在 [这里](https://github.com/rawbin-/fe_practice/tree/master/cross_origin) 下载

### 动态不受限标签
### JSONP

#### 源域 source.test.com/source-client.html

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>jsonp Test</title>
        <script type="text/javascript">
            
            // function JSONPCallback(){
            //     console.log(arguments[0])
            // }
            
            // function loadScript(url){
            //     var script = document.createElement('script');      
            //     script.src = url;
                
            //     document.head.appendChild(script);
            // }
            
            // loadScript("http://target.test.org:9000/getData.do?callback=JSONPCallback")


​            
            //下面这个是jQuery风格的JSONP,更容易理解

            function getJSON(url,callback){
                var script = document.createElement('script');      
                var callbackName = "ProxyFunc_" + (new Date().getTime())
                
                window[callbackName] = function(){
                    callback(arguments[0]);
                };
                
                script.onload = script.onratechange = function(){
                    if(this.readyState == 'complete'){
                    window[callbackName] = null;
                    }
                }
                
                script.src = url.replace("JSONPCallback",callbackName);
                document.head.appendChild(script);
            }
            
            getJSON("http://target.test.org:9000/getData.do?callback=JSONPCallback",function(data){
                console.log(data)
            })


        </script>
    </head>
    <body>
        
    </body>
    </html>

#### 目标域 target.test.org:9000

    var http = require("http");
    var url = require("url");
    
    var server = new http.Server();
    server.listen(9000);
    
    server.on("request",function(request,response){
        var paramDict = url.parse(request.url,true).query;
        var callback = paramDict.callback
        var retData = callback + '(' +'{"status":"success",data:{"name":"test JSONP"}}'  + ')';
        response.end(retData);
    });    

#### 操作方法

打开源域页面，在控制台查看拿到的数据    

### Form提交

这个我不会写~

### window.name

#### 源域 source.test.com/source.html

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title> window.name test</title> 
        <script type="text/javascript">
            window.name = "source shared windowname"
            alert(window.name)
            window.location.href = "http://target.test.org/target.html"
        </script>  
    </head>
    <body>
    
    </body>
    
    </html>

#### 目标域 target.test.org/target.html

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>window.name test</title>
        <script type="text/javascript">
            alert(window.name)
        </script>    
    </head>
    <body>
    </body>
    </html>    

#### 操作 

打开源域页面看 即使跳转到目标域 数据依然存在

### document.domain

#### 源域 source.test.com/source.html

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>document.domain test</title> 
    </head>
    <body>
        <iframe src="http://target.test.com/target.html"></iframe>
    </body>
        <script type="text/javascript">
            document.domain = "test.com";
            window.onload = function(){
                var doc = window.frames[0].document;
                console.log(doc.getElementById('tid').outerHTML);
                console.log(doc.body.innerHTML)
                setTimeout(function(){
                    doc.body.innerHTML = "data from source.test.com"
                },3000)
            }
        </script>  
    </html>

#### 目标域 target.test.com/target.html

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>document.domain test</title>
        <script type="text/javascript">
            document.domain = "test.com"
        </script>    
    </head>
    <body>
        <p id="tid">data of target.test.com</p>
    </body>
    </html>

#### 操作

打开源域页面 查看源域操作目标域dom情况

### CORS

#### 源域 source.test.com/client.html 源代码

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>cross doamin resource sharing test</title>
        <script type="text/javascript">
            var xhr = new XMLHttpRequest();
    
            xhr.onreadystatechange = function(){
                if(xhr.readyState == 4 && xhr.status == 200){
                    console.log(xhr.responseText)
                }
            }
            
            xhr.open('POST','http://target.test.org:9000/getInfo.json',true)
            xhr.send();
        </script>
    </head>
    <body>
        
    </body>
    </html>

#### 目标域 target.test.org:9000

    var http = require("http");

    var server = new http.Server();
    server.listen(9000);
    
    server.on("request",function(request,response){
        //不加这个相应头，客户端会报错，无法跨越发请求
        response.setHeader("Access-Control-Allow-Origin","http://source.test.com")
        response.writeHead(200,{"Content-Type":"text/html;charset=UTF-8"});
        response.write('msg: "cross origin by cors"');
        response.end();
    });


#### 操作方法
+ 将两个文件部署上
+ 浏览器打开源域的页面 
+ 在控制台和网络请求中查看交互数据


### P3P

这个用的相对较少，直接参考[前面一篇](https://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/2015/07/15/xmpp-httpbind-ie8/)

### postMessage

源域的页面嵌入加载了目标域的页面，并在两个域之间进行通信。


#### 源域 source.test.com/source.html 源代码：

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>postMessage Test Source</title>
        <script type="text/javascript">
            window.addEventListener('message',function(evt){
            console.log('source getmessage:',evt.data); 
            evt.source.postMessage('##source message##',evt.origin);
            });
        </script>    
    </head>
    <body>
        <iframe src="http://target.test.org/target.html"></iframe>
    </body>
    </html>

#### 目标域 target.test.org/target.html 源代码:

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>postMessage Test Source</title>
        <script type="text/javascript">
            window.addEventListener('message',function(evt){
            console.log('target getmessage:',evt.data); 
            });
            window.parent.postMessage('##target message##','http://source.test.com');
        </script>    
    </head>
    <body>
        
    </body>
    </html>

#### 操作方法
+ 将两个文件部署上
+ 浏览器打开源域的页面 
+ 在控制台可以看到两个页面交互的信息


### WebSocket

源域的页面中的脚本在页面加载时向目标域的服务发送信息，并接受服务返回的信息。 

#### 目标域 target.test.org:9000 源代码：

    var WebSocketServer = require('ws').Server;
    var socketServer = new WebSocketServer({port:9000});
    socketServer.on('connection',function(websocket){
        websocket.on('message',function(message){
        console.log(new Date().getTime(),' received ',message) 
        websocket.send('###' + message + "###")
        });
    })

#### 源域 source.test.com/index.html 源代码：

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>WebSocket Cross Origin Test</title>
        <script type="text/javascript">
            var websocket = new WebSocket("ws://target.test.org:9000");
            websocket.onopen = function(){
                console.log('websocket opened');
                websocket.send('I am opened');
            }
            
            websocket.onmessage = function(evt){
                console.log('recevie message')
                console.log(evt.data)
            }
            
            websocket.onclose = function(){
                console.log('websocket closed')
            }
            
            websocket.onerror = function(){
                console.log('websocket meets error')
            }
        </script>
    </head>
    <body>
        
    </body>
    </html>

#### 操作方式
+ npm install ws
+ node 启动目标域的socket server
+ 浏览器打开源域的页面
+ 在控制台可以看到客户端和服务端交互的消息


## 安全相关



## 参考资料

0. [RFC 6454 The Web Origin Concept](http://tools.ietf.org/html/rfc6454)
1. [同源策略和跨域访问](http://blog.csdn.net/shimiso/article/details/21830313)
2. [W3C 同源策略](http://www.w3.org/Security/wiki/Same_Origin_Policy)
3. [你真的会使用XMLHttpRequest吗？](https://segmentfault.com/a/1190000004322487)
4. [跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)
5. [JavaScript的同源策略](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)
6. [Same Origin Policy Part 0: Origins](http://blogs.msdn.com/b/ieinternals/archive/2014/03/13/explaining-same-origin-policy-part-0-origins.aspx)
7. [Same Origin Policy Part 1: No Peeking](http://blogs.msdn.com/b/ieinternals/archive/2009/08/28/explaining-same-origin-policy-part-1-deny-read.aspx)
8. [Same Origin Policy Part 2: Limited Write](http://blogs.msdn.com/b/ieinternals/archive/2012/04/03/explaining-same-origin-policy-part-2-limited-write.aspx)
9. [同源策略](http://www.cnblogs.com/dsky/archive/2012/04/06/2434010.html)
10. [同源策略理解](https://rawbin-.github.io/%E5%BC%80%E5%8F%91%E6%8A%80%E6%9C%AF/2015/03/05/javascript-crossorigin/)
11. [[CORS：跨域资源共享] W3C的CORS Specification](http://www.cnblogs.com/artech/p/cors-4-asp-net-web-api-02.html)
12. [JavaScript最全的10中跨域共享的方法](http://www.csdn.net/article/2011-01-27/290968)
13. [前端解决跨域问题的8种方案](http://www.cnblogs.com/JChen666/p/3399951.html)
14. [JSONP原理详解](http://www.cnblogs.com/dowinning/archive/2012/04/19/json-jsonp-jquery.html)
15. [跨域方法汇总](http://www.udpwork.com/item/11695.html)
16. [跨域方法汇总](http://www.raychase.net/2216)
17. [父子页面跨域通信的方法](http://tid.tenpay.com/?p=4695)
18. [父子页面跨域解决办法](http://www.ttlsa.com/web/cross-domain-solutions/)
19. [优雅绝妙的Javascript跨域问题解决方案](http://blog.csdn.net/sfdev/article/details/5807045)
20. [JavaScript跨域访问解决方案](http://blog.csdn.net/sfdev/article/details/3887006)
21. [JS几种实用的跨域方法原理详解](http://www.cnblogs.com/2050/p/3191744.html)
22. [JavaScript跨域总结与解决办法](http://www.cnblogs.com/rainman/archive/2011/02/20/1959325.html)
23. [JavaScript跨域解决方法大全](http://blog.csdn.net/freshlover/article/details/40827207)
24. [近乎完美的简单JS跨域解决方法](http://rubel.iteye.com/blog/901182)
25. [Post方式跨域上传文件](http://blog.csdn.net/black_ox/article/details/20645957)
26. [AJAX机制详解及跨域通信](http://www.cnblogs.com/renlong0602/p/4414872.html)
27. [跨域数据传输方法](http://www.cnblogs.com/GodIsBoy/p/3563865.html?utm_source=tuicool)
28. [异步上传文件并获得返回值（完全跨域）](http://blog.csdn.net/lrz1011/article/details/7913992)
29. [异步上传文件并获得返回值（完全跨域）](http://my.oschina.net/whynotAZ/blog/206871#OSC_h2_1)
30. [同源策略以及cookie安全策略](http://blog.csdn.net/turkeyzhou/article/details/8818173)
31. [Google浏览器安全策略](https://code.google.com/p/browsersec/wiki/Main)
32. [同源策略详解及绕过](http://www.freebuf.com/articles/web/65468.html)
33. [理解 CORS (Cross-Origin Resource Sharing)](http://huang-jerryc.com/2016/05/15/%E7%90%86%E8%A7%A3%20CORS%20(Cross-Origin%20Resource%20Sharing)/)

