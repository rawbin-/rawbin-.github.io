---
layout: post
title: "代码设计基本原则"
categories: [代码设计,程序设计]
tags: [设计原则,代码设计,可维护代码]
---





### DRY (Don't Repeat Yourself) 

### KISS (Keep It Simple,Stupid)

### XP (eXtreme Programming)

+ 快速迭代
+ 持续集成
+ 单元测试

### 面向对象设计原则

#### 单一职责原则 (The Single Responsibility Principle)
+ 应该有且仅有一个原因引起类的变更
+ 类的复杂度降低
+ 可读性提高
+ 可维护性提高
+ 变更引起的风险降低 

#### 开放-封闭原则 (The Open-Close Principle)
+ 软件实体(类，模块，函数等)应该是可以扩展的，但是不可修改的

#### 里氏替换原则 (The Liskov Substitution Principle)
+ 所有引用基类的地方必须能透明地使用其子类
+ 子类必须完全的实现父类的方法
+ 子类可以有自己的个性
+ 覆盖或实现父类的方法时输入参数可以被放大
+ 覆盖或实现父类的方法时输出结果可以被缩小

#### 接口隔离原则 (The Interface Segregation Principle)
+ 不应该强迫使用者依赖于他们不用的方法
+ 类间的依赖关系应该建立在最小的接口上
+ 接口尽量要小
+ 接口要高内聚
+ 可提供定制
+ 接口设计粒度要适度

#### 依赖导致原则 (The Dependency Inversion Principle)
+ 高层模块不应该依赖于低层模块，二者都应该依赖于抽象
+ 抽象不应该依赖于细节，细节应该依赖于抽象

#### 最少知识原则 (Least Knowledge Principe)，迪米特法则(Low of Demeter)
+ 出现在成员变量，方法的输入输出参数中的类被称为朋友类
+ 只和朋友交流
+ 尽量内敛，少对外开放接口



### 参考资料

1. 《程序员修炼之道-从小共到专家》,The Pragmatic Programmer, From Journeyman to Master
2. 《敏捷软件开发：原则、模式和实践》
3. 《您的设计模式》
4. 《设计模式之禅》