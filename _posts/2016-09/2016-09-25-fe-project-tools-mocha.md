---
layout: post
title: "mocha-前端工程化模块化工具之mocha"
categories: [前端开发,Web开发,开发工具]
tags: [模块化,工程化,JavaScript,mocha,单元测试]
---



看[这里](http://git.shepherdwind.com/velocity.js/runner/tests.html)体会一下效果，结构一目了然，点击测试行，还能看到具体的测试代码。

### NodeJS环境测试

基本看一遍[Mocha官网](http://mochajs.org/)的说明，就可以玩起来了。

+ 先安装`npm install mocha -g`,这样就可以在命令行使用mocha命令了。


+ 再找一个断言库，[chai](http://chaijs.com/)的功能比较丰富，`npm install chai --save-dev`


+ 然后建立一个tests目录,在里面创建js文件写测试代码
  + describe 代表描述信息目录，it代表具体的用例
  + 注意引入断言库，要用it包含断言语句才会被当做case
+ 使用mocha命令执行刚刚的测试文件就ok。
+ 也可以在IDE中搞Mocha相关的插件




### 浏览器环境测试

+ 使用mocha init PATH，初始化一个用于浏览器中展示Test Case的资源目录。

  + 这里面有一个tests.js，在这里面引入上文说到的各个测试文件
  + 然后执行`browserify tests.js>tests-browser.js` 转换浏览器中能执行的文件
  + 将html中的tests.js改为转换后的
  + 打开页面就可



### 针对ES6+的测试代码的测试

+ 安装babel 依赖
+ 使用`mocha --compilers js:babel/register` 执行测试



### 其他说明

+ mocha 默认查找 `./test/*.js`,`./test/*.coffee`,如果代码在这里就可以不用指定路径



### 参考资料

0. [mocha 官网](http://mochajs.org/)
1. [ 测试框架 Mocha 实例教程](http://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html)
2. [javascript单元测试框架mochajs详解](http://www.cnblogs.com/tzyy/p/5729602.html)
3. [JavaScript单元测试框架JsUnit基本介绍和使用](http://www.cnblogs.com/mengdd/p/3957963.html)
4. [JavaScript 单元测试框架：Jasmine 初探](http://www.ibm.com/developerworks/cn/web/1404_changwz_jasmine/)
5. [JavaScript单元测试框架-Jasmine](http://www.cnblogs.com/zhcncn/p/4330112.html)
6. [边译边学-QUnit下的JavaScript自动化单元测试](http://www.zhangxinxu.com/wordpress/2013/04/qunit-javascript-unit-test-%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/)
7. [Javascript单元测试框架比较Qunit VS Jasmine](http://blog.csdn.net/dyllove98/article/details/9500033)