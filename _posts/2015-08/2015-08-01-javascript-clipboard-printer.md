---
layout: post
title: "跨浏览器JavaScript剪贴板 打印机操作 生成二维码"
categories: [开发技术,Web开发,JavaScript]
tags: [JavaScript,打印机,剪贴板,复制,粘贴,部分打印,二维码]

---

## 剪贴板操作

使用脚本访问剪贴板会导致安全性问题，比如恶意脚本写入剪贴板内容，进行剪贴板投毒 或者 读取剪贴板内容发送到远程，导致用户隐私信息泄露

因此各大浏览器都对脚本访问剪贴板做了不同的限制，比如必须是用户操作（点击，菜单等）的响应才行。

目前有的较通用的剪贴板写入方案是利用透明的Flash，获取用户操作并通过浏览器Flash插件的方式访问系统剪贴板,用Flash插件来兼容低版本浏览器。[参考](http://zeroclipboard.org/)

如果只需要兼容高版本浏览器，可[参考](http://zenorocha.github.io/clipboard.js/)，利用新的浏览器API来实现。


### 复制到剪贴板

使用jquery-zclip 或者直接使用ZeroClipBoard 实现内容的复制。

一个完整的Demo如下：	

	<!DOCTYPE html>
	<html>
	<head>
	<title>ZeroClipboard Test</title>
	<meta charset="utf-8">
	<style type="text/css">
	.line{margin-bottom:20px;}
	/* 复制提示 */
	.copy-tips{position:fixed;z-index:999;bottom:50%;left:50%;margin:0 0 -20px -80px;background-color:rgba(0, 0, 0, 0.2);filter:progid:DXImageTransform.Microsoft.Gradient(startColorstr=#30000000, endColorstr=#30000000);padding:6px;}
	.copy-tips-wrap{padding:10px 20px;text-align:center;border:1px solid #F4D9A6;background-color:#FFFDEE;font-size:14px;}
	</style>
	<script type="text/javascript" src="jquery-1.10.1.js"></script>
	<script type="text/javascript" src="jquery.zclip.js"></script>
	</head>
	<body>
	<div class="line">
		<h2>demo1 点击复制当前文本</h2>
		<a href="#none" class="copy">点击复制我</a>
	</div>
	<div class="line">
		<h2>demo2 点击复制表单中的文本</h2>
		<a href="#none" class="copy-input">点击复制单中的文本</a>
		<input type="text" class="input" value="输入要复制的内容" />
	</div>
	</body>
	</html>
	<script type="text/javascript">
	$(document).ready(function(){
	/* 定义所有class为copy标签，点击后可复制被点击对象的文本 */
		$(".copy").zclip({
			path: "ZeroClipboard.swf",
			copy: function(){
			return $(this).text();
			},
			beforeCopy:function(){/* 按住鼠标时的操作 */
				$(this).css("color","orange");
			},
			afterCopy:function(){/* 复制成功后的操作 */
				var $copysuc = $("<div class='copy-tips'><div class='copy-tips-wrap'>? 复制成功</div></div>");
				$("body").find(".copy-tips").remove().end().append($copysuc);
				$(".copy-tips").fadeOut(3000);
			}
		});
	/* 定义所有class为copy-input标签，点击后可复制class为input的文本 */
		$(".copy-input").zclip({
			path: "ZeroClipboard.swf",
			copy: function(){
			return $(this).parent().find(".input").val();
			},
			afterCopy:function(){/* 复制成功后的操作 */
				var $copysuc = $("<div class='copy-tips'><div class='copy-tips-wrap'>? 复制成功</div></div>");
				$("body").find(".copy-tips").remove().end().append($copysuc);
				$(".copy-tips").fadeOut(3000);
			}
		});
	});
	</script>

### 参考
0. [zeroclipboard官网](http://zeroclipboard.org/)
0. [zeroclipboard github](https://github.com/zeroclipboard/zeroclipboard)
0. [jquery zclip github](https://github.com/patricklodder/jquery-zclip)
0. [clipboard.js](http://zenorocha.github.io/clipboard.js/)
0. [Javascript实现复制到剪贴板，兼容所有浏览器](http://www.cnblogs.com/PeunZhang/p/3324727.html)

### 粘贴剪贴板内容



## 打印机操作

可以打印页面部分内容，可以选择弹层或者IFrame的方式，直接查看其中的例子即可[参考](https://github.com/RitsC/PrintArea)


### 参考
0. [jquery 插件](http://plugins.jquery.com/PrintArea/)
0. [jquery printarea github](https://github.com/RitsC/PrintArea)

## JavaScript 生成二维码

采用Table布局来兼容低版本浏览器，也可以使用Canvas进行二维码绘制。
直接[参考](https://github.com/jeromeetienne/jquery-qrcode)中的示例即可


#### 参考
0. [jquery qrcode](https://larsjung.de/jquery-qrcode/)
0. [jquery qrcode github](https://github.com/jeromeetienne/jquery-qrcode)

## 其他参考文档
0. [The Definitive Guide to Copying and Pasting in JavaScript](https://www.lucidchart.com/techblog/2014/12/02/definitive-guide-copying-pasting-javascript/)
0. [Accessing the System Clipboard with JavaScript – A Holy Grail?](https://brooknovak.wordpress.com/2009/07/28/accessing-the-system-clipboard-with-javascript/)
0. [JS获取剪贴板内容和设置剪贴板内容](http://blog.sina.com.cn/s/blog_7ccfeb360100xki1.html)
0. [JavaScript get clipboard data on paste event (Cross browser)](http://stackoverflow.com/questions/2176861/javascript-get-clipboard-data-on-paste-event-cross-browser)
0. [JavaScript 如何获得粘贴的内容](http://www.zhihu.com/question/20747877)
0. [clipboard.js](http://zenorocha.github.io/clipboard.js/)
0. [JavaScript实现复制粘贴功能](http://blog.csdn.net/zenson_g/article/details/49510617)
0. [实现JS复制、粘贴，Chrome/Firefox下可用](http://blog.csdn.net/hackersaillen/article/details/45694181)
0. [Clipboard cut, copy and paste with JavaScript](http://www.geekpedia.com/tutorial126_Clipboard-cut-copy-and-paste-with-JavaScript.html)
0. [Accessing the System Clipboard with JavaScript](https://brooknovak.wordpress.com/2009/07/28/accessing-the-system-clipboard-with-javascript/)
0. [Javascript 在网页中实现读取剪贴板截图功能](http://www.jb51.net/article/50766.htm)
0. [How do I copy to the clipboard in JavaScript?](http://stackoverflow.com/questions/400212/how-do-i-copy-to-the-clipboard-in-javascript)






### 临时其他

    function getSelectedText(){
        if(window.getSelection){
            return window.getSelection().toString()
        }else if(document.selection){
            return document.selection.createRange().text
        }
       
    }