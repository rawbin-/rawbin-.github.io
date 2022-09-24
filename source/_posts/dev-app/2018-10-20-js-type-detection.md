---
layout: post
title: "JavaScript检测变量类型 typeof instanceof Object.toString"
categories: [前端开发,Web开发,JavaScript,代码规范]
tags: [css,markdown,W3C,代码规范]
---



#  检测方法

## typeof 操作符

- 只能检测出基本类型`undefined`,`object`,`string`,`boolean`,`number`,`function`,`symbol`
- 检测原始值的类型，其他引用类型统一都在`object`中，如果要区分`object`的不同引用类型，需要用其他方法

## Object.prototype.toString

- 可以根据返回不同对象包装函数类型的字符串 
- 可以用来区分系统内类类型

## instanceof

- 可以用来检测自定义类的类型，检测对象是否属于指定的类的实例
- 不同执行上下文不同的构造函数属于不同的结果，比如即使同样是`Array`也不行，所以有了`Array.isArray`来处理类似数组的判断
- `o instance t`检测`o`是否在`t.protoptype`的原型链上

## constructor属性

- 通过对象的`constructor`属性来判断
- 多个执行上下文（比如浏览器的多个框架子页面）无法判断出来是同一个，即使内容完全一样也不是同一个，参考`instanceof`

## 构造函数的名称

- 非标准的方法，让不同上下文中的构造函数都用同一个名字，可以参考下v8源码，`Array.isArray`的实现

