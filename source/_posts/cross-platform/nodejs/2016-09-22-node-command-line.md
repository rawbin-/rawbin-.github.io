---
layout: post
title: "Node 命令行工具开发"
categories: [前端开发,Web开发,开发工具]
tags: [NodeJS,命令行]
---



### 1 NodeJS 基本命令行工具

+ 新建并初始化工程

  ```
  mkdir node-cmd
  cd node-cmd
  npm init
  ```

- 在工程目录下增加 bin目录 `mkdir bin`

- 在工程目录下的package.json中增加bin的配置

  ```
  {
    "bin":{
      "testcmd":"./bin/testcmd.js"
    }
  }
  ```

- 在bin 目录下新建文件`testcmd.js`

  ```
  #!/usr/bin/env node

  console.log('hello node cmd')
  ```

- 注册命令行到全局npm `npm link`

- 执行测试命令 `testcmd`



### 2 参考资料

0. [Node.js 命令行程序开发教程](http://www.ruanyifeng.com/blog/2015/05/command-line-with-node.html)