---
layout: post
title: "JavaScript 点击下载 下载页面文件"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,点击下载]

---



## 哪些方法可以用

在翻看了众多页面之后，可以列出一些可以尝试的点：

### 直接打开URL window.open


### HTML5 A 标签 的download属性
    
可以如下实现
    
    <a href="/url/to/file/path" download="downloadName">点击下载</a>


### 使用封装的JavaScript库




## 参考资料
0. [Save/download data generated in JavaScript](http://hackworthy.blogspot.co.at/2012/05/savedownload-data-generated-in.html)
0. [JavaScript Local I/O tests](http://wizard.ae.krakow.pl/~jb/localio.html)
0. [Force Download an Image Using Javascript](http://stackoverflow.com/questions/6796974/force-download-an-image-using-javascript)
0. [How to trigger a file download when clicking an html button or javascript](http://stackoverflow.com/questions/11620698/how-to-trigger-a-file-download-when-clicking-an-html-button-or-javascript)
0. [Using HTML5/Javascript to generate and save a file](http://stackoverflow.com/questions/2897619/using-html5-javascript-to-generate-and-save-a-file)
0. [在浏览器端用JS创建和下载文件](http://www.alloyteam.com/2014/01/use-js-file-download/)
0. [JavaScript多文件下载](http://www.cnblogs.com/hustskyking/p/multiple-download-with-javascript.html)
0. [JavaScript多文件下载](http://div.io/topic/765)
0. [Starting file download with Javascript ](http://thezedienblog.blogspot.jp/2013/05/starting-file-download-with-javascript.html)
0. [什么是data URI scheme及如何使用data URI scheme](http://sjolzy.cn/What-is-the-data-URI-scheme-and-how-to-use-the-data-URI-scheme.html)
0. [Data URI和MHTML完整解决所有浏览器](http://www.jb51.net/web/20368.html)
0. [DataURL在Web浏览器中的兼容性总结](http://blog.csdn.net/cuixiping/article/details/14215947)
0. [Download data url file](http://stackoverflow.com/questions/3916191/download-data-url-file)
0. [JS魔法堂：Data URI Scheme介绍](http://www.cnblogs.com/fsjohnhuang/p/3903688.html)
0. [How to write a file / give it to the user](https://stuk.github.io/jszip/documentation/howto/write_zip.html)
0. [HTML5 Cross Browser Polyfills](https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-browser-Polyfills)
0. [How to force save as dialog box in firefox besides changing headers?](http://stackoverflow.com/questions/833068/how-to-force-save-as-dialog-box-in-firefox-besides-changing-headers)
0. [Better approach to download file in JavaScript](http://pixelscommander.com/en/javascript/javascript-file-download-ignore-content-type/)
0. [Does execCommand SaveAs work in Firefox?](http://stackoverflow.com/questions/833015/does-execcommand-saveas-work-in-firefox/13696029#13696029)
0. [Saving generated files on the client-side](https://developers.google.com/web/updates/2011/08/Saving-generated-files-on-the-client-side)
0. [Save to Drive Button](https://developers.google.com/drive/v2/web/savetodrive)
0. [通过js保存图片到本地](http://blog.csdn.net/zajin/article/details/17113501)

### HTTP Content Disposition
0. [Content-Disposition header - forcing SaveAs in browsers](http://www.jtricks.com/bits/content_disposition.html)
0. [Content-Disposition](http://www.w3.org/Protocols/rfc2616/rfc2616-sec19.html#sec19.5.1)

### HTML5 A 标签
0. [W3C HTML5 A](http://www.w3.org/TR/html51/semantics.html#the-a-element)
0. [HTML: The Living Standard](https://developers.whatwg.org/links.html#downloading-resources)
0. [HTML5 download attribute with JavaScript](http://www.2ality.com/2012/04/html5-download.html)
0. [Better approach to download file in JavaScript](http://pixelscommander.com/en/javascript/javascript-file-download-ignore-content-type/)
0. [Save files on disk using JavaScript](http://muaz-khan.blogspot.jp/2012/10/save-files-on-disk-using-javascript-or.html)
0. [Downloading resources in HTML5](https://developers.google.com/web/updates/2011/08/Downloading-resources-in-HTML5-a-download)
0. [变通方法实现保存文件](http://ucren.com/demos/savefile/index.html)

### 下载库参考 HTML5
0. [Blob.js](https://github.com/eligrey/Blob.js)
0. [canvas-toBlob.js](https://github.com/eligrey/canvas-toBlob.js)
0. [FileServer.js](https://github.com/eligrey/FileSaver.js)
0. [Downloadify](https://github.com/dcneiner/Downloadify)
0. [Downloadify Example](http://pixelgraphics.us/downloadify/test.html)
0. [jquery.fileDownload](https://github.com/johnculviner/jquery.fileDownload)
0. [jQuery File Download Plugin for Ajax](http://johnculviner.com/jquery-file-download-plugin-for-ajax-like-feature-rich-file-downloads/)   