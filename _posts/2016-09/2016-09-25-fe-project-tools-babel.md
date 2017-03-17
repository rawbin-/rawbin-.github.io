---
layout: post
title: "babel-前端工程化模块化工具之babel"
categories: [前端开发,Web开发,开发工具]
tags: [模块化,工程化,JavaScript,Babel,ES6,代码转换]
---

[TOC]

### Babel

#### 为什么要用这个？

+ 目前各大浏览器对ES2015+ 的支持参差不齐，需要一个统一的转换工具来将ES6+的代码转成浏览器都支持的ES5的代码，以便适配是有运行环境。这就需要一个代码转换工具，刚开始的时候出现和好些个类似的代码转换工具，到最后慢慢的就剩下Babel的声音了。

#### 能做哪些事？

+ 代码转换，通过不同插件来对不同新功能的代码转换做相应的支持，比如ES6，React等
+ 代码填充，上面的只是对代码做翻译，这里是对ES5没有的代码，而ES6+有的对象和API做补充实现。
+ 代码映射，转换后的代码跟转换前的代码的映射，方便调试。

#### 如何安装

+ [这里](https://babeljs.io/docs/setup/) 有对各种环境下使用babel的配置说明。

#### 相关介绍

+ babel-cli 主要包含babel 的可执行文件，包含babel命令和babel-node这个交互环境
+ babel-register  是一个对require的装饰版本，可以直接引用相应的模块，并自动转码
+ babel-core 整个转码的主体工具，包含解析，转义和代码生成。
+ babel-polyfill 整个新特性的对象和API的补充实
+ 相关插件的介绍 [详情参考](https://babeljs.io/docs/plugins/)
  + [babel-preset-latest](https://babeljs.io/docs/plugins/preset-latest/) 包含所有年度更新的转码，目前是es2015,es2016,es2017。。。
  + [babel-preset-env](https://babeljs.io/docs/plugins/preset-env/) 默认跟babel-preset-latest功能一致，对环境的配置更加灵活，可定制程度更高
  + [babel-preset-react](https://babeljs.io/docs/plugins/preset-react/) 包含React相关的的转码
  + [babel-preset-stage-x](https://babeljs.io/docs/plugins/) 包含所有非标准的提议的转码 前面的都包含后面的(包含stage-1,stage-2,stage-3),[参考](https://babeljs.io/docs/plugins/preset-stage-0/)

#### 注意事项

+ babel 推荐项目本地安装而不是全局安装，这样项目之间的babel版本可以独立不一致

  ```
  npm install babel-cli --save-dev 
  ```

  ```
  #package.json
  "scripts":{
    "build":"babel src -d lib"
  }
  ```

  ```
  npm run build
  ```

+ babel 相关的配置选项可以砸命令行中指定，更推荐的是配置的形式，放入项目根目录下的`.babelrc`,[详细配置](http://babeljs.io/docs/usage/babelrc/)

  ```
  {
    "presets":["env","react"]
  }
  ```

  并确保在项目的依赖中安装了相应的依赖包

+ 一个比较懒的方法是包含`babel-preset-env`插件来包含所有相关的插件的最新版本，同时配置更加灵活

+ babel多用来多ES6+的代码进行转码，但模块化的代码是需要支持ES5 和ES6+的语法，也就是CMD的模块和ES6的模块引入的方式都是需要支持的,输出的代码需要支持require 和 import

  ```
  const PI = 3.14
  let square = edge => edge * edge
  export default {
    PI,
    square
  }

  export {
    PI,
    square
  }
  ```

  ​





### 参考资料

0. [babel github](https://github.com/babel/babel)
1. [babel英文手册](https://github.com/thejameskyle/babel-handbook/tree/master/translations/en)
2. [babel中文手册](https://github.com/thejameskyle/babel-handbook/tree/master/translations/zh-Hans)
3. [babel入门教程](http://www.ruanyifeng.com/blog/2016/01/babel.html)


