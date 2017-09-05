---
layout: post
title: "JavaScript UTC 时间本地时间问题"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,UTC,Date]
---



### 问题由来

后台返回一个`2016-08`的月份字符串,在前端被展示成了`2016年7月`，大致代码如下

```javascript
var dateObj = new Date("2016-08");
console.log([dateObj.getFullYear(),'年',dateObj.getMonth(),'月'].join(''));
```



### 原因分析

+ 首先排除浏览器兼容问题
+ 当时区为西半球的时区的时候，Chrome、Firefox、Safari都有问题。
+ 在v8源码里面看起来不是很清晰，先看看ECMAScript规范的描述。
+ 第一行`dateObj = new Date("2016-08")` 得到的是一个UTC时间，也就是一个世界人民看着都一样的绝对时间（当然他也是一个相对于1970年1月1日的一个结果），值为`2016-08-01 00:00:00`
+ 然后如果是西半球的话时间比UTC晚，也就是还没有到`2016-08-01 00:00:00`,也就是还在`2016-07-31`的某个时间
+ 这个时候使用`getMonth`得到的是本地时间，也就是会根据UTC时间减掉一部分（考虑时区时间差）后的时间。得到的结果自然是7月。
+ 自然东半球的时间比西半球早，也就是始终在八月份，得到的结果是预期的8月。
+ 东西半球的时间差约为24小时，也就是一天。所以在本地时间取天以下的时间单位的时候都可能出现差异（有的很小很小，因为时间本身是不准的）。
+ 在刚刚的例子中，会因为一天的差异导致一月的差异，所以月份也会受到影响

### 解决办法

使用UTC时间获取，屏蔽时区的问题。

```javascript
console.log([dateObj.getFullYear(),'年',dateObj.getUTCMonth(),'月'].join(''));
```

