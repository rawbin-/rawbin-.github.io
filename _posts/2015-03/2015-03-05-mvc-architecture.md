---
layout: post
title: "MVC分层架构"
date: 2015/4/5 22:03:42 
categories: [Web前端,Web开发]
tags: [MVC,MVVM,MVP,前端框架]
---

## 基础MVC
MVC即Model-View-Controller，进行系统分层的一种架构模式。
+ M即Model 指的是业务层
  该层是应用程序的主体部分，应用程序中的业务数据和业务逻辑都在该层实现。
+ V即View 指的是表示层
  该层时应用程序中显示给用户的界面，如浏览器显示的页面，桌面程序的UI等
+ C即Controller 指的是控制层
  该层用来控制表示层和业务层之间的交互，根据用户交互事件控制用户界面显示和更新Model层的数据
他们之间的依赖关系如下：
----------->Model<-----------------
|                                 |
<-------Controller<-------View---->
即View依赖于Controller，同时View和Controller都依赖于Model

##　MVC变种
在不同的上下文中，有了不同的Controller的实现，MVC功能也有一些调整。
### MVC-1：
+ M即Model 指的是业务层
  + 封装应用程序的数据
  + 代表应用程序的状态
  + 响应用户查询
  + 实现业务功能
  + 通知视图业务状态更新
+ V即View 指的是表示层
  + 显示数据
  + 接受数据更新请求
  + 发送用户数据给控制器
+ C即Controller 指的是控制层
  + 接受用户请求
  + 调用模型相应用户请求
  + 选择视图显示响应结果
他们之间的依赖关系如下：
----------->Model<-----------------
|                                 |
<-----Controller<------->View<---->
即Model和View是双向依赖关系，View和Controller是双向依赖关系，Controller依赖于View。
流程为：View发送用户事件给Controller，Controller接收用户事件，将其转发给Model，Model处理事件，并通知View更新。
这种方式无疑增加了View和Model的耦合度，于是为了解耦，有了下面的变种。

### MVC-2
+ M即Model 指的是业务层
  + 封装应用程序的数据
  + 实现业务功能
+ V即View 指的是表示层
  + 显示数据
  + 接受数据更新请求
  + 发送用户数据给控制器
+ C即Controller 指的是控制层
  + 接受用户请求
  + 调用模型相应用户请求
  + 选择视图显示响应结果
他们之间的依赖关系如下：
----------->Model------------------
|                                 |
<-----Controller<------->View------
即View和Controller是双向依赖关系，Controller和Model之间是双向依赖关系。
流程是：用户交互事件通过Controller来操作Model，Model也通过Controller来控制数据更新。
这是分层模式想要达到的效果，真正的解耦。

### MVP(Model-View-Presenter)
MVP跟MVC-2类似，仅仅是Controller被Presenter替换掉

### MVVM（Model-View-ViewModel）
MVVM跟MVP类似，区别在于MVVM包含了双向数据绑定，即V上的数据变化会直接反映到（同步到）VM上，反之亦然。



## 参考资料
0. [MVVM模式原理分析及实践][0]
1. [MVVM架构][1]
2. [12个JavaScript MVC框架评估][2]
3. [Scaling Isomorphic Javascript Code][5]
3. [MVC，MVP 和 MVVM 的图示][4]
0. [对MVC、MVP、MVVM的理解][7]
1. [MVC, MVP, MVVM比较以及区别(上)][8]
0. [JavaScript MVC框架PK：Angular、Backbone、CanJS与Ember][3]
0. [从Script到Code Blocks、Code Behind到MVC、MVP、MVVM][6]
1. [JavaScript富应用MVC MVVM框架][9]
2. [Web前端开发：为何选择MVVM而非MVC][10]



[0]: http://www.cpiso.cn/jsyj/ghxx/2012/5/18/373.shtml "MVVM模式原理分析及实践"
[1]: http://www.cnblogs.com/cdts_change/archive/2010/11/28/1890584.html "MVVM架构"
[2]: http://www.open-open.com/news/view/208b86 "12个JavaScript MVC框架评估"
[3]: http://www.ituring.com.cn/article/38394 "JavaScript MVC框架PK：Angular、Backbone、CanJS与Ember"
[4]: http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html "MVC，MVP 和 MVVM 的图示"
[5]: http://blog.nodejitsu.com/scaling-isomorphic-javascript-code/ "Scaling Isomorphic Javascript Code"
[6]: http://www.cnblogs.com/indream/p/3602348.html "从Script到Code Blocks、Code Behind到MVC、MVP、MVVM"
[7]: http://blog.csdn.net/napolunyishi/article/details/22722345 "对MVC、MVP、MVVM的理解"
[8]: http://www.cnblogs.com/justrun1983/p/3679827.html "MVC, MVP, MVVM比较以及区别(上)"
[9]: http://www.cnblogs.com/aaronjs/p/3149402.html "JavaScript富应用MVC MVVM框架"
[10]: http://www.cnblogs.com/winter-cn/archive/2012/09/16/2687184.html "Web前端开发：为何选择MVVM而非MVC"