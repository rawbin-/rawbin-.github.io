---
layout: post
title: "从零搞一个Node项目结构模板"
categories: [前端开发,Web开发,JavaScript,NodeJS]
tags: [JavaScript,NodeJS,项目结构,项目模板]
---



# Nest Web项目结构生成

第一次接触框架结构生成器是在好几年前用Django的时候，后面用Node也有类似的操作，确实省了不少事，直接上手吧

## 项目结构生成

### 观望一下

- 先安装命令行工具

```
npm i @nestjs/cli -g
```

- 查看都能干些啥

  ```
  $ nest -h
  Usage: nest <command> [options]
  
  Options:
    -v, --version                                   Output the current version.
    -h, --help                                      Output usage information.
  
  Commands:
    new|n [options] [name]                          Generate Nest application.
    build [options] [app]                           Build Nest application.
    start [options] [app]                           Run Nest application.
    info|i                                          Display Nest project details.
    update|u [options]                              Update Nest dependencies.
    add [options] <library>                         Adds support for an external library to your project.
    generate|g [options] <schematic> [name] [path]  Generate a Nest element.
      Available schematics:
        ┌───────────────┬─────────────┐
        │ name          │ alias       │
        │ application   │ application │
        │ class         │ cl          │
        │ configuration │ config      │
        │ controller    │ co          │
        │ decorator     │ d           │
        │ filter        │ f           │
        │ gateway       │ ga          │
        │ guard         │ gu          │
        │ interceptor   │ in          │
        │ interface     │ interface   │
        │ middleware    │ mi          │
        │ module        │ mo          │
        │ pipe          │ pi          │
        │ provider      │ pr          │
        │ resolver      │ r           │
        │ service       │ s           │
        │ library       │ lib         │
        │ sub-app       │ app         │
        └───────────────┴─────────────┘
  
  ```

- 只是看看都干了些啥

  - `--dry-run` 这是非常有用，非常通用的选项

  - 如果记不住，请记住我的翻译——干-跑，当然只是为了印象深刻哈，请专业一点~

  - 跑跑看，只是看看做了什么，只是看看

    ```
    $ nest new test-app --dry-run
    ⚡  We will scaffold your app in a few seconds..
    
    CREATE test-app/.eslintrc.js (599 bytes)
    CREATE test-app/.prettierrc (51 bytes)
    CREATE test-app/README.md (3370 bytes)
    CREATE test-app/nest-cli.json (64 bytes)
    CREATE test-app/package.json (1896 bytes)
    CREATE test-app/tsconfig.build.json (97 bytes)
    CREATE test-app/tsconfig.json (336 bytes)
    CREATE test-app/src/app.controller.spec.ts (617 bytes)
    CREATE test-app/src/app.controller.ts (274 bytes)
    CREATE test-app/src/app.module.ts (249 bytes)
    CREATE test-app/src/app.service.ts (142 bytes)
    CREATE test-app/src/main.ts (208 bytes)
    CREATE test-app/test/app.e2e-spec.ts (630 bytes)
    CREATE test-app/test/jest-e2e.json (183 bytes)
    
    
    Command has been executed in dry run mode, nothing changed!
    ```

    

  

### 直接上手

- 新建一个项目

  ```
  npm i @nestjs/cli -g
  nest new test-application
  ```

- 在当前目录下初始化，如果对应文件存在会报错

  ```
  nest new .
  ```

- 更多命令，请自行玩耍，用上面的方法 [参考](https://docs.nestjs.com/cli/overview)



## 目录结构解析

### 查看目录结构

```
$ tree -I node_modules
.
├── LICENSE                
├── README.md
├── nest-cli.json
├── package-lock.json
├── package.json
├── src
│   ├── app.controller.spec.ts
│   ├── app.controller.ts
│   ├── app.module.ts
│   ├── app.service.ts
│   └── main.ts
├── test
│   ├── app.e2e-spec.ts
│   └── jest-e2e.json
├── tsconfig.build.json
└── tsconfig.json
```





# 通用项目结构文件

可以参考TS官方的TS项目结构，大体都差不多，[TypeScript-Node-Starter](https://github.com/microsoft/TypeScript-Node-Starter/)

## 基础文件

### 协议文件

- 添加`LICENCE`或`LICENSE`文件， 说明对应的开源协议
  - 到[SPDX License List](https://spdx.org/licenses/) 或者[Open Source Initiative](https://opensource.org/licenses/alphabetical)，下载相应协议的模板，我们这里选用MIT
  - 修改必要的协议时间和作者
  - 这里我觉得没必要保留`80`列宽了，现在的显示设备几乎没必要这么玩了

```
MIT License
Copyright (c) <year> <copyright holders>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
```

### 说明文件

- 添加`README`或者`ReadMe.md`或者`README.md`文件
  - 说明项目的一些信息
  - 给出详细参考资料的链接
  - 给读者一个整体的导航内容
  - 等等

### git文件过滤

- 添加`.gitignore` 文件，忽略不需要提交的文件变更
- 自动生成
  - 可以去[github gitignore](https://github.com/github/gitignore) 下载一个最相近的模板然后改改
  - 可以去[gitignore.io](https://www.gitignore.io/)生成一个,然后把内容拿下了
  - 可以参考[gitignore.io 文档](https://github.com/joeblau/gitignore.io)，自己配命令行工具，以便随时可以玩
  - 我们这里生成了一个[macOS + Node + Webstorm](https://www.gitignore.io/api/node,macos,webstorm) 的结果
- 自己手写
  - 内容语法参考[gitignore doc](https://git-scm.com/docs/gitignore)



### 生成项目文件

```
npm init
```





## 增加TypeScript支持

### 生成配置文件

 ```
npm i typescript -D
npx tsc --init
 ```

在当前目录下会生成`tsconfig.json`，可以根据情况自行调整

### 增加TS的Lint规则

TypeScript官方采用ESLint规则了，通过配置可以生成对应的配置，如果要手动改，配置对应的包就行`@typescript-eslint/parser` `@typescript-eslint/eslint-plugin`

```
npm i eslint -D
npx eslint --init 
```

- 手动创建规则排除文件`.eslintignore`

  ```
  # /node_modules/* in the project root is ignored by default
  # build artefacts
  dist/*
  coverage/*
  # data definition files
  **/*.d.ts
  # 3rd party libs
  /src/public/
  # custom definition files
  /src/types/
  ```

  

### 类型定义文件生成

- 包文件类型定义生成 

  ```
  npm i dts-gen -D
  npx dts-gen -m MODULE_NAME -t TEMPLATENAME  
  ```

- 单个文件类型定义文件生成

  ```
  npm i dtsmake -D
  npx dtsmake -s ./path/to/sourcefile.jsdtsmake -s ./path/to/sourcefile.jsD 
  ```

  

### 使用TypeScript直接运行

只建议在开发环境使用，效率问题

```
npm i ts-node -D
```



## 添加相关框架

### 添加Web框架

几乎每一个Web框架都有对应的不同成熟度的项目结构生成工具，用起来，省点劲

- express

  生成App相关目录结构

  ```
  npm i express-generator -D
  npx express APP_NAME 
  ```

- koa

  ```
  npm i koa-generator -D
  npx koa APP_NAME 
  ```
  
- nest

  ```
  npm i @nestjs/cli -D
  npx nest APP_NAME
  ```

- egg

  ```
  npm init egg APP_NAME
  ```

  

### 添加测试框架

添加相关依赖并生成配置文件

```
npm install -D jest ts-jest @types/jest
npx jest --init 
```



## 其他相关内容

根据情况添加，比如CI/CD的，Docker的

