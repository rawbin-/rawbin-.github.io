---
layout: post
title: "JavaScript 隐式类型转换 JS类型转换"
categories: [编程语言]
tags: [类型转换,JavaScript,隐式类型转换]
---

### 1 介绍
JavaScript作为一种弱类型的语言，我们很多时候不需要直接关心到底是经过怎样的转换过程。JavaScript会在需要我们来探索下到底都是些什么样的过程~~，同时了解在什么样的情况下JavaScript需要什么样的值。

先介绍基本的概念，然后说明各种类型的转换规则，最后说转换的场景。

### 2 基本概念
以下的讲解过程中可能需要用到的概念，这里做一个列表。
JavaScript中的类型大致可以分为六类：
Undefined，Null，String，Boolean，Number，Object。

其中Undefined，Null，String，Boolean，Number这几种类型的成员叫做原始值。

Object的成员叫对象，包括Array，Function，Math，Date，JSON，RegExp等除了原始值之外的所有的类型的成员。

#### 2.1 toString，valueOf
+ Object
    + toString返回"[object ",class,"]"拼接的字符串，其中class为类名，一般为"[object Object]"
    + valueOf 返回值为对象本身
+ String
    + toString，valueOf都返回String对象的原始字符串
+ Array
    + toString join方法返回的结果
    + valueOf 返回对象原始值
+ Number
    + toString返回数字的字符串表示
    + valueOf返回Number对象的原始值
+ Date
    + toString 返回本地时间字符串
    + valueOf 返回毫秒时间
+ Boolean
    + toString 返回"true"或"false"
    + valueOf 返回原始值


### 3 类型转换

[这里][3]有二维全部的类型转换表，可以看看。  
也可以到[这里][4]看看单个的表格。

#### 3.1 转换为原始类型
+ 当输入类型为Undefined,Null,String,Boolean,Number 则转换后的结果为原样
+ 当输入类型为Object时，转换方法如下：
    指定转换的方式为Number或者String两种方式，不指定默认为Number，日期类型特殊，即使不指定为String，默认也按照String的方式转换。
    如果转换为字符串，则优先调用toString方法，如果不存在则调用valueOf方法，如果还是不存在则抛出TypeError异常。
    如果转换为数字，则优先调用valueOf方法，如果不存在则调用toString方法，如果还是不存在则抛出TypeError异常。
    
#### 3.2 转换为Boolean值
+ 输入类型Undefined，Null转换为false
+ 输入类型为Boolean值，则结果为原样输入
+ 输入类型为Number，如果参数是+0,-0,,NaN结果为false，否则为true
+ 输入类型为字符串，如果为空字符串结果为false，否则为true
+ 输入类型为Object对象，始终为true

#### 3.3 转换为Number
+ 输入类型为Undefined，返回NaN
+ 输入类型为Null，返回+0，即0
+ 输入类型为Boolean，如果是true，则返回1，否则返回0
+ 输入类型为Number，则返回原样输入
+ 输入类型为String，如果全为数字则返回数字的值，否则返回NaN
+ 输入类型为Object，则先转换为原始值，再转换为Number，否则为NaN

#### 3.4 转换为String
+ 输入类型为Undefined，Null，则输出分别为他们的字符串值"undefined","null"
+ 输入类型为Boolean，则返回toString的结果"true"或者"false"
+ 输入类型为Number，则返回输入toString的结果
+ 输入类型为String，则返回原样输入
+ 输入类型为Object，则先将其转换为原始值，再返回toString的结果

#### 3.5 转换为Object
+ 输入类型为Undefined，Null，则抛出TypeError异常
+ 输入类型为Boolean，则返回原始值为输入的Boolean对象
+ 输入类型为Number，则返回原始值为输入的Number对象
+ 输入类型为String，则返回原始值为输入的String对象
+ 输入类型为Object，则返回原样输入。


### 4 类型转换的场景
+ 转换为Number
    + 运算符
        + 一元+，-运算符
        + ++，--运算符
        + 按位取反会转换为Int32的有符号整数
        + *，/, %，-运算
        + +运算操作数没有一个为字符串，则均转换为数字进行求和运算
        + 移位操作>>,<<会将左操作数转换为Int32,右操作数转换为无符号32位整数
        +  无符号移位运算符>>>,<<<,会将两操作数都转换为无符号32位整数
        +  <, <=, >, >=操作数都不是String类型，则都转换为数值进行运算
        +  ==，!=操作数中只有String和Number类型，或者其中有一个是Boolean
        +  二进制位运算会将操作数转为Int32进行运算

+ 转换为Boolean
    + 运算符
        + && || ！运算会先转换为Boolean
        + 条件运算会先转为Boolean
    + 语句
        + if，while，do-while，for


+ 转换为String
    + 运算符
        + 加号其中一个操作数位字符串，则转换为字符串拼接运算
        + <, <=, >, >=操作数至少有一个String类型，则都转换为字符串进行运算

+ 转换为原始值
    + 运算符
        + ==，！= 其中一个操作数为Object类型
        
+ 转换为对象
    + 语句
        + for-in

### 5 应用举例
+ 逻辑运算&&， ||
    + 逻辑与表达式的值是第一个不为真的表达式的值或者最后一个表达式的值
    + 逻辑或为第一个值为真的表达式的值，或者最后一个表达式的值
    + 表达式的结果还需要进行转Boolean运算
    比如 1 == 2 || 3 == 4 || 45 || null，表达式的结果为第一个为真的表达式的值，这里为45（短路规则）
    如果放到条件语句如if中，需要再进行一步转Boolean的运算

    

### 6 参考资料
0. [JavaScript数据类型转换][0]
1. [JavaScript类型转换规则][1]
2. [JavaScript中的隐式转换][2]
3. [JavaScript的数据类型转换][3]
4. [JavaScript数据类型转换那些事][4]
5. [JavaScript学习之类型隐式转换][5]
6. [JavaScript数据类型转换方法汇总][6]
7. [无处不在的隐式转换][7]

[0]: http://javascript.ruanyifeng.com/grammar/conversion.html#toc5 "JavaScript数据类型转换"
[1]: http://www.cnblogs.com/mizzle/archive/2011/08/12/2135885.html "JavaScript类型转换规则"
[2]: http://www.cnblogs.com/snandy/archive/2011/03/18/1987940.html "JavaScript中的隐式转换" 
[3]: http://blog.csdn.net/yangqicong/article/details/6865513 "JavaScript的数据类型转换"
[4]: http://www.cnblogs.com/2050/archive/2012/08/17/2644189.html "JavaScript数据类型转换那些事"
[5]: http://blog.csdn.net/zzzkk2009/article/details/44600229 "JavaScript学习之类型隐式转换"
[6]: http://www.nowamagic.net/librarys/veda/detail/262 "JavaScript数据类型转换方法汇总"
[7]: http://snandy.iteye.com/blog/718621 "无处不在的隐式转换"