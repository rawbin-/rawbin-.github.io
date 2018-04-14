---
layout: post
title: "页面布局 固定布局 流式布局  响应式布局 自适应布局"
categories: [Web开发,前端开发]
tags: [CSS,JavaScript,响应式布局,自适应布局,流式布局,固定布局,布局]
---





## 1 布局方式

### 1.1 静态/固定布局

站点使用固定的宽度（通常是像素宽度），改变浏览器的宽度或者用不同的设备上查看站点的展示效果一样，这会在小屏设备上引入横向滚动条，对移动设备体验不友好。

#### 1.1.1 优势

- 设计简单，维护方便
- 不用考虑图片的尺寸问题

#### 1.1.2 劣势

- 在大屏设备上会有大量白边
- 不适应不同的设备，对用户不友好，不利于搜索引擎优化

### 1.2 流式布局

站点使用百分比宽度（或者其他相对宽度，某些内容也可以固定宽度），页面相对于不同的浏览器或者不同设备的宽度，流式适应。

#### 1.2.1 优势

- 比固定宽度更友好
- 不同设备上展现白边较少

#### 1.2.2 劣势

- 需要保证设计在不同的设备上没有问题，有时候具有挑战性
- 固定宽度的内容（比如图片和视频），不能根据不同对的屏幕进行适配
- 大屏设备上需要确保内容足够，以减少过度的白边

### 1.3 自适应布局

针对特定的设备尺寸进行断点划分，或者针对特定的设备有不同的设计，需要针对新设备进行设计。

#### 1.3.1 优势

- 根据不同的设备类型进行专门设计适配，对用户友好
- 移动设备可以感知用户环境
- 设计者可以基于智能设备的用户数据优化广告投放

#### 1.3.2 劣势

- 不便于对不同的屏幕尺寸使用不同的布局，成本相对较高，每一种尺寸都是新的一套
- 在创建不同屏幕尺寸不同布局的时候不能覆盖所有的设备，尤其是新出来的设备

### 1.4 响应式布局

在流式布局的基础上，针对不同宽度的设备进行不同的设计，不需要针对新设备进行设计。

#### 1.4.1 优势

- 比自适应加载更快，所有设备只有一个主布局
- 对搜索引擎友好，容易实现

#### 1.4.2 劣势

- 实现难度较高，需要一个完整的开发测试流程来保证在所有设备上好使
- 元素位置会移动，广告可能会丢失
- 下载时间较长



## 2 自适应布局和响应式布局区别

|      | 自适应                                      | 响应式                                  |
| ---- | ---------------------------------------- | ------------------------------------ |
| 设备识别 | 使用服务端或者客户端代码来检测设备                        | 使用媒体查询来检测设备                          |
| 实现方式 | 提供单独的HTML或页面，使用CSS根据屏幕大小来改变内容展现          | 使用弹性图片和流式网格来适配图片                     |
| 内容优化 | 内容预先选择，只有针对设备的需要的内容才会被下载                 | 不管什么设备都会下载所有内容，不管是否需要                |
| 设备优化 | 使用不同的模板，针对不同的设备进行优化                      | 对所有设备使用同一个模板                         |
| 特性   | 使用脚本来支撑不同的设备和屏幕尺寸，检测不同的设备类型来改变站点的展现行为    | 包含所有的移动端和桌面浏览器支持的特性                  |
| 性能   | 只加载用户设备需要的资源，加载较快；图片针对特定的设备分辨率优化，减少文件尺寸和加载时间 | 加载针对所有设备的资源，加载较慢；加载全尺寸的图片通过压缩来适配用户设备 |
| 开发   | 对已经存在的站点来使用可适应的模板                        | 整个站点重建                               |
| 部署   | 门槛较高，开发者需要扎实的JavaScript                  | 设计和实现曲线陡峭，用户体验需要根据不同的设备定制            |
|      | 不需要站点重建；需要扎实的JavaScript和CSS知识；不同的设备性能表现良好 | 对新站点更容易实现；需要站点重建；不同设备上性能表现较差         |
|      |                                          |                                      |
|      |                                          |                                      |



### 2.1 参考资料

0. [Static Fixed Adaptive Responsive区别样例](http://www.liquidapsive.com/)
1. [UXPin responsive vs adaptive](https://www.uxpin.com/studio/blog/responsive-vs-adaptive-design-whats-best-choice-designers/)
2. [Adaptive Web Design vs. Responsive Web Design](https://visual.ly/community/infographic/technology/adaptive-web-design-vs-responsive-web-design)
3. [Adaptive vs Responsive Design](https://www.interaction-design.org/literature/article/adaptive-vs-responsive-design)
4. [Web Design 101: Adaptive Vs. Responsive Design](http://www.rasmussen.edu/degrees/design/blog/adaptive-versus-responsive-design/)
5. [Fixed vs Fluid vs Adaptive vs Responsive Layout](https://www.markupbox.com/blog/fixed-vs-fluid-vs-adaptive-vs-responsive-layout/)
6. [What’s the Difference Between Fixed, Fluid, Adaptive, and Responsive Web Design?](http://blog.teamtreehouse.com/whats-the-difference-between-fixed-fluid-adaptive-and-responsive-web-design-treehouse)
7. [Responsive vs. Adaptive vs. Fluid Design](https://learn.onemonth.com/responsive-vs-adaptive-vs-fluid-design/)
8. [Adaptive vs. Responsive Web Design: Quantifying the Difference on Mobile](http://blog.catchpoint.com/2014/07/16/adaptive-vs-responsive-web-design-quantifying-difference/)
9. [Difference between responsive and adaptive](https://css-tricks.com/the-difference-between-responsive-and-adaptive-design/)
10. [What’s the difference between adaptive and responsive web design?](http://www.digitalfamily.com/tutorials/css-article/whats-the-difference-between-adaptive-and-responsive-web-design/)
11. [Responsive Web Design](http://alistapart.com/article/responsive-web-design)
12. [Responsive design 和 Adaptive design 的区别？](https://www.zhihu.com/question/20628050)
13. [布局类型及其应用场景区别](https://www.cnblogs.com/yanayana/p/7066948.html)
14. [布局类型区别](https://www.cnblogs.com/snowcan/p/7528404.html)
15. [布局样例参考](http://wow.techbrood.com/fiddle/1753)
16. [布局样例参考站点](http://www.liquidapsive.com/)
17. [响应式和自适应区别](http://blog.csdn.net/zxl1033394132/article/details/50574382)
18. [响应式与自适应设计:设计师的最佳选择是什么?](http://blog.csdn.net/jljf_hh/article/details/50524324)
19. [Is adaptive better than responsive design?](https://thenextweb.com/dd/2015/09/01/is-adaptive-better-than-responsive-design/)
20. [Responsive vs. Adaptive Design: What’s the Best Choice for Designers?](https://www.uxpin.com/studio/blog/responsive-vs-adaptive-design-whats-best-choice-designers/)
21. [Adaptive Web Design](https://en.wikipedia.org/wiki/Adaptive_web_design)
22. [Responsive Web Design](https://en.wikipedia.org/wiki/Responsive_web_design)
23. [adaptive vs responsive](https://viljamis.com/2012/adaptive-vs-responsive-design/)
24. [详解响应式布局设计](http://caibaojian.com/356.html)
25. [Responsive vs Adaptive Design](http://mediumwell.com/responsive-adaptive-mobile/)
26. [The Pros and Cons of Adaptive Web Design](http://mediumwell.com/pros-cons-adaptive-web-design-web-developers-need-know/)
27. [difference between responsive and adaptive](https://www.mockplus.com/blog/post/difference-between-responsive-and-adaptive)
28. [Responsive Web Design – What It Is And How To Use It](https://www.smashingmagazine.com/2011/01/guidelines-for-responsive-web-design/)
29. [adaptive vs responsive design](https://usabilitygeek.com/adaptive-vs-responsive-design/)
30. [What is Adaptive Design and is It Different from Responsive Design](https://www.sitepoint.com/adaptive-design-different-responsive-design/)
31. [CSS布局解决方案（终结版）](https://segmentfault.com/a/1190000013565024)