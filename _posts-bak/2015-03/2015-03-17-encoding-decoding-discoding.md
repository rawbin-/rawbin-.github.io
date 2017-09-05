---
layout: post
title: "Web开发中的编码解码乱码问题深入解析"
categories: [计算机基础]
tags: [编码,解码,乱码,字符集]
---

# 基本概念
+ 字符：各种文字和符号的统称，包括各国各族各地的文字，标点符号，图形符号，数字等。
+ 字符集： 字符的集合，比如汉字字符集，ASCII字符集，简体中文字符集，罗马字母等。字符集关心的是这个集合需要包含哪些东西，怎么唯一表示这个集合中的每一个元素。
+ 字符编码： 将字符集处理为计算机能够识别和处理的规则。字符编码关注一个字符如何用二进制表示，如何让计算机精确识别。
+ 内码: 内码是指计算机汉字系统中使用的二进制字符编码，是沟通输入、输出与系统平台之间的交换码，通过内码可以达到通用和高效率传输文本的目的。比如MS Word中所存储和调用的就是内码而非图形文字。英文ASCII字符采用一个字节的内码表示，中文字符如国标字符集中，GB2312、GB12345、GB13000皆用双字节内码，GB18030（27,533汉字）双字节内码汉字为20,902个，其余6,631个汉字用四字节内码。
+ 编码操作： 将信息转换为二进制串的过程。(字符-->字节)
+ 解码操作： 将字符从二进制串中还原的操作。(字节-->字符)

## 编码和字符集关系
+ ASCII字符集: ASCII编码
+ ISO 8859-\*字符集: ISO 8859-\*编码 
+ GB2313字符集： GB2312编码
+ BIG5字符集： BIG5编码
+ GB18030字符集： GB18030编码
+ UCS字符集： UCS-2编码，UCS-4编码
+ Unicode字符集：UTF-7，UTF-8编码，UTF-16编码，Unicode编码（UCS-2），Unicode大端，Unicode小端，UTF-32编码
+ ANSI: 使用2个字节来代表一个字符的各种汉字延伸编码方式，称为ANSI编码。 在简体中文系统下，ANSI编码代表GB2312编码，在日文操作系统下，ANSI编码代表JIS编码。 

#文件编码
每个键盘按键都有一个对应的编码，这个是与硬件相关的、跟厂家相关的（扫描码）。为了使各个硬件厂商间的键码进行兼容，出台了一个通用的键盘编码（虚拟键码），虚拟键码是与硬件无关的，不同厂家使用同一套虚拟键码。(默想冯洛伊曼体系)。

汉字的输入过程：各种输入码---交换码（国标码）---内码---字形码（显示汉字，打印汉字）.

输入过程（你在记事本里面写东东）：

+ 打开记事本，切换输入法，并输入“简单”，这里用到的是外码（也叫输入码），不同输入法编码不一样，自然外码也不一样。
+ 键盘驱动程序检测到键盘输入的硬件信号（扫描码），把键码转换为虚拟键码（ASCII），然后将扫描码和虚拟键码（ASCII）及相关信息通过操作系统发给输入法程序
+ 输入法程序将收到的信息转换为汉字内码，同时将汉字内码对应的输出码（字形码）通过操作系统返回给应用程序
+ 应用程序（notepad）处理消息内容，并作出进一步响应。
+ 点击保存，可以选择编码ANSI为系统默认编码，可选Unicode的一种。
+ 应用程序(notepad)将文件内容进行编码并通过操作系统的文件管理系统保存文件。

输出过程（你用记事本打开你写的东东）：

+ 应用程序打开文件（记事本打开文件），应用程序向操作系统读取文件内容（读到的是汉字内码）
+ 操作系统给应用程序返回字节流（汉字内码）
+ 应用程序探测二进制流的编码，并按得到的编码解码，如果没有检测到特征，则按系统默认编码解码（发现是汉字，找汉字字库找对应的的字形码发给显示驱动程序进行显示）。
+ 显示驱动程序把汉字的点阵打到显示器上。
  比如在记事本中输入“联通”，然后以ANSI编码，然后关闭再打开，可以看到乱码，因为记事本搞不清楚编码格式，认为是UTF8。

### 参考
0. [字符，字节和编码][12]
1. [输入法工作原理][0]
2. [输入法工作原理][1]
3. [键盘输入工作原理][2]
4. [键盘输入工作原理][3]
5. [关于输入法的工作原理及编程][4]
6. [输入法的基本原理][5]
7. [关于输入法的工作原理及编程][6]
8. [谈谈Windows程序中的字符编码][7]
9. [Windows基础-[文字编码]&lt;转: 编码术语&gt;][8]
10. [文件编码格式（转）][9]
11. [汉字编码表示与显示][10]
12. [汉字编码与编程相关问题总结：ASCII、机内码、区位码、国标码、Unicode码是如何转换的][11]

# 浏览器编码

## URL编码
+ 网址编码
  在浏览器地址栏输入中文，并回车，浏览器会对URL进行UTF-8编码，并将编码后的结果回写入地址栏。
  如：输入 `http://www.baigoogledu.com/北京`，然后回车  
  得到 `http://www.baigoogledu.com/%E5%8C%97%E4%BA%AC`，是UTF-8编码 
+ 查询串编码
  在查询字符串中输入中文，并回车；浏览器对URL进行UTF-8编码，并且结果不回写。
  如： `http://www.baigoogledu.com/?l=北京`
  得到： `http://www.baigoogledu.com/?l=%E5%8C%97%E4%BA%AC` 
+ JavaScript AJAX，GET，POST
  可以用firebug的console简单测一下，还是UTF-8
        var r = new XMLHttpRequest();
        var methods = ['get', 'post'];
        var url ='http://www.baigoogledu.com/北京';
        for(var method in methods){
          r.open('post',url,null)
          r.send()
        } 

## 请求相关
在HTTP中，与字符集和字符编码相关的消息头是Accept-Charset/Content-Type，另外主区区分Accept-Charset/Accept-Encoding/Accept-Language/Content-Type/Content-Encoding/Content-Language：
Accept-Charset：浏览器申明自己接收的字符集，这就是本文前面介绍的各种字符集和字符编码，如gb2312，utf-8（通常我们说Charset包括了相应的字符编码方案）；
Accept-Encoding：浏览器申明自己接收的编码方法，通常指定压缩方法，是否支持压缩，支持什么压缩方法（gzip，deflate），（注意：这不是只字符编码）；
Accept-Language：浏览器申明自己接收的语言。语言跟字符集的区别：中文是语言，中文有多种字符集，比如big5，gb2312，gbk等等；
Content-Type：WEB服务器告诉浏览器自己响应的对象的类型和字符集。例如：Content-Type: text/html; charset='gb2312'
Content-Encoding：WEB服务器表明自己使用了什么压缩方法（gzip，deflate）压缩响应中的对象。例如：Content-Encoding：gzip
Content-Language：WEB服务器告诉浏览器自己响应的对象的语言。

## 页面编码
HTML文档是作为带有字符编码信息的字节流在网络中传送。编码信息可以在HTTP响应头中指定(header)，也可以在HTML标签中指定（&lt;meta&gt;）。浏览器根据字符编码信息将字节流转换为显示在浏览器上的字符集合。

浏览器获取编码的过程是
+ 如果用户指定了编码，则按用户指定的编码进行解码。
+ 先对文件进行预解析，如果文件存在BOM（Byte Order Mark），则按BOM确定的编码解码。
+ 如果HTTP响应头中存在编码信息，则按响应头中的编码进行解码。
+ 先按浏览器的编码选择算法选择一个编码进行预解析，如果解析出<meta>标签中存在字符集设置，则按<meta>标签中的字符集进行解码。
+ 按浏览器的默认编码。

### 参考
12. [网络编程中的编码问题汇总][13]
13. [各种编码UNICODE、UTF-8、ANSI、ASCII、GB2312、GBK详解][16]
14. [浏览器如何解析HTML字符编码][17]
15. [百分号编码][18]
16. [说说http协议中的编码和解码][19]
17. [深入理解浏览器解析机制和XSS向量编码][20]
18. [浏览器编码][21]
19. [关于URL编码](http://www.ruanyifeng.com/blog/2010/02/url_encoding.html)
20. [Unicode与JavaScript详解](http://www.ruanyifeng.com/blog/2014/12/unicode.html)
21. [URL编码与解码](http://www.cnblogs.com/Wahitler/p/4761373.html)

# 终端编码

## SSH终端编码
使用putty 连接服务器的时候，页面中显示的中文有时候会出现乱码，可以通过更改终端的的编码方式来解决，默认为系统编码。

## mysql 命令行乱码
在windows命令行下，插入中文数据或者查询结果中有中文数据的时候，会出现乱码。
输入命令 `show variables like ‘character_set_%’;` 查看当前设置的编码情况  
在命令行输入 :set names utf8;  
则等同于  
set character_set_client=utf8;  
set character_set_results=utf8;  
set character_set_connection=utf8;  
这连续的三条命令。
在命令行插入数据之前
执行命令set names gbk;或者set names gb2312;
再插入

查询数据之前
执行命令set names gbk;或者set names gb2312;

完了之后再set names utf8;
这样就能正常插入和查询中文并且保持数据库编码为utf8

## windows gvim 中文乱码
$VIM_HOME/vimrc 
文件末尾添加如下几行：
let &termencoding=encoding
set fileencodings=utf-8,gb2312,gbk
set encoding=gbk


# 语言内部编码

## python
  python 2.x 内部使用Unicode字符集
### 代码编码
  python的文件头部可以通过 `#-*- coding: utf-8 -*-`的方式来指定源代码的编码，通常在源文件中有非ASCII字符时使用。文件头部编码声明决定了python解析源码中的str的编码选择方式，比如头部声明的是utf-8编码，则代码中s="中文"python就会按照utf-8编码格式来解析，通过repr(s)可以看到字符编码是"\xe4\xb8\xad\xe6\x96\x87"，如果头部声明的编码是gbk编码，则python会对s采用gbk编码解析，结果是"\xd6\xd0\xce\xc4"。
  需要注意的是，文件本身的编码要跟文件头部声明编码一致，不然就会出现问题。文件本身的编码在Linux下面可以在vim下用命令set fenc来查看。如果文件本身编码是gbk，而源码文件头部声明的编码是utf-8，这样如果源码中有中文就会有问题了，因为本身中文str存储是按照gbk编码来的，而python在解析str的时候又以为是utf-8编码，这样就会报SyntaxError: (unicode error) 'utf8' codec can't decode byte错误。
    #coding: utf-8
    u = u"中文"
    print repr(u) # u'\u4e2d\u6587'
     
    s = "中文"
    print repr(s) # '\xe4\xb8\xad\xe6\x96\x87'
     
    u2 = s.decode("utf-8")
    print repr(u2) # u'\u4e2d\u6587'
     
    #s2 = u.decode("utf-8") #编码错误
    #u2 = s.encode("utf-8") #解码错误
  注意实例中注释掉的2行代码，对于unicode最好不要直接调用decode，str最好不要直接调用encode方法。因为如果是直接调用，则相当于<code>u.encode(default_encoding).decode("utf-8")</code>，default_encoding是python的unicode实现中用的默认编码，即<code>sys.getdefaultencoding()</code>得到的编码,如果你没有设置过，那么默认编码就是ascii，如果你的unicode本身超出了ascii编码范围就会报错。同理，如果对str直接调用encode方法，那么默认会先对str进行解码，即s.decode(default_encoding).encode("utf-8"),如果str本身是中文，而default_encoding是ascii的话，解码就会出错，从而导致上面这两行会分别报<code>UnicodeEncodeError: 'ascii' codec can't encode characters in position...</code>错误和<code>UnicodeDecodeError: 'ascii' codec can't decode byte 0xe4 in position...</code>错误。

### 文件读写编码
采用python的open()方法打开文件时，read()读取的是str，编码就是文件本身的编码。

## javascript
JavaScript 采用Unicode字符集，使用UCS-2编码，也就是现在UTF-16的一部分

### 参考
13. [Unicode与JavaScript][14]
14. [Unicode HOWTO][15]
15. [Python编码和Unicode][25]
16. [python 文件编码 字符串编码 文件读写 文件编码控制 编码转换][26]
17. [python 字符编码与解码——unicode、str和中文：UnicodeDecodeError: 'ascii' codec can't decode][27]
18. [【总结】Python 2.x中常见字符编码和解码方面的错误及其解决办法][29]
19. [详解 python 中文编码与处理][28]
20. [python 中文乱码 问题深入分析][30]
21. [Python编码问题详解][22]
22. [Python字符编码详解][23]
23. [python读写文件，和设置文件的字符编码比如utf-8][24]

# 编码检测

## vim中
使用`:set fileencoding` 或者`set fenc`来显示文件编码

## notepad++中
使用格式菜单来查看文件编码

[0]: http://blog.csdn.net/shuilan0066/article/details/6883629 "输入法工作原理"
[1]: http://www.xp85.com/html/article-98-3220.html "输入法工作原理"
[2]: https://msdn.microsoft.com/zh-cn/library/ms171535v "键盘输入工作原理"
[3]: http://blog.csdn.net/zztfj/article/details/5106832 "键盘输入工作原理"
[4]: http://blog.csdn.net/xiajian2010/article/details/23596865 "关于输入法的工作原理及编程"
[5]: http://blog.163.com/zhao_a_s/blog/static/21635295200791810554941/ "输入法的基本原理"
[6]: http://m.blog.csdn.net/blog/xiajian2010/23596865 "关于输入法的工作原理及编程"
[7]: http://blog.csdn.net/fmddlmyy/article/details/399661 "谈谈Windows程序中的字符编码"
[8]: http://www.cnblogs.com/ztercel/articles/1934752.html "Windows基础-[文字编码]&lt;转: 编码术语&gt;"
[9]: http://www.cnblogs.com/imissherso/articles/640727.html "文件编码格式（转）"
[10]: http://blog.csdn.net/buxoman/article/details/299110 "汉字编码表示与显示"
[11]: http://blog.csdn.net/yeyuangen/article/details/6722193 "汉字编码与编程相关问题总结：ASCII、机内码、区位码、国标码、Unicode码是如何转换的"
[12]: http://www.regexlab.com/zh/encoding.htm "字符，字节和编码"
[13]: http://blog.csdn.net/xfxyy_sxfancy/article/details/39059465 "网络编程中的编码问题汇总"
[14]: http://www.ruanyifeng.com/blog/2014/12/unicode.html "Unicode与JavaScript"
[15]: https://docs.python.org/2/howto/unicode.html "Unicode HOWTO"
[16]: http://blog.csdn.net/lvxiangan/article/details/8151670 "各种编码UNICODE、UTF-8、ANSI、ASCII、GB2312、GBK详解"
[17]: http://blog.csdn.net/xiaojianpitt/article/details/6793158 " 浏览器如何解析HTML字符编码"
[18]: http://zh.wikipedia.org/zh/%E7%99%BE%E5%88%86%E5%8F%B7%E7%BC%96%E7%A0%81 "百分号编码"
[19]: http://www.csdn123.com/html/itweb/20130730/29422_29378_29408.htm "说说http协议中的编码和解码"
[20]: http://bobao.360.cn/learning/detail/292.html "深入理解浏览器解析机制和XSS向量编码"
[21]: http://every-best.iteye.com/blog/970861 "浏览器编码"
[22]: http://www.2cto.com/kf/201407/317866.html "Python编码问题详解"
[23]: http://www.cnblogs.com/huxi/archive/2010/12/05/1897271.html "Python字符编码详解"
[24]: http://outofmemory.cn/code-snippet/629/python-duxie-file-setting-file-charaeter-coding-biru-utf-8 "python读写文件，和设置文件的字符编码比如utf-8"
[25]: http://blog.jobbole.com/50345/ "Python编码和Unicode"
[26]: http://hi.baidu.com/41202197514/item/1d47553cfb184af4a984280c "python 文件编码 字符串编码 文件读写 文件编码控制 编码转换"
[27]: http://blog.csdn.net/trochiluses/article/details/16825269 "python 字符编码与解码——unicode、str和中文：UnicodeDecodeError: 'ascii' codec can't decode"
[28]: http://my.oschina.net/leejun2005/blog/74430 "详解 python 中文编码与处理"
[29]: http://www.crifan.com/summary_python_2_x_common_string_encode_decode_error_reason_and_solution/ "【总结】Python 2.x中常见字符编码和解码方面的错误及其解决办法"
[30]: http://blog.csdn.net/kiki113/article/details/4062063 "python 中文乱码 问题深入分析"