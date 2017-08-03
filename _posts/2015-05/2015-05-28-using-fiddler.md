---
layout: post
title: "Web调试、开发工具"
description: "HTTP Web调试工具介绍"
category: 开发环境
tags: [HTTP调试,Web调试,开发工具]
---

### 1 引子
Web开发难免会需要各种监控和调试，现在各大浏览器基本都有了自带的调试工具，比如Firefox的Web开发者，Chrome Inspect，以及IE自带的开发者工具，这些工具都可以很方便的用F12调出。现在已经是不带调试器的浏览器不是好的开发工具的情形了。

### 2 调试工具列表
+ 首推各大浏览器的F12工具，各大浏览器的插件库
+ Firefox的Firebug、WebDeveloper
+ Fiddler
+ HTTP Watch
+ Charles
+ HTTP Debugger
+ HTTP Analyzer
+ Wireshark 通用的网络抓包工具
基本已经够用了，还是首推的F12，顺手拈来。实在不行可以Fiddler基本能解决你所有的问题。
当然后面的工具也是可以尝试的，都是特别好的工具，只是后面的刀比较多，比较疼，当然可以自行想办法。

需要相关的插件的可以在浏览器的插件库中很方便的搜到，比如需要JSON相关的，在插件信息中查JSON，同样的查REST。


### 3 浏览器兼容测试
+ Chrome，Firefox兼容性较强，可以不必多费周章
+ 高版本的IE开发者工具（F12），可以对较低版本的IE进行模拟和兼容测试。
+ 需要更高精度的环境测试，到[Modern IE][19]下载多种版本的IE环境进行实测。
+ 在线工具http://browsershots.org/
+ IETester(比较古老)
+ [SuperPreview][17]
+ [Modern IE][18]




### 4 参考资料
0. [Fiddler官网][0]
1. [HTTPWatch 官网][1]
2. [Charles 官网][2]
3. [HTTP Debugger 官网][3]
4. [WireShark 官网][9]
4. [Fiddler 教程][4]
5. [Fiddler入门教程][5]
6. [Fiddler使用小结][6]
7. [Web开发又一利器之——Fiddler][7]
8. [Web调试利器fiddler介绍][8]
9. [抓包工具Fiddler的使用][10]
10. [浏览器HTTP分析调试工具收集][11]
11. [WEB前端开发中的性能调试工具（简记）][12]
12. [介绍几款浏览器兼容性测试工具][13]
13. [12款浏览器兼容性测试工具推荐][14]
14. [Web浏览器兼容性测试工具如何选择？][15]
15. [12款很棒的浏览器兼容性测试工具推荐][16]


[0]: http://www.telerik.com/fiddler "Fiddler官网"
[1]: http://www.httpwatch.com/download/ "HTTPWatch 官网"
[2]: http://www.charlesproxy.com/ "Charles 官网"
[3]: http://www.httpdebugger.com/ "HTTP Debugger 官网"
[9]: https://www.wireshark.org/download.html "WireShark 官网"
[4]: http://kb.cnblogs.com/page/130367/ "Fiddler 教程"
[5]: http://www.cnblogs.com/phphuaibei/archive/2011/11/07/2240181.html "Fiddler入门教程"
[6]: http://www.cnblogs.com/forcertain/archive/2012/11/29/2795139.html "Fiddler使用小结"
[7]: http://blog.csdn.net/yhawaii/article/details/7245625 "Web开发又一利器之——Fiddler"
[8]: http://blog.chinaunix.net/uid-27105712-id-3738821.html "Web调试利器fiddler介绍 "
[10]: http://help.locoy.com/Document/Url/Fiddler.htm "抓包工具Fiddler的使用 "
[11]: http://www.open-open.com/lib/view/open1354262775311.html "浏览器HTTP分析调试工具收集"
[12]: http://blog.csdn.net/cuixiping/article/details/6275087 "WEB前端开发中的性能调试工具（简记）"
[13]: http://www.cnblogs.com/gaoweipeng/archive/2010/04/10/1708873.html "介绍几款浏览器兼容性测试工具"
[14]: http://www.cnbeta.com/articles/149063.htm "12款浏览器兼容性测试工具推荐"
[15]: http://www.ltesting.net/ceshi/ceshijishu/gncs/jrxcs/2011/0825/203126.html "Web浏览器兼容性测试工具如何选择？"
[16]: http://blog.jobbole.com/62404/ "12款很棒的浏览器兼容性测试工具推荐"
[17]: https://www.microsoft.com/zh-cn/download/details.aspx?id=2020 "SuperPreview下载"
[18]: https://www.modern.ie/zh-cn "Modern IE"
[19]: https://www.modern.ie/zh-cn/tools "Modern IE 工具下载"