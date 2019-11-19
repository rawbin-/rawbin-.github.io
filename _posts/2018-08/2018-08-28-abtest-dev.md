---
layout: post
title: "使用nginx实现灰度发布"
categories: [前端开发,Web开发,灰度发布,ABTest]
tags: [客户端,桌面端,灰度发布,ABTest]
---



## 前言

参考资料中已经包含了几种不同的通过nginx实现灰度发布的方式

- 编程方式
  - OpenResty
  - nginx+lua
- nginx默认
  - Cookie判断
  - IP 判断

跟上面相关的可以直接参考对应的内容，咱们这里讲另外一个编程方式，`nginx+njs`的方式，是`nginx`官方支持的，用`js`的语法来写的扩展方式。（对大家都友好，哈哈）



### njs [ref](http://nginx.org/en/docs/njs/index.html)

#### 简介(简单翻译了下)

- `njs` 是用来扩展`nginx`功能的`JavaScript`子集，目前支持ES5严格模式以及部分ES6+的特性，特性支持在不断完善中

#### 使用场景

- 在转发上游之前进行复杂的访问控制或安全检查
- 调整响应头
- 进行灵活的异步内容处理或过滤
- 等等



### MacOS 下使用nginx+njs模块

#### MacOS安装nginx+njs

- `brew install nginx` 直接安装，这样安装的不具备定制性，纯默认配置
- `brew install nginx-full`  这样安装可以传入配置参数，可以通过`brew install nginx-full --help`来查看哪些可以添加的配置参数，比如安装调试模式的 `nginx`
- `源码编译安装` 如果上面两种都满足不了需求，比如当前`njs`模块还没被参数支持，那就需要源码编译安装咯
  - 先安装`mercurial`这个代码管理工具（为嘛还不切到git呢）
    - `brew install mercurial` 安装 `mercurial` [ref](https://www.mercurial-scm.org/wiki/Download)   
  - 搞一个操作空间并切过去
    - `mkdir nginx-install && cd nginx-install`  
  - 拉取 `nginx` 源码
    - `hg clone http://hg.nginx.org/nginx`  
  - 拉取 `njs` 源码
    - `hg clone http://hg.nginx.org/njs`  
  - 执行nginx和动态模块配置
    - `cd nginx && ./auto/configure --add-dynamic-module=../njs/nginx/`  
  - 安装nginx
    - `make && make install` 
    - 如果遇到权限问题 `sudo mkdir /usr/local/nginx && sudo chown -R $(whoami) /usr/local/nginx`
  - 安装好的`nginx` 会在 `/usr/local/nginx/sbin/nginx` 路径下，使用这个路径执行启动即可



#### 配置 nginx+njs

- 安装好`njs`模块之后，包含两部分[ngx_stream_js_module](http://nginx.org/en/docs/stream/ngx_stream_js_module.html) 和 [ngx_http_js_module](http://nginx.org/en/docs/http/ngx_http_js_module.html) 两部分，可以根据文档配置，我们这里以`ngx_http_js_module`为例，[配置参考](http://nginx.org/en/docs/http/ngx_http_js_module.html)

- 进入nginx 目录，并建立相关配置文件 [语法参考](http://nginx.org/en/docs/njs/compatibility.html)

  - `cd /usr/local/nginx/conf && mkdir njs-conf `  创建

  - `touch  njs-conf/main.js njs-conf/divide-by-user.js`

  - `njs-conf/main.js` 内容如下：(注意路径)

  - ```js
    import divideByUser from 'conf/njs-conf/divide-by-user.js'
    var divideByPolicy = (req) => {
         divideByUser(req);
    }
    ```

  - `njs-conf/divide-by-user.js` 内容如下 [可操作对象参考](http://nginx.org/en/docs/njs/reference.html)

  - ```js
    export default  (req) => {
        req.status = 200;
        req.headersOut['Content-Type'] = 'text/html;charset=utf-8';
        req.sendHeader();
        req.send('divide by user\r\n');
        req.send(JSON.stringify(req));
        req.send('\n\nrequest object info:\n\n')
        for(var i in req){
            if(i === 'parent'){
                continue;
            }
            req.send(`${Object.prototype.toString.apply(req[i]).slice(7)}\b ${i} = ${JSON.stringify(req[i])} \n\n`)
        }
        req.send('\n\n');
        req.finish();
    }
    ```

  - 修改并调整`nginx.conf`

  - ```shell
    ## 在主配置里面加载动态模块
    load_module modules/ngx_http_js_module.so;
    ## events { ...}
    ## http {...}
    
    ## 在http 中引入 我们的 njs配置
    js_include njs-conf/main.js;
    ## server {...}
    
    ```

  - 重启`nginx`

    - `/usr/local/nginx/sbin/nginx -s reload`
    - 如果配置有错误会报错，改掉即可。  [语法参考](http://nginx.org/en/docs/njs/compatibility.html)
    - 如果不容易发现错误，可以用`njs`调试，路径在`nginx-install/njs/build/njs`  [参考](http://nginx.org/en/docs/njs/cli.html)





### 参考资料

0. [A/B测试和灰度发布的关系](http://www.appadhoc.com/blog/ab-testing-and-grey-release/)
1. [nginx灰度发布](https://www.cnblogs.com/tongxiaoda/p/8473586.html)
2. [web service灰度发布](https://www.cnblogs.com/jiyukai/p/9326965.html)
3. [ABTest及流量切换实现](https://www.cnblogs.com/veggiegfei/p/11090958.html)
4. [灰度发布的原理及实现](http://c.biancheng.net/view/5551.html)
5. [使用Nginx实现Web应用灰度发布](https://carey.akhack.com/2017/07/11/%E4%BD%BF%E7%94%A8Nginx%E5%AE%9E%E7%8E%B0Web%E5%BA%94%E7%94%A8%E7%81%B0%E5%BA%A6%E5%8F%91%E5%B8%83/)
6. [使用Nginx实现灰度发布](https://www.cnblogs.com/weifeng1463/p/7353710.html)
7. [OpenResty搭建高性能服务端](https://www.jianshu.com/p/09c17230e1ae)