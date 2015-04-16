---
layout: post
title: "字符集和编码详解"
date: 2015/4/16 星期四 16:34:09 
categories: 编码 字符集
---

Basic Multilingual Plane, BMP
Supplementary Multilingual Plane


## 基本概念
+ 字符：各种文字和符号的统称，包括各国各族各地的文字，标点符号，图形符号，数字等。
+ 字符集： 字符的集合，比如汉字字符集，ASCII字符集，简体中文字符集，罗马字母等。字符集关心的是这个集合需要包含哪些东西，怎么唯一表示这个集合中的每一个元素。
+ 字符编码： 将字符集处理为计算机能够识别和处理的规则。字符编码关注一个字符如何用二进制表示，如何让计算机精确识别。
+ 内码: 内码是指计算机汉字系统中使用的二进制字符编码，是沟通输入、输出与系统平台之间的交换码，通过内码可以达到通用和高效率传输文本的目的。比如MS Word中所存储和调用的就是内码而非图形文字。英文ASCII字符采用一个字节的内码表示，中文字符如国标字符集中，GB2312、GB12345、GB13000皆用双字节内码，GB18030（27,533汉字）双字节内码汉字为20,902个，其余6,631个汉字用四字节内码。
+ 编码操作： 将信息转换为二进制串的过程。
+ 解码操作： 将字符从二进制串中还原的操作。


## 常见字符集
+ ASCII: ASCII（American Standard Code for Information Interchange，美国信息互换标准代码）是基于罗马字母表的一套电脑编码系统。用7位二进制来表示字符，共128个字符。同 ISO 646,ASCII字符集包括英文字母、阿拉伯数字和标点符号等字符。。
+ 扩展ASCII：用8位二进制来表示字符，共256个.ASCII扩展字符集比ASCII字符集扩充出来的符号包括表格符号、计算符号、希腊字母和特殊的拉丁符号。
+ ISO 8859-\* ASCII收录了空格及94个“可印刷字符”，足以给英语使用。 
但是，其他使用拉丁字母的语言(主要是欧洲国家的语言)，都有一定数量的变音字母，故可以使用ASCII及控制字符以外的区域来储存及表示。 
除了使用拉丁字母的语言外，使用西里尔字母的东欧语言、希腊语、泰语、现代阿拉伯语、希伯来语等，都可以使用这个形式来储存及表示。
+ GB2312： GB2312是中国国家标准的简体中文字符集。它所收录的汉字已经覆盖99.75%的使用频率，基本满足了汉字的计算机处理需要。在中国大陆和新加坡获广泛使用。字符集中除常用简体汉字字符外还包括希腊字母、日文平假名及片假名字母、俄语西里尔字母等字符，未收录繁体中文汉字和一些生僻字。
+ GBK： GBK是GB2312的扩展，是向上兼容的，因此GB2312中的汉字的编码与GBK中汉字的相同。GBK编码是GB2312编码的超集，向下完全兼容GB2312，同时GBK收录了Unicode基本多文种平面中的所有CJK汉字。同 GB2312一样，GBK也支持希腊字母、日文假名字母、俄语字母等字符，但不支持韩语中的表音字符（非汉字字符）。GBK还收录了GB2312不包含的汉字部首符号、竖排标点符号等字符。
+ BIG5： Big5收录的汉字只包括繁体汉字，不包括简体汉字，一些生僻的汉字也没有收录。GBK收录的日文假名字符、俄文字符Big5也没有收录。因为Big5当中收录的字符有限，因此有很多在Big5基础上扩展的编码，如倚天中文系统。Windows系统上使用的代码页CP950也可以理解为是对Big5的扩展，在Big5的基础上增加了7个汉字和一些符号。Big5编码对应的字符集是GBK字符集的子集，也就是说Big5收录的字符是GBK收录字符的一部分，但相同字符的编码不同。
+ GB18030： GB 18030字符集标准解决汉字、日文假名、朝鲜语和中国少数民族文字组成的大字符集计算机编码问题。该标准的字符总编码空间超过150万个编码位，收录了27484个汉字，覆盖中文、日文、朝鲜语和中国少数民族文字。满足中国大陆、香港、台湾、日本和韩国等东亚地区信息交换多文种、大字量、多用途、统一编码格式的要求。并且与Unicode 3.0版本兼容，填补Unicode扩展字符字汇“统一汉字扩展A”的内容。并且与以前的国家字符编码标准（GB2312，GB13000.1）兼容。
+ UCS: 通用字符集(Universal Character Set，UCS)是由ISO制定的ISO 10646(或称ISO/IEC 10646)标准所定义的字符编码方式，采用4字节编码。 
UCS包含了已知语言的所有字符。 
除了拉丁语、希腊语、斯拉夫语、希伯来语、阿拉伯语、亚美尼亚语、格鲁吉亚语，还包括中文、日文、韩文这样的象形文字，UCS还包括大量的图形、印刷、数学、科学符号。 
+ Unicode字符集： Unicode字符集编码是Universal Multiple-Octet Coded Character Set 通用多八位编码字符集的简称，是由一个名为 Unicode 学术学会(Unicode Consortium)的机构制订的字符编码系统，支持现今世界各种不同语言的书面文本的交换、处理及显示。

## 编码方式（编码）
一种字符集，都会有与其对应的一个或多个编码方式，如下大的编码方式是跟上面的字符集对应的。

+ ASCII字符集: ASCII编码
+ ISO 8859-\*字符集: ISO 8859-\*编码 
+ GB2313字符集： GB2312编码
+ BIG5字符集： BIG5编码
+ GB18030字符集： GB18030编码
+ UCS字符集： UCS-2编码，UCS-4编码
+ Unicode字符集：UTF-7，UTF-8编码，UTF-16编码，Unicode编码（UCS-2），Unicode大端，Unicode小端，UTF-32编码
+ ANSI: 使用2个字节来代表一个字符的各种汉字延伸编码方式，称为ANSI编码。 在简体中文系统下，ANSI编码代表GB2312编码，在日文操作系统下，ANSI编码代表JIS编码。 

## 编码转换
Unicode是内存编码表示方案（是规范），而UTF是如何保存和传输Unicode的方案（是实现）
GBK、GB2312等与UTF8之间都必须通过Unicode编码才能相互转换： 
GBK、GB2312－－Unicode－－UTF8 
UTF8－－Unicode－－GBK、GB2312 

## 字节序
UTF-8以字节为编码单元，没有字节序的问题。UTF-16以两个字节为编码单元，在解释一个UTF-16文本前，首先要弄清楚每个编码单元的字节序。例如收到一个“奎”的Unicode编码是594E，“乙”的Unicode编码是4E59。如果我们收到UTF-16字节流“594E”，那么这是“奎”还是“乙”？

为了识别 Unicode 文件，Microsoft 建议所有的 Unicode 文件应该以 ZERO WIDTH NOBREAK SPACE（U+FEFF）字符开头。这作为一个“特征符”或“字节顺序标记（byte-order mark，BOM）”来识别文件中使用的编码和字节顺序。

Unicode规范中推荐的标记字节顺序的方法是BOM。BOM不是“Bill Of Material”的BOM表，而是Byte Order Mark。BOM是一个有点小聪明的想法：
 
在UCS编码中有一个叫做"ZERO WIDTH NO-BREAK SPACE"的字符，它的编码是FEFF。而FFFE在UCS中是不存在的字符，所以不应该出现在实际传输中。UCS规范建议我们在传输字节流前，先传输字符"ZERO WIDTH NO-BREAK SPACE"。
 
这样如果接收者收到FEFF，就表明这个字节流是Big-Endian的；如果收到FFFE，就表明这个字节流是Little-Endian的。因此字符"ZERO WIDTH NO-BREAK SPACE"又被称作BOM。
 
UTF-8不需要BOM来表明字节顺序，但可以用BOM来表明编码方式。字符"ZERO WIDTH NO-BREAK SPACE"的UTF-8编码是EF BB BF（读者可以用我们前面介绍的编码方法验证一下）。所以如果接收者收到以EF BB BF开头的字节流，就知道这是UTF-8编码了。


## 编码识别
+ XML解析读取XML文档时，W3C定义了3条规则： 
    + 如果文档中有BOM，就定义了文件编码；
    + 如果文档中没有BOM，就查看XML声明中的编码属性；
    + 如果上述两者都没有，就假定XML文档采用UTF-8编码。
+ 对于Unicode文本最标准的途径是检测文本最开头的几个字节。如： 
 开头字节&nbsp;&nbsp;&nbsp;&nbsp;&emsp;&emsp;&emsp;&emsp;&emsp;Charset/encoding
 EF BB BF&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;UTF-8
 FE FF&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;&nbsp;UTF-16/UCS-2, little endian(UTF-16LE)
 FF FE&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;&nbsp;UTF-16/UCS-2, big endian(UTF-16BE)
 FF FE 00 00&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;UTF-32/UCS-4, little endian.
 00 00 FE FF&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;UTF-32/UCS-4, big-endia

## 参考
0. [字符编码方式发展历程（强烈推荐） ][14]
13. [字符集和字符编码（Charset & Encoding）][15]
14. [字符集与字符集编码简介][17]
15. [字符，字节和编码][18]
16. [字节那些事儿][28]
16. [UNICODE与UTF-8的转换详解][27]
0. [字符集编码详解][0]
1. [常用字符集编码详解][1]
2. [深入了解字符集和编码问题][2]
3. [字符编码详解——彻底理解掌握编码知识，“乱码”不复存在][3]
4. [Unicode 字符集与它的编码方式][4]
5. [字符集编码详解][5]
6. [各种字符集和编码详解][6]
7. [字符编解码的故事（ASCII，ANSI，Unicode，Utf-8）][7]
8. [实例详细介绍各种字符集编码转换问题][8]
9. [字符编码详解][9]
10. [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)][10]
11. [十分钟搞清字符集和字符编码][11]
11. [遇到乱码不怕不怕啦——计算机字符编码详尽讲解][12]
12. [字符编码笔记：ASCII，Unicode和UTF-8][13]
13. [字符编码的奥秘][16]
14. [十分钟搞清字符集和字符编码][19]
15. [字符集编码发展简史][20]
16. [深入了解字符集和编码][21]
17. [谈谈Unicode编码，简要解释UCS、UTF、BMP、BOM等名词][22]
18. [ANSI编码和Unicode编码的不同][23]
19. [各种编码UNICODE、UTF-8、ANSI、ASCII、GB2312、GBK详解][24]
20. [计算机内部编码][25]
21. [说说Unicode，UTF8，UTF16，BOM，Big endian，Little][26]
22. [字符编码深入解析][27]




[0]: http://www.cppblog.com/humanchao/archive/2007/09/27/32989.html "字符集编码详解"
[1]: http://blog.csdn.net/zhoubl668/article/details/6914018 "常用字符集编码详解"
[2]: http://webcenter.hit.edu.cn/articles/2009/04-01/04193356.htm "深入了解字符集和编码问题"
[3]: http://polaris.blog.51cto.com/1146394/377468/ "字符编码详解——彻底理解掌握编码知识，“乱码”不复存在"
[4]: http://blog.csdn.net/nodeathphoenix/article/details/7057760 "Unicode 字符集与它的编码方式"
[5]: http://blog.csdn.net/xufenghfut/article/details/11585311 "字符集编码详解"
[6]: http://blog.csdn.net/ancky/article/details/2034809 "各种字符集和编码详解"
[7]: http://www.cnblogs.com/KevinYang/archive/2009/01/31/1381570.html "字符编解码的故事（ASCII，ANSI，Unicode，Utf-8）"
[8]: http://blog.csdn.net/dracularking/article/details/2257468 "实例详细介绍各种字符集编码转换问题"
[9]: http://blog.csdn.net/xiongxuanwen/article/details/44080029 "字符编码详解"
[10]: http://www.joelonsoftware.com/articles/Unicode.html "The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)"
[11]: http://blog.jobbole.com/84903/ "十分钟搞清字符集和字符编码"
[12]: http://www.guokr.com/blog/763017/ "遇到乱码不怕不怕啦——计算机字符编码详尽讲解"
[13]: http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html "字符编码笔记：ASCII，Unicode和UTF-8"
[14]: http://blog.chinaunix.net/uid-26790551-id-3203316.html "字符编码方式发展历程（强烈推荐） "
[15]: http://www.cnblogs.com/skynet/archive/2011/05/03/2035105.html "字符集和字符编码（Charset & Encoding）"
[16]: http://www.jianshu.com/p/zYxssN "字符编码的奥秘"
[17]: http://blog.csdn.net/gogor/article/details/5323599 "字符集与字符集编码简介"
[18]: http://www.regexlab.com/zh/encoding.htm "字符，字节和编码"
[19]: http://cenalulu.github.io/linux/character-encoding/ "十分钟搞清字符集和字符编码"
[20]: http://www.360doc.com/content/11/1222/19/6938655_174302929.shtml "字符集编码发展简史"
[21]: http://www.iteye.com/topic/97803 "深入了解字符集和编码"
[22]: http://www.pythonclub.org/python-basic/encode-detail "谈谈Unicode编码，简要解释UCS、UTF、BMP、BOM等名词"
[23]: http://blog.csdn.net/chaijunkun/article/details/4654397 "ANSI编码和Unicode编码的不同"
[24]: http://blog.csdn.net/lvxiangan/article/details/8151670 "各种编码UNICODE、UTF-8、ANSI、ASCII、GB2312、GBK详解"
[25]: http://www.cnblogs.com/BeyondTechnology/archive/2011/02/23/1963325.html "计算机内部编码"
[26]: http://cnn237111.blog.51cto.com/2359144/1080628 "说说Unicode，UTF8，UTF16，BOM，Big endian，Little"
[27]: http://blog.csdn.net/iefreer/article/details/4836844 "UNICODE与UTF-8的转换详解"
[28]: http://blog.csdn.net/dandycheung/article/details/5881620 "字节那些事儿"
[29]: http://www.blue1000.com/bkhtml/c155/2010-06/68084.htm "字符编码深入解析"