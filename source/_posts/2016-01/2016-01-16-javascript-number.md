---
layout: post
title: "JavaScript数值处理小结"
categories: [前端开发,Web开发,JavaScript]
tags: [JavaScript,数值,int,float]
---

## 1 数值处理的方法

### 1.1 全局转换方法

#### 1.1.1 parseFloat

从参数字符串的第一个字符开始进行解析，处理所有连续的有效浮点数字的部分（包含小数点），如果一个有效数位都没有就返回NaN，否则返回从第一个字符开始连续的数字部分的值。

#### 1.1.2 parseInt
从参数字符串的第一个字符开始，按照给定的进制（不提供默认为10）进行解析，处理所有连续的指定进制的有效整形数字的部分（不包含小数点），如果一个有效数位都没有就返回NaN，否则返回从第一个字符开始连续的数字部分的值。

### 1.2 Number对象


### 1.3 Math对象

#### 1.3.1 Math.min

#### 1.3.2 Math.max

#### 1.3.3 Math.floor

#### 1.3.4 Math.ceil

#### 1.3.5 Math.round