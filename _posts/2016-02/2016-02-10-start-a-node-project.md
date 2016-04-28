---
layout: post
title: "开始一个NodeJS项目"
categories: [Web开发,前端开发]
tags:[NodeJS,JavaScript]
---



### 初步了解

+ 首先了解下 [npm](https://docs.npmjs.com/) 包管理
+ 再了解下NodeJS配置文件 [package.json](https://docs.npmjs.com/files/package.json) 的结构和大致内容。
+ 使用`npm init` 并按照提示信息初始化一个项目
+ 查看`npm run XXX`中XXX可以配置的脚本 [scripts](https://docs.npmjs.com/misc/scripts), 其实可以配置任何自定义的脚本，如果只是项目内部使用。

### 站在巨人的肩上

包管理系统的一个作用就是可以很方便的使用和集成这个系统内的其他包。

+ `npm install/uninstall packageName`  在当前目录下安装/卸载一个包

+ `npm install/uninstall packageName -g` 在全局的npm中安装/卸载一个包

+ `npm install/uninstall packageName --save`  在当前工程下安装/卸载一个包，同时处理package.json中的依赖

+ `npm install/uninstall packageName --save-dev` 在当前工程下安装/卸载一个包，同时处理package.json中的开发依赖

  ​

### 做得更多

+ 进行[单元测试](http://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/2016/02/17/using-mocha/)
+ 进行[脚本集成](http://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/2016/02/18/using-browserify/)



### 参考资料

1. [npm doc](https://docs.npmjs.com/)
2. [使用Mocha进行JavaScript单元测试](http://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/2016/02/17/using-mocha/)
3. [开始使用Browserify](http://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/2016/02/18/using-browserify/)