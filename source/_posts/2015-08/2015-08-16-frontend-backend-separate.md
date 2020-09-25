---
layout: post
title: "使用NodeJS实现前后端分离"
categories: [Web开发,前端开发]
tags: [Node,前后端分离]

---


## 1 参考资料
0. [Web 研发模式的演变](http://blog.jobbole.com/65509/)
0. [Web 研发模式演变](https://github.com/lifesinger/blog/issues/184)
0. [淘宝前后端分离实践](http://2014.jsconf.cn/slides/herman-taobaoweb/index.html#/)
0. [前后端分离的思考与实践（一）](http://blog.jobbole.com/65513/)
0. [前后端分离的思考与实践（二）](http://blog.jobbole.com/65534/)
0. [前后端分离的思考与实践（三）](http://blog.jobbole.com/65541/)
0. [前后端分离的思考与实践（一）](http://ued.taobao.org/blog/2014/04/full-stack-development-with-nodejs/)
0. [前后端分离的思考与实践（二）](http://ued.taobao.org/blog/2014/04/xtpl/)
0. [前后端分离的思考与实践（三）](http://ued.taobao.org/blog/2014/04/modelproxy/)
0. [前后端分离的思考与实践（四）](http://ued.taobao.org/blog/2014/05/midway-security/)
0. [前后端分离的思考与实践（五）](http://ued.taobao.org/blog/2014/05/cross-platform-tpl/)
0. [前后端分离的思考与实践（六）](http://ued.taobao.org/blog/2014/05/midway-deploy/)
0. [前后端分离的思考与实践(1)](http://blog.csdn.net/zhoudaxia/article/details/29413117)
0. [前后端分离的思考与实践(2)](http://blog.csdn.net/zhoudaxia/article/details/29851265)
0. [基于NodeJS的前端后端分离](http://www.tuicool.com/articles/2MnyYn7)
0. [基于NodeJS的前后端分离](http://blog.csdn.net/asd131531/article/details/42433761)
0. [前后端分离项目实践分析](http://segmentfault.com/a/1190000002978095)
0. [Web前后端分离开发思路](http://segmentfault.com/a/1190000002413526)
0. [再来讨论前后端分离的实践](http://cn.v2ex.com/t/149090)
0. [架构设计：前后端分离之Web前端架构设计](http://www.cnblogs.com/sharpxiajun/p/3870797.html)
0. [一个简单粗暴的前后端分离方案](http://www.cnblogs.com/lvdabao/p/4204858.html)
0. [前后端分离开发部署模式](http://www.qttc.net/201506464.html)
0. [系统架构：Web应用架构的新趋势---前端和后端分离的一点想法](http://www.cnblogs.com/sharpxiajun/p/3531665.html)
0. [Web系统开发构架再思考-前后端的完全分离](http://developer.51cto.com/art/201504/473308.htm)
0. [Node.js Web应用代码热更新的另类思路](http://fex.baidu.com/blog/2015/05/nodejs-hot-swapping/)
0. [前后端完全分离初探](http://segmentfault.com/a/1190000002662501)
0. [前后端分离了，然后呢？](http://icodeit.org/2015/06/whats-next-after-separate-frontend-and-backend/)
0. [前后端分离了，然后呢？](http://kb.cnblogs.com/page/524041/)
0. [Web 前后端分离的意义大吗？](http://www.zhihu.com/question/28207685)
0. [Web系统开发构架再思考-前后端的完全分离](http://www.kuqin.com/shuoit/20150424/345865.html)
0. [前后端分离方案交流，大家发表一下看法](http://www.oschina.net/question/1475582_156458?fromerr=8jIqy7kH)