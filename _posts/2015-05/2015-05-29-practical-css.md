---
layout: post
title: "CSS实战详解"
categories: [开发技术]
tags: [CSS详解,CSS属性,CSS]
---
## 基本规则
+ 缩写和扩展写法
    + 扩展写法就是属性大类-属性小类的写法，比如background-color,border-top-width,margin-left,font-size等。
    + 缩写就是按照一定的规则将扩展写法写到一起，各个属性之间用空格隔开，比如background，font，margin，border等，由于各个属性之间的值比较容易区分，所以CSS解析器不容易混淆属性,对于不容易区分的需要用单撇号`/`分隔。
+ 常见的缩写
    + font
    font: font-style|font-variant|font-weight|font-size|line-height|font-family
    + margin 或 padding
    margin: margin-top|margin-right|margin-bottom|margin-left
    padding: padding-top|padding-right|padding-bottom|padding-left
    这种涉及到四个值的缩写属性有一个规则，写一个值是应用到四方；写两个值分别为上下和左右的值；写三个值为上、左右、下的值；写四个值依次为上、右、下、左的值。
    + border
    border:border-width|border-style|border-color
    + list-style
    list-style:list-style-type|list-style-position|list-style-image
    + background
    background:background-color|background-image|background-repeat|background-attachment|background-position
    + color
    六位十六进制可以缩写为三位，如果每两位都是相同的话。
##属性详解
### background
+ 属性详解
背景色 背景图片 背景平铺模式 背景滚动模式 背景定位
    <div style="background-color:blue;width:120px;height:20px;">
    <div style="width:100px;height:20px;background-color:green;">
    </div>
    </div>
+ 实现进度条

+ 实现淘宝评价效果

### border
+ 实现Tab页效果

+ 实现选中效果

### position
+ 实现弹窗效果

+ 实现模态、遮罩效果








##参考资料
0. [使用css实现全兼容浏览器的三角形][1]
1. [20个实用的CSS技巧代码][2]
2. [CSS使用技巧][3]
3. [CSS使用技巧收集，包含CSS2/CSS3][4]
4. [用CSS代码绘制三角形 纯CSS绘制三角形的代码][5]
5. [CSS创建三角形（小三角）的几种方法][6]
6. [CSS新手整理的CSS技巧][7]
7. [CSS使用技巧20则][8]
8. [十大CSS技巧][9]
9. [10个非常有用的CSS技巧][10]


[1]: http://www.bitscn.com/school/HTMLCSS/201410/337133.html "使用css实现全兼容浏览器的三角形"
[2]: http://www.w3cplus.com/css/20-incredibly-useful-CSS-snippets-for-developers "20个实用的CSS技巧代码"
[3]: http://www.ruanyifeng.com/blog/2010/03/css_cookbook.html "CSS使用技巧"
[4]: http://www.shejidaren.com/css%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E6%94%B6%E9%9B%86-%E5%8C%85%E5%90%ABcss2-css3.html "CSS使用技巧收集，包含CSS2/CSS3"
[5]: http://www.jb51.net/css/54994.html "用CSS代码绘制三角形 纯CSS绘制三角形的代码"
[6]: http://www.daqianduan.com/4721.html "CSS创建三角形（小三角）的几种方法"
[7]: http://www.duote.com/tech/1/1952.html "CSS新手整理的CSS技巧"
[8]: http://www.jb51.net/article/1187.htm "CSS使用技巧20则"
[9]: http://blog.csdn.net/budinger/article/details/18086543 "十大CSS技巧"
[10]: http://www.cnblogs.com/hnyei/archive/2011/11/12/hnyei.html "10个非常有用的CSS技巧"