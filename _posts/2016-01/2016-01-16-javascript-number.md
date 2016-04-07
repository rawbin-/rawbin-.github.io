---
layout: post
title: "JavaScript数值处理小结"
categories: [前端开发,Web开发,JavaScript]
tags: [JavaScript,数值,int,float]
---

## 数值处理的方法
### 全局转换方法
#### parseFloat

从参数字符串的第一个字符开始进行解析，处理所有连续的有效浮点数字的部分（包含小数点），如果一个有效数位都没有就返回NaN，否则返回从第一个字符开始连续的数字部分的值。

#### parseInt
从参数字符串的第一个字符开始，按照给定的进制（不提供默认为10）进行解析，处理所有连续的指定进制的有效整形数字的部分（不包含小数点），如果一个有效数位都没有就返回NaN，否则返回从第一个字符开始连续的数字部分的值。

### Number对象


### Math对象

#### Math.min

#### Math.max

#### Math.floor

#### Math.ceil

#### Math.round