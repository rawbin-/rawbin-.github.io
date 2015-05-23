---
layout: post
title: "nginx 源码阅读 merge_slashes问题解决"
date: 2015/4/18 17:46:09 
categories: nginx
---

## 问题描述
使用nginx uWSGI django 做REST API开发。
在URL中传入`http://host/test/http://aa.com/`,但在Django中收到的URI始终是`http:/aa.com`，嗯，就是少了斜杠，后来尝试发现多少个连续的斜杠都会被干掉只留下一个。

## 解决途径
+ 查看nginx和uWSGI的日志，请求的路径都是正确的，即都为没有去掉斜杠的
+ 查看Django源码，发现在django.core.handlers.base.BaseHandler中，处理URL匹配的时候使用的是path_info对应的值
+ 于是找到处理类在django.core.handlers.wsgi.WSGIHandler,在这里打印请求获取到的path_info参数，发现已经是被去掉斜线的了。
+ 怀疑是Nginx传过来的时候已经被处理过了
+ 于是查看Nginx配置目录下，Nginx向uWSGI传的参数
        uwsgi_param  REQUEST_URI        $request_uri;
        uwsgi_param  PATH_INFO          $document_uri;
+ 于是把$document_url打印到Nginx的日志中，发现确实是处理过的
+ 想看看在哪处理的，下了一份Nginx源码，翻到src/http/ngx_http_parse.c 果然有相关的逻辑（意思是配了这个值的话就不把重复的写进去了^_^废话）
         if (!merge_slashes) {
                    *u++ = ch;
         }
+ 能不能改改？翻了翻Nginx的模块配置文档发现可以，就用这个关键字搜
+ 当然最后的解决办法不是直接修改这个配置，而是将要放在URL中的那一串串作为base64编码的结果再传递


## 参考
0. [nginx源码分析（7）——请求处理][2]
1. [Nginx开发从入门到精通][3]
2. [运维与架构手册][4]
3. [linux下安装nginx、pcre、zlib、openssl][5]
4. [nginx编译选项详解][6]
0. [Module ngx_http_core_module][0]
1. [Nginx merge_slashes redirect][1]


[0]: http://nginx.org/en/docs/http/ngx_http_core_module.html "Module ngx_http_core_module"
[1]: http://stackoverflow.com/questions/14832780/nginx-merge-slashes-redirect "Nginx merge_slashes redirect"
[2]: http://blog.csdn.net/chosen0ne/article/details/7861048 "nginx源码分析（7）——请求处理"
[3]: http://tengine.taobao.org/book/index.html "Nginx开发从入门到精通"
[4]: http://www.nginx.cn/manual "运维与架构手册"
[5]: http://blog.csdn.net/wanwang8/article/details/7200454 "linux下安装nginx、pcre、zlib、openssl"
[6]: http://sjsky.iteye.com/blog/1146520 "nginx编译选项详解"