---
layout: post
title: "JavaScript 图片预览，上传预览"
categories: [开发技术]
tags: [JavaScript,图片预览,图片上传]
---

# 文件上传预览概述
在现在的Web开发中不可避免的会做一个图片预览的功能，比如在上传图片的情况下，一个很简单的办法就是讲图片上传至服务器之后，再将文件的URL返回回来，然后异步通过这个URL加载刚刚上传的图片，实现图片的预览，很明显的在这个过程中两次Web请求，一次发送文件，一次下载文件，到最后这个文件如果在客户端被删除（取消上传，弃用这次的上传），这整个过程都白费了。我们希望能够在图片上传之前就能进行图片的预览，这样就避免了不必要的网络请求和时间等待。下面的内容就围绕这个话题展开。

# 本地图片预览

## IE中的本地图片预览（以本地文件的形式访问）
在IE中能够很方便的实现本地网页的图片预览，IE中的`<input type="file" id="file_upload">`中的File对象中的value属性，存储的是要上传的文件的完整路径，在IE中只需要将这个完整路径作为一个Image对象的src属性，就能实现在这个Image对象中对这个上传的图片进行预览。

在IE中有如下方式:

    var url;
	var fileobj = document.getElementById(sourceId);
    fileobj.select();
    url = document.selection.createRange().text;

或者

    var url = document.getElementById(sourceId).value;

两种方式获取到的路径直接给img src 可以进行本地图片的预览（可以加上file:///协议，效果一样），这两种方式对IE7、8、9、10、11下有效。


## Firefox和Chrome的本地图片预览
在Firefox和Chrome中使用如下方式:

    var url = window.URL.createObjectURL(document.getElementById(sourceId).files[0]) 
将得到的值给img src 进行图片预览。可能还会看到如下的方式：

    var url = obj.files.item(0).getAsDataURL();

这种使用Firefox File对象的getAsDataURL的方式，已经在Firefox 7.0以后弃用，[Firefox DOM File]("https://developer.mozilla.org/en-US/docs/Web/API/File")，可能原因是在HTML5标准中有相关的定义。


# 服务端图片预览

## IE中的本地图片预览（以服务端URL的形式访问）
 上面提到的本地预览的方式，在以服务端URL的形式方式下没有预览的效果，需要使用如下滤镜的形式。 
   
    function PreviewImg(imgFile){ 
         var newPreview = document.getElementById("newPreview");     
         var imgDiv = document.createElement("div"); 
         document.body.appendChild(imgDiv); 
         imgDiv.style.width = "118px";     imgDiv.style.height = "127px"; 
         imgDiv.style.filter="progid:DXImageTransform.Microsoft.AlphaImageLoader(sizingMethod = scale)";    
         imgDiv.filters.item("DXImageTransform.Microsoft.AlphaImageLoader").src = imgFile.value; 
         newPreview.appendChild(imgDiv);  
    }

上面的实现可以在IE7、8、9下运行，在IE10、11下无效。

## Firefox和Chrome的本地图片预览
在Firefox和Chrome中使用如下方式:

    var url = window.URL.createObjectURL(document.getElementById(sourceId).files[0]) 
将得到的值给img src 进行图片预览。可能还会看到如下的方式：

    var url = obj.files.item(0).getAsDataURL();

这种使用Firefox File对象的getAsDataURL的方式，已经在Firefox 7.0以后弃用，[Firefox DOM File]("https://developer.mozilla.org/en-US/docs/Web/API/File")，可能原因是在HTML5标准中有相关的定义。

# 一个浏览器兼容的实现方案（兼容IE7、8、9、10、11，Firefox，Chrome）

## 基础
+ 在Chrome中，window.URL和window.webkitURL都存在
+ 在Firefox中，仅Window.URL存在
+ 在IE11（Edge），10中仅window.URL存在
+ 在IE7、8、9中不存在window.URL
+ 在IE中能通过FileObject 的value 属性获取文件全路径
+ 在Chrome中无法获取FileObject的全路径，得到的是一个假路径
+ 在Firefox中根本获取不到路径，得到的是一个文件名
+ 在IE7、8、9中无法获取到FileObject的files属性

## 实现
以前我们总是按照userAgent，通过判断IE，还是Chrome，还是Firefox，或者Safari、Opera等来对应支持代码，现在这种方式可能需要有所调整，File API是HTML5的规范特性，因此可以将浏览器大致先分为两个大类，一个是支持HTML5的一类，另一个是不支持的。

    <html xmlns="http://www.w3.org/1999/xhtml">
    
    <head id="Head1">
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
        <style type="text/css">
        .image_container {
            width: 48px;
            height: 48px;
            position: relative;
        }
        </style>
        <script type="text/javascript" src="jquery.js"></script>
        <script language="javascript">
        $(function() {
            $("#file_upload").change(function() {
                var $file = $(this);
                var fileObj = $file[0];
                var windowURL = window.URL || window.webkitURL;
                var dataURL;
                var $img = $("#preview"); 
    
                if(fileObj && fileObj.files && fileObj.files[0]){
                    dataURL = windowURL.createObjectURL(fileObj.files[0]);
                    $img.attr('src',dataURL);
                }else{
                    dataURL = $file.val();
                    
                    // $img.css("filter",'progid:DXImageTransform.Microsoft.AlphaImageLoader(sizingMethod = scale,src="' + dataURL + '")');
    
                    // var imgObj = document.getElementById("preview");
                    // imgObj.style.filter = "progid:DXImageTransform.Microsoft.AlphaImageLoader(sizingMethod=scale,src=\"" + dataURL + "\")";
                    // imgObj.style.width = "48px";
                    // imgObj.style.height = "48px";
    
                    var imgObj = document.getElementById("preview");
                    // 两个坑:
                    // 1、在设置filter属性时，元素必须已经存在在DOM树中，动态创建的Node，也需要在设置属性前加入到DOM中，先设置属性在加入，无效；
                    // 2、src属性需要像下面的方式添加，上面的两种方式添加，无效；
                    imgObj.style.filter = "progid:DXImageTransform.Microsoft.AlphaImageLoader(sizingMethod=scale)";
                    imgObj.filters.item("DXImageTransform.Microsoft.AlphaImageLoader").src = dataURL;
                    
                }
            });
        });
        </script>
    </head>
    <body>
        <div id="demo">
            <input id="file_upload" type="file" />
            <div class="image_container">
                <img id="preview" width="60" height="60">
            </div>
        </div>
    </body>
    </html>


## 参考资料
0. [JavaScript图片上传预览效果][0]
0. [兼容IE8、火狐的本地图片预览+等比例缩放][1]
1. [图片上传预览（IE使用滤镜）][2]
2. [[转]很简单的JS实现上传前预览图片(兼容IE8)][10]
3. [图片上传预览（支持IE，Chrome，Firefox）][14]
3. [JS魔法堂之实战：纯前端的图片预览][9]
3. [关于IE中CSS-filter滤镜小知识][3]
4. [BT9011: 只有 IE 支持 CSS Filter][4]
5. [css filter详解][5]
6. [精通CSS滤镜（filter）（实例解析）][6]
7. [不得不收藏的——IE中CSS-filter滤镜小知识大全][8]
8. [MSDN Filters and Transitions][11]
9. [Visual Filters and Transitions Reference][12]
8. [JS魔法堂：Data URI Scheme介绍][7]
9. [Using files from web applications][13]
10. [如何在web应用程序中使用文件][15]
11. [[译]JavaScript文件操作(4)-URL对象][16]
12. [window.url.createobjecturl 兼容多个浏览器（IE,google,360,Safari,firefox）][17]
13. [微软相关产品API参考][18]
13. [Windows Internet Explorer API 参考][19]
14. [Developer Guides (by IE version)][20]
15. [window.URL参考][21]
16. [Firefox API参考][22]

[0]: http://www.cnblogs.com/cloudgamer/archive/2009/12/22/ImagePreview.html "JavaScript图片上传预览效果"
[1]: http://it.oyksoft.com/post/974/ "兼容IE8、火狐的本地图片预览+等比例缩放"
[2]: http://blog.163.com/xsjwan@126/blog/static/8371818720146744031771/ "图片上传预览（IE使用滤镜）"
[3]: http://www.qianduan.net/guan-yu-ie-zhong-css-filter-lv-jing-xiao-zhi-shi/ "关于IE中CSS-filter滤镜小知识"
[4]: http://w3help.org/zh-cn/causes/BT9011 "BT9011: 只有 IE 支持 CSS Filter"
[5]: http://zzstudy.offcn.com/archives/8877 "css filter详解"
[6]: http://www.cnblogs.com/shiyangxt/archive/2008/11/16/1334633.html "精通CSS滤镜（filter）（实例解析）"
[7]: http://www.cnblogs.com/fsjohnhuang/p/3903688.html "JS魔法堂：Data URI Scheme介绍"
[8]: http://segmentfault.com/a/1190000002433305 "不得不收藏的——IE中CSS-filter滤镜小知识大全"
[9]: http://www.cnblogs.com/fsjohnhuang/p/3925827.html "JS魔法堂之实战：纯前端的图片预览"
[10]: http://www.cnblogs.com/seasons1987/archive/2012/11/16/2773548.html "[转]很简单的JS实现上传前预览图片(兼容IE8)"
[11]: https://msdn.microsoft.com/en-us/library/ms532849(VS.85).aspx "MSDN Filters and Transitions"
[12]: https://msdn.microsoft.com/zh-cn/library/ms532853(v=VS.85).aspx "Visual Filters and Transitions Reference"
[13]: https://developer.mozilla.org/en-US/docs/Using_files_from_web_applications "Using files from web applications"
[14]: http://201110113545.iteye.com/blog/2087215 "图片上传预览（支持IE，Chrome，Firefox）"
[15]: https://developer.mozilla.org/zh-CN/docs/Using_files_from_web_applications "如何在web应用程序中使用文件"
[16]: http://www.iunbug.com/archives/2012/06/05/254.html "[译]JavaScript文件操作(4)-URL对象"
[17]: http://blog.csdn.net/ybb350680013/article/details/24006535?utm_source=tuicool "window.url.createobjecturl 兼容多个浏览器（IE,google,360,Safari,firefox）"
[18]: https://msdn.microsoft.com/library "微软相关产品API参考"
[19]: https://msdn.microsoft.com/library/hh828809 "Windows Internet Explorer API 参考"
[20]: https://msdn.microsoft.com/en-us/library/dn997182(v=vs.85).aspx "Developer Guides (by IE version)"
[21]: https://developer.mozilla.org/en-US/docs/Web/API/Window/URL "window.URL参考"
[22]: https://developer.mozilla.org/zh-CN/docs/Web "Firefox API参考"