---
layout: post
title: "开始使用Browserify"
categories: [Web开发,前端开发]
tags: [Browserify,浏览器,NodeJS]
---



### 将NodeJS的依赖进行打包

写了一个NodeJS工程，主文件有一堆依赖，需要对外引用，而对应的依赖文件在特定环境下又用不起来。

于是将主文件及其依赖打包到一个文件，使用如下命令：

`browserify src-main.js --standalone exportModuleName>dst-main.js --node`

上面的命令会将src-main.js打包起来，并能同时浏览器和NodeJS环境下运行。但下面的情况除外：

#### 一个坑

查看 [node-browserify](https://github.com/substack/node-browserify) 文档，参数说明如下

```
 --bare

    Alias for both --no-builtins, --no-commondir, and sets --insert-global-vars
    to just "__filename,__dirname". This is handy if you want to run bundles in
    node.

  --no-browser-field, --no-bf

    Turn off package.json browser field resolution. This is also handy if you
    need to run a bundle in node.

  --node

    Alias for --bare and --no-browser-field.
```

即使使用了--node 参数，browserify还是会处理__dirname 导致转换后的代码，跟转换前的代码运行结果不一致。可以用下面的代码测一下：

module.js

```javascript
var sysFs = require('fs');
var sysPath = require('path');

var testModule = {
	test:function(){
		if(sysFs.existsSync("aa.txt")){
			console.log("exist")
		}else{
			console.log("not exist");
		}
		
		console.log(sysPath.join(__dirname,'aa'))
	}
}

module.exports = testModule;
```

然后通过 `browserify module.js --standalone exportModuleName>module-bundle.js --node`

然后查看module-bundle.js的代码，__dirname被转换写死了。

原则上这个是需要browserify中处理这个参数逻辑，也没找到其他能规避的参数，既然都强制使用--node了为什么还需要Node环境的变量呢？

[这里](https://github.com/substack/node-browserify/issues)提了好些个Issue。



### NodeJS的模块，在浏览器里面跑

直接看[这里](http://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/2016/02/17/using-mocha/)

将NodeJS的单元测试模块直接，转换为浏览器可用的版本，在浏览器里面运行同样的代码。

使用`browserify src.js > dst.js` 即可。







### 参考资料

1. [浏览器加载 CommonJS 模块的原理与实现](http://www.ruanyifeng.com/blog/2015/05/commonjs-in-browser.html)
2. [解析browserify工作原理](https://segmentfault.com/a/1190000004128257)
3. [Browserify原理](http://zhenhua-lee.github.io/nodejs/browserify.html)
4. [browserify运行原理分析](http://www.alloyteam.com/2014/10/browserify-yun-xing-yuan-li-fen-xi/)
5. [通过 Browserify 在浏览器中使用 NodeJS 模块](http://www.ibm.com/developerworks/cn/web/1501_chengfu_browserify/)
6. [browserify.org](http://browserify.org/)
7. [node-browserify](https://github.com/substack/node-browserify)