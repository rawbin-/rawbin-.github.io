---
layout: post
title: "2020前端遮罩镂空聚焦引导技术方案大全"
categories: [前端开发,Web开发,JavaScript,代码规范]
tags: [css,markdown,W3C,代码规范]
---



# 开胃菜：简单遮罩、形状

##  遮罩

> 教科书式的标准做法，需要考虑内容有滚动条的时候的在不同情况下的处理

```
.mask{
	position:fixed;
	top:0;
	right:0;
	bottom:0;
	left:0;
	background: rgba(0, 0, 0, 0.4);
  z-index: 10;
}
```

## border

### 简单看一下原理图就知道了

```
.border-show{
  width:0;
  height:0;
  border-top: 100px solid aqua;
  border-right: 100px solid green;
  border-bottom: 100px solid fuchsia ;
  border-left: 100px solid chartreuse;
}
```

<html>
	<head>
		<style type="text/css">
		.border-show{
  width:0;
  height:0;
  border-top: 100px solid aqua;
  border-right: 100px solid green;
  border-bottom: 100px solid fuchsia ;
  border-left: 100px solid chartreuse;
}
		</style>
	</head>
	<body>
		<div class="border-show"></div>
	</body>
</html>



### 画一个三角形

 ```
.border-shape-triangle{
  width:0;
  height:0;
  border-top: 100px solid aqua;
  border-right: 100px solid transparent;
  /*border-bottom: 100px solid fuchsia ;*/
  border-left: 100px solid transparent;
}
 ```

### 换个角度的三角形

```
.border-shape-triangle-left{
  width:0;
  height:0;
  border-top: 100px solid aqua;
  border-right: 100px solid transparent;
  /*border-bottom: 100px solid fuchsia ;*/
  /*border-left: 100px solid transparent;*/
}
```

### 向右的大于号

```
.border-shape-arrow{
  width:100px;
  height:100px;
  border-right:20px solid blue;
  border-top: 20px solid blue;
  transform: rotate(45deg);
}
```



## outline

## 伪元素:before或:after

## box-shadow

## background

##  background-*

## mask-*

## mix-blend-mode



# 难度提升：镂空遮罩

## border

## outline

## box-shadow

## background

##  background-*

## mask-*

## mix-blend-mode

## Canvas

## SVG

## 图片

## DIV 拼接



# 正式挑战：镂空可点击



# 参考资料

0. [CSS实现镂空遮罩](https://juejin.im/post/6844903919059992584)
1. [CSS实现镂空效果](https://juejin.im/post/6844903745248034829)
2. [CSS实现镂空效果](https://www.jqhtml.com/24047.html)
3. [遮罩层镂空效果的多种实现方法](https://www.cnblogs.com/ypppt/p/12883912.html)
4. [遮罩层镂空效果的多种实现方法](https://segmentfault.com/a/1190000022588200)
5. [CSS镂空遮罩研究](https://zhuanlan.zhihu.com/p/98697301)
6. [用纯 CSS 实现镂空效果](https://juejin.im/post/6844903781591842829)
7. [幻术，一行代码实现镂空效果](https://www.codercto.com/a/32810.html)
8. [用纯CSS实现镂空效果的示例代码](https://www.jb51.net/css/660316.html)
9. [CSS遮罩CSS3 mask/masks详细介绍](CSS遮罩CSS3 mask/masks详细介绍)
10. [CSS遮罩——如何在CSS中使用遮罩](https://www.w3cplus.com/css3/css-masking.html)
11. [史上最强大的40多个纯CSS绘制的图形](http://www.codeceo.com/article/40-css-shapes.html)
12. [CSS3绘制各种形状的图形](https://juejin.im/post/6844903881680355341)

