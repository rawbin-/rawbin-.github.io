---
layout: post
title: "Web开发中编码解码那些事"
categories: [Web开发,前端开发,Web安全]
tags: [Web安全,网络攻防,编码解码]
---

[TOC]

### 源代码编码

### 页面内容编码

#### HTML编码

##### 编码设定

```
<meta http-equiv="Content-Type" content="text/html; charset=GBK"/> <!--HTML4.x-->
<meta charset="UTF-8"> <!--HTML5.x-->
<script charset="GBK"></script>
<form accept-charset="GBK"></form>
<link charset="GBK" rel="stylesheet" type="text/css" />
```



##### 浏览器编码解析

+ 字节流里面能确认编码的就使用这个确认了的编码（比如BOM）
+ 没有明确编码的页面，浏览器需要使用下面的编码嗅探算法进行编码确认
  + 用户明确设定了编码的，就用用户设定的编码，比如在Firefox 查看菜单中设定页面解析编码
  + 浏览器预解析500ms之内收到的资源或者1024字节（哪个条件先满足就用谁）来确定页面设定的编码，比如FEFF开头的两字节为UTF-16大端，FFFE开头的两字节为UTF-16小端，EFBBBF开头的三字节为UTF-8
  + 如果传输层指定了编码，就用传输层指定的编码
  + （可选）预扫描字节流来确定内容编码，取meta标签设定的编码
  + 如果HTML解析器关联了一个Document对象，且这个文档对象的父文档满足同源和非UTF-16编码，就使用这个父文档的编码
  + 上面都没找到编码的话，就猜一个可能的编码，比如上次这个页面用的编码
  + 浏览器可以用资源的信息来自动识别编码，但不能用资源的内容和地址信息，如果识别出来了就用识别了的编码
  + 还是没确定编码，就按情况给个UTF-8或者根据系统语言信息给个默认的值，比如简体中文GB18030
  + 拿到编码，开始解码内容
+ 提交表单编码
  + 按算法算出一个可选编码列表来
  + 如果没有可选编码就用UTF-8，如果有就用第一个能编码所有表单键值对的编码。

#### CSS编码

#### JavaScript 编码



### 传输编码解码



### 后端编码解码



### 存储编码解码



### 参考资料

1. [字符，字节和编码](http://www.regexlab.com/zh/encoding.htm)
2. [W3C HTML5.1](https://www.w3.org/TR/2016/REC-html51-20161101/)
3. [whatwg HTML](https://html.spec.whatwg.org/multipage/)
4. 《软件剖析-代码攻防之道》
5. 《Web应用安全威胁与防治 基于OWASP Top 10与ESAPI》
6. 《WEB之困 现代WEB应用安全指南》
7. 《Web应用安全权威指南》
8. 《黑客大曝光Web应用程序安全》第3版
9. 《黑客攻防技术宝典Web实战篇》第2版—《The Web Application Hacker's Handbook》2nd Edition

