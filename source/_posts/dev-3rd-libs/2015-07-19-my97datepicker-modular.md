---
layout: post
title: "让My97DatePicker支持modular编译"
categories: [开发技术,Web开发]
tags: [My97Datepicker,Modular,模块化编译,JavaScript,日期时间选择器]

---

[My97DatePicker](http://www.my97.net/dp/demo/index.htm) 是一个老牌的日期时间选择器，简约而不简单。

但免费使用的源代码是经过压缩和加密过的。

文件结构清晰
My97DatePicker   
----WdatePicker.js //主文件   
----calendar.js //日期时间选择器封装   
----lang   
--------zh-CN.js   
--------en.js   
--------zh-TW.js   

简单打开文件查看：

+ WdatePicker中除了配置部分，均经过混淆
+ calendar.js 进行过加密，使用eval执行
+ 语言包未进行过压缩或者加密

使用浏览器打开demo页面，点击并选择一个日期，在调试工具中可以看到加载的这些资源。   
使用调试工具中的代码美化工具可以将WdatePicker.js 进行格式美化，便于调试和阅读。   
但在calendar的代码，只是一行eval代码，在Chrome和Firefox下无法查看详细代码。   
但在IE8-11、Edge中可以通过开发人员工具查看资源动态代码中的 eval code并进行代码美化，。

使用状态如下：

+ Chrome中无法查看eval 代码；
+ Firefox Firebug插件能查看eval代码，无法进行代码格式化，可以拷出来在编辑器或者在线代码格式化工具中进行格式化；
+ IE开发人员工具中可以查看eval code并进行代码格式化； 

拿到了eval code之后，基本的代码逻辑就有了，没有混淆过的代码。

直接用eval code 替换calendar.js的内容，其实本身代码效果就是一样的。

修改部分如下：
+ WdatePicker.js 代码末尾加

		window.$dp = $dp;
		window.WdatePicker = WdatePicker;
		
+ calendar.js 代码末尾加

	window.My97DP = My97DP;
    window.$c = $c;		
	
+ lang/zh-CN.js 代码末尾加

	window.$lang = $lang
	
原本以为这样将每个模块对外输出的对象挂到window上就行了，结果发现还是找不到$c这个对象。将$c相关的代码抽取出来效果如下“
		
		(funciton(){
			var $c;	
			function My97DP() {
				$c = this;
				//window.$c = $c = this;
				......
			}
			window.My97DP = My97DP;
			window.$c = $c;
		}());		
		
		new My97DP();
		console.log(window.$c)
		
在`My97DP`函数中，改变的是它所在的闭包环境中的`$c`的值，在调用`new My97DP()`执行`$c = this`后`$c`会指向`My97DP`，然而并不改变`windows.$c`的值。

因为在外层IIFE执行后，`window.$c`的值为未初始化的`$c`的值`undefined`。 这是基本数据类型值传递的过程。

最终的解决办法就是被注释的那行代码`window.$c = $c = this;`