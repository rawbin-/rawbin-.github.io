---
layout: post
title: "如何自己写一个公用的NPM包"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,npm,Node] Table of Contents 
---

[TOC]

**以[markdown-clear] (https://github.com/rawbin-/markdown-clear),创建过程为例，讲解整个NPM包创建和发布流程**

### 1 如何创建一个包

#### 1.1 创建并使用一个工程

+   在GitHub上新建一个仓库,其名`markdown-clear`
+   `clone` 这个工程到本地

#### 1.2 添加`LICENCE`或`LICENSE`文件， 说明对应的开源协议

+   到[SPDX License List](https://spdx.org/licenses/) 或者[Open Source Initiative](https://opensource.org/licenses/alphabetical)，下载相应协议的模板，我们这里选用MIT
+   修改必要的协议时间和作者
```
MIT License
Copyright (c) <year> <copyright holders>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
```

#### 1.3 添加`README`或者`ReadMe.md`或者`README.md`文件

+   说明项目的一些信息
+   给出详细参考资料的链接
+   给读者一个整体的导航内容

#### 1.4 添加`.gitignore` 文件，忽略不需要提交的文件变更

+    可以去[github gitignore](https://github.com/github/gitignore) 下载一个最相近的模板然后改改
+    可以去[gitignore.io](https://www.gitignore.io/)生成一个,然后把内容拿下了
+    可以参考[gitignore.io 文档](https://github.com/joeblau/gitignore.io)，自己配命令行工具，以便随时可以玩
+    我们这里生成了一个[Node + IntellJ-all](https://www.gitignore.io/api/node%2Cintellij%2Ball) 的结果
+    内容语法参考[gitignore doc](https://git-scm.com/docs/gitignore)

#### 1.5 初始化NPM包

+   使用`npm init` 初始化工程
+   按照提示填入相应的内容


#### 1.6 到这里的目录结构

+   工程三大件以及npm包配置文件都有了

```
markdown-clear
------------- .gitignore
------------- LICENCE
------------- README.md
------------- package.json
```

#### 1.7 editconfig

#### 1.8 ESLint

### 2 代码结构组织

#### 2.1 加入代码相关的目录

```
markdown-clear
-------------- src     // 源代码目录 比如coffee,typescript,es6+等代码的目录
-------------- lib     // 转义生成的代码目录，比如babel转义后的es5代码的目录
-------------- docs    // 代码相关的设计和使用文档
-------------- tests   // 相关的测试目录
```

#### 2.2 代码实现

+   写代码 src 目录
+   转换后的代码 lib 目录

##### 2.2.1 使用babel 转换代码

+   babel 配置文件 `.babelrc`

```
{
  "presets":["es2015","stage-0"]
}
```

+   添加 npm 命令

```
  "scripts": {
    "build": "babel src -d lib",
   }
```

##### 2.2.2 实现一个可以全局安装的npm包

+   添加`package.json`的配置

```
  "bin": {
    "markdown-clear": "./lib/cli.js"
  }
```

+   `cli.js`文件第一行添加

```
#!/usr/bin/env node
```



#### 2.3 测试

+   写测试用例 tests 目录
+   调用最终生成的 lib 下面的目录
+   可以考虑使用测试框架 mocha, jasmine, karma...

##### 2.3.1 安装测试

+   使用npm 安装本地文件 作为本地包

```
npm install path/to/markdown-clear
```

+   使用npm 安装本地文件 作为全局包

```
npm install path/to/markdown-clear -g
```



#### 2.4 文档输出

+   写文档 docs 目录
+   写代码相关的设计和使用文档，没有自然可以不用写
+   这里的文档应该在README.md 中会有入口。



### 3 发布NPM包

+   如果没有注册npm账户

```
npm adduser USERNAME
```

+   如果没有登录

```
npm login
```

+   登录后发布包

```
npm publish
```














