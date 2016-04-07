---
layout: post
title: "ECMAScript 2015 ES 6"
categories: [开发技术]
tags: [JavaScript,ECMAScript,ECMAScript2015, ECMAScript6, ES2015, ES6]

---

# 特性介绍

## 语法改进

### 支持模块化语法

# module,import,export

### 支持面向对象语法
+ 类（class），继承（extends），静态方法（static），属性存取（getter,setter）

### 块级作用域 let
+ 只在声明的代码块中有效
+ 不存在声明提前
+ 不允许重复声明       

### 解构赋值
+ 适合var，let，const
+ 适合字符串、数组、对象（可嵌套）、函数参数
+ 可带默认值
+ 用途
    + 交换变量
    + 函数返回多个值
    + 函数参数定义及参数默认值
    + 提取对象数据，JSON数据
    + 遍历Map解包
    + 模块部分加载

### 扩展运算符


### 生成器
+ 特性介绍
+ 应用举例

## 函数
+ 支持参数解构赋值
+ 参数设置默认值（默认参数），默认参数的作用域是函数作用域,默认参数必须在最后
+ 扩展运算符
+ rest参数，rest参数必须是最后一个
+ 箭头函数（不能当构造，无arguments，this固定为定义时所在的对象）
+ 尾调用、尾递归优化

## 数组
+ 增加from，of原型方法，
+ 增加entries，keys，values，includes实例方法
+ for of遍历

## 对象
+ 属性、方法简写
+ is，assign，getPrototypeOf，setPrototypeOf原型方法
+ 新增对象Proxy， Reflect，Promise
+ Set，Map,Generator,Iterator,Promise

## 字符串
+ 简单模板字符串${JS表达式}
+ 多行字符串
+ 标签模板
+ 新增includes，startsWith，endsWith，repeat方法


## 其他
+ const关键字申明常量



# 使用方法



## 参考资料
0. [ecma.org](http://www.ecmascript.org/)
1. [ECMAScript-262 6th](http://www.ecma-international.org/ecma-262/6.0/index.html)
2. [ECMAScript-262 6th](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
2. [ECMAScript-262 5-](http://www.ecma-international.org/publications/standards/Ecma-262-arch.htm)
3. [ECMAScript 6浏览器支持情况](http://kangax.github.io/compat-table/es6/)
3. [ECMAScript-262 6th Firefox支持](https://developer.mozilla.org/en-US/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla)
4. [ECMAScript6值得看好的特性](http://blog.csdn.net/ydj9931/article/details/7028390)
5. [来试试ECMAScript6](http://blog.chinaunix.net/xmlrpc.php?r=blog/article&uid=26672038&id=4410549)
6. [ES6将会有的几个新东西](http://www.cnblogs.com/ziyunfei/archive/2012/11/25/2784392.html)
7. [ES6新特性概览](http://www.cnblogs.com/Wayou/p/es6_new_features.html)
7. [现在开始使用ES6](http://www.open-open.com/lib/view/open1425130804218.html)
8. [ECMAScript6入门](http://es6.ruanyifeng.com/)
9. [Understanding ECMAScript6](https://leanpub.com/understandinges6/read/)
10. [理解ES6](https://github.com/lenville/understandinges6)
14. [Using Babel](https://babeljs.io/docs/setup/)
11. [ES6环境支持](http://babeljs.io/)
12. [ES6 Fiddle](http://www.es6fiddle.com/)
13. [Learn ES2015](https://babeljs.io/docs/learn-es2015/)
14. [ES6 tools](https://github.com/addyosmani/es6-tools)
15. [traceur-compiler](https://github.com/google/traceur-compiler)
16. [traceur-online](http://google.github.io/traceur-compiler/demo/repl.html#)
17. [typescript](http://www.typescriptlang.org/)