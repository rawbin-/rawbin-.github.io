---
layout: post
title: "设计模式"
categories: [开发技术]
tags: [设计模式]

---

## 1 UML用例图表示方式
+ 扩展  
    实线和箭头指向被扩展方
+ 包含  
    实线和箭头指向被包含方

## 2 UML类图表示方式
+ 继承、泛化   
    用实线和空心三角表示，指向父类
+ 接口实现   
    用虚线和空心三角表示，指向接口
+ 基本聚合   
    空心棱形指向父类，实线和箭头指向子类
+ 组合聚合   
    实心棱形指向父类，实线和箭头指向子类
+ 依赖   
    虚线和箭头指向被依赖方
+ 单向关联   
    实线和箭头指向被关联方
+ 双向关联   
    实线连接

## 3 设计模式比较总结

|模式类型| 中文名称|英文名称|本质| 意图（定义）|实现方式|
|------|------|------|------|------|------|
|创建型|单例模式|Singleton|控制实例数量|保证一个类只有一个实例，并提供一个访问它的全局访问点|利用缓存和延迟加载的思想来实现实例数量的控制，需要注意线程安全性|
|创建型|工厂方法|Factory Method|延迟到子类来选择实现|定义一个用于创建对象的接口，让子类决定实例化哪一个类，它使一个类的实例化延迟到子类|工厂方法的判断逻辑在客户端里面，选择用什么样的工厂生产产品，修改是修改客户端|
|创建型|简单工厂|Simple Factory|选择实现|提供一个创建对象实例的功能，而无须关心其具体实现，被创建实例的类型可以是接口，抽象类也可以是具体的类|简单工厂方法的判断逻辑在工厂里面，只需要传入需要生成的产品类型，修改是修改工厂类|
|创建型|抽象工厂模式|Abstract Factory|选择产品簇的实现|提供一个创建一系列相关或者相互依赖对象的接口，而无需指定具体的类|抽象工厂中创建一系列的抽象产品，这些产品是有关联的|
|创建型|构建器模式|Builder|分离整体构建算法和部件构造|将一个复杂对象的构建和他的表示分离，是的同样的构建过程可以创建不同的表示|构建器负责构建产品部件和装配，指导者扶着产品构建|
|创建型|原型模式|Prototype|克隆生成对象|用原型实例指定创建对象的种类，并通过拷贝这些原型创建新的对象|通过克隆已知对象来生成新的对象|
|结构型|外观模式|Facade|封装交互，简化调用|为子系统的一组接口提供一个一致的界面，Facade模式定义了一个高层接口，这个接口使得这一子系统更加容易使用|单向调用的封装和复杂度屏蔽|
|结构型|适配器模式|Adapter|转换匹配，复用功能|将一个类的接口转换为客户希望的另一个接口，适配器模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作|复用已有的功能的基础上实现接口适配|
|结构型|装饰者模式|Decorator|动态组合|动态透明地给一个对象添加一些额外的职责，比生成子类更灵活|装饰者对象与被装饰着实现了相同的接口，使用对象组合的方式沿用被组合对象的功能并进行增强和改造|
|结构型|桥接模式|Bridge|分离抽象和实现|将抽象部分与实现部分分离，是他们可以独立变化|将两个同时变化的维度独立出来，让抽象部分用用实现部分的接口对象，维护桥接关系，并用组合的方式把两个维度连接起来|
|结构型|组合模式|Composite|统一叶子对象和组合对象|将对象组合成树形结构便是“部分-整体”的层次结构，尊组合模式使得用户对单个对象和组合对象的使用具有一致性|使用抽象的组件类，让他可以代表尊对象和叶子对象，这样在客户端就不需要区分对象组合对象和叶子对象|
|结构型|代理模式|Proxy|控制对象访问|为其他对象提供一种代理以控制对这个对象的访问|创建一个代理对象，去代表真实的对象，对客户端使用没有影响|
|行为型|中介者模式|Mediator|封装交互|用一个中介对象来封装一系列的对象交互，中介者使得个对象不需要显式的相互引用，从而使其耦合松散，而且可以独立的改变他们之间的交互|将各个交互对象之间的交互逻辑封装到中介者中，需要交互时向中介者发送请求|
|行为型|观察者模式|Observer|触发联动|定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于他的对象得到通知并被自动更新|观察者管理被观察者对象，并区分群体进行消息通知|
|行为型|命令对象|Command|封装请求|将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化，对请求排队或记录请求日志以及支持可撤销的操作|封装命令对象，实现统一的操作接口|
|行为型|迭代器模式|Iterator|控制访问聚合对象中的元素|提供一种方法顺序访问一个聚合对象中的各个元素，而不暴露该对象的内部表示|把对聚合对象的遍历和访问从聚合对象中分离出来放入单独的迭代器中|
|行为型|解释器模式|Interpreter|分离实现，解释执行|给定一个语言，定义他的文法的一种表示，并定义一个解释器，这个解释器使用该表示来解释语言中的句子|客户端创建多个解释器对象组成抽象语法树，对语言进行解释操作，粒度细化到了一条语法规则对应一个解释器，应对语法的变化|
|行为型|模板方法模式|Template Method|固定算法骨架|定义一个操作中的算法的骨架，而将一些步骤延迟到子类中，模板方法使得子类可以不改变一个算法的结构即可重新定义算法的某些特定步骤|用抽象类来定义模板，使得可以部分重写模板的功能，这也是不用接口的原因|
|行为型|状态模式|State|根据状态来分离和选择行为|允许一个对象在其内部状态改变时改变他的行为|分离状态的行为，通过维护状态的变化来调用不同状态对应的功能，状态和行为是绑定的|
|行为型|策略模式|Strategy|分离算法，选择实现|定义一系列的算法，把它们一个个封装起来，并使他们可以相互替换，使算法可以独立于他的客户而变化|策略类实现相同的接口，使得他们之间可以相互替换|
|行为型|备忘录模式|Memento|保存和恢复内部状态|在不破坏封装的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样以后就可以将对象恢复到原先保存的状态|通过保存的方式实现恢复的目的|
|行为型|享元模式|Flyweight|分离与共享|利用共享技术有效地支持大量细粒度对象|将对象的状态分为内部和外部两部分，内部为不变的状态，外部为变化的状态，将不变的部分进行共享，达到减少对象数量和减少存储的目的|
|行为型|责任链模式|Chain of Responsibility|分离职责，动态组合|使多个对象都有机会处理请求，避免发送者和接受者之间的耦合关系，将处理请求的对象形成一条链，并沿着链传递请求，直到有一个对象处理了请求|把职责分开，每个处理对象只需要判断是否符合自己的职责范围，一旦被处理就返回不继续传递|
|行为型|访问者模式|Visitor|预留通路，回调实现|在不改变现有类的前提下，添加新的功能或操作|在抽象类中添加接受访问者的方法，把自身传给访问者，不改变具体的类；访问者类中实现访问具体类的方法，调用传入对象的方法|


## 4 参考资料
0. [精简版设计模式，设计模式类图](http://blog.chinaunix.net/uid-20459533-id-2975176.html)
1. [GOF23种设计模式精简版描述](http://blog.csdn.net/jk6801251/article/details/23479677)
2. [设计模式的总结](http://blog.csdn.net/xtwolf008/article/details/8807006) 
3. [设计模式总结，设计原则，精简，详细](http://www.cnblogs.com/chenssy/p/3357683.html) 
4. [设计模式大杂烩](http://www.cnblogs.com/zuoxiaolong/p/pattern26.html)
5. [23种设计模式对比与总结](http://blog.csdn.net/yoyoshaoye/article/details/7252181)
6. [总结23种设计模式应用场景](http://blog.chinaunix.net/xmlrpc.php?r=blog/article&uid=26874207&id=4121024) 
7. [设计模式之总结和回顾](http://www.cnblogs.com/wing011203/archive/2013/05/02/3055299.html)





