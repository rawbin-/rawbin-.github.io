---
layout: post
title: "JavaScript正则表达式及其应用"
categories: [Web开发,前端开发,开发技术,JavaScript]
tags: [JavaScript,正则表达式]

---

正则表达式是一套文本处理的规则，最初在Perl语言中实现，其他各种编程语言的实现或多或少有一些差异，所以需要看具体的语言中的实现和支持情况。

比如Perl语言中的如下正则语法在JavaScript中不被支持：

	\a \e \l \u \L \U \E \Q \A \Z \z \G
	(?<=  (?<| (?# (?<xxx>

# JavaScript 正则表达式语法

JavaScript正则表达式用于字符串的匹配，分隔，查找，替换。

String对象的match,replace,search,split

RegExp对象的test,exec

# JavaScript正则表达式语法

## 字符匹配模式

|表达式|含义|示例|
|-----|----|----|
|字母和数字字符|原样匹配|abc123 匹配 abc123|
|^|匹配字符串的开头位置||
|$|匹配字符串的结尾位置||
|\b|匹配单词的边界||
|\B|匹配非单词的边界||
|(?=p)|接下来的匹配需要匹配p，前面的匹配项才算匹配||
|(?!p)|接下来的匹配不能匹配p，前面的匹配项才算匹配||
|[...]|括号中字符列表中的任意一个||
|[^...]|不是括号中字符列表中的任意一个||
|\w|任何ASCII字符组成的单词，等价于[a-zA-Z0-9_]||
|\W|任何不是ASCII字符组成的单词，等价于[a-zA-Z0-9_]||
|\d|任何ASCII数字，等价于[0-9]||
|\D|任何非ASCII数字，等价于[^0-9]||
|\s|任何Unicode空白符，等价于[\t\r\n\0xB\f]||
|\S|任何非Unicode空白符的字符[^\t\r\n\0xB\f]||
|.|匹配除换行符和其他Unicode行终止符之外的任意字符||
|{n}|匹配前面的模式n次||
|{n,}|匹配前面的模式至少n次||
|{n,m}|匹配前面的模式至少n次，至多m次||
|?|匹配前面的模式0次或者一次，等价于{0,1}||
|*|匹配前面的模式0次或者多次，等价于{0,}||
|+|匹配前面的模式至少一次，等价于{1,}||
|\||选择匹配左边的表达式或者右边的表达式||
|(...)|将括号内的匹配合为一个单元，并按左括号出现的顺序进行组合编号，可在后续的表达式中使用\n 来引用第n个匹配，n从1开始||
|(?:...)|只对括号内的匹配进行组合，但不进行单元编号，不能后续引用||
|\o |NUL \u0000||
|\t |制表符 \u0009||
|\n |换行符 \u000A||
|\v |垂直制表符 \u000B||
|\f |换页符 \u000C||
|\r |回车符 \u000D||
|[\b]|匹配退格键||
|\xnn |由十六进制nn 指定的拉丁字符| \x0A 等价于 \n|
|\uxxxx |由十六进制xxxx指定的Unicode字符|\u000D 等价于 \r|
|\cX |控制字符^X 也就是Ctrl+X|如vi编辑器中\cJ 等价于换行符\n|

## 匹配模式修饰
|修饰符|含义|示例|
|------|---|----|
|i |匹配模式不区分大小写||
|g |找到所有的匹配，而不是第一个||
|m |多行匹配模式，^匹配一会的开头和字符串的开头，$匹配行的结束和字符串的结束||


# 正则表达式方法

## String 对象

### 对象方法

#### stringObj.match(regexp)
+ 参数被当做一个正则表达式
+ 如果不包含g属性，则执行一次匹配，如果没找到匹配则返回null；如果有匹配，返回一个数组，第一个元素为匹配字符串，后面的元素依次为分组的结果，数组包含两个属性，index代表匹配文本在string中的开始位置，input则是对该string的引用。
+ 如果包含了g属性，则执行一次全局匹配，在string中查找所有的匹配的子串，如果没有找到则返回null，如果找到则返回一个数组，这时候数组的内容为所有的匹配字符串的结果，此时数组不包括正则分组的信息，也没有index和input属性。

		"abc123abcdef789xyz".match(/((\d+)([a-z]+))/)
		//["123abcdef", "123abcdef", "123", "abcdef"]
		"abc123abcdef789xyz".match(/((\d+)([a-z]+))/g)
		//["123abcdef", "789xyz"]
		
		var result = "abc123abcdef789xyz".match(/((\d+)([a-z]+))/);
		console.log(result)        //["123abcdef", "123abcdef", "123", "abcdef"]
		console.log(result.index)  //3
		console.log(result.input)  //abc123abcdef789xyz
		var result = "abc123abcdef789xyz".match(/((\d+)([a-z]+))/g);
		console.log(result)        //["123abcdef", "789xyz"]
		console.log(result.index)  //undefined
		console.log(result.input)  //undefined

#### stringObj.replace(regexp,replacement)
+ 如果查询参数是一个字符串，则只替换字符串对应的文本；
+ 如果查询参数是一个正则表达式直接量或者正则表达式对象，则进行正则替换。
+ 如果查询参数中正则表达式包含g参数，则在字符串中替换所有的匹配，否则替换第一个匹配。
+ 替换参数如果是一个字符串，则进行直接替换，如果是一个函数，可自定义替换规则，函数返回值即为替换文本，函数参数列表为（$&,$1,$2...index,input）,前面为匹配的文本和每个分组的匹配，后面为匹配的起始位置，最后为输入字符串。
+ 如果查询参数是正则表达式，则有特殊的字符串引用可以使用：

|符号|含义|  | 
|---|----|--|
|$n|查询正则中第n个分组的匹配结果||
|$&|整个查询正则的匹配结果||
|$`|匹配子串左边的文本||
|$'|匹配子串右边的文本||
|$$|美元符号||
||||

		"abc123abcdef789xyz".replace(/((\d+)([a-z]+))/,"#")
		//abc#789xyz
		"abc123abcdef789xyz".replace(/((\d+)([a-z]+))/g,"#")
		//abc##
		
		"abc123abcdef789xyz".replace(/((\d+)([a-z]+))/,"#$1,$2,$3#")
		//abc#123abcdef,123,abcdef#789xyz

		"abc123abcdef789xyz".replace(/((\d+)([a-z]+))/g,"#$1,$2,$3#")
		//abc#123abcdef,123,abcdef##789xyz,789,xyz#

		"abc123abcdef789xyz".replace(/((\d+)([a-z]+))/,function(match,group1,group2,group3,group4){
			console.log(arguments)
			return "#"
		})
		//["123abcdef", "123abcdef", "123", "abcdef", 3, "abc123abcdef789xyz"]
		//abc#789xyz

		"abc123abcdef789xyz".replace(/((\d+)([a-z]+))/g,function(match,group1,group2,group3,group4){
			console.log(arguments)
			return "#"
		})
		//["123abcdef", "123abcdef", "123", "abcdef", 3, "abc123abcdef789xyz"]
		//["789xyz", "789xyz", "789", "xyz", 12, "abc123abcdef789xyz"]
		//abc##


#### stringObj.search(regexp)
+ 此方法按照查询参数在目标字符串中查询匹配，并返回第一个匹配的位置，如果没找到返回-1；
+ 此方法中正则模式的g参数会被忽略，同时正则对象的lastIndex属性也会被忽略，总是返回目标文本中的第一个匹配位置。

		"abc123abcdef789xyz".search(/((\d+)([a-z]+))/)
		//3
	

#### stringObj.split(delimiter,limit)
+ 在目标字符串中匹配所有delimiter，通过delimiter 的匹配切分字符串 ，返回结果为delimiter之间的部分；
+ 如果delimiter匹配字符串的开头，则返回结果的第一个字符串为空串；
+ 如果没有指定delimiter，则返回的结果是只包含输入字符串一个元素的数组；
+ 如果指定delimiter为空字符串或者匹配控制串的模式，则返回结果是输入字符串中的每个字符分开组成的数组（注意：第一个字符之前和最后一个字符之后的空字符不匹配）；
+ 如果指定delimiter是一个包含分组的正则表达式，则匹配这些分组的子串会包含中返回值数组中。
	
		"123abcdef789xyz".split(/((\d+)([a-z]+))/)
		//匹配开头第一个返回值为空，如果带有分组，一次返回每个分组
		//["", "123abcdef", "123", "abcdef", "", "789xyz", "789", "xyz", ""]

## RegExp对象

通过正则表达式字面量/pattern/ 或者new RegExp(pattern,attributes)的方式获得正则表达式对象。

### 对象属性
+ global 是否具有g属性
+ ignoreCase 是否具有i属性
+ lastIndex 上一次匹配的位置，由于在一个字符串内多次匹配
+ multiline 是否具有m属性
+ source 正则表达式的源文本

### 方法

#### regexObj.exec(string) 通用的模式匹配
+ 如果没有匹配，返回null
+ 如果有匹配，返回一个结果数组，第一个元素是匹配的结果result[0],后面的都是分组元素的结果。数组具有两个属性index 表示匹配第一个字符的位置，input为输入的字符串。
+ 如果正则表达式包含g属性，则每次则会从lastIndex出开始匹配，如果能匹配上则将lastIndex本次匹配的最后的位置，如果不能匹配则将lastIndex置为0。
	
		var result = /(\d+)abc([a-z]{3})/.exec("abc123abcdef123abcdef123");
		console.log(result)         //["123abcdef", "123", "def"]
		console.log(result.index)   //3
		console.log(result.input)   //abc123abcdef123abcdef123
	
		var regex = /(\d+)abc([a-z]{3})/g;
		var target = 'abc123abcdef789abcxyz789';
		result = regex.exec(target)
		console.log(result)          //["123abcdef", "123", "def"]
		console.log(result.index)    //3
		console.log(regex.lastIndex) //12  
		console.log(result.input)    //abc123abcdef789abcxyz789
		
		result = regex.exec(target)  
		console.log(result)          //["789abcxyz", "789", "xyz"]
		console.log(result.index)    //12
		console.log(regex.lastIndex) //21
		console.log(result.input)    //abc123abcdef789abcxyz789
		

#### regexObj.test(string) 检测一个字符串是否匹配某个模式

如果有检测到匹配返回true，如果没有匹配返回false。

##### 检测邮箱
	
这是一个较粗糙的表达式，可以根据自身情况进行调整	
	
	/^\w+@[0-9a-z]+(\.\w+)+$/.test("test@test.com.cn")

##### 检测IPv4地址

将地址的一段分为一位、两位、三位的数字并进行穷举

	/^((25[0-5]|2[0-4]\d|1\d\d|[1-9]\d|\d)\.){3}(25[0-5]|2[0-4]\d|1\d\d|[1-9]\d|\d)$/.test("192.168.1.1")


# 正则表达式应用

## 一个坑
	
	用一个正则表达式来测试多个字符串是否符合全为数字的规则
	
	var regex = /^\d+$/g;
	console.log(regex.test("123456"))  //true
	console.log(regex.lastIndex)
	console.log(regex.test("123456"))  //false
	console.log(regex.lastIndex)


### 解决一 去掉全局匹配

	var regex = /^\d+$/;
	console.log(regex.test("123456"))  //true
	console.log(regex.lastIndex)
	console.log(regex.test("123456"))  //true
	console.log(regex.lastIndex)

### 解决二 每次使用之前重置lastIndex，从头开始
	
	var regex = /^\d+$/g;
	console.log(regex.test("123456"))  //true
	console.log(regex.lastIndex)
	
	regex.lastIndex = 0;
	console.log(regex.test("123456"))  //true
	console.log(regex.lastIndex)

## 给HTML文件中所有的CSS和JS文件加上版本号
	
将`<script src="./test.js">` 改为 `<script src="./test@dev.js">`

将`<link href="./test.css">` 改为 `<script src="./test@dev.css">`
	
HTML中属性的表示方式参考[SGML Attribute](http://www.w3.org/TR/html4/intro/sgmltut.html)

> 3.2.2 Attributes

>> Elements may have associated properties, called attributes, which may have values (by default, or set by authors or scripts). Attribute/value pairs appear before the final ">" of an element's start tag. Any number of (legal) attribute value pairs, separated by spaces, may appear in an element's start tag. They may appear in any order.

>> In this example, the id attribute is set for an H1 element:

>>	<H1 id="section1">
		This is an identified heading thanks to the id attribute
	</H1> 
>> By default, SGML requires that all attribute values be delimited using either double quotation marks (ASCII decimal 34) or single quotation marks (ASCII decimal 39). Single quote marks can be included within the attribute value when the value is delimited by double quote marks, and vice versa. Authors may also use numeric character references to represent double quotes (&#34;) and single quotes (&#39;). For double quotes authors can also use the character entity reference &quot;.

>> In certain cases, authors may specify the value of an attribute without any quotation marks. The attribute value may only contain letters (a-z and A-Z), digits (0-9), hyphens (ASCII decimal 45), periods (ASCII decimal 46), underscores (ASCII decimal 95), and colons (ASCII decimal 58). We recommend using quotation marks even when it is possible to eliminate them.

>> Attribute names are always case-insensitive.

>> Attribute values are generally case-insensitive. The definition of each attribute in the reference manual indicates whether its value is case-insensitive.

>> All the attributes defined by this specification are listed in the attribute index.	

+ 匹配script src的正则  `/<script.*?src=(['"])([^'"\s]*?)\1[^>]*>/ig`
+ 匹配link href的正则  `/<link.*?href=(['"])([^'"\s]*?)\1[^>]*>/ig`

为了加入版本号，咱们将script标签匹配为`<script ... src="......`和`.js ...>`，然后在这两段之间加入版本号`@dev`

将正则表达式做如下调整：
	
	var regexSrc = /(<script.*?src=(['"])([^'"\s]*?))(\.js\2[^>]*>)/ig;
	var regexHref = /(<link.*?href=(['"])([^'"\s]*?))(\.css\2[^>]*>)/ig;
	


	var testStr = [
	'<link rel="stylesheet" href="http://test.com/test1.css" type="text/css"/>',
	'<link rel="stylesheet" type="text/css" href="http://test.com/test2.css"/>',
	'<script type="text/javascript" src="http://test.com/test1.js"></script>',
	'<script src="http://test.com/test2.js" type="text/javascript"></script>'
	].join('');
	
	var regexSrc = /(<script.*?src=(['"])([^'"\s]*?))(\.js\2[^>]*>)/ig;
	console.log(testStr.match(regexSrc));
	console.log(regexSrc.exec(testStr));
	
	var regexHref = /(<link.*?href=(['"])([^'"\s]*?))(\.css\2[^>]*>)/ig;
	console.log(testStr.match(regexHref));
	console.log(regexHref.exec(testStr));
	
	function addVersion(input,regex,version){
		input = input || "";
		return input.replace(regex,function(){
			//数星星
			var left = arguments[1];
			var right = arguments[4]
			return [left,version,right].join('');
		});
	}
	
	var result;
	var version = "@dev";
	result = addVersion(testStr,regexSrc,version);
	console.log("script result:",result);
	result = addVersion(result,regexHref,version);
	console.log("style result:",result);
	
	
# 工具推荐
0. [Regex Match Tracer](http://www.regexlab.com/zh/mtracer/)
0. [Regex Match Tracer](http://www.regexlab.com/tools)
0. [正则表达式测试工具](http://www.cnblogs.com/TianFang/archive/2009/02/07/1386000.html)
0. [Regex Buddy,Regex Magic 测试调试工具](http://www.regular-expressions.info/tools.html)

# 参考资料
0. [正则表达式30分钟入门教程](http://deerchao.net/tutorials/regex/regex.htm)
0. [JavaScript正则表达式](http://www.cnblogs.com/dolphinX/p/3486214.html)
0. [正则表达式图形化工具](http://regexper.com/)
0. [MDN 正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)
0. [JavaScript正则表达式](http://www.cnblogs.com/rubylouvre/archive/2010/03/09/1681222.html)
0. [JavaScript正则表达式](http://www.w3cfuns.com/article-5598591-1-1.html)
0. [Regex Expression Info](http://www.regular-expressions.info/index.html)