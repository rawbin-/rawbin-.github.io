---
layout: post
title: "ESLint 配置及应用"
categories: [JavaScript,Web前端,Web开发]
tags: [eslint,代码规范,规范检查]
---

### 1 安装方式

- 全局安装 `npm install eslint -g`
  - `eslint --init`生成配置文件
- 局部安装 `npm install eslint --save-dev`

### 2 配置方式

- 注释配置，在代码文件中设置相应的规则

  ```JavaScript
  /* eslint-disable */
  /* eslint-enable */
  ```

  ​

- 配置文件，单独的配置文件`.eslintrc.*`文件，作用范围是其所在的的目录及其子目录；配置的范围包括环境，全局变量和规则。

  - JavaScript格式 `.eslintrc.js`

    ```javascript
    module.exports = {
        "parser":"babel-eslint",
        "parserOptions": {
            "ecmaVersion": 6,
            "sourceType": "module",
            "ecmaFeatures": {
                "jsx": true
            }
        },
        "rules": {
            "semi": 2
        }
    }
    ```

    ​

  - JSON格式 `.eslintrc.json`

    ```json
    {
        "parser":"babel-eslint",
        "parserOptions": {
            "ecmaVersion": 6,
            "sourceType": "module",
            "ecmaFeatures": {
                "jsx": true
            }
        },
        "rules": {
            "semi": 2
        }
    }
    ```

    ​

  - YAML格式 `.eslintrc.yml`或者`.eslintrc.yaml`

    ```yaml
    ---
    parser: babel-eslint

    rules:
    	
    ```

- package.json 配置

  - package.json 中 eslintConfig 配置

    ```json
    {
        "name": "mypackage",
        "version": "0.0.1",
        "eslintConfig": {
            "env": {
                "browser": true,
                "node": true
            }
        }
    }
    ```

    ​

### 3 禁用配置方式

- 注释方式

  - 禁用单行上的多个规则

    ```JavaScript
    alert('foo'); // eslint-disable-line no-alert, quotes, semi

    // eslint-disable-next-line no-alert, quotes, semi
    alert('foo');  
    ```

  - 禁用单行上的单个规则

    ```JavaScript
    alert('foo'); // eslint-disable-line no-alert

    // eslint-disable-next-line no-alert
    alert('foo');
    ```

  - 禁用单行上的所有规则

    ```JavaScript
    alert('foo'); // eslint-disable-line

    // eslint-disable-next-line
    alert('foo');
    ```

  - 禁用整个文件中的某个规则

    ```javascript
    /* eslint-disable no-alert */

    alert('foo');
    ```

  - 禁用整个文件的多个规则

    ```javascript
    /* eslint-disable no-alert, no-console */

    alert('foo');
    console.log('bar');
    ```

  - 禁用整个文件的所有规则

    ```JavaScript
    /* eslint-disable */

    alert('foo');
    ```

    ​

- 配置文件方式 `.eslintignore` 文件，语法同`.gitignore`

  ```
  path/to/file
  path/to/directory/*
  ```

### 4 配置内容

+ 配置文件中包含几部分内容
  + `parserOptions` 解析器版本校验
  + `parser`解析器
  + `env`运行环境，node，browser等
  + `globals`额外的全局变量
  + `plugins`相关的插件
  + 具体的配置规则，规则列表[参考](http://eslint.org/docs/rules/)

### 5 配置注意事项

- 同一个目录下出现了不同的配置，优先级别
  - .`eslintrc.js`
  - `.eslintrc.yaml`
  - `.eslintrc.yml`
  - `.eslintrc.json`
  - `.eslintrc`
  - `package.json`
- 在配置中使用`root:true` 来设置项目工根配置
- eslint会直接找上级目录直到找到根目录，如果还是没找到，还是会用`~/.eslintrc`这个文件。