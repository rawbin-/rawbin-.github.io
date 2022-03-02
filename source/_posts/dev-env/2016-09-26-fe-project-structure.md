---
layout: post
title: "目录结构-前端工程化模块化之目录结构"
categories: [前端开发,Web开发,开发工具]
tags: [模块化,工程化,JavaScript,webpack,打包工具]
---

### 1 项目中经常出现的结构和文件

#### 1.1 src

+ 源代码所在目录

#### 1.2 doc/docs

+ 设计文档/API等所在目录 

#### 1.3 dist

+ 生成的发布文件所在目录

#### 1.4 test/tests

+ 测试用例,单元测试所在目录

#### 1.5 demo/example/expmples

+ 可运行的功能使用样例代码所在目录

#### 1.6 package.json

+ npm项目的主配置文件

#### 1.7 bower.json

+ 使用bower管理的node项目的包依赖配置文件

#### 1.8 ChangeLog/ChangeLog.md/CHANGELOG

+ 项目的版本发布流水账

#### 1.9 ReadMe/ReadMe.md/README

+ 项目的整体概要说明文件，比如说明项目的特性，如何部署，如何使用等

#### 1.10 `.babelrc`

+ babel转换工具的配置文件，[参考](http://babeljs.io/docs/usage/babelrc/)

+ 参考样例文件

  ```json
  {
    "presets": ["es2015"],
    "plugins": ["transform-es2015-modules-umd"]，
    "ignore": [
      "foo.js",
      "bar/**/*.js"
    ]
  }
  ```



#### 1.11 `.npmignore`

+ 在发布到npm源上需要忽略的文件

+ npm默认的配置文件，如果需要添加此外的文件(node_modules除外)，需要自己再添加

  ```
  .*.swp
  ._*
  .DS_Store
  .git
  .hg
  .npmrc
  .lock-wscript
  .svn
  .wafpickle-*
  config.gypi
  CVS
  npm-debug.log
  ```

  ​

#### 1.12 LICENCE/LICENSE

+ 项目支持的开源协议许可

+ 可以到[这里](https://spdx.org/licenses/),或者[这里](https://opensource.org/licenses/alphabetical)查看一个完整的列表

+ 样例文件参考 MIT

  ```
  Copyright <YEAR> <COPYRIGHT HOLDER>

  Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

  The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
  ```

  ​

#### 1.13 `.gitignore`

+ 是git用来区分哪些文件需要不加入版本库（被忽略）的配置文件，只影响未加入版本库的文件，所以这个最好在项目开始就搞好。

+ 如果你不了解，可以到[这里](https://github.com/github/gitignore)去下载一个相似的模板，也可以到[这里](https://www.gitignore.io/) 去自动生成一个更详尽的，需要本地配置参考[这里](https://github.com/joeblau/gitignore.io)。

+ 解析匹配规则参考[git doc](https://git-scm.com/docs/gitignore),[Linux glob](http://man7.org/linux/man-pages/man7/glob.7.html)

+ 常用规则

  + `#`开头的行和空行不对规则产生影响，常用来做注释和格式化
  + 单纯的字符串表示同名的目录或文件
  + `?`匹配单个字符,`*`匹配多个字符,`!`表示对规则例外
  + `[a-z]`表示匹配范围中的任何一个字符，`[!a-z]`表示匹配不在此范围内的任何一个字符
  + 通配字符不匹配路径中的`/`字符，也不匹配`.`字符
  + 以`/`开头的只匹配当前目录下的文件
  + 以`/`结尾的规则表示匹配一个目录
  + 路径中的`**`匹配多层级的目录结构

+ 样例配置参考(在[这里](https://www.gitignore.io/)自动生成的macOS下用WebStorm做Node开发的配置)

  ```
  # Created by https://www.gitignore.io/api/node,macos,webstorm

  ### Node ###
  # Logs
  logs
  *.log
  npm-debug.log*

  # Runtime data
  pids
  *.pid
  *.seed
  *.pid.lock

  # Directory for instrumented libs generated by jscoverage/JSCover
  lib-cov

  # Coverage directory used by tools like istanbul
  coverage

  # nyc test coverage
  .nyc_output

  # Grunt intermediate storage (http://gruntjs.com/creating-plugins#storing-task-files)
  .grunt

  # node-waf configuration
  .lock-wscript

  # Compiled binary addons (http://nodejs.org/api/addons.html)
  build/Release

  # Dependency directories
  node_modules
  jspm_packages

  # Optional npm cache directory
  .npm

  # Optional eslint cache
  .eslintcache

  # Optional REPL history
  .node_repl_history

  # Output of 'npm pack'
  *.tgz

  # Yarn Integrity file
  .yarn-integrity



  ### macOS ###
  *.DS_Store
  .AppleDouble
  .LSOverride

  # Icon must end with two \r
  Icon
  # Thumbnails
  ._*
  # Files that might appear in the root of a volume
  .DocumentRevisions-V100
  .fseventsd
  .Spotlight-V100
  .TemporaryItems
  .Trashes
  .VolumeIcon.icns
  .com.apple.timemachine.donotpresent
  # Directories potentially created on remote AFP share
  .AppleDB
  .AppleDesktop
  Network Trash Folder
  Temporary Items
  .apdisk


  ### WebStorm ###
  # Covers JetBrains IDEs: IntelliJ, RubyMine, PhpStorm, AppCode, PyCharm, CLion, Android Studio and Webstorm
  # Reference: https://intellij-support.jetbrains.com/hc/en-us/articles/206544839

  # User-specific stuff:
  .idea/workspace.xml
  .idea/tasks.xml

  # Sensitive or high-churn files:
  .idea/dataSources/
  .idea/dataSources.ids
  .idea/dataSources.xml
  .idea/dataSources.local.xml
  .idea/sqlDataSources.xml
  .idea/dynamic.xml
  .idea/uiDesigner.xml

  # Gradle:
  .idea/gradle.xml
  .idea/libraries

  # Mongo Explorer plugin:
  .idea/mongoSettings.xml

  ## File-based project format:
  *.iws

  ## Plugin-specific files:

  # IntelliJ
  /out/

  # mpeltonen/sbt-idea plugin
  .idea_modules/

  # JIRA plugin
  atlassian-ide-plugin.xml

  # Crashlytics plugin (for Android Studio and IntelliJ)
  com_crashlytics_export_strings.xml
  crashlytics.properties
  crashlytics-build.properties
  fabric.properties

  ### WebStorm Patch ###
  # Comment Reason: https://github.com/joeblau/gitignore.io/issues/186#issuecomment-215987721

  # *.iml
  # modules.xml
  # .idea/misc.xml
  # *.ipr

  # End of https://www.gitignore.io/api/node,macos,webstorm
  ```

  ​

#### 1.14 `.editorconfig`

+ 是用来维护同一套代码在不同的编辑环境中的代码风格一致性的配置文件。

+ 这个工具目前有多种语言的开发库，并对目前常见的编辑器和IDE都有相应的插件适配，是一个靠谱的方案。

+ [使用EditorConfig的项目列表](https://github.com/editorconfig/editorconfig/wiki/Projects-Using-EditorConfig),  [Node.js的配置文件](https://github.com/nodejs/node/blob/master/.editorconfig), [webpack的配置文件](https://github.com/webpack/webpack/blob/master/.editorconfig)

+ 通配符参考

  + `*` 匹配除`/`之外的任意单个字符
  + `**`匹配字符串
  + `?`匹配任意单个字符
  + `[xyz]`匹配中括号内的任一字符
  + `[!xyz]`匹配不在中括号内的任一字符
  + `[a-z]`匹配范围内的任一字符
  + `{abc,opq,xyz}`匹配这三个字符串中的任何一个
  + {123..456}匹配两个数字之间的任何一个

+ 常用属性参考, [全部属性](https://github.com/editorconfig/editorconfig/wiki/EditorConfig-Properties)

  + `root=true`表示到此终止对`.editorconfig`配置的查找，这里就是最顶层的配置
  + `indent_style` 可选值为`tab`,`space`,`unset` 设置缩进的风格
  + `indent_size` 当`indent_style=space`时的缩进字符数
  + `tab_width`当`indent_style=tab`时的缩进字符数
  + `end_of_line`可选值为`lf`，`cr`,`crlf`，设置换行的风格
  + `charset` 设置编码比如`utf-8`
  + `trim_trailing_whitespace`可选值为`true`,`false`设置是否删除行尾空白符
  + `intsert_final_newline`可选值`true`，`false`设置是否添加文件结尾的空行

+ 样例配置参考

  ```yaml
  root = true

  [*]
  indent_style = tab
  indent_size = 4
  charset = utf-8
  trim_trailing_whitespace = true
  insert_final_newline = true
  max_line_length = 233

  [*.json]
  indent_style = space
  indent_size = 2

  [*.yml]
  indent_style = space
  indent_size = 2

  [test/cases/parsing/bom/bomfile.{css,js}]
  charset = utf-8-bom

  [*.md]
  trim_trailing_whitespace = false
  ```



### 2 参考资料

0. [git ignore files](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository#Ignoring-Files)
1. [gitignore ](https://github.com/github/gitignore)
2. [gitignore.io](https://github.com/joeblau/gitignore.io)
3. [glob wiki](https://en.wikipedia.org/wiki/Glob_(programming))
4. [Linux glob](http://man7.org/linux/man-pages/man7/glob.7.html)
5. [editorconfig github](https://github.com/editorconfig)
6. [editorconfig.org](http://editorconfig.org/)
7. [开源协议说明 OpenSource](https://opensource.org/licenses/alphabetical)
8. [开源协议说明 SPDX](https://spdx.org/licenses/)
9. [babelrc配置](http://babeljs.io/docs/usage/babelrc/)
