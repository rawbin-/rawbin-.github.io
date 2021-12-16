---
layout: post
title: "Chrome数组排序那些事"
sticky: 98
cagegories: [Web开发,前端开发,移动端]
tags: [异步,JavaScript,NodeJS,浏览器]
---

# 前言

[v8源码](https://github.com/v8/v8) 可以在[github](https://github.com/v8/v8)上面直接下，[早期的ArraySort](https://github.com/v8/v8/blob/98d735069d0937f367852ed968a33210ceb527c2/src/js/array.js#L709) 是用JS来实现的，整合了插入排序和快速排序两种算法，在10以下就用插入，以上就用快排。后来更换了算法和实现方式，到目前为止(20211125) [最新的数组排序还是](https://github.com/v8/v8/blob/main/third_party/v8/builtins/array-sort.tq) 使用的TimSort这个算法，看起来是从[Python](https://github.com/python/cpython/blob/main/Objects/listobject.c)那学来的。 

详情可以参考[这里](https://juejin.cn/post/6844903953964990471)，也有一些过程内容可以[译文](https://juejin.cn/post/6844903765707866119) 或者 [原版V8开发博客](https://v8.dev/blog/array-sort)

解决的问题就一个，[排序稳定性问题](https://bugs.chromium.org/p/v8/issues/detail?id=90)

# 先看看这里面的几个算法





# 排序算法集锦





# 排序算法对比评估

## 时间复杂度



## 空间复杂度



## 稳定性



## 正确性



##  易维护性



