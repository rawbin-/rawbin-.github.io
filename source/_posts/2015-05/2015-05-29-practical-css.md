---
layout: post
title: "CSS实战详解"
categories: [开发技术]
tags: [CSS详解,CSS属性,CSS]
---
## 1 基本规则
+   缩写和扩展写法
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


## 2 背景

### 2.1 属性详解
+ background

设置元素的背景色，背景图，背景重复方式，背景滚动方式，背景位置
+ background-color

设置背景色，可以是颜色名称，十六进制值或者rgb值或者是transparent（默认）
+ background-image

可以给一个url对象或者none（默认）
+ background-repeat

repeat（默认）或者repeat-x或者repeat-y或者no-repeat,设置两个方向，单向或者不重复
+ background-attachment

随着页面滚动而滚动（scroll）或者固定在页面的某一个位置（fixed）
+ background-position

设置背景图片的位置，可以是top left center bottom right这些位置关键词，或者是百分比，也可以是像素值

### 2.2 应用实例
+ 实现进度条

        <div style="position:relative;width:100px;height:20px;border:solid 1px grey;">
        	<div style="width:50px;height:18px;margin:1px;background-color:blue;"></div>
        	<span style="position:absolute;left:40%;top:0;">50%</span>
        </div>

+ 实现淘宝评价效果
      	
        <div style="position:relative;width:120px;height:19px;background:url(star_both_five.png) 0 -18px no-repeat;">
        	<div style="height:20px;float:left;background:url(star_both_five.png) 0 0 no-repeat;width:70%;"></div>
        	<div style="position:absolute;left:0;top:0;height:19px;z-index:1">
        		<span style="display:inline-block;width:18px;height:18px;cursor:pointer;"></span>
        	</div>
        </div>    
        <div style="position:relative;width:120px;height:19px;background:url(star_both_five.png) 0 -18px no-repeat;">
        	<div style="height:19px;float:left;background:url(star_both_five.png) 0 0 no-repeat;width:70%;"></div>
        	<div style="position:absolute;left:0;top:0;height:19px;z-index:1">
        		<span style="display:inline-block;width:18px;height:18px;cursor:pointer;"></span>
        		<span style="display:inline-block;width:18px;height:18px;cursor:pointer;"></span>
        		<span style="display:inline-block;width:18px;height:18px;cursor:pointer;"></span>
        		<span style="display:inline-block;width:18px;height:18px;cursor:pointer;"></span>
        		<span style="display:inline-block;width:18px;height:18px;cursor:pointer;"></span>
        	</div>
        </div>        	
        <div style="position:relative;width:98px;height:19px;background:url(star_both_one.png) 0 -22px repeat-x;">
        	<div style="height:20px;float:left;background:url(star_both_one.png) 0 0 repeat-x;width:70%;"></div>
        	<div style="position:absolute;left:0;top:0;height:19px;z-index:1">
        		<span style="display:inline-block;width:18px;height:18px;cursor:pointer;"></span>
        	</div>
        </div>    
        <div style="position:relative;width:98px;height:19px;background:url(star_dark.png) 0 2px repeat-x;">
        	<div style="height:20px;float:left;background:url(star_bright.png) repeat-x;width:70%;"></div>
        	<div style="position:absolute;left:0;top:0;height:19px;z-index:1">
        		<span style="display:inline-block;width:18px;height:18px;cursor:pointer;"></span>
        	</div>
        </div>

### 2.3 参考资料
1. [CSS背景属性background详解](http://www.chinaz.com/design/2009/0918/92150.shtml)
2. [CSS背景属性详解](http://paranimage.com/css-background-attribute/)
3. [CSS新属性及多背景](http://www.html5jscss.com/css3%E6%AC%B2%E9%80%9F%E5%88%99%E4%B8%8D%E8%BE%BE-background%E6%96%B0%E5%B1%9E%E6%80%A7%E4%BB%A5%E5%8F%8A%E5%A4%9A%E8%83%8C%E6%99%AF.html)
4. [CSS3 background相关介绍](http://www.zhangxinxu.com/wordpress/2011/05/%e7%bf%bb%e8%af%91-css3-backgrounds%e7%9b%b8%e5%85%b3%e4%bb%8b%e7%bb%8d/)
5. [CSS3 background属性介绍](http://www.cnblogs.com/ATree/archive/2011/05/10/CSS3-Backgrounds.html)
6. [css background简写方式](http://www.webhek.com/the-background-shorthand-property-has-new-values/)
7. [CSS中背景图片定位方法](http://www.ruanyifeng.com/blog/2008/05/css_background_image_positioning.html)
8. [background-position 用法详细介绍](http://blog.csdn.net/jeamking/article/details/5617088)

## 3 边距、边框

### 3.1 属性详解

#### 3.1.1 margin
+ margin
+ margin-top
+ margin-right
+ margin-bottom
+ margin-left

margin 属性可以以上右下左的顺序设定各个方向的外边距值，也可以通过margin-方向来设定单个方向的外边距值，值可以是具体的像素、厘米等单位的值，也可以是百分比。

#### 3.1.2 padding
+ padding
+ padding-top
+ padding-right
+ padding-bottom
+ padding-left

padding 属性可以以上右下左的顺序设定各个方向的内边距值，也可以通过padding-方向来设定单个方向的内边距值，值可以是具体的像素、厘米等单位的值，也可以是百分比。

#### 3.1.3 border
+ border
+ border-width
+ border-style
+ border-color
+ border-top-width
+ border-top-style
+ border-top-color
+ border-right-width
+ border-right-style
+ border-right-color
+ border-bottom-width
+ border-bottom-style
+ border-bottom-color
+ border-left-width
+ border-left-style
+ border-left-color

border 依次设置宽度，样式和颜色，也可以通过border-width，border-style，border-color单独来以上右下左的顺序设置其中的每一项，同样也可以用border-方向-width,border-方向-style,border-方向-color,来单独设置各个方向的值。

### 3.2 应用实例
+ 实现Tab页效果

        	<div style="background-color:grey;height:28px;width:250px;border-bottom:1px solid white;">
        		<ul style="list-style-type:none;">
        			<li style="float:left;padding:4px;margin:1px; border-width: 1px;border-style: solid solid none; border-color:darkgrey;">页签一</li>
        			<li style="float:left;padding:4px;margin:1px; border-width: 1px;border-style: solid solid none; border-color:darkgrey; background-color:white;">页签二</li>
        			<li style="float:left;padding:4px;margin:1px; border-width: 1px;border-style: solid solid none; border-color:darkgrey;">页签三</li>
        		</ul>
        	</div>

+ 实现选中效果

        	<div style="text-align:center;">
        		<a href="#" style="display:inline-block;text-decoration:none;border: 1px solid lightgrey;width:20px;height:20px;background-color:grey;">1</a>
        		<a href="#" style="display:inline-block;;text-decoration:none;border: 1px solid red;width:20px;height:20px;background-color:transparent;">2</a>
        		<a href="#" style="display:inline-block;;text-decoration:none;border: 1px solid lightgrey;width:20px;height:20px;background-color:grey;">3</a>
        	</div>	

+ 实现各向小三角

        	<div style="border-style:solid;border-width: 50px; border-color: red green blue yellow; width:0;height:0;">
        	</div>
        	<div style="border-style:solid;border-width: 50px 50px 0px; border-color: red transparent transparent; width:0;height:0;">
        	</div>

### 3.3 参考资料
1. [使用css实现全兼容浏览器的三角形][1]
2. [用CSS代码绘制三角形 纯CSS绘制三角形的代码][5]
3. [CSS创建三角形（小三角）的几种方法][6]

## 4 定位

### 4.1 属性详解

#### 4.1.1 float
+ left
+ right

float的元素脱离了文档流，不能撑开父元素，同时也会影响文档流布局。浮动元素左右边界为父元素的边界或者是左右浮动元素的边界。
浮动元素的块级兄弟元素会显示在浮动元素的下方，文本会被"挤开"形成环绕的效果（包括块级兄弟元素中的文本）
可以在float元素的父元素中加overflow属性，使浮动元素撑开父元素，或者同时浮动父子元素。
可以在浮动元素的兄弟元素中加clear属性清除浮动元素的布局影响。
float 属性可以用来做文字环绕的效果，或者靠在靠右排列的效果，或者将块级元素显示为行内元素的效果。

#### 4.1.2 position
+ static

默认的普通文档流的定位方式，顺序排放。
+ relative

相对于元素当前位置的定位方式，元素还存在在文档流中，可以用top，left，right，bottom进行偏移定位。可以在元素位置保留（不被其他元素占用）的情况下进行位置调整。
+ absolute

此种定位方式的元素，脱离了文档流，定位不是完全绝对的；他的定位的相对位置是范围从小扩大的过程中遇到的第一个非static（默认文档流定位）定位的祖先元素，如果没有最后则相对于body元素。这种特性便于设定一个非static的祖先元素来界定其中absolute元素的相对定位。
+ fixed

这种定位方式的元素也脱离了文档流，他的定位的原点是视窗本身，不随文档的滚动而变动。

#### 4.1.3 z-index
通过设置不同大小的数值，来约束元素在z轴上的层叠位置，改变元素的堆叠效果

### 4.2 应用实例
+ 实现弹窗效果

        <div style="background-color:lightblue;border:1px solid grey;z-index 100;width:100px;height:100px;position:absolute;top:50%;left:50%;">
      </div>	

+ 实现进度条

        <div style="position:relative;width:100px;height:20px;border:solid 1px grey;">
        	<div style="width:50px;height:18px;margin:1px;background-color:blue;"></div>
        	<span style="position:absolute;left:40%;top:0;">50%</span>
        </div>


### 4.3 参考资料
1. [W3School CSS Position](http://www.w3school.com.cn/css/css_positioning.asp)
2. [CSS Position 博客园](http://www.cnblogs.com/polk6/archive/2013/07/26/3214847.html)
3. [CSS 定位属性](http://paranimage.com/css-position-attribute/)
4. [CSS浮动属性](http://paranimage.com/css-float-attribute/)
5. [CSS属性float详解](http://blog.163.com/wangming_cn/blog/static/1464109320096611441538/)
6. [图文详解CSS float](http://aqfy.net/561.html)
7. [CSS float深入剖析](http://www.w3cplus.com/css/float.html)
8. [CSS z-index用法](http://www.cnblogs.com/gisdream/archive/2010/06/10/1755891.html)
9. [CSS遮罩层的实现](http://fansofjava.iteye.com/blog/1522126)
10. [Bootstrap 模态框（Modal）插件](http://www.w3cschool.cc/bootstrap/bootstrap-modal-plugin.html)
11. [DIV+CSS模拟弹层遮罩效果](http://sunshuaij2ee.iteye.com/blog/949113)
12. [奇妙的CSS Shapes](http://www.cnblogs.com/coco1s/p/6992177.html)
13. [一些有趣的CSS话题汇总](https://github.com/chokcoco/iCSS)
14. [CSS2 属性列表 可继承性](https://www.w3.org/TR/CSS21/propidx.html)


## 5 参考资料
0. [Flex布局语法](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
1. [Flex布局实例](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)
2. [W3School在线教程](http://www.w3school.com.cn/)
3. [W3CSchool菜鸟教程](http://www.w3cschool.cc/)
4. [W3CHTMl教程](http://www.w3chtml.com/)
5. [20个实用的CSS技巧代码][2]
6. [CSS使用技巧][3]
7. [CSS使用技巧收集，包含CSS2/CSS3][4]
8. [CSS新手整理的CSS技巧][7]
9. [CSS使用技巧20则][8]
10. [十大CSS技巧][9]
11. [10个非常有用的CSS技巧][10]
12. [纯CSS气泡框实现方法探究](http://www.cnblogs.com/daxiong/articles/3158630.html)
13. [CSS布局奇淫巧计之-强大的负边距](http://www.cnblogs.com/2050/archive/2012/08/13/2636467.html)
14. [负值之美：负margin在页面布局中的应用](http://www.cnblogs.com/jscode/archive/2012/08/28/2660078.html)
15. [认识hasLayout——IE浏览器css bug的一大罪恶根源](http://neverned.blog.163.com/blog/static/1265524200933021130561/)
16. [认识hasLayout——IE浏览器css bug的一大罪恶根源](http://www.cnblogs.com/ideaplusl/archive/2011/07/07/2099843.html)
17. [CSS:haslayout知多少](http://blog.sina.com.cn/s/blog_51048da701018o29.html)
18. [hasLayrou综合](https://www.qianduan.net/comprehensive-haslayout/)
19. [hasLayout 介绍，以及其触发条件](http://blog.csdn.net/hedong37518585/article/details/6639263)
20. [关于IE6、IE7、IE8实现盒子阴影shadow的几个注意点](http://blog.sina.com.cn/s/blog_4c1e6a010101fvgw.html)

    [1]: http://www.bitscn.com/school/HTMLCSS/201410/337133.html&quot;使用css实现全兼容浏览器的三角形&quot;
    [2]: http://www.w3cplus.com/css/20-incredibly-useful-CSS-snippets-for-developers&quot;20个实用的CSS技巧代码&quot;
    [3]: http://www.ruanyifeng.com/blog/2010/03/css_cookbook.html&quot;CSS使用技巧&quot;
    [4]: http://www.shejidaren.com/css%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E6%94%B6%E9%9B%86-%E5%8C%85%E5%90%ABcss2-css3.html&quot;CSS使用技巧收集，包含CSS2/CSS3&quot;
    [5]: http://www.jb51.net/css/54994.html"用CSS代码绘制三角形 纯CSS绘制三角形的代码"
    [6]: http://www.daqianduan.com/4721.html&quot;CSS创建三角形（小三角）的几种方法&quot;
    [7]: http://www.duote.com/tech/1/1952.html&quot;CSS新手整理的CSS技巧&quot;
    [8]: http://www.jb51.net/article/1187.htm&quot;CSS使用技巧20则&quot;
    [9]: http://blog.csdn.net/budinger/article/details/18086543&quot;十大CSS技巧&quot;
    [10]: http://www.cnblogs.com/hnyei/archive/2011/11/12/hnyei.html&quot;10个非常有用的CSS技巧&quot;

