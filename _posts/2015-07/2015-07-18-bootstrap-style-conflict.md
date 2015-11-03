---
layout: post
title: "解决使用bootstrap时的CSS样式冲突问题"
categories: [开发技术,Web前端]
tags: [bootstrap,css,样式冲突]

---

遇到过两个类似的问题，在这里小结一下：

+ bootstrap的box-sizing 引起的宽度挤压 导致的内容换行
+ 自定义样式被bootstrap覆盖 导致样式错乱

一般的使用场景是：

+ 页面本身是使用bootstrap风格的
+ 页面本身是自定义样式，单独引入bootstrap风格的组件的

在上面第一种场景里面，问题相对容易解决，找到对应的样式，在自定义的样式表中重写相应的样式，并在合适的位置添加 !important 标志即可。

在自定义样式和bootstrap组件混搭的样式里面，问题相对难处理。

样式的引入要么bootstrap在前，要么自定义样式在前，但不论怎样都会存在或多或少的样式冲突问题。

冲突的来源大致如下：

+ bootstrap的box-sizing 引起的
+ bootstrap中对基本元素 如input ul li 等元素的样式定义引起的
+ bootstrap和自定义样式中的同名样式覆盖引起的

冲突的解决办法：

+ 不使用相关的组件

这样可以避免由组件引入的bootstrap样式引起的冲突问题，但结果要么重新选组件，要么自己写组件

+  使用相关组件，处理样式问题
	+ 添加样式的方法
	
	不引人bootstrap完整样式，使用浏览器调试工具将使用到的样式剥离出来，加入相应的命名空间
	
	+ 删除样式的方法。
	
	引入完整的bootstrap样式，将引起冲突的样式进行重写或者删除。
	

 