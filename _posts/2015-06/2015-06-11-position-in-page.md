---
layout: post
title: "网页中的定位 鼠标定位 页面定位 事件定位"
categories: [开发技术]
tags: [网页定位,鼠标定位,页面位置]

---

## 1 事件位置属性支持情况

Safari和Chrome这两个Webkit发源的兄弟，将所有见过的属性都支持了。。。

### 1.1 Event.screenX，Event.screenY
+ W3C+ IE+ Firefox+ Opera+ Safari+ chrome+

W3C标准属性，所有浏览器都支持

### 1.2 Event.clientX，Event.clientY
+ W3C+ IE+ Firefox+ Opera+ Safari+ chrome+

W3C标准属性，所有浏览器都支持

### 1.3 Event.pageX，Event.pageY
+ W3C- IE- Firefox+ Opera+ Safari+ chrome+

非标准属性，IE不支持

### 1.4 Event.layerX，Event.layerY
+ W3C- IE- Firefox+ Opera- Safari+ chrome+

非标准属性，IE和Opera不支持

### 1.5 Event.offsetX，Event.offsetY
+ W3C- IE+ Firefox- Opera+ Safari+ chrome+

非标准属性，Firefox不支持

### 1.6 Event.x, Event.y
+ W3C- IE+ Firefox- Opera+ Safari+ chrome+

非标准属性，Firefox不支持


## 2 Event对象属性
+ clientX clientY
+ screenX screenY
+ pageX pageY
+ layerX layerY
+ offsetX offsetY
+ x y 

### 2.1 Event.screenX, Event.screenY
发生事件的位置相对于屏幕左上角的坐标，所有浏览器都支持的标准属性。

### 2.2 Event.clientX, Event.clientY
发生事件的位置相对于可视区域左上角的坐标，所有浏览器都支持的标准属性。

### 2.3 Event.pageX，Event.pageY
不是标准的属性，但被广泛的支持,类似于clientX和clientY，它们使用的是文档坐标而不是窗口坐标，表示当前点与页面左上角的距离，包含滚动距离。IE6-8不支持此属性。替代的属性为Event.offsetX，Event.offsetY。

### 2.4 Event.layerX, Event.layerY
最近的绝对定位的父元素位置，以border左上角为原点，如果没有就为document，IE6-8，Opera不支持此属性，替代属性为Event.offsetX，Event.offsetY。

### 2.5 Event.offsetX，Event.offsetY
最近的绝对定位的父元素位置， 如果没有就为document，Firefox不支持此属性。替代属性为Event.layerX,Event.layerY。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     

### 2.6 Event.x, Event.y
事件发生位置的x坐标和y坐标，它们相对于CSS动态定位的最内层包容元素,仅IE8及更早的版本支持。


## 3 Element对象属性
+ clientHeight clientWidth
+ clientLeft clientTop
+ offsetHeight offsetWidth
+ offsetLeft offsetTop
+ scrollHeight scrollWidth
+ scrollLeft scrollTop


### 3.1 Element.clientHeight,Element.clientWidth
元素内部的尺寸（元素内边距和内容的尺寸），除去滚动条和其他包装元素之外的尺寸，如果当前元素是根元素document.documentElement,这个两个属性等于浏览器可视窗口的尺寸。

### 3.2 Element.clientLeft,Element.clientTop
元素边框的宽度加滚动的距离，如果没有滚动，返回的是边框的宽度。

### 3.3 Element.offsetHeight，Element.offsetWidth
当前元素极其所有内容的高度及宽度，单位为像素，包括元素的CSS内边距及边框及滚动条，但不包括外边距,也不包括滚动的部分，仅仅返回可见区域的宽高。

### 3.4 Element.offsetLeft，Element.offsetTop
当前元素菜单CSS边框的左上角相对于他的offsetParent容器元素的坐标。

### 3.5 Element.scrollHeight,Element.scrollWidth
元素的全部宽度和高度，包含滚动的部分（比如div overflow：scroll）

### 3.6 Element.scrollLeft,Element.scrollTop
元素滚动过的距离。

## 4 Window对象属性
+ innerHeight innerWidth
+ outerHeight outerWidth
+ screenX screenY
+ pageXOffset pageYOffset
+ scrollX scrollY

### 4.1 Window.innerHeight,Window.innerWidth
当前窗口显示区域的文档的高度和宽度，不包含浏览器的边框，单包含滚动条，单位是像素,IE8及更早的版本不支持。

### 4.2 Window.outerHeight,Window.outerWidth
当前浏览器窗口的总高度和宽度、包含浏览器的边框，单位为像素,IE8及更早的版本不支持。

### 4.3 Window.screenLeft，Window.screenTop
窗口的左上角（浏览器的内边缘）在屏幕上的X坐标和Y坐标；
IE、Safari、Opera支持screenLeft和screenTop
Firefox和Safari支持screenX和screenY

### 4.4 Window.screenX，Window.screenY
浏览器的外边缘到屏幕边缘的距离。

### 4.5 Window.pageXOffset，Window.pageYOffset
在Firefox中`window.pageXOffset == window.scrollX; // always true`,这两个是一回事。
当前温度向右和向下滚动过的像素数，IE8及更早的版本中不支持这些属性，IE中替代的属性使document.documentElement或者document.body 的scrollLeft和scrollTop属性

### 4.6 Window.scrollX, Window.scrollY 
表示浏览器X轴（水平）、Y轴（垂直）滚动条的偏移距离。

## 5 Screen对象属性

### 5.1 Screen.width，Screen.height
屏幕的宽度、高度（指的是屏幕的分辨率，单位为像素）

### 5.2 Screen.availWidth，Screen.availHeight
屏幕的可用宽度、高度（通常与屏幕的宽度、高度一致）

## 6 兼容方案

### 6.1 获取滚动距离
    var x = (window.pageXOffset !== undefined)
      ? window.pageXOffset
      : (document.documentElement || document.body.parentNode || document.body).scrollLeft;
    
    var y = (window.pageYOffset !== undefined)
      ? window.pageYOffset
      : (document.documentElement || document.body.parentNode || document.body).scrollTop;



## 7 参考资料
0. [前端攻城狮学习笔记九：让你彻底弄清offset](http://www.cnblogs.com/jscode/archive/2012/09/03/2669299.html))
0. [各个属性测试demo](http://shanmao.me/wp-content/uploads/2013/02/pageX-clientX.html)
0. [各浏览器的鼠标位置测试](http://www.cnblogs.com/xesam/archive/2011/12/08/2280509.html)
1. [pageX、clientX、screenX、offsetX、layerX、x](http://blog.sina.com.cn/s/blog_780a94270101kdgo.html)
2. [pageX,clientX,offsetX,layerX的区别](http://shanmao.me/web/js/pagex-clientx-offsetx-layerx-de-qu-bie)
3. [JavaScript网页定位详解](http://www.jb51.net/article/45553.htm)
4. [JavaScript中的一些定位属性图解](http://www.jb51.net/article/24238.htm)
5. [pageX,clientX,screenX,offsetX区别](http://blog.csdn.net/ajaxuser/article/details/7549661)
6. [获取浏览器滚动条的偏移距离](http://blog.sina.com.cn/s/blog_8fba8b5201016num.html)
7. [MDN-Window.scrollX](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollX)
8. [scrollLeft,scrollTop兼容差异](http://www.w3help.org/zh-cn/causes/BX9008)
9. [JavaScript事件对象 坐标说明](http://www.jb51.net/article/23723.htm)
10. [原生JS活树鼠标坐标方法详解](http://www.bbs0101.com/archives/124.html)
11. [javascript的offset、client、scroll使用方法详解](http://www.jb51.net/article/32801.htm)
12. [JavaScript获取浏览器的高度和宽度值](http://blog.snsgou.com/post-574.html)
13. [JavaScript概念之screen/client/offset/scroll/inner/avail的width/left](http://caibaojian.com/js-name.html)
14. [offsetLeft,Left,clientLeft的区别](http://www.cnblogs.com/panjun-Donet/articles/1294033.html)
15. [用Javascript获取页面元素的位置](http://www.ruanyifeng.com/blog/2009/09/find_element_s_position_using_javascript.html)
16. [JavaScript获取DOM元素位置和尺寸大小](http://www.cnblogs.com/dolphinX/archive/2012/11/19/2777756.html)
17. [HTML DOM Element](http://javascript.ruanyifeng.com/dom/element.html)
18. [javascript中top、clientTop、scrollTop、offsetTop的讲解](http://www.cnblogs.com/trlanfeng/archive/2012/11/04/2753280.html)