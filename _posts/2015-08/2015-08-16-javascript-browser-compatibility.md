---
layout: post
title: "JavaScript浏览器兼容性"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,兼容性,浏览器]

---

## 浏览器特性检测

绝大部分通过User-Agent的检测方式已经一去不复返了，越来越分不清楚了；特性检测成为现有的JS类库推行的方式，将浏览器分为支持W3C标准的现代浏览器和非标准浏览器。
下面看看一些特性的坑：

### document.all

执行代码：

    console.log(document.all,typeof document.all,!!document.all,'all' in document)


|特性值|Firefox|Chrome|Edge|IE11|IE10|IE9|IE8|IE7|IE5|
|------|-------|------|---|----|----|---|----|---|---|
|document.all|undefined|[html,head,script]|[object HTMLAllCollection]|[object HTMLAllCollection]|[object HTMLCollection]|[object HTMLCollection]|[object]|[object]|[object]|
|typeof document.all|undefined|"object"|undefined|undefined|object|object|object|object|object|
|!!document.all|false|false|false|false|true|true|true|true|true|
|'all' in document|true|true|true|true|true|true|true|true|true|

上面的情况看起来还是比较乱的：
+ Firefox不支持`document.all` 属性，但保留了all属性，只是没用起来
+ Chrome 支持`document.all`属性，但其布尔值跟没支持的效果一样
+ Edge 支持`document.all 属性，但其布尔值看起来跟没支持一样
+ IE11的效果跟Edge一样，IE11可被视为现代浏览器的分界线，跟较低的IE版本比较，IE11算是不是IE的IE。
+ 用document.all 来判断是IE已经不是一个靠谱的方法了


### ActiveXObject

执行代码：

    console.log(ActiveXObject,typeof ActiveXObject,!!ActiveXObject,"ActiveXObject" in window)

|特性值|Firefox|Chrome|Edge|IE11|IE10|IE9|IE8|IE7|IE5|
|------|-------|------|---|----|----|---|----|---|---|
|ActiveXObject|ActiveXObject is not defined|ActiveXObject is not defined|ActiveXObject is not defined|function ActiveXObject(){[native code]}|function ActiveXObject(){[native code]}|function ActiveXObject(){[native code]}|function ActiveXObject(){[native code]}|function ActiveXObject(){[native code]}|function ActiveXObject(){[native code]}|
|typeof ActiveXObject|--|--|--|undefined|function|function|function|function|function|
|!!ActiveXObject|--|--|--|false|true|true|true|true|true|
|"ActiveXObject" in window|--|--|--|true|true|true|true|true|true|

这里的情况比较一致，但其内心还是激荡不已：
+ IE11可被视为现代浏览器的分界线，跟较低的IE版本比较，IE11算是不是IE的IE。
+ 在IE11控制台输入ActiveXObject，和window.ActiveXObject都提示undefined，然而console.log的结果却是一个函数对象
+ 在IE11中使用ActiveXObject来区分IE已经不太靠谱了，勉强可以使用 `"ActiveXObject" in window` 来判断

综上，要判断一个浏览器是否支持Flash，可如下实现： （`/*@cc_on!@*/0` 也失效了）
    
    function isFlashSupported(){
        var swf,versionInfo;
        // mordern W3C browsers
        if(navigator.plugins && navigator.plugins.length > 0){
            swf = navigator.plugins['Shockwave Flash'];
            swf && (versionInfo = swf.description)
            //Plugin {0: MimeType, 1: MimeType, name: "Shockwave Flash", filename: "pepflashplayer.dll", description: "Shockwave Flash 20.0 r0", length: 2}
        }else{
            //older IE
            try{
                swf = new ActiveXObject('ShockwaveFlash.ShockwaveFlash')
                swf && (versionInfo = swf.GetVariable("$version"))
            }catch(e){}
        }
        
        if(swf){
            return true
        }else{
            return false;
        }
        
    }
    
在上述浏览器中可以获得正确的结果。    

## 参考资料
0. [Can I Use](http://caniuse.com/)
0. [W3Help兼容性](http://www.w3help.org/zh-cn/causes/index.html)
0. [W3Help主页](http://www.w3help.org/zh-cn/home/forum.html)
0. [document.all兼容性](http://www.w3help.org/zh-cn/causes/BX9002)
0. [JavaScript初学者建议：不要去管浏览器兼容](https://github.com/nimojs/blog/issues/1)
0. [很全面：Javascript和CSS浏览器兼容性方面经验总结](http://www.cuplayer.com/player/PlayerCode/Html5/2015/0714/2008.html)
0. [不同浏览器的Javascript兼容性总结](http://yaya-wiscom.iteye.com/blog/1140521)
0. [Javascript和CSS浏览器兼容总结](http://bbs.csdn.net/topics/370056500)
0. [Javascript兼容多种浏览器](http://www.cnblogs.com/dwjaissk/archive/2006/04/01/364329.html)
0. [Javascript不同浏览器差异及兼容方法](http://caibaojian.com/js-ie-different-from-firefox.html)
0. [【总结】IE和Firefox的Javascript兼容性总结](http://www.cnblogs.com/wiky/archive/2010/01/09/IE-and-Firefox-Javascript-compatibility.html)
0. [Javascript解决浏览器兼容性问题](http://developer.51cto.com/art/201104/255056.htm)
0. [12中JavaScript解决常见浏览器兼容的办法](http://blog.bingo929.com/12-javascript-browser-css-wrong.html)
0. [12中JavaScript解决常见浏览器兼容的办法](http://www.cnblogs.com/analyzer/archive/2008/12/14/1354694.html)