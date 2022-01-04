---
layout: post
title: "项目规范标准化"
categories: [前端开发,Web开发,JavaScript,代码规范]
tags: [JavaScript,NodeJS,TypeScript,代码规范]

---



# 规范化工具

## EditorConfig

用来统一不同平台不同编辑器中的编辑内容规范的配置，被各种编辑器广泛的支持，一次配置处处统一的感觉 

- [官网](https://editorconfig.org/)
- [配置规范](https://editorconfig-specification.readthedocs.io/en/latest/)

### 配置样例 `.editorconfig`

```
# 项目根目录下的这个值要 为true, 配置这一个就可以用了
root = true

# 针对所有文件的配置
[*]
# end_of_line = lf # 不配置就让系统自己定
charset = utf-8
tab_width = 4
indent_size = 4
indent_style = space
insert_final_newline = false
trim_trailing_whitespace = true

# 可以针对不同的文件类型单独配置
# [*.{js,jsx}]
# indent_size = 4

# 也可以针对具体的文件单独配置
# [{package.json,package-lock.json}]
# indent_size = 4
```



### 文件格式

- 空行 只包含空格
- 注释 `;` 或者 `#` 开头的行
- 区块头 `[`开头`]`结尾，`/`为路径分隔符,`\\`不能为路径分隔符
- 键值对 A=B的形式，中间可以有空格

### 匹配规则

- `*`  匹配不带`/`的字符串
- `**` 匹配任意字符串
- `?` 匹配非`/`的单字符
- `[seq]` 任意中括号内的单字符
- `[!seq]`任意非中括号内的单字符
- `{s1,s2,s3}` 任意单个字符串
- `{num1,num2}` 任意一个范围内的整数

### 配置属性

所有属性值都是大小写不敏感的，都会被核心库转为小写

- `root`
  - `true` 表示这个是顶层配置

- `indent_style`
  - `tab`
  - `space`
- `indent_size`
  - `tab`   用 `tab_width`  或 tab的宽度
  - 列的数量
- `tab_width`
  - 列的数量，当`indent_size`是数字的时候，默认是`indent_size` 

- `end_of_line` 不设置就可以不同平台用不同平台的，代码管理平台来控制
  - `lf` 
  - `crlf`
  - `cr`
- `charset`
  - `latin`
  - `utf-8`
  - `utf-16be`
  - `utf-16le`
  - `utf-8-bom`
- `trim_trailing_whitespace`
  - `true`
  - `false`
- `insert_final_newline`
  - `true`
  - `false`



## ESLint

冲破一切，最后胜出的一款ECMAScript及其周边的静态规范检查工具

- [官网](https://eslint.org/)
- [中文站点](https://cn.eslint.org/)

### 安装配置

```
npm i eslint -g # 全局安装
npm i eslint -D # 本地安装
eslint --init 生成配置文件
eslint --fix  path/to/files
eslint --fix-dry-run path/to/files
eslint --fix-type Array path/to/files 
```

### 配置方式

- 注释方式，可以直接配置到源代码中

- 配置文件的方式，可以使用`JavaScript`,`JSON`或者`YAML`文件来进行配置

  - 可以使用 `.eslintrc.*` 文件来配置，也可以在`package.json` 中的`eslintConfig`中配置
  - 同一个目录下的 优先级顺序
    - `.eslintrc.js`
    - `.eslintrc.yaml`
    - `.eslintrc.yml`
    - `.eslintrc.json`
    - `.eslintrc`
    - `package.json`

- 禁用规则

  - 注释的方式
  - 禁用一组文件的配置文件中的规则，使用`rules`,`overrides` 中再配置`rules来做`

- 忽略的文件 `.eslintignore`

  - `#` 开头的行被当做注释
- 路径是相对于本配置文件的路径
  
  - 忽略模式的规范跟`.gitignore`一致 [gitignore](https://git-scm.com/docs/gitignore)
- 以`!`开头的为否定模式，重新包含前面被忽略的模式
  
-  默认lint  `.js`文件，其他的需要根据命令行参数指定

  ```
  eslint --fix-dry-run --no-ignore --ext .js,.jsx .
  ```

  

  



###  配置属性

- `parser`  常用默认的和 `babel-eslint`
- `parserOptions` 解析器的信息
  -  `ecmaVersion`  这个字段不会自动启动比如ES6的全局变量
  - `sourceType` 默认`script` 可以设为`module`
  - `ecmaFeatures` 额外的语言特性
    - `globalReturn`
    - `impliedStrict` 全局严格模式
    - `jsx`
    - `experimentalObjectRestSpread` 实验的特性
- `plugins` 插件可以提供处理器
- `overrides` 插件重写的配置
- `processor` 可以从另一种文件中提取代码，然后处理其中的JavaScript
- `env` 检查所支持的环境，`browser`,`node`,`commonjs`,`es6`等
- `globals` 全局变量
  - 每个变量的属性值`writable`,`readonly`,`off`
- `rules` 具体的规则
  - 每一个规则的值`off`,`warn`,`error` 或者 `0`,`1`,`2`
  - 配置在插件中的规则时，需要是` 插件名/ 规则ID`的形式
  - [规则配置文档](https://cn.eslint.org/docs/rules/)
- `extends` 扩展配置
  - `eslint:recommended` 扩展推荐的配置 



## StyleLint

通用的样式规则检查工具

- [官网](https://stylelint.io/user-guide/get-started)

```
npm install --save-dev stylelint stylelint-config-standard 
```

### 禁用规则

```
/* stylelint-disable */
/* stylelint-enable foo */
/* stylelint-disable foo */
/* stylelint-enable */
/* stylelint-disable foo, bar */
/* stylelint-disable baz */
/* stylelint-enable baz, bar */
/* stylelint-enable foo */
/* stylelint-disable-line */
/* stylelint-disable-next-line */
```





##  配置Prettier

非常受欢迎的代码美化工具

- [官网](https://prettier.io/docs/en/options.html)

```
npm i eslint-config-prettier -D
npm i eslint-plugin-prettier -D
```

- 配置改到`.eslintrc.js`中的`extends`序列中





## 配置lint-staged

```
npm i lint-staged -D
```

在`package.json` 中添加如下配置

```
  "lint-staged": {
      "*.js": [
          "eslint --fix"
      ]
  },
```

- 匹配规则
  - 使用[micromatch](https://github.com/micromatch/micromatch) 来匹配
  - `*.js`所有的js文件
  - `!(*test).js`除了`*test.js`的js文件
  - 如果不带`/` 就不管路径，如果带`/`就需要管路径
  - 可以通过`*.{js,jsx}`来指定多个匹配后缀 



## 配置 commitlint

- 安装

  ```
  npm install --save-dev @commitlint/{cli,config-conventional}
  ```

- 配置文件 `commitlint.config.js`

  ```
  module.exports = {
    extends: ['@commitlint/config-conventional']
  }
  ```

  

## 配置husky

```
npm i husky -D
```

在`package.json`中加入如下配置


```
  "husky": {
      "hooks": {
          "pre-commit": "lint-staged", # 这个命令是想要的hook命令
          "commit-msg": "commitlint -e $HUSKY_GIT_PARAMS"
      }
  },
```





# 编辑器配置

## WebStorm 或 IDEA系列的

### 配置EditorConfig

【Preference】=>【Editor】=> 【Code Style】=> [Enable EditorConfig Support]



### 配置ESLint

【Preference】=> 【Language & Frameworks】=>【JavaScript】=> 【Code Quality Tools】=> 【ESLint】=> [Automatic ESLint Configuation]



## VSCode 配置

### 配置EditorConfig

【Extention】=>搜索[EditorConfig for VSCode] 然后 【install】即可



### 配置ESLint

【Extention】=>搜索[ESLint] 然后 【install】即可





# 项目实践操作

## EditorConfig

拷贝如下内容到项目`.editorconfig` 文件中，

```
# 项目根目录下的这个值要 为true, 配置这一个就可以用了
root = true

# 针对所有文件的配置
[*]
# end_of_line = lf # 不配置就让系统自己定
charset = utf-8
tab_width = 4
indent_size = 4
indent_style = space
insert_final_newline = false
trim_trailing_whitespace = true

# 可以针对不同的文件类型单独配置
# [*.{js,jsx}]
# indent_size = 4

# 也可以针对具体的文件单独配置
# [{package.json,package-lock.json}]
# indent_size = 4
```



## 配置ESLint

使用 `eslint --init` 并选择对应的选项 ，生成 基础的配置

- 增加基础的lint配置

​       ` npm i eslint-config-recommended -D` 然后在`.eslintrc.js`的`extends`中把 `eslint:recommended`放到第一个

- 增加对 babel的支持

  `npm i babel-eslint  -D`  然后在`eslint` 的配置文件里面增加 `"parser":"babel-eslint"`的属性配置



## 配置Stylelint

### 小程序配置

 ```
npm install --save-dev stylelint stylelint-config-standard stylelint-config-standard-wxss
 ```

- 配置文件`.stylelintrc.json`

  ```
  {
    "extends": "stylelint-config-standard-wxss"
  }
  ```

- 配置文件 `.stylelintignore`

  ```
  node_modules
  miniprogram_npm
  ```



### React工程配置

```
npm install --save-dev stylelint stylelint-config-standard stylelint-scss
```

- 配置文件`.stylelintrc.json`

  ```
  {
    "extends": "stylelint-config-standard",
    "plugins": ["stylelint-scss"]
  }
  ```

-  配置文件  `.stylelintignore`

  ```
  node_modules
  dist
  ```

  



## 配置lint-staged

`npm i lint-staged -D`

然后就可以在`package.json`中添加对应的配置了

```
  "lint-staged": {
      "*.{js,jsx}": [
          "eslint --fix --ext .js,.jsx"
      ],
      "*.wxss": [
      		"stylelint --fix --syntax css"
      ]
  },
```



## 配置 commitlint

- 安装

  ```
  npm install --save-dev @commitlint/{cli,config-conventional}
  ```

- 配置文件 `commitlint.config.js`

  ```
  module.exports = {
    extends: ['@commitlint/config-conventional']
  }
  ```

  



## 配置husky

`npm i husky -D`

然后就可以在`package.json`  中添加hooks了  

 ```
  "husky": {
      "hooks": {
          "pre-commit": "lint-staged", # 这个命令是想要的hook命令
          "commit-msg": "commitlint -e $HUSKY_GIT_PARAMS"
      }
  },
 ```







# 参考文档

- [ESLint 配置及应用]([https://rawbin-.github.io/javascript/web%E5%89%8D%E7%AB%AF/web%E5%BC%80%E5%8F%91/2016/09/01/eslint-application/](https://rawbin-.github.io/javascript/web前端/web开发/2016/09/01/eslint-application/))
- [magic-lint](https://github.com/ineo6/magic-lint)
- [commitlint](https://commitlint.js.org/#/)
- [commitzen](https://github.com/commitizen/cz-cli)
- [standard-version](https://github.com/conventional-changelog/standard-version)
- [stylelint](https://github.com/stylelint/stylelint)

