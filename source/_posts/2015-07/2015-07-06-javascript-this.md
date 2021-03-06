---
layout: post
title: "JavaScript this 理解"
categories: [开发技术,JavaScript]
tags: [Web前端,JavaScript,前端基础,this]

---

### 1 this
Javascript中的this是当前执行上下文对象的一个属性，在创建执行上下文时生成，在执行上下文中不可改变。但会随着不同执行上下文的不对变化而改变。 这也是问题所在。

### 2 严格模式
以 "use strict"; 指定的JavaScript解析模式
在非严格模式下，this在没有具体的指向的时候会指向全局对象，即浏览器运行时的window对象。
严格模式下，不会进行这样的转换，在this没有指向的时候，就赋值为undefined。

### 3 非严格模式
目前是默认的模式，即不加任何限制的模式。非严格模式会因为JavaScript的作用域的和执行上下文的变化导致一些难以排查的问题，因此目前JavaScript从5.1开始逐渐向严格模式倾斜。

### 4 有没有银弹？
JavaScript中的this取决于函数的调用环境和调用方式，后者比前者更重要。

#### 4.1 JavaScript this 确定规则（从前到后优先级逐渐降低）
+ 如果使用了new 关键字，this 就是新创建的这个对象；
+ 如果使用了函数的abc方法（apply，bind，call），this即为这个明确指定的对象。
+ 如果函数使用了明确对象来调用（对象方法），this为这个调用对象。
+ 默认，严格模式this为undefined，非严格模式为全局对象（ES6的箭头函数会默认使用外层作用域的this）

#### 4.2 如何理解？（测试的结果）
+ new关键字调用构造函数时，优先级最高，及时函数被明确绑定到对象上，也是会指向新创建的对象。  
  
        function foo(something) {
            this.a = something;
        }
        var obj1 = {};
        var bar = foo.bind( obj1 );
        bar( 2 );
        console.log( obj1.a ); // 2
        var baz = new bar( 3 );
        console.log( obj1.a ); // 2
        console.log( baz.a ); // 3

+ 明确绑定了对象的方法，使用的this就是该对象        
   
        function foo() {
            console.log( this.a );
        }
        var obj = {
            a: 2
        };
        var bar = function() {
            foo.call( obj );
        };
        bar(); // 2
        setTimeout( bar, 100 ); // 2
        // hard-bound `bar` can no longer have its `this` overridden
        bar.call( window ); // 2　　
        
+ 对象方法调用，this指向，该对象，纯函数调用

        function foo() {
            console.log( this.a );
        }
        var obj2 = {
            a: 42,
            foo: foo
        };
        var obj1 = {
            a: 2,
            obj2: obj2
        };
        obj1.obj2.foo(); // 42

+ 默认的纯函数调用，回调调用等可认为是window在进行方法调用       

        function foo() {
            console.log( this.a );
        }
        function doFoo(fn) {
            // `fn` is just another reference to `foo`
            fn(); //
        }
        var obj = {
            a: 2,
            foo: foo
        };
        var a = "oops, global"; // `a` also property on global object
        doFoo( obj.foo ); // "oops, global"
+ ES6 箭头函数调用，默认会使用当前环境中的局部this值，而不是全局this默认值  
          
        var count = 100;
        function foo(){
            this.count++;
            console.log(this.count)
        }
        var obj = {
            count: 0,
            cool: function coolFn() {
                    setTimeout(function(){
                        this.count++;
                        console.log(this.count) //window.count
                    },1000)
                    
                    var self = this;
                    setTimeout(function(){
                        self.count++;
                        console.log(self.count) //obj.count
                    },1000)
                    
                    setTimeout( () => {
                        this.count++;
                        console.log(this.count) //obj.count
                    },1000)
                }
        };
        obj.cool();


### 5 参考资料
0. [理解JavaScript系列](http://www.cnblogs.com/fool/category/264215.html)
0. [深入理解JavaScript系列](http://www.cnblogs.com/TomXu/archive/2011/12/15/2288411.html)
0. [深入理解JavaScript系列(13): This?Yes,this!](http://www.cnblogs.com/TomXu/archive/2012/01/17/2310479.html)
0. [You Don't Know JS: this & Object Prototypes](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20&%20object%20prototypes/README.md#you-dont-know-js-this--object-prototypes)
0. [JavaScript严格模式详解](http://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html)
1. [JavaScript this工作原理及注意事项](http://blog.jobbole.com/67347/)
2. [深入浅出JavaScript中的this](http://www.ibm.com/developerworks/cn/web/1207_wangqf_jsthis/)
3. [JavaScript的this用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)
4. [MDN this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)
5. [JavaScript this关键字详解](http://www.cnblogs.com/justany/archive/2012/11/01/the_keyword_this_in_javascript.html)
6. [理解JavaScript Function Object](http://kb.cnblogs.com/page/77480/)
7. [JavaScript 的Function与Object浅析](http://blog.csdn.net/sykent/article/details/8003343)
8. [Javascript中Function,Object,Prototypes,__proto__等概念详解](http://anykoro.sinaapp.com/2012/01/31/javascript%E4%B8%ADfunctionobjectprototypes__proto__%E7%AD%89%E6%A6%82%E5%BF%B5%E8%AF%A6%E8%A7%A3/)
9. [Js中Prototype、__proto__、Constructor、Object、Function关系介绍](http://www.blogjava.net/heavensay/archive/2013/10/20/405440.html)
