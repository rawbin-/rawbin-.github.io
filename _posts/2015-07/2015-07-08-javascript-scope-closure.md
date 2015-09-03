---
layout: post
title: "JavaScript作用域 闭包 理解"
categories: [开发技术,JavaScript]
tags: [Web前端,前端基础,JavaScript,作用域,闭包]

---


## 相关概念理解
### 作用域

### 闭包
#### 相关的说法：
+ 闭包，是指语法域位于某个特定的区域，具有持续参照（读写）位于该区域内自身范围之外的执行域上的非持久型变量值能力的段落。这些外部执行域的非持久型变量神奇地保留它们在闭包最初定义（或创建）时的值（深连结）
+ 闭包是在其词法上下文中引用了自由变量的函数。
+ 在实现深约束时，需要创建一个能显式表示引用环境的东西，并将它与相关的子程序捆绑在一起，这样捆绑起来的整体被称为闭包。
+ 函数对象可以通过作用域链相互关联起来，函数体内部的变量都可以保存在函数作用域内，这种特性在计算机科学文献中成为“闭包”
+ This combination of a function object
and a scope (a set of variable bindings) in which the function’s variables are resolved
is called a closure in the computer science literature












## 参考资料
0. [了解JavaScript执行上下文](http://yanhaijing.com/javascript/2014/04/29/what-is-the-execution-context-in-javascript/)
0. [深入理解JavaScript系列：JavaScript核心](http://www.cnblogs.com/tomxu/archive/2012/01/12/2308594.html)
0. [深入理解JavaScript系列（11）：执行上下文（Execution Contexts）](http://www.cnblogs.com/TomXu/archive/2012/01/13/2308101.html)
0. [深入理解JavaScript系列（12）：变量对象（Variable Object）](http://www.cnblogs.com/TomXu/archive/2012/01/16/2309728.html)
0. [深入理解JavaScript系列（14）：作用域链(Scope Chain)](http://www.cnblogs.com/TomXu/archive/2012/01/18/2312463.html)
0. [深入理解JavaScript系列（16）：闭包（Closures）](http://www.cnblogs.com/TomXu/archive/2012/01/31/2330252.html)
0. [JavaScript核心](http://weizhifeng.net/javascript-the-core.html)
0. [理解JavaScript系列](http://www.cnblogs.com/fool/category/264215.html)
0. [深入理解JavaScript系列](http://www.cnblogs.com/TomXu/archive/2011/12/15/2288411.html)
0. [JavaScript 变量作用域(续)](http://heroicyang.com/2013/07/22/javascript-scope-sequel/)
1. [JavaScript 作用域链解析](http://xmuzyq.iteye.com/blog/1198717)
1. [JavaScript变量作用域之殇](http://blog.jobbole.com/47296/)
2. [javascript 执行环境，变量对象，作用域链](http://segmentfault.com/a/1190000000533094)
3. [理解Javascript_15_作用域分配与变量访问规则,再送个闭包d]
(http://www.cnblogs.com/fool/archive/2010/10/19/1855265.html)
9. [闭包的概念、形式与应用](http://www.ibm.com/developerworks/cn/linux/l-cn-closure/)
4. [学习JavaScript闭包](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)
5. [JavaScript 闭包 概念实例](http://www.cnblogs.com/rubylouvre/archive/2009/07/24/1530074.html)
6. [详解js闭包](http://segmentfault.com/a/1190000000652891)
7. [编程语言中的闭包](http://www.nowamagic.net/librarys/veda/detail/1696)
8. [什么是闭包，我的理解](http://www.cnblogs.com/xiaotie/archive/2011/08/03/2126145.html)
0. [什么是闭包](http://kb.cnblogs.com/page/111780/)
0. [JavaScript执行过程](http://blog.csdn.net/cxiaokai/article/details/7552653)


