---
layout: post
title: "使用Mocha进行JavaScript单元测试"
categories: [Web开发,前端开发]
tags: [mocha,chai,单元测试]
---



以前也了解过一些JavaScript自动化测试的内容，没有真正上手，这阵子搞一个Velocity模板文件压缩的功能，刚好有机会用上。看[这里](http://git.shepherdwind.com/velocity.js/runner/tests.html)体会一下效果，结构一目了然，点击测试行，还能看到具体的测试代码。



### 1 NodeJS环境测试

基本看一遍[Mocha官网](http://mochajs.org/)的说明，就可以玩起来了。

+ 先安装`npm install mocha -g`,这样就可以在命令行使用mocha命令了。


+ 再找一个断言库，[chai](http://chaijs.com/)的功能比较丰富，`npm install chai --save-dev`


+ 然后建立一个tests目录,在里面创建js文件写测试代码
  + describe 代表描述信息目录，it代表具体的用例
  + 注意引入断言库，要用it包含断言语句才会被当做case
+ 使用mocha命令执行刚刚的测试文件就ok。
+ 也可以在IDE中搞Mocha相关的插件




### 2 浏览器环境测试

+ 使用mocha init PATH，初始化一个用于浏览器中展示Test Case的资源目录。

  + 这里面有一个tests.js，在这里面引入上文说到的各个测试文件
  + 然后执行`browserify tests.js>tests-browser.js` 转换浏览器中能执行的文件
  + 将html中的tests.js改为转换后的
  + 打开页面就可

  ​


### 3 参考资料

1. [Web页面自动化测试](http://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/2016/02/08/web-auto-test/)
2. [JavaScript自动化测试，测试框架](http://rawbin-.github.io/%E5%BC%80%E5%8F%91%E6%8A%80%E6%9C%AF/2015/06/11/javascript-testing-framework/)
3. [测试框架 Mocha 实例教程](http://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html)
4. [Mocha 官网](http://mochajs.org/)