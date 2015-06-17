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
    

## 背景

###  属性详解
背景色 背景图片 背景平铺模式 背景滚动模式 背景定位

### 应用实例
+ 实现进度条
+ 实现淘宝评价效果

### 参考资料
0. [CSS背景属性background详解](http://www.chinaz.com/design/2009/0918/92150.shtml)
1. [CSS背景属性详解](http://paranimage.com/css-background-attribute/)
2. [CSS新属性及多背景](http://www.html5jscss.com/css3%E6%AC%B2%E9%80%9F%E5%88%99%E4%B8%8D%E8%BE%BE-background%E6%96%B0%E5%B1%9E%E6%80%A7%E4%BB%A5%E5%8F%8A%E5%A4%9A%E8%83%8C%E6%99%AF.html)
3. [CSS3 background相关介绍](http://www.zhangxinxu.com/wordpress/2011/05/%e7%bf%bb%e8%af%91-css3-backgrounds%e7%9b%b8%e5%85%b3%e4%bb%8b%e7%bb%8d/)
4. [CSS3 background属性介绍](http://www.cnblogs.com/ATree/archive/2011/05/10/CSS3-Backgrounds.html)
5. [css background简写方式](http://www.webhek.com/the-background-shorthand-property-has-new-values/)
6. [CSS中背景图片定位方法](http://www.ruanyifeng.com/blog/2008/05/css_background_image_positioning.html)

## 边距、边框

### 属性详解

### 应用实例
+ 实现Tab页效果
+ 实现选中效果
+ 实现各向小三角

## 定位

### 属性详解

### 应用实例
+ 实现弹窗效果
+ 实现模态、遮罩效果


### 参考资料
0. [W3School CSS Position](http://www.w3school.com.cn/css/css_positioning.asp)
1. [CSS Position 博客园](http://www.cnblogs.com/polk6/archive/2013/07/26/3214847.html)
2. [CSS 定位属性](http://paranimage.com/css-position-attribute/)
3. [CSS浮动属性](http://paranimage.com/css-float-attribute/)
4. [CSS属性float详解](http://blog.163.com/wangming_cn/blog/static/1464109320096611441538/)
5. [图文详解CSS float](http://aqfy.net/561.html)
6. [CSS float深入剖析](http://www.w3cplus.com/css/float.html)
7. [CSS z-index用法](http://www.cnblogs.com/gisdream/archive/2010/06/10/1755891.html)


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
