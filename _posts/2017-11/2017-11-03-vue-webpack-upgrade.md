```
layout: post
title: "Vue CLI webpack 升级指南"
categories: [前端开发,打包工具]
tags: [Vue,Node,Webpack]
```



### Vue CLI webpack 2.6.1 升级

- 升级到babel 7

  - 安装babel升级工具 `npm i babel-upgrade -g`   [参考](https://babeljs.io/docs/en/v7-migration)

  - 在工程目录下执行 `npx babel-upgrade` .根据输出结果增加package.json中的内容

  - 增加babel 配置，原有的preset废弃了，直接用 env

    ```
    {
        "presets": [
            [
                "@babel/preset-env",
                {
                    "useBuiltIns": "entry"
                }
            ]
        ]
    }
    ```

- 安装webpack `npm i webpack@latest -S`

- 更新html打包插件 `npm i html-webpack-plugin@latest -S` 解决如下异常

  ```
  TypeError: compilation.mainTemplate.applyPluginsWaterfall is not a function
  ```

- 更新Vue Loader `npm i vue-loader@latest -S` 解决如下异常

  ```
  TypeError: Cannot read property 'vue' of undefined
  ```



### Vue CLI webpack 3.6.1 升级

- 升级到babel 7

  - 安装babel升级工具 `npm i babel-upgrade -g`   [参考](https://babeljs.io/docs/en/v7-migration)

  - 在工程目录下执行 `npx babel-upgrade` .根据输出结果增加package.json中的内容

  - 增加babel 配置，原有的preset废弃了，直接用 env

    ```
    {
        "presets": [
            [
                "@babel/preset-env",
                {
                    "useBuiltIns": "entry"
                }
            ]
        ]
    }
    ```

- 安装webpack `npm i webpack@latest -S`

- 安装webpack-dev-server  `npm i webpack-dev-server@latest -S`

- 安装webpack-cli `npm i webpack-cli@latest -S`

- 更新html打包插件 `npm i html-webpack-plugin@latest -S` 解决如下异常

  ```
  TypeError: compilation.mainTemplate.applyPluginsWaterfall is not a function
  ```

- 更新Vue Loader `npm i vue-loader@latest -S` ,并通过webpack配置加载VueLoaderPlugin

  ```
  // webpack.config.js
  const VueLoaderPlugin = require('vue-loader/lib/plugin')
  
  module.exports = {
    module: {
      rules: [
        // ... 其它规则
        {
          test: /\.vue$/,
          loader: 'vue-loader'
        }
      ]
    },
    plugins: [
      // 请确保引入这个插件！
      new VueLoaderPlugin()
    ]
  }
  ```

  解决如下异常

  ```
  TypeError: Cannot read property 'vue' of undefined
  ```