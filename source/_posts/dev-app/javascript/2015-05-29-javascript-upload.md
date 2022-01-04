---
layout: post
title: "JavaScript Ajax jQuery 文件上传 异步上传"
categories: [开发技术]
tags: [JavaScript, Ajax, jQuery, 文件上传, 异步上传]
---
## 1 基本原理

### 1.1 定制UI

label 标签下面嵌套`<input type="file">`点击label会自动打开文件浏览框，这个是W3C的约定，点击标签触发跟标签关联的第一个表单元素，或者标签内嵌的第一个表单元素。参考：[W3School][15],[W3C][16]（这个英文更容易看懂，不信试试看，^_^）。

这样我们就可以在Label外面包裹样式元素，然后我们需要把原生的文件上传控件搞掉，最简单的方式是隐藏。但是这个在万恶的低版本IE中（IE8）下是不行的，我们可以将这个控件变透明，然后。。。，参考：[IE8 Label for input file 无效][17];当然也可以使用Flash来解决这个问题，plupload就是这么做的,采用事件传递的形式，给定一个触发元素，然后自动生成一个跟这个触发元素关联的file元素，点击这个触发元素的时候执行file的动作。

要解决的问题如下：
+ 定义自己的btn样式
+ 将file input 变透明；opacity:0;filter:alpha(opacity=0) IE8;
+ 将file input 叠于文字上方
+ 将file input 放大，并将原生的文件上传按钮对准，btn容器，这里放大然后把多的切掉，overflow：hidden；



          <style type="text/css">
            .btn_container{
                position:relative;
                width:120px;
                height:40px;
                line-height:40px;
                color: #563d7c;
                background-color: transparent;
                border: 2px solid #563d7c;
                border-radius: 6px;
                overflow:hidden;
                text-align:center;
            }
        
            .file_input{
                position:absolute;
                opacity:0;
                filter:alpha(opacity=0);
                z-index:1;
                font-size:300%;
                left:-550px;
            }
        
            .btn{
                padding: 10px 16px;
                font-size: 18px;
            }
        </style>
        <div class="btn_container">
            <label>
                <input type="file" class="file_input"><span class="btn">点选文件</span>
            </label>
        </div>


### 1.2 自动上传
监听`<input type="file">`的change事件，如果改变，则触发文件上传。
    

### 1.3 异步HTTP上传
使用`enctype='multipart/form-data'`的表单提交，传输`content-type:text/plain`的字节流数据。使用隐藏的iframe来提交表单，实现视区内页面无刷新。

## 2 组件推荐  
在这二十来个上传组件或插件中，根据可定制UI，支持度较广（仅支持Flash或HTML5的就pass），网站能打开，有能上手的实例等条件过滤掉了一些像ajaxfileupload，uploadify等还不错的资源，留下下面这些个。

### 2.1 plupload  
自动切换多种方式的支持，有国际化的支持，支持自定义容器。

#### 2.1.1 参考资料  
0. [plupload 官网][5]
1. [plupload Github][4]
2. [前端上传组件Plupload使用指南][6]
3. [Ajax多文件上传组件plupload（推荐）][13]
4. [plupload 实例][18]

### 2.2 jquery file upload
样式不错，引入的库较多，只支持绑定input file。

#### 2.2.1 参考资料
0. [jQuery-File-Upload][8]
1. [jQuery File Upload Demo][9]

### 2.3 jQuery Ajax File Uploader / uploader
有进度，有预览，不支持IE8；

### 2.4 参考资料
0. [JQuery File Uploader][10]

### 2.5 JSAjaxFileUploader
有进度，有预览，不支持IE8；

#### 2.5.1 参考资料
0. [JSAjaxFileUploader][11]
1. [JSAjaxFileUploader Demo][12]

## 3 参考资料：
0. [文件上传的渐进式增强][19]
0. [前端JavaScript上传组件插件][0]
0. [22个很棒的jQuery文件上传插件][1]
1. [15 个最好的 jQuery 文件上传插件][2]
2. [7 款 JavaScript 的 Ajax 文件上传插件][3]
3. [开源中国jQuery文件上传插件][20]

[0]: http://ajax.open-open.com/Upload.htm "前端JavaScript上传组件插件"
[1]: http://www.csdn.net/article/2011-08-04/302659 "22个很棒的jQuery文件上传插件"
[2]: http://www.open-open.com/news/view/6dcd5c "15 个最好的 jQuery 文件上传插件"
[3]: http://www.oschina.net/news/28767/7-javascript-ajax-file-upload-plugins "7 款 JavaScript 的 Ajax 文件上传插件"
[4]: https://github.com/moxiecode/plupload "plupload Github"
[5]: http://www.plupload.com/ "pupload 官网"
[6]: http://www.cnblogs.com/2050/p/3913184.html "前端上传组件Plupload使用指南"
[7]: http://chaping.github.io/plupload/doc/ "plupload文档翻译"
[8]: https://github.com/blueimp/jQuery-File-Upload "jQuery-File-Upload"
[9]: http://blueimp.github.io/jQuery-File-Upload/basic.html "jQuery File Upload Demo"
[10]: https://github.com/danielm/uploader "jQuery Ajax File Uploader"
[11]: https://github.com/JSEver/JSAjaxFileUploader "JSAjaxFileUploader"
[12]: http://jseverdemo.0fees.net/JSAjaxFileUploader/Demo.html "JSAjaxFileUploader Demo"
[13]: http://blog.sina.com.cn/s/blog_667ac0360102echn.html "Ajax多文件上传组件plupload（推荐）"
[14]: http://blog.csdn.net/it_man/article/details/43800957 "jquery插件--ajaxfileupload.js上传文件原理分析"
[15]: http://www.w3school.com.cn/tags/tag_label.asp "W3School Label 参考"
[16]: http://www.w3.org/TR/2014/REC-html5-20141028/forms.html#the-label-element "W3C Label 参考手册"
[17]: http://lililucky1211.iteye.com/blog/1916129 "IE8 Label for input file 无效"
[18]: http://chaping.github.io/plupload/demo/index4.html "plupload 实例"
[19]: http://www.ruanyifeng.com/blog/2012/08/file_upload.html "文件上传的渐进式增强"
[20]: http://www.oschina.net/project/tag/356/jquery-file-upload "开源中国jQuery文件上传插件"