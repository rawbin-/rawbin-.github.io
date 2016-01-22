---
layout: post
title: "JavaScript面向对象编程小结"
categories: [Web开发,前端开发,JavaScript]
tags: [面向对象,面向过程,JavaScript]
---

## 一些必要的前提

### 面向过程

在计算机发展的早期阶段，所有的与计算机相关的操作都是以计算机为中心的（迁就计算机），比如穿孔纸带记录信息，机器码编程，汇编语言编程等。

受早期计算机文化的影响，后来的很多编程语言的设计和实现、以及相关的软件开发过程都与以计算机为中心有关。比如大名鼎鼎的C语言，结构化的设计和编码。

### 面向对象

+ 以人为本
	我们擅长于对现实世界的事物进行抽象归类，比如好人坏人，男人女人，高富帅，白富美等，也能很容易的道出各种对象的属性和操作，比如有钱、有权、有地位，比如开豪车，泡美眉等。
+ 代码复用
	通过分析归纳，根据业务将类型及其关系抽象出来，通过继承的方式复用代码，减少冗余。

### 区别和联系
+ 不同的侧重点，面相过程侧重于过程，面向对象侧重于对象（废话，顾名思义）
+ 不同视角的思维方式，面向过程以执行流程为导向，面向对象以对象交互为导向。
+ 不同的抽象程度，面向过程着眼于具体流程，抽象程度低；面向对象可以更高级别的抽象。
+ 总是同时出现，面向对象的操作就是一个个的过程，面向过程的作用目标就是对象。

一个例子,[参考](http://www.zhihu.com/question/19701980)：

+ 面向对象： 狗.吃(屎)
+ 面向过程： 吃.(狗,屎)

看完了你好像知道该怎么选了，^_^。狗，屎都是对象，吃是一个过程，二者是难舍难分啊，所以面向对象和面向过程是无法一刀切开的。

面向过程更关注吃这个过程（狗和屎都是依赖），面向对象更关注狗这个对象，吃只是操作，屎是操作的数据。

## JavaScript 面向对象编程

### 原型（prototype）

JavaScript通过原型属性来实现继承，几个概念揉在了一起，咱们撕开来看看：
+ 函数原型 FuncObj.prototype 对象
函数对象（一切皆是对象，函数也不例外）的原型对象（是一个对象，一个对象）
+ 对象原型 obj.__proto__ 对象
对象引用的构造器提供的原型，在ECMAScript规范中使用[[Prototype]]内部属性来表达，在IE中不知道是什么，在W3C浏览器中使用__proto__属性来表达，可直接访问。
在ECMAScript5之前是非标准属性，在ECMAScript2016（ES6）中__proto__被标准化为一个对象的原型属性。
+ 函数prototype 属性
+ 对象内部[[Prototype]]属性
+ 以上都叫原型 

### 继承和重写
+ 继承就是要复用现有的代码
+ 可以通过原型的方式实现共享
+ 可以通过浅拷贝的方式进行共享
+ 通过深拷贝的方式实现重写
+ 有了上面这几条就可以变出各种姿势，如jQuery的深浅拷贝，ExtJS的原型继承+浅拷贝等

### 原型继承

JavaScript特有的原型继承，就是基于上面的原型。

使用过程如下：
+ 定义一个带原型的构造函数
    
        function Person(config) {
            config = config || {};
            this.name = config.name || 'defaultName';
        }
                    
        Person.prototype = {
            say: function () {
                console.log(this.name, 'say something')
            }
        }

+ 通过new Person 生成对象
    
        var jim = new Person({
            name:'jim'
        });
        jim.say();
        
        var john = new Person({
            name:'john'
        });
        john.say();

+ jim 和 john这两个对象共享了Person.prototype对象中的say（内存中就一份）
+ new Person的执行过程
    + 创建一个空对象,并把this执行这个对象
    + 将对象的[[Prototype]]属性(__proto__)设置为Person.prototype
    + 然后执行Person构造函数中的代码 
+ 等价的代码：
    + 一种姿势
    
            var jim = new Person({
                name: 'jim'
            })
        
    + 另一种姿势
    
            var jim = {};
            Person.call(jim,{
                name:'jim'
            })    

+ 执行的查找过程
    
        var john = new Person({
            name:'john'
        });
        john.say();
调用john.say()时，编译器先查找john本身是否有say这个属性，发现没有；于是查找john.__proto__这个对象里面是否包含say这个属性，结果找到了就做一些检测，比如说是不是函数啊等，然后执行得到结果。
当然调用的this始终是当前的对象。上面的查找过程，就是愚公移山的过程，子又生孙，孙又生子。。。。

### 多级继承

#### 一个多级继承的实例
    
    function Man(config){
        this.sex = 'man'
        Person.call(this,config);
    }
	
	Man.prototype = Person.prototype;
        
    function Woman(config){
        this.sex = 'woman'
        Person.call(this,config)
    }
	
	Woman.prototype = Person.prototype;
	
	var man = new Man({
		name:'testMan'
	});
	
	var woman = new Woman({
		name:'testWoman'
	});
	
	man.say();
	woman.say();

#### 需要修改的原型对象

如果我们需要修改Man和Women的原型对象，分别加入Man和Women各自特有的属性和方法，那么上面的代码就不太适用，有如下问题：
+ 修改Man或Woman的原型都影响到了Woman或Man的原型
+ 修改Women和Man的原型影响到了Person的原型
+ 上面两句都是废话，因为他们三个引用的是同一个对象

可以用如下方法解决：

    Man.prototype = new Person();
    Woman.prototype = new Person();    

但这样之后：
+ 在查找say方法的过程中多了一次查找到person对象的过程，然后才找到Person.prototype,原型链多了一个层级
+ 在man 或woman对象中有一个name属性，在person中还有一个多余的name属性

我们可以直接用一个空函数来代替作为衔接的构造函数Person,但Person构造函数还会被借用（作为基类构造函数），不能直接干掉。
    
    function TmpConstructor(){}
    
    TmpConstructor.prototype = Person.prototype;

    Man.prototype = new TmpConstructor();
    Woman.prototype = new TmpConstructor();    

这样就解决了冗余熟悉的问题，但多一层原型链，在需要修改原型的需求中不能被干掉，干掉就回到上一步了，满足不了需求。


#### 需要重写的原型属性

如果是完全重写的属性或方法，直接在子类的原型中修改就行，这样就会在原型链查找的过程中出现短路的效果，从而无视原型中的同名属性。

但如果只是想在原来的基础上做一些小的改动，增加特殊的操作，完全重写一遍总不是我们想要的，JavaScript没有提供直接访问父类的原型的途径（ES6中可以使用__proto__）
还是方法借用，一个简单的方式是直接使用父类原型来访问。这个跟其他像Java这样的面向对象语言的super关键字不一样，只是单纯的方法借用，跟继承关系没有关联。

    function Person(config) {
        config = config || {};
        this.name = config.name || 'defaultName';
    }
				
    Person.prototype = {
        say: function () {
            console.log(this.name, 'say something')
        }
    }
	
    function Man(config){
        this.sex = 'man'
        Person.call(this,config);
    }
	
      
    function Woman(config){
        this.sex = 'woman'
        Person.call(this,config)
    }

	function TmpConstructor(){}
    
    TmpConstructor.prototype = Person.prototype;

    Man.prototype = new TmpConstructor();
	Man.prototype.say = function(){
		console.log('a man:')
		TmpConstructor.prototype.say.call(this);
	}
    Woman.prototype = new TmpConstructor();    
	Woman.prototype.say = function(){
		console.log('a woman:')
		TmpConstructor.prototype.say.call(this);
	}
	
	var man = new Man({
		name:'testMan'
	});
	
	var woman = new Woman({
		name:'testWoman'
	});
	
	man.say();
	woman.say();
	
### 无招胜有招

JavaScript的面向对象实现方式有些局限性，对面向对象的支持还在不断的完善，ES6中会直接提供相关的语法支持。

然而对信息隐藏的支持还是有限，比如想提供一个私有变量，直接读取：

    function Person(config){
        config = config || {};
        var secret = config.secret || 'mysecret';
        this.getSecret = function(){
            return secret;
        }
    }
    
这样secret作为一个私有变量，只能通过getSecret方法来访问，但这个访问方法每个对象会保存一份。
    
当然我们的目标是封装和代码复用，有了上面的道道，要用什么姿势，随你~

## 参考资料
0. [JavaScript面向对象编程](http://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/javascript/2015/08/15/javascript-oop/)
0. [JavaScript prototype 原型 继承 理解](http://rawbin-.github.io/%E5%BC%80%E5%8F%91%E6%8A%80%E6%9C%AF/javascript/2015/07/07/javascript-object-prototype/)