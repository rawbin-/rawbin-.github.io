--- 
layout: post
title: "如何阅读混淆加密过的JavaScript代码"
categories: [开发技术, Web前端]
tags: [JavaScript,加密,混淆]

---

### 代码混淆
+ 删除注释和多于的空格换行等分隔符
+ 变量和函数名称替换为较短的a,b,c等
+ 压缩代码体积减少流量和提高页面加载性能
+ 降低代码可读性，在一定程度上保护代码、增加攻击难度、提高安全性

### 代码加密
+ 通过字符串分隔、替换、拼接等方式生成JavaScript源代码文本
+ 通过将代码转换为十进制、十六进制，进行编码等方式来进行加密
+ 通过eval和Function来执行生成的JavaScript源代码文本
+ 最终始终会生成能被JavaScript引擎解析的JavaScript代码
+ 最终始终会生成能被JavaScript引擎解析的JavaScript代码
+ 最终始终会生成能被JavaScript引擎解析的JavaScript代码
+ 重要的事情说三遍

### 解决办法

#### 代码混淆
+ 直接通过各大浏览器的　“{}” 代码美化工具进行格式化
+ 通过各种在线的代码美化、格式化工具进行格式化

#### 代码加密
+ 直接避开加密算法，在代码生成之后在浏览器调试工具中中进行获取
+ 研究下代码加密方法，然后在生成后浏览器调试工具中进行获取
+ 写解密方法，获取代码

### 实例

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

先来看一段拿到的eval code：

	if($cfg.eCont) {
		$dp = {
		};
		for(var p in $pdp) {
			if(typeof $pdp[p] == "object") {
				$dp[p] = {
				};
				for(var pp in $pdp[p]) {
					$dp[p][pp] = $pdp[p][pp];
				}
			} else {
				$dp[p] = $pdp[p];
			}
		}
	} else {
		$dp = $pdp;
	}
	for(p in $cfg) {
		$dp[p] = $cfg[p];
	}
	var $c;
再来看一段eval 中函数的第一个参数：

	'l($4o.44){$f={};1b(q p 4r $2s)l(6p $2s[p]=="6o"){$f[p]={};1b(q 4G 4r $2s[p])$f[p][4G]=$2s[p][4G]}t $f[p]=$2s[p]}t $f=$2s;1b(p 4r $4o)$f[p]=$4o[p];q $c;

大致可以看出，这里面就是做了一个字符串映射和替换：
	
	if-->l
	$dp-->$f;
	for-->1b;
	var-->q;
	等等

然后再来看看这个加密函数：

	eval(function(p, a, c, k, e, d) {
		e = function(c) {
			return (c < a ? "" : e(parseInt(c / a))) + ((c = c % a) > 35 ? String.fromCharCode(c + 29) : c.toString(36))
		}
		;
		if (!''.replace(/^/, String)) {
			while (c--)
				d[e(c)] = k[c] || e(c);
			k = [function(e) {
				return d[e]
			}
			];
			e = function() {
				return '\\w+'
			}
			;
			c = 1;
		}
		;while (c--)
			if (k[c])
				p = p.replace(new RegExp('\\b' + e(c) + '\\b','g'), k[c]);
		return p;
	}(codeString,62,575,keywordString.split('|'),0,{}));

参数对应：

	codeString-->p   //这里就是加密过的代码
	62---------->a   
	575--------->c   //猜这里是一个执行的限度，大小跟关键字列表的大小相同
	keywords---->k   //代码中的关键字列表
	0----------->e   //会直接被重写，不论传什么值效果都一样，或者说这个值没有意义
	{}---------->d   //存放临时结果的字典
	
再回过头去看代码，并除掉一些冗余代码之后：

	eval(function(p, a, c, k, e, d) {
		//重写e, 不论传什么值进来结果都一样
		e = function(c) {
			return (c < a ? "" : e(parseInt(c / a))) + ((c = c % a) > 35 ? String.fromCharCode(c + 29) : c.toString(36))
		}
		
		// 有值就取值，没有值就算一个值,构成一个完整的字典
		while (c--)
			d[e(c)] = k[c] || e(c);
			
		//把对应位置上的关键字替换回来	
		p = p.replace(new RegExp('\\b\\w+\\b','g'), function(e) {
			return d[e]
		});
		return p;
	}(codeString,62,575,keywordString.split('|'),0,{}));	
	

还没解决的一个问题，这个62 是什么意思？

等我收拾好了Chrome和Firefox的调试工具看看再说，调试工具失效了。



### 参考文档
0. [使用 estools 辅助反混淆 Javascript](http://blog.knownsec.com/2015/08/use-estools-aid-deobfuscate-javascript/)
0. [JS混淆还原](http://www.ralap.net/it/web/7-js-confusion-restore.html)
0. [JS混淆代码还原－JS反混淆：利用JS进行赋值实现](http://www.cnblogs.com/vnii/archive/2011/12/14/2287504.html)
0. [JAVASCRIPT加密解密终级指南](http://www.williamlong.info/archives/242.html)
0. [如何去除My97DatePicker控件上右键弹出官网的链接—如何debug混淆过的代码](http://www.tzwhx.com/NewShow/newBodyShow/%E6%8E%A7%E4%BB%B6_30010.html)
0. [My97DatePicker Demo](http://www.my97.net/dp/demo/index.htm)
0. [My97DatePicker 官网](http://www.my97.net/)