---
layout: post
title: "Django框架流程 请求处理流程 源码分析"
date: 2015/4/11 18:27:10 
category: [Django]
tags: [Django 流程,Django 源码,WSGI]
---

## 创建服务器
//todo django commands
### python manage.py runserver
+ manage.py 设置DJANGO_SETTINGS_MODULE环境变量为当前项目的settings.py,然后通过django.core.management.execute_from_command_line 继续执行命令（此处为runserver及其相关参数）
[django-admin commands](http://django.readthedocs.org/en/latest/howto/custom-management-commands.html)
+ 使用django命令行的方式，执行django.core.management.commands.runserver, 执行该模块下的Command类的handle方法，通过django.core.servers.basehttp.get_internal_wsgi_application 方法获取一个处理类的名称，这里获取到的是项目setting.py中的WSGI_APPLICATION 配置，一般是项目目录下wsgi.py 里面定义的application，配置为xxx.wsgi.application.wsgi.py中定义的application为django.core.wsgi.get_wsgi_application返回的django.core.handlers.wsgi.WSGIHandler的实例。最后通过调用django.core.servers.basehttp.run方法创建并启动http服务器，并启用监听。

+ 请求到达后，会调用django.core.handlers.wsgi.WSGIHandler的实例


## 参考
0. [wsgiref 源代码分析][14]
1. [WSGI 简介][15]
2. [Wsgi研究][16]
0. [Django 结构及处理流程分析][0]
1. [Django源码分析--引导][1]
2. [Django中的request和response][2]
3. [Django 结构及处理流程分析][3]
5. [Django运行方式及处理流程总结][4]
6. [How to write a web framework in Python][5]
7. [Why so many Python web frameworks][6]
8. [用Python写一个简单的Web框架][7]
9. [Django 结构及处理流程分析][8]
10. [Django运行方式及处理流程总结][9]
11. [Python与Django篇][10]
12. [Django运行方式及处理流程总结（xianglong.me）][11]
13. [Django运行方式及处理流程总结][12]
14. [Django 结构及处理流程分析][13]


[0]: http://jianlee.ylinux.org/Computer/Python/django-arch.html "Django 结构及处理流程分析"
[1]: http://www.cnblogs.com/chenzehe/archive/2011/04/30/1947628.html "Django源码分析--引导"
[2]: http://my.oschina.net/shniu/blog/205634 "Django中的request和response"
[3]: http://my.oschina.net/leejun2005/blog/75227 "Django 结构及处理流程分析"
[4]: http://xianglong.me/article/django-request-work-flow/ "Django运行方式及处理流程总结"
[5]: http://anandology.com/blog/how-to-write-a-web-framework-in-python/ "How to write a web framework in Python"
[6]: http://bitworking.org/news/Why_so_many_Python_web_frameworks "Why so many Python web frameworks"
[7]: http://www.tuicool.com/articles/aYBRBz "用Python写一个简单的Web框架"
[8]: http://my.oschina.net/tenking/blog/29439 "Django 结构及处理流程分析"
[9]: http://www.tuicool.com/articles/BjInM3B "Django运行方式及处理流程总结"
[10]: http://www.nowamagic.net/academy/part/13/286 "Python与Django篇"
[11]: http://segmentfault.com/a/1190000002399134 "Django运行方式及处理流程总结（xianglong.me）"
[12]: http://www.kuqin.com/shuoit/20141109/343106.html "Django运行方式及处理流程总结"
[13]: http://blog.csdn.net/aixiaohei/article/details/6585980 "Django 结构及处理流程分析"
[14]: http://blog.csdn.net/on_1y/article/details/18818081 "wsgiref 源代码分析"
[15]: http://blog.csdn.net/on_1y/article/details/18803563 "WSGI 简介"
[16]: http://blog.kenshinx.me/blog/wsgi-research/ "Wsgi研究"