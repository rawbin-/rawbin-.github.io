---
layout: post
title: "JavaScript中的金额（钱）数据处理，始终保留两位小数"
categories: [前端开发,Web开发]
tags: [JavaScript,四舍五入]
---





### 参考资料

0. [ROUNDING AND TRUNCATING NUMBERS IN JAVASCRIPT](https://pawelgrzybek.com/rounding-and-truncating-numbers-in-javascript/)

1. [JavaScript Number](https://javascript.info/number)

2. [Rounding Decimals in JavaScript](http://www.jacklmoore.com/notes/rounding-in-javascript/)

3. [Round to at most 2 decimal places (only if necessary)](https://code.i-harness.com/en/q/b48e52)

4. [如何避开JavaScript浮点数计算精度问题（如0.1+0.2!==0.3）](https://blog.csdn.net/u013347241/article/details/79210840)

5. [JavaScript 浮点数运算的精度问题](https://www.css88.com/archives/7340)

6. [JavaScript 格式化数字、金额、千分位、保留几位小数、舍入舍去](https://www.css88.com/archives/7324)

7. [javaScript 中 toFixed() 精度问题及解决方案](https://www.jianshu.com/p/c2eca2b1be3c?fromApp=1)

8. [toFixed计算错误(依赖银行家舍入法的缺陷)解决方法](http://www.chengfeilong.com/toFixed)

9. [JavaScript 浮点数精度之谜](https://mp.weixin.qq.com/s?__biz=MzAwNTAzMjcxNg==&mid=2651424976&idx=1&sn=7c0d3e315ceb25b22dd3eca42798a6b7&chksm=80dff6b3b7a87fa58a79591819af0d78ee3190d1153c73d5530eb196ad12b43c9be4a7237b09&mpshare=1&scene=1&srcid=0802C7xoLq9bLQXMEViEHTSM&key=92e1ed454942560da716c8641291a5f89ff92ec74c4a1d7e26aa3b115d4f07370a00d2c8399463eda46c2e11e4fe48c8c10cfae499e900311b9a85d2538acdd7eccfbe8de145175cc8c131b27e697381&ascene=0&uin=MjEzMTAwMzgyNQ%3D%3D&devicetype=iMac+MacBookAir7%2C1+OSX+OSX+10.12.6+build(16G29)&version=12020)

10. [js浮点数精度问题的前世今生？](https://www.zhihu.com/question/26806477)

11. [JS的浮点数计算精度丢失问题解决方案](https://blog.csdn.net/u012143360/article/details/53148591)

12. [JavaScript 浮点数陷阱及解法](https://www.cnblogs.com/sunshq/p/7682109.html)

