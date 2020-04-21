---
layout: post
title: "Node PDF文件生成带中文字体尺寸太大问题解决"
categories: [Web开发,前端开发,自动化]
tags: [自动化,Node,PDF,字体]
---



## 关于PDF版式文件生成需要中文字体的问题
- 载入完整的中文字体的话，比如pingfang.ttf 那么再小的PDF都会加上整个字体的尺寸，也就是8M+，这个是不能忍的
- 上一次的解决办法是将唯一的那一部分文字用图片的方式替代
- 现在文字需要动态改变那么图片的方式已经失效

## 通过字体裁剪的方式解决 [代码参考](https://github.com/rawbin-/node-pdf-test)

### 通过`pdfkit` 这个库来做的话，本身就默认字体裁剪，也就是没有上面的PDF文件过大的问题
- 参考 `with-pdfkit.js`
### 通过`pdf-lib` 这个库来做，默认没有裁剪，就会出现上面的问题，看源码可以设置裁剪参数
- 参考 `wiith-pdflib.js`
### 当然你也可以蛋疼自己裁剪
- 参考 `with-pdflig-fontmin.js` 

