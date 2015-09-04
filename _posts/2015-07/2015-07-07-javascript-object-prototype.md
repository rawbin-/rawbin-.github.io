---
layout: "post"
title: "JavaScript prototype 原型 继承 理解"
categories: [开发技术,JavaScript]
tags: [Web前端,前端基础,JavaScript,原型]

---

## 一些相关概念理解

### 相关概念
+ 对象
    + 哲学上万物皆对象，具体指人们要研究的客观事物及对其进行的主观抽象；
    + 计算机理论思想中的对象是包含属性和相关行为的封装结构，是类的实例，类是对对象的抽象；
    + 计算机程序的对象是一块具体的内存区域，可以是变量、函数、数据结构以及他们的组合。
    + JavaScript 中的对象指Object类型的实例，是属性的集合；
+ 属性    
属性是对象，原始值、函数的容器。
+ 原始值   
原始值是Undefined，Null,Boolean,Number,String中的任意一个类型的成员，他们是直接代表语言实现的最底层的数据；这是编译器使用的数据。
+ 字面量
顾名思义，就是在程序代码中看到的表示固定含义的特定的字符序列，如{}表示空对象,'2'表示字符串2，这是编码时使用的数据。
+ 函数
    + 函数是可调用的对象，是对象类型的成员
    + 通过属性与对象关联的函数，或者说是作为对象属性的函数，称做对象的方法；
    + 创建和初始化对象的函数（与new一起使用）称做构造函数或构造器；
+ 原型
    + 广义上来讲，原型是指事物的初始形态，是一种模型或样板；
    + JavasScript中的原型
        + 可以指函数的prototype属性名，函数的prototype属性值（原型对象）
        + 可以指对象的原型属性\_\_proto\_\_(这个属性在JavaScript引擎中使用，在ES6之前作为内部属性存在，在ES6中被标准化和显式的使用)
        + \_\_proto\_\_作为对象的属性指向他的原型，prototype作为函数的属性指向他的原型。函数也是对象，所以函数可能同时拥有这两个属性。

### 概念理解

#### 一切皆为对象
+ JavaScript是弱类型的语言，会自动进行相应的类型转换（隐式类型转换）    
在JavaScript中，原始值没有对应的方法，原始值在执行的过程中会被自动转换成对应类型的对象再进行运算和执行。比如字符串原始值"abcde"没有任何方法，会被转换成String对象，然后拥有String.prototype这个对象上定义的方法。
+ 函数也是对象（上面不是说了么，函数是可调用的对象）   
看看构造器的定义，创建和初始化对象的函数。首先构造器是函数，构造器创建的是对象，那么Function构造器所创建的实例也是对象。

        typeof (new Function()) === 'function' // true 
        (new Function()) instanceof Function   // true
        (new Function()) instanceof Object     // true
        Function instanceof Object             // true 
        
+ JavaScript对象继承树   
所有对象都继承自Object.prototype

        Object.prototype.__proto__ // null
        Function.prototype.__proto__ === Object.prototype // true

#### 函数是第一级对象
+ 函数是基本数据类型   
使用typeof运算符进行求值，可得到undefined,null,string,boolean,number,object,function这几种类型，这里函数和object作为并列的类型出现，同时对象又是由构造器（函数）创建的，虽然函数也是object类型的成员，但它同时拥有自己的typeof值。
+ 函数可以跟作为参数值传递（回调函数）
    
#### 对象构造过程
+ 一般对象创建过程  
构造函数创建对象时，新建一个空对象，并将当前this值设置为这个对象的引用；同时将这个对象的\_\_proto\_\_属性设置为构造函数（构造器）的prototype 属性;设置对象的constructor属性为构造函数的原型的prototype属性;

        var proto = {name: 'first proto'};
        function Foo(){
            this.val = 'test val'
        }
        Foo.prototype = proto;
        var obj = new Foo();
        obj.__proto__ === proto;        // true 
        obj.__proto__ === Foo.prototype // true
        obj.constructor === Foo.prototype.constructor // true
        
#### 原型继承
+ 对象属性的访问   
还是用上面的例子

        var proto = {name: 'first proto'};
        function Foo(){
            this.val = 'test val'
        }
        Foo.prototype = proto;
        var obj = new Foo();
        obj.val  // 'test val'
        obj.name // 'first proto'
        
对象的属性，一部分来自于本身构造函数中构建的属性，另一部分来自于构造函数的原型proto，这样我们通过构造函数的prototype属性实现了数据（proto）的复用。     

JavaScript对象属性和方法的查找过程是先在对象本身的属性中查找，如果没找到就在对象原型（\_\_proto\_\_）中查找，直到找到属性或者找不下去（找到原型链末端\_\_proto\_\_ === null）为止。

+ 原型式继承

        function Creature(){
            this.tag = 'creature'
        }

        function Animal(){
            this.type = 'Animal'
            this.say = function(){
                throw Error('this must be implemented')
            }
        }
        
        Animal.prototype = new Creature();
        
        function Cat(){
            this.legs = 4;
            this.say = function(){
                console.log('miao miao')
            }
        }
        
        Cat.prototype = new Animal();
        
        function Dog(){
            this.type = 'Dog';
            this.legs = 4;
            this.say = function(){
                console.log('wang wang')
            }
        }
        Dog.prototype = new Animal();
        
        var cat = new Cat();
        console.log(cat.type)  // Animal
        cat.say(); // miao miao
        
        var dog = new Dog();
        console.log(dog.type); // Dog
        dog.say(); //wang wang
        dog.tag // creature
        
 + 原型链  
 在上例中存在一条访问路径   
 `dog.__proto__.__proto__.__proto__.__proto__.__proto__`  
查找dog.type 的时候 直接在对象中找到了自有属性 `dog.type`，返回的是dog.type
查找dog.tag 的时候访问的就是`dog.__proto__.__proto__`,返回的是Animal.prototype.tag
查找dog.no 的时候访问的是`dog.__proto__.__proto__.__proto__.__proto__.__proto__`，返回的是`undefined`
这条查找属性和方法的访问路径，就叫原型链。   
从上面可以看出，原型链长了不太合适，查找效率太低（尤其是对于那些根本不存在的属性，他们会遍历整条原型链而最终返回的还是undefined）。
            
## 参考资料
0. [理解JavaScript系列](http://www.cnblogs.com/fool/category/264215.html)
0. [深入理解JavaScript系列](http://www.cnblogs.com/TomXu/archive/2011/12/15/2288411.html)
0. [What is Object?](https://en.wikipedia.org/wiki/Object)
0. [You Don't Know JS: this & Object Prototypes](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20&%20object%20prototypes/README.md#you-dont-know-js-this--object-prototypes)
0. [JavaScript Types](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof)
0. [ECMAScript 5.1 中文版](http://lzw.me/pages/ecmascript/)
1. [ECMAScript标准](http://www.ecma-international.org/publications/standards/Ecma-262-arch.htm)
0. [JavaScript原型和继承](http://blog.jobbole.com/19795/)
1. [JavaScript原型继承工作原理](http://developer.51cto.com/art/201309/410991.htm)
2. [深入理解JavaScript系列（5）：强大的原型和原型链](http://www.cnblogs.com/TomXu/archive/2012/01/05/2305453.html)
0. [W3School ECMAScript继承机制实现](http://www.w3school.com.cn/js/pro_js_inheritance_implementing.asp)
0. [理解JavaScript Function与Object](http://kb.cnblogs.com/page/77480/)