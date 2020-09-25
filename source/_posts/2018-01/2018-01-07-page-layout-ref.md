---
layout: post
title: "前端页面布局简明参考手册"
categories: [前端开发,Web开发,CSS]
tags: [CSS,页面布局]
---





### 布局方式分类

#### 固定布局

- 栅格布局
  - 优势
    - 布局可预见，更好控制行长，容易设计和制作
  - 劣势
    - 浏览器窗口比网页窄，内容显示不全；大屏幕下白边很多；大字体行长难以控制，用户无法控制
  - 实现方式
    - 固定内容块的宽度

#### 流式布局

- 优势
  - 可以避免留白，桌面浏览器用户可以控制窗口内容和宽度，没有水平滚动条
- 劣势
  - 大屏显示器下行长过长，不适合阅读；内容难以预见
- 实现方式
  - 设置百分比宽度
  - 设置min-width和max-width，控制阈值



#### 弹性布局

- 优势
  - 允许文本尺寸适当变动时，提供一致的布局体验；比固定和流式布局更紧密控制行长
- 劣势
  - 图像和影像不能随着文本和布局的其他部分缩放，最大文本尺寸时布局宽度可能超过浏览器宽度，在解决不同设备和浏览器的大小时不是很有用
- 实现方式
  - 使用em来设置尺寸



#### 混合布局

- 将像素，百分比，em等在合适的地方同时使用



#### 响应式布局

- 实现方式
  - 媒体查询，根据不同屏幕设置规则
  - 流式布局，使用em或者百分比等相对单位设置总体宽度
  - 弹性图片，使用相对单位确保图片再大也不会超过其容器



#### 自适应布局



#### Flex 布局



#### Grid布局



#### 