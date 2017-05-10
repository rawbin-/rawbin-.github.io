---
layout: post
title: "说好的代码优化变劣化，为什么？"
categories: [Web开发,前端开发,JavaScript]
tags: [代码优化,高性能]
---

[TOC]

### 引言


在前面`高性能代码优化规则参考`一文中，我们举了一个优化的例子；但是，但是，结果优化变成劣化（恶化了），演砸了。我们继续看看到底是怎么回事？

### 优化规则评价

先复原一下场景。

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



### 问题深入研究

#### 找工具

+ 搜索`v8 perf tool`  Google 第一条得到一个工具列表
+ 筛选掉其中内存和CPU相关的，剩下`irhydra`可以做中间代码分析的
+ 我们就是想看看咱们的源码被转成什么样的代码来执行了



#### 搞出来看一看

+ node 本身是基于v8的，所以也不用各种下载v8源码编译什么的了，直接上
+ 使用node 生成`irhydra`需要的内容
+ 将上面的源代码存为`perf-opt.js`
+ 执行下面的命令

```
node --trace-hydrogen --trace-phase=Z --trace-deopt --code-comments --hydrogen-track-positions --redirect-code-traces --redirect-code-traces-to=code.asm --print-opt-code perf-opt.js
```

+ 在`IRHydra2`的界面导入上面生成的`.asm`和`.cfg`文件
+ 可以看到我们四个函数分别对应的中间代码了



#### 找文档 捋主线

+ 搜索`v8 intermediate representations` 在得到的结果中可以得到如下的概念
  + `Crankshaft`是v8的编译优化器
  + `Hydrogen` 是`Crankshaft`中的HIR(High-Level intermediate representations)中间代码，更接近于源代码，跟机器无关
  + `Lithium`是`Crankshaft`中的LIR(Low-Level intermediate representations)中间代码，更接近于机器码，跟机器相关
  + `SSA(Static Single Assignment)`是`Hydrogen`的描述格式，是一种组织IR(intermediate representations)的方式。
+ 搜索`Static Single Assignment`可以得到一些关于SSA的参考
+ 参考资料
  + 《Compiled Compiler Templates for V8》
  + 《Static Single Assignment Book》
+ 没有找到更详尽的关于SSA的文档，比如SSA的指令及其含义说明,只有v8源码中的`src/crankshaft/hydrogen-instructions`，如果你找到了，请不吝赐教（怎么找的？）。



#### 中间代码分析

##### 工具介绍

+ `Load Compilation Atrifacts` 加载`.asm`和`.cfg`文件
+ `IR` 标签页，打开眼睛可以看到源代码和中间代码的映射关系，深红色的竖条表示循环范围
+ `Graph` 标签页，打开问号可以查看控制流图的说明，点击图块可以跳转到`IR`标签页

##### 总体结果

+ 前三段代码(origin,optLen,optIdx)的控制流图完全一致,B3-B8是循环内容
+ 第三段代码(optCmp)与其他三段差别较大，



##### 详细对比

###### origin 与 optLen的区别

+ origin比optLen在B2块少一个`LoadNamedField t2.%length@24 Smi` 获取数组长度，代号`i33`，在循环内容之外
+ origin比optLen在B3块多一个`LoadNamedField t2.%length@24 Smi ` 获取数组长度，代号`i42`，每次循环都有这个操作
+ origin比optLen在B5块少一个`LoadNamedField t2.%length@24 Smi ` 获取数组长度，代号`i62`,每次循环都有，这个地方不科学（mark），需要用`i62`的地方直接使用`i33`就ok
+ 其他操作无差异



###### optLen与optIdx的区别

+ optLen比optIdx在B5块多一个`var[4] = t106`赋值，但这个赋值后面好像没用上。。。



######  optCmp与optLen比较的异常

+ 获取data[i]的时候多了`CheckMaps t2 [0x12044e7ace69](stability-check)`操作
+ 存储乘积结果的时候多了`CallWithDescriptor t76 t7 t16 t86 t87 s88 t74 #0 changes[*] Tagged `
+ 异常的描述（这是代码未被编译器优化识别的警告信息）

```
This instruction has side effects **unknown** to the compiler.
It can change hidden classes of objects, kind of array backing stores, etc.
Such instructions should be avoided inside hot loops because they inhibit optimizations.
```



#### 看起来还没有找到想要的

+ 上面的`SSA`本身存在问题，参考意义不大
+ 没有比较出前面三个代码之间的差异
+ 最后一个优化搞砸了还是没解释清楚



### 重头再来

#### 使用PHP来实现（为什么？）

+ 在这个例子上，PHP跟JavaScript极其相似
+ PHP是世界上最好的语言（别打脸~~）
+ PHP下用`VLD` 查看`opcode`我玩过一次，比`SSA`看起来更细致



```
<?php

define('TESTCOUNT',9999);
define('REPEATCOUNT',999);
define('PRECISION',8);

function mstime_str()
{
    list($usec, $sec) = explode(" ", microtime());
    //不丢失系统原有精度
    return (bcadd($usec,$sec,PRECISION));
}

function get_exec_time($func,$param){
    $start = mstime_str();
    call_user_func(do_repeat, $func,$param);
    $end = mstime_str();
    return bcsub($end,$start,PRECISION);
}


function do_init(&$arr){
  for($i = TESTCOUNT; $i--;){
    $arr[$i] = $i;
  }
}

function do_repeat($operation,$data){
  for($i = 0; $i < REPEATCOUNT;$i++){
    $operation($data);
  }
}
function origin($data){
  $ret = array();
  for($i = 0; $i < count($data); $i++){
    $ret[$i] = $data[$i] * $data[$i];
  }
}

function opt_len($data){
  $ret = array();
  for($i = 0,$len = count($data); $i < $len; $i++){
    $ret[$i] = $data[$i] * $data[$i];
  }
}

function opt_idx($data){
  $ret = array();
  for($i = 0,$len = count($data); $i < $len; $i++){
    $tmp = $data[$i];  
    $ret[$i] = $tmp * $tmp;
  }
}

function opt_cmp($data){
  $ret = array();
  for($i = count($data); $i--;){
    $ret[$i] = $data[$i] * $data[$i];
  }
}


$test_data = array();

do_init($test_data);
echo 'origin:'.(get_exec_time(origin,$test_data)).PHP_EOL;
echo 'opt_len:'.(get_exec_time(opt_len,$test_data)).PHP_EOL;
echo 'opt_idx:'.(get_exec_time(opt_idx,$test_data)).PHP_EOL;
echo 'opt_cmp:'.(get_exec_time(opt_cmp,$test_data)).PHP_EOL;

?>
```



#### 执行上面的PHP代码

+ 将上面的内容存为`perf-opt.php`
+ 运行`php -f perf-opt.php`
+ 在`PHP 5.6.30` 上面的执行结果(稳定)

```
origin:4.74019000
opt_len:3.53197500
opt_idx:3.35585800
opt_cmp:2.95980300
```



### 结论

+ 某些优化规则在不同的编译环境下，不一定能得到一致的效果
+ 规则是死的，但编译系统不断在改变和发展
+ 有可能我们的优化规则，跟编译器的优化冲突了，成了执行优化的障碍



### 参考资料

1.  《Compiled Compiler Templates for V8》

2.  《Static Single Assignment Book》

3.  [V8源码](https://github.com/v8/v8/blob/master/src/crankshaft/hydrogen-instructions.h)

4.  [IRHydra](http://mrale.ph/irhydra/)

5.  [IRHydra github](https://github.com/mraleph/irhydra)

6.  [v8: a tale of two compilers](https://wingolog.org/archives/2011/07/05/v8-a-tale-of-two-compilers)

7.   [Optimizing for V8 - Hydrogen](https://floitsch.blogspot.com/2012/03/optimizing-for-v8-hydrogen.html)

8.  [A tour of V8: Crankshaft, the optimizing compiler](http://jayconrod.com/posts/54/a-tour-of-v8-crankshaft-the-optimizing-compiler)

9.  [V8 之旅：优化编译器 ](http://newhtml.net/v8-crankshaft-the-optimizing-compiler/)

10.   [[WebKit\] JavaScriptCore解析--高级篇(一) SSA (static single assignment)](http://blog.csdn.net/horkychen/article/details/9075827)

11.  [GNU Compiler Collection (GCC) Internals](https://gcc.gnu.org/onlinedocs/gccint/index.html#Top)

12.  [Analysis and Optimization of GIMPLE tuples](https://gcc.gnu.org/onlinedocs/gccint/Tree-SSA.html#Tree-SSA)

13.  [static single assignment for functional programmers](https://wingolog.org/archives/2011/07/12/static-single-assignment-for-functional-programmers)

14.  [a closer look at crankshaft, v8's optimizing compiler](https://wingolog.org/archives/2011/08/02/a-closer-look-at-crankshaft-v8s-optimizing-compiler)

     ​

