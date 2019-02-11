---
layout: post
title: "高性能代码优化规则参考"
categories: [Web开发,前端开发]
tags: [代码优化,高性能]
---

[TOC]

### 引言

前面谈到了Web性能优化的思路和参考点，没有涉及到具体可实施的代码层面的内容。代码优化也是有追求的工程师津津乐道的话题，但不同的程序设计语言的代码优化姿势可能千差万别，本文试图给出一些切实可行而与语言无关的优化规则参考，并简述其原理。如果涉及到特定语言的XXX，请参考`XXX 性能优化` `高效XXX`类似的书籍。



### 代码优化概述

代码优化的说法有不少，现在印象比较深的有两个：

+ 不要优化，还是不要优化
+ 不要过早优化

但个人认为上面的说法都是片面的，参考意义不大。

为什么这么说呢？高质量的代码需要从设计的时候就要考虑了，而且每一步都可以随手优化（比如我们下面会提到的规则）。如果当事人的知识或经验积累还没有达到类似的程度，可能的一个结果是在整体代码完成后发现有类似性能指标达不到的问题，这个时候要做的是分析相关问题的瓶颈，然后给出优化调整。这样一来项目的人力资源投入将会不同程度的增加，而且可能还会影响软件交付。当这个当事人再遇见类似的场景时，估计就不会相信上面两种说法的字面意思了。

如果我们每个阶段都是局部最优的状态产出，绝大部分情况下不会有类似的指标达不到的问题，如果确实还是出现了这样的情况，这才是我们真正的挑战。当然我们的优化必须遵循一定的规则：

+ 等价原则（这不废话吗？看起来像）
  + 别将正确的代码优化成不正确了，别以为测试了某些条件就完全等价了
  + 正是这个规则，让我们对优化有很大的操作空间
+ 有效原则
  + 只要能满足我们的指标要求
  + 可以是任何的时间换空间，空间换时间
  + 不能优化变劣化，比如跟编译器的优化规则冲突，导致优化后的结果更糟糕
+ 合算原则
  + 尽可能降低投入产出比，以较低的代价获取较好的效果
  + 可以适当的选择不优化，比如就某些场景下会不达标，规则可商量
+ 自愿原则
  + 如果这些规则束缚了你，大可以完全忽略



### 代码优化规则参考

都是一些常理之中的简单规则，比如让使用最频繁的代码尽可能的高效，让多步操作尽可能一步完成，不做多次完全相同的计算等。

贯穿始终的一个思想是缓存，缓存计算结果，缓存获取的数据等。



#### 循环优化

+ 将循环变量无关的计算都放到循环外
+ 尽可能减少循环内的操作数
  + 在循环外使用局部变量，缓存多步操作的结果
  + 将一次性的计算逻辑从循环中挪出来作为一次性计算结果


+ 外提循环内的各种条件判断

  + 循环中结构中的循环元素无关的逻辑判断条件的计算，比如 `if（a > b && b > c）`改为`if(conditionX)`
  + 循环判断条件的计算，比如`i < xxxList.length` 改为 `i < xxxListLen`

+ 交换内外循环，将次数多的循环放在最内层

  + 减少循环次数，降低循环切换代价

+ 利用前面循环的结果

  + 比如记录一个变量，每次循环增加值，利用这个变量使用加法算本次的结果，而不是使用循环来从头计算

+ 将多次全循环合并（可能降低可读性）

  + 如果可能的话讲多个可相关循环的操作放到一个循环中

  ​

#### 逻辑判断优化

+ switch 语句按照出现频率排序，让最常用的放在最前面，减少无效判断
+ if 语句将弱计算的条件放在前面，避免过多的计算（短路规则）
+ 逻辑判断条件转换为与0值比较
+ 使用结果缓存，而不是每次都计算结果
+ 使用查表的方法代替复杂的条件计算



#### 表达式优化

+ 公共的表达式用局部变量替代
+ 布尔逻辑优化，比如`!a && !b` 转换为`!(a || b)` 会减少计算
+ 使用自增、自减表达式
+ 降低计算强度
  + 用加法代替乘法
  + 用乘法代替幂乘
  + 使用移位代替整数2的倍数的乘除
  + 使用位运算代替取余运算



#### 类型、结构和算法的选择

+ 使用尽量简单的数据类型
  + 使用int 或long 代替 long long
  + 使用定点或整数代替浮点数
  + 使员工单精度单体双精度
+ 尽可能降低数组维度
+ 尽可能减少数组的引用
+ 使用更高效的算法
  + 比如二分查找代替顺序查找
  + 快速排序代替插入排序和冒泡排序



### 优化规则评价

光说不练假把式，每种语言运行环境都有各自的表达方式，这里用“最简单“的JavaScript做一个演示。

```
var i,len,testData = [],testCount = 9999,repeatCount = 999;

function doInit(testData){
  for(var i = testCount; i--;){
  	testData[i] = i;
  }
}

function doRepeat(operation,data){
  for(var i = 0; i < repeatCount;i++){
    operation(data);
  }
}
function origin(data){
  var ret = [];
  for(var i = 0; i < data.length; i++){
    ret[i] = data[i] * data[i];
  }
}

function optLen(data){
  var ret = [];
  for(var i = 0,len = data.length; i < len; i++){
    ret[i] = data[i] * data[i];
  }
}

function optIdx(data){
  var ret = [],tmpData;
  for(var i = 0,len = data.length; i < len; i++){
    tmpData = data[i];
    ret[i] = tmpData * tmpData;
  }
}

function optCmp(data){
  var ret = [];
  for(var i = data.length; i-- > 0;){
    ret[i] = data[i] * data[i];
  }
}

doInit(testData);

console.time("origin")
doRepeat(origin,testData);
console.timeEnd('origin')

console.time('optmize len');
doRepeat(optLen,testData)
console.timeEnd('optmize len');

console.time('optmize index');
doRepeat(optIdx,testData)
console.timeEnd('optmize index');

console.time('optmize compare');
doRepeat(optCmp,testData)
console.timeEnd('optmize compare');

```



多次执行上面的代码查看效果，结果如下：

在`Safari 10.1 (12603.1.30.0.34)` 下的运行结果（基本稳定）：

```
origin: 163.225ms
optmize len: 139.275ms
optmize index: 118.999ms
optmize compare: 248.611ms
```

在`Chrome 58.0.3029.96` 下的运行结果（不太稳定）：

```
origin: 81.718017578125ms
optmize len: 77.73486328125ms
optmize index: 74.80517578125ms
optmize compare: 378.320068359375ms
```

在`NodeJS 7.9.0`下的运行结果（不太稳定）:

```
origin: 110.480ms
optmize len: 104.123ms
optmize index: 98.463ms
optmize compare: 383.104ms
```



上面结果看来，优化循环判断条件和数据缓存都有一定的效果；但最后一种风骚的写法，看起来少了一个局部变量，但执行结果是成倍的劣化（恶化）。

这曾经也是一种代码优化的方式，作者也深中其毒，但这已经不那么重要了。现在问题来了，为什么会这么劣化，是一个值得研究的问题。



### 参考资料

1. 《代码大全》第二版

2. 《编程匠艺-编写卓越代码》

3. 《程序设计语言编译原理》

4. 《深入理解计算机系统》 第二版

5. 《程序设计方法与优化》

6. 《编程珠玑》

7. 《高效程序的奥秘》

8. 《算法心得-高效算法的奥秘》

   ​