---
layout: post
title: "Python中的编码"
categories: [Web开发,前端开发,Python]
tags: [Python,编码,Unicode,str]

---

这里我们简单问题复杂化，说几个编码



## 源文件编码

    #coding=utf-8

    #-*- coding: utf-8 -*-

这样的格式，那么源代码中的内容就会以指定的编码进行存取，比如解析源码中的字符串的时候



## 字符串编码

ustr = u'test中文' 这个就是Unicode编码的字符串

varstr = 'test中文'  这个就是系统默认编码的字符串 用sys.getdefaultencoding()可以搞到


## 数据编码

+ 文件存储编码，就是存储文件的时候什么格式，windows默认ansi，unix 默认utf8

+ 文件内容编码，跟字符串编码相同，这个决定读取的数据如何转码



python文件写入的时候，会根据写入的内容确定数据的编码，举例：

手动将文件编码转换为ANSI，那么用python读取此文件内容然后写入另一个文件，则新写入的文件的编码为ANSI，可用NotePad++的格式菜单查看

手动将文件编码转换为UTF-8，那么用python读取此文件内容然后写入另一个文件，则新写入的文件的编码为UTF-8，可用NotePad++的格式菜单查看


如果单独写入字符串，python默认使用UTF8 无BOM的存储格式保存文件，文件的内容编码为UTF8



## 下面这堆就是浮云了：

python 中有两种字符串对象，str 和unicode

str类型是字节数组，unicode类型的是字符数组

简单举例，unicode 使用两个字节表示字符

strings = '"中文测试"  #len(strings) = 8

stringu = u'"中文测试" #len(stringu) = 4



unicode是python编码转换的中间编码，有两个方法来做编码转换操作decode(encoding)和encode(encoding)

decode 是按照指定的编码对对象进行解码，解码的结果是unicode对象

encode是将unicode对象，按照指定的编码编码成相应的格式比如说gbk



str 对象是已经被编码过的对象，需要现将其按照指定编码进行解码成unicode，再转换成其他编码

python中从一个编码到另一个编码之间的转换都是先将对象解码为unicode 然后再将unicode对象编码为指定的编码


## 参考资料
0. [python读写不同编码txt文件](http://blog.csdn.net/zm2714/article/details/8012474)
0. [Python字符集编码和文件读写](http://www.cnblogs.com/springbarley/articles/2338501.html)
0. [详解Python2.x中对Unicode编码的使用](http://www.jb51.net/article/63390.htm)
0. [Python中的str与unicode处理方法](http://python.jobbole.com/81244/)
0. [PYTHON编码处理-str与Unicode的区别](http://www.mamicode.com/info-detail-308445.html)
0. [Unicode 码制转换问题](http://blog.sina.com.cn/s/blog_730edb930100qfh5.html)
0. [初探python编码](http://blog.csdn.net/liuxincumt/article/details/8183391)
0. [python对16位unicode汉字处理](http://blog.csdn.net/garinwang/article/details/6329262)