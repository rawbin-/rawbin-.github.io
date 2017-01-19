---
layout: post
title: "npm-前端工程化模块化工具之npm"
categories: [前端开发,Web开发,开发工具]
tags: [模块化,工程化,JavaScript,npm,包管理]
---

### npm

npm 是Node平台的包管理器，跟各大操作系统类似Mac的appStore，brew，安卓的市场，window的商店，Ubuntu的apt-get，RedHat的yum；各大语言也有类似的实现Java的ant，maven，Python的pip，Ruby的gem,PHP 的Composer等。

### package.json

npm 在项目中的直接体现就是`package.json` 

+  [样例文件参考](https://registry.npmjs.org/npm/latest)

+  [文档说明](https://docs.npmjs.com/files/package.json)

+  通过`npm init`生成相应的配置文件 一个基本的样例，最重要和必不可少的是name 和version 字段

   ```json
      {
        "name": "algorithms-combined-js",
        "version": "1.0.1",
        "description": "algorithms for combined",
        "main": "libs/index.js",
        "scripts": {
          "build":"babel src -d libs",
          "prepublish":"npm run build",
          "test": "echo \"Error: no test specified\" && exit 1"
        },
        "repository": {
          "type": "git",
          "url": "git+https://github.com/rawbin-/algorithms-combined-js.git"
        },
        "keywords": [
          "algorithms",
          "javascript",
          "combination",
          "arrangement"
        ],
        "author": "rawbin-",
        "license": "ISC",
        "bugs": {
          "url": "https://github.com/rawbin-/algorithms-combined-js/issues"
        },
        "homepage": "https://github.com/rawbin-/algorithms-combined-js#readme",
        "devDependencies": {
          "babel-cli": "^6.18.0",
          "babel-preset-es2015": "^6.18.0",
          "babel-preset-stage-0": "^6.16.0"
        },
        "dependencies": {
          "underscore": "^1.8.3"
        }
      }
   ```

+  [依赖的版本配置](https://docs.npmjs.com/misc/semver)

      版本的配置有讲究，经常的兼容性问题都出在这，比如node-sass，多版本react等

   + 使用`>` `>=` `<` `<=` `=` 和 `||`来组合具体范围
   + 使用`-`来指定范围
   + 使用`x` `X` `*` 或者 留空 来指定版本通配
   + 使用`~`来指定最邻近右边的大版本范围，放开小版本的变更
   + 使用`^`来限定最左边的大版本

+  files 字段明确指定包含的文件 比如

   + package.json
   + ReadMe.md
   + ChangeLog
   + LICENCE

+  main 字段指定当前包的默认加载文件 index.js

+  bin 字段指定当前项目的可执行文件名称和路径配置，一般包含一些自定义的项目命令

+  scripts 配置使用npm能运行的脚本的命令，这个字段的内容比较丰富，也经常用到

   + 命令列表
     + prepublish publish postpublish
     + preinstall install postinstall
     + preuninstall uninstall postuninstall
     + preversion version postversion
     + pretest test posttest
     + prestop stop poststop
     + prestart start poststart
     + prerestart restart postrestart
   + 任意命令的运行方式为`npm run scriptname`,特殊的`start` `stop` `restart` `test` 可以直接用`npm scriptname`来运行
   + [scripts参考文档](https://docs.npmjs.com/misc/scripts)


###  常用命令

+ `npm i xxx -g` === `npm install xxx --global`
+ `npm i xxx -S` === `npm install xxx --save`
+ `npm i xxx -D` === `npm install xxx --save-dev`
+ `npm un xxx`  === `npm uninstall xxx`
+ `npm publish xxx`
+ `npm unpublish xxx`



### 编码相关

+ [编码规范](https://docs.npmjs.com/misc/coding-style)
+ [相关文件和配置](https://docs.npmjs.com/misc/developers)


### 参考资料

0. [npm 文档](https://docs.npmjs.com/)
1. [npm scripts使用指南](http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html)
2. [package.json说明](https://docs.npmjs.com/files/package.json)
3. [sermver 版本说明](https://docs.npmjs.com/misc/semver)
4. [开源协议说明 OpenSource](https://opensource.org/licenses/alphabetical)
5. [开源协议说明 SPDX](https://spdx.org/licenses/)


