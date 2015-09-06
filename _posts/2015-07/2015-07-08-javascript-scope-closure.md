---
layout: post
title: "JavaScript作用域 闭包 理解"
categories: [开发技术,JavaScript]
tags: [Web前端,前端基础,JavaScript,作用域,闭包]

---


## 相关概念理解

### 作用域
顾名思义，作用域就是起作用的范围，可访问的范围，有效的范围。

### 词法作用域
词法就是单词的形式和用法，词法作用域就是由词法确定的作用域。    
JavaScript的作用域是词法作用域，可以等同于静态作用域，意指只需要读取源代码进行静态分析（不需要代码执行）就能确定的作用域。

### 变量作用域
一提到变量作用域，很快能想到的两个词“全局变量”和“局部变量”，是根据变量的作用域属性来对作用域进行限制和描述。

### JavaScript作用域
PS：JavaScript有关执行作用域相关的信息在ECMAScript标准中目前都会有相应的说明，在标准1，2，3版总在第十章，前三版差别不是很大，可以直接看第三版。第五版可以直接看2011年的版本，然后就是2015版（第六版）。    
变量对象，活动对象这些概念在前三版中有提及，现在的资料里面提到的也是这些。

#### 局部作用域（地方）
JavaScript中的局部代码就是函数内部的代码，现在包括函数内部的函数，函数内部的表达式，早期的JavaScript不支持内部函数，所以只有函数代码是局部代码。      
局部代码确定的作用域就是局部作用域。      

#### 全局作用域（中央）
JavaScript的全局代码是除函数内部代码之外的代码。他们确定的作用域就是全局作用域。    
全局作用域由JavaScript内置全局对象来表达，比如我们常说的浏览器端的window宿主对象，window对象是全局对象的一个属性，他的值是全局对象的一个引用，是暴露出来的全局对象。

### 执行上下文
这里要多废话一段，看了好些资料，说的都是执行上下文堆栈。这里需要做一些解释。

堆是堆（Heap），是栈（Stack）。栈是操作系统自动进行维护的一个保存临时状态的逻辑结构，堆一般是程序代码来维护的结构。 栈比堆有更高的优先级使用更低级别的缓存（越低级别的缓存速度越快，有更高的命中率，如CPU的一级缓存比二级缓存快，二级比三级快，缓存比物理内存块）。栈有更小的内存限制，一般为几M（可配置），而堆的限制最大可以达到物理内存的可用大小，现在一般为N个G吧。

这里说的堆栈是说，执行上下文的数据，一部分在栈中，一部分在堆上。比如定义一个数组，一万个元素。都是单独申请一块内存来存放，同时把这个内存的首地址（指针）放入栈中。而其他简单的数据比如一个简单的循环变量，那就直接放入栈中。

#### 变量对象
变量对象，是全局执行上下文对象的一个属性，记录当前作用域的所有变量和函数的定义。

####  活动对象 
活动对象，是局部执行上下文的一个属性（地方特色），用来记录当前作用域的所有变量和函数的定义，函数的参数信息和其他内部的函数执行相关的信息。

#### 全局执行上下文
全局上下文初始化在代码执行之前（废话），最可能是预编译阶段，初始化全局对象，包含JavaScript的全局对象的属性，各种内置构造函数，String，Boolean，Number，以及各种全局函数，parseInt, parseFloat, 记忆各种全局属性undefined,Infinite等。    

全局上下文包含几个特别的属性，window，this，都分别指向全局执行上下文本身（引用）；变量对象是全局上下文中所有的变量和函数声明的集合（也是一个对象）。在JavaScript中对象是一个key：value的集合，所以会出现变量和同名函数相互覆盖的情况。

全局上下文初始化的时候，会带上变量的声明和函数的声明，也就是说在变量对象里面有跟所有全局变量名一样的属性，以及跟全局函数名一致的属性，全局变量名的属性没有赋值（为默认的undefined）,全局函数名对应的属性值就是函数的引用。 所以这里是在代码执行前将所有要定义的变量做了一次统一声明，这就是JavaScript作用域里面经典的“声明提前”的原因。

可以用如下JavaScript示意，以帮助理解,有如下代码：

    var a = 10;
    function globalFunc(a,b){
        var inner1Var = 10;
        function inner1Func(){
            var inner2Var = 20;
        }
    }
    
    globalFunct(100,200);

    var GLOBAL_CONTEXT = {
        parseInt：PARSEINT_REF,
        ......//其他全局函数
        String：STRING_REF,
        ......//其他全局构造器
        undefined:undefined,
        ......//其他全局属性
        
        this: GLOBAL_CONTEXT,
        window: GLOBAL_CONTEXT,
        VariablesObject:{
            a: {
                value: undefined // 预编译时不赋值，执行到具体代码时赋值。
                attributes:{
                    writable:true,
                    configurable:true,
                    enumerable:true
                }
            },
            f:{
                value:globalFunc_REF,
                attributes:{
                    writable:true,
                    configurable:true,
                    enumerable:true
                }
            }
        },
        OutterScope:null
    }

#### 局部执行上下文 
还是上面的代码， 除了全局上下文之外，还有局部的上下文对象。每次进入一个局部上下文之前，类似于下面的一个对象就会被创建，this值，变量对象的值被初始化。
    
    var INNER1_CONTEXT = {
        this: GLOBAL_CONTEXT,
        VariablesObject: {
            a:{
                value:undefined,   //定义的时候不会初始化，在调用的时候由调用者在进入执行上下文之前初始化为200
                attributes:{
                    writable:true,
                    configurable:false,
                    enumerable:true
                }
            }，
            b:{
                value:undefined,   //定义的时候不会初始化，在调用的时候由调用者在进入执行上下文之前初始化为200
                attributes:{
                    writable:true,
                    configurable:false,
                    enumerable:true
                }
            }，
            arguments:{
                '0':{
                    value:undefined,   
                    attributes:{
                        writable:false,
                        configurable:false,
                        enumerable:true
                    }
                }，
                '1':{
                    value:undefined,   
                    attributes:{
                        writable:false,
                        configurable:false,
                        enumerable:true
                    }
                }，
                length: 2,
                callee: globalFunc_REF
            }
        },
        OutterScope:GLOBAL_CONTEXT
    }
    
    
        
    var INNER2_CONTEXT = {
        this: GLOBAL_CONTEXT,
        VariablesObject: {
            ......//同 INNER1_CONTEXT
        },
        OutterScope:INNER1_CONTEXT
    }

#### 作用域链

INNER2_CONTEXT.OutterScope.OutterScope ===  GLOBAL_CONTEXT

INNER2_CONTEXT.OutterScope.OutterScope.OutterScope ===  null

像上面这种以OutterScope来访问外部作用域的访问路径，叫做作用域链。

### 闭包

#### 相关的说法：
+ 闭包，是指语法域位于某个特定的区域，具有持续参照（读写）位于该区域内自身范围之外的执行域上的非持久型变量值能力的段落。这些外部执行域的非持久型变量神奇地保留它们在闭包最初定义（或创建）时的值（深连结）
+ 闭包是在其词法上下文中引用了自由变量的函数。
+ 在实现深约束时，需要创建一个能显式表示引用环境的东西，并将它与相关的子程序捆绑在一起，这样捆绑起来的整体被称为闭包。
+ 函数对象可以通过作用域链相互关联起来，函数体内部的变量都可以保存在函数作用域内，这种特性在计算机科学文献中成为“闭包”
+ This combination of a function object
and a scope (a set of variable bindings) in which the function’s variables are resolved
is called a closure in the computer science literature

#### 什么是闭包
上面看了这么多的概念，其实我们想要的是一个关键词，这里面有的关键词有:段落，函数，程序和环境，特性。还是很难区分哪。

我们先来看看闭包产生的原因：      

+ 首先是内部的作用域能引用外部作用域的数据，这是JavaScript词法作用域特性。
+ 其次是被引用的数据不能被回收，这是JavaScript的垃圾回收机制。
+ 再次函数作为数据传递，这里是一个数据保持的概念，闭包需要能保持本该被回收的数据，比如函数内部的变量。

如上可以看出，闭包是一个在运行期的一个概念，是一个动态的概念。

于是我们可以给闭包下一个定义：闭包是执行时具有独立上下文的词法结构及其持有的内部和外部数据组成的结构。


#### 闭包的使用陷阱
需求：循环创建一组输出序号值的函数。      

实现代码：
    var funcs = [],i = 0;
    for(i = 0; i < 5; i++){
        funcs[i] = function(){
            console.log(i)
        }
    }
    
    funcs[2](); // 5 出现这种情况的原因是，匿名函数内引用的是同一个变量i，在执行的时候i的值是循环后的最终值5
    
如何解决上面的问题，根据闭包的特性，这里的想要将各个循环状态的值保留下来，就需要创建一个执行上下文才能利用闭包的特性
    
    var funcs = [],i = 0;
    for(i = 0; i < 5; i++){
        funcs[i] = (function(i){
            return function(){
                console.log(i)
            }
        })(i);
    }
    
    funcs[2](); // 2
    
或者可以使用下面的方式
    
    var funcs = [],i = 0;
    for(i = 0; i < 5; i++){
        funcs[i] = (function(){
            var j = i;
            return function(){
                console.log(j)
            }
        })();
    }
    
    funcs[2](); // 2
    
在ES6中，我们可以用let来避免多层闭包（这种方式还是比较low）

    "use strict";
    var funcs = [],i = 0;
    for(i = 0; i < 5; i++){
        let j = i;	
        funcs[i] = function(){
            console.log(j)
        }
    }
    
    funcs[2](); // 2

当然，既然有ES6，我们就可以有更优雅的方式（注意看区别哈）。

    "use strict";
    var funcs = [];
    for(let i = 0; i < 5; i++){
        funcs[i] = function(){
            console.log(i)
        }
    }
    
    funcs[2](); // 2
    
这应该就是我们最初的代码想实现的效果，然而在ES6之前基本都是词法作用域（除了try-catch算块作用域），所以无法达到我们想要的效果。在ES6中let 引入了块作用域，使得i本身是for循环块中的一个局部变量，在闭包中被保持。

## 参考资料
0. [了解JavaScript执行上下文](http://yanhaijing.com/javascript/2014/04/29/what-is-the-execution-context-in-javascript/)
0. [深入理解JavaScript系列：JavaScript核心](http://www.cnblogs.com/tomxu/archive/2012/01/12/2308594.html)
0. [深入理解JavaScript系列（11）：执行上下文（Execution Contexts）](http://www.cnblogs.com/TomXu/archive/2012/01/13/2308101.html)
0. [深入理解JavaScript系列（12）：变量对象（Variable Object）](http://www.cnblogs.com/TomXu/archive/2012/01/16/2309728.html)
0. [深入理解JavaScript系列（14）：作用域链(Scope Chain)](http://www.cnblogs.com/TomXu/archive/2012/01/18/2312463.html)
0. [深入理解JavaScript系列（16）：闭包（Closures）](http://www.cnblogs.com/TomXu/archive/2012/01/31/2330252.html)
0. [JavaScript核心](http://weizhifeng.net/javascript-the-core.html)
0. [You Don't Know JS: Scope & Closures](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/README.md#you-dont-know-js-scope--closures)
0. [理解JavaScript系列](http://www.cnblogs.com/fool/category/264215.html)
0. [深入理解JavaScript系列](http://www.cnblogs.com/TomXu/archive/2011/12/15/2288411.html)
0. [JavaScript 变量作用域(续)](http://heroicyang.com/2013/07/22/javascript-scope-sequel/)
1. [JavaScript 作用域链解析](http://xmuzyq.iteye.com/blog/1198717)
1. [JavaScript变量作用域之殇](http://blog.jobbole.com/47296/)
2. [javascript 执行环境，变量对象，作用域链](http://segmentfault.com/a/1190000000533094)
3. [理解Javascript_15_作用域分配与变量访问规则,再送个闭包](http://www.cnblogs.com/fool/archive/2010/10/19/1855265.html)
0. [Closure](https://en.wikipedia.org/wiki/Closure)
9. [闭包的概念、形式与应用](http://www.ibm.com/developerworks/cn/linux/l-cn-closure/)
4. [学习JavaScript闭包](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)
5. [JavaScript 闭包 概念实例](http://www.cnblogs.com/rubylouvre/archive/2009/07/24/1530074.html)
6. [详解js闭包](http://segmentfault.com/a/1190000000652891)
7. [编程语言中的闭包](http://www.nowamagic.net/librarys/veda/detail/1696)
8. [什么是闭包，我的理解](http://www.cnblogs.com/xiaotie/archive/2011/08/03/2126145.html)
0. [什么是闭包](http://kb.cnblogs.com/page/111780/)
0. [JavaScript执行过程](http://blog.csdn.net/cxiaokai/article/details/7552653)


