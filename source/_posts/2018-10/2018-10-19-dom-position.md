---
layout: post
title: "屏幕事件，DOM位置及相关处理应用"
categories:
- [前端开发,Web开发,JavaScript,DOM]
tags:
- [DOM位置,DOM事件,屏幕位置]
---



# 相关通用属性（不兼容的就不用了）

## 关于事件属性

### Event.screenX, Event.screenY

相对于屏幕左上角的x,y坐标值，所有的鼠标事件都有定义

### Event.clientX,Event.clientY

相对于客户端左上角的x,y坐标值（不考虑滚动条），所有的鼠标事件都有定义

### Event.Touch

- screenX，screenY 相对于屏幕左上角的坐标
- clientX，clientY  相对于视口的坐标（不搞包含滚动距离）
- pageX，pageY 相对于视口的坐标（包含滚动距离）

### TouchEvent

- touches 每一个接触点的事件信息
- targetTouches 当前事件目标节点上触发的接触点的事件信息
- changedTouches 

## 关于DOM位置

### Element.clientWidth,Element.clientHeight

- 如果当前元素是根元素，表达的是不包含滚动条的视窗宽高
- 非根元素，表达的是不包含滚动条的元素内容宽高+`Padding`值，也就是`Padding`边界的宽高值

### Element.clientTop,Element.clientLeft

- 左边（或上边）的`Border`边到`Padding`边间的距离，一般就是`Border`的值(没有滚动条)，有滚动条的话要算滚动条的宽度

### Element.offsetWidth,Element.offsetHeight

- 包含`Padding`和`Border`，但不包含`Margin`的宽高值，也就是`Border`边界的宽高值

### Element.offsetTop,Element.offsetLeft

- `Border`左上角相对于`offsetParent`的值
- 一般`offsetParent`为`body`元素，有动态定位容器`offsetParent`就是这个定位的容器，如果在`table`内，这个`offsetParent`就是`table`

### Element.scrollWidth,Element.scrollHeight

- 元素整体的宽高尺寸，没有滚动条的话对应等于`offsetWidth`,`offsetHeight`的值，有滚动条的话就是整个内容区域的宽高（要加上可滚动的距离）

### Element.scrollTop,Element.scrollLeft

- 滚动了的距离

### Element.getBoundingClientRect()

- 包含`Border`和`Padding`，不包含`Margin`
- 返回元素`top`,`left`,`right`,`bottom`,`width`,`height`等属性，表达左上角和右下角的x,y的坐标值及尺寸
- 元素框的位置，不包含滚动条，要滚动条还需要进一步计算
- 返回的结果不是实时的只是一个当时的静态快照

### Element.getClientRects()

- 内联元素的每一个矩形框

### Document.elementFromPoint()

- 传递`x`，`y`坐标来获取指定视口坐标上是什么元素

### Node.contains()

- 判断某个节点是否包含某个其他节点，或者说某个其他节点是否是某个节点的后代

### Window.getComputedStyle()

- 获取样式尺寸，计算过的像素尺寸

