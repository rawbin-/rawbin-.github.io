```
layout: post
title: "Vue CLI 中 webpack 升级指南"
categories: [前端开发,打包工具]
tags: [Vue,Node,Webpack]
```



### Vue CLI webpack 2.6.1 升级 webpack 4

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

- 安装webpack `npm i webpack@latest -D`

- 更新html打包插件 `npm i html-webpack-plugin@latest -D` 解决如下异常

  ```
  TypeError: compilation.mainTemplate.applyPluginsWaterfall is not a function
  ```

- 更新Vue Loader `npm i vue-loader@latest -D` ,并通过webpack配置加载VueLoaderPlugin

  ```
  // webpack.base.config.js
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
  
  vue-loader was used without the corresponding plugin. Make sure to include VueLoaderPlugin in your webpack config.
  ```

- 修改配置文件 `webpack.dev.conf.js` 增加`mode=development`，修改配置文件`webpack.prod.conf.js` 增加 `mode=development`

  ```
  const devWebpackConfig = merge(baseWebpackConfig, {
    mode:'development',
    //webpack.dev.conf.js
  }
  
  const webpackConfig = merge(baseWebpackConfig, {
    mode:'production',
    // webpack.prod.conf.js
  }  
  ```

  消除如下警告

  ```
  The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
  You can also set it to 'none' to disable any default behavior.
  ```

- 移除 `webpack.prod.conf.js`中的 `UglifyJsPlugin`相关内容，解决如下异常

  ```
  Error: webpack.optimize.UglifyJsPlugin has been removed, please use config.optimization.minimize instead.
  ```

- 移除`webpack.prod.conf.js`中的 `CommonChunkPlugin`相关内容，解决如下异常

  ```
  Error: webpack.optimize.CommonsChunkPlugin has been removed, please use config.optimization.splitChunks instead.
  ```

- 增加`webpack.optimize.CommonsChunkPlugin`相关对应的配置在`webpack.prod.conf.js`

  ```
    mode:'production',
    optimization:{
        minimize:true,
        runtimeChunk:{
            name:'manifest'
        },
        splitChunks:{
            name:true,
            cacheGroups:{
                vendor:{
                    reuseExistingChunk:true,
                    test:function (module,chunks) {
                        // any required modules inside node_modules are extracted to vendor
                        return (
                            module.resource &&
                            /\.js$/.test(module.resource) &&
                            module.resource.indexOf(
                                path.join(__dirname, '../node_modules')
                            ) === 0
                        )
                    }
                },
                app:{
                    minChunks:3
                }
            }
        },
        namedModules:true,
        namedChunks:true,
    },
  ```

- 安装 copy-webpack-plugin `npm i copy-webpack-plugin @latest -D`, 解决如下问题

  ```
  TypeError: compilation.contextDependencies.push is not a function
  ```







### Vue CLI webpack 3.6.1 升级 webpack 4

- 升级到babel 7

  - 安装babel升级工具 `npm i babel-upgrade -g`   [参考](https://babeljs.io/docs/en/v7-migration)

  - 在工程目录下执行 `npx babel-upgrade` .根据输出结果增加package.json中的内容，并做必要的调整，比如`babel-polyfill`

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

- 安装webpack `npm i webpack@latest -D`

- 安装webpack-dev-server  `npm i webpack-dev-server@latest -D`

- 安装webpack-cli `npm i webpack-cli@latest -D`

- 更新html打包插件 `npm i html-webpack-plugin@latest -D` 解决如下异常

  ```
  TypeError: compilation.mainTemplate.applyPluginsWaterfall is not a function
  ```

- 更新Vue Loader `npm i vue-loader@latest -D` ,并通过webpack配置加载VueLoaderPlugin

  ```
  // webpack.base.config.js
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
  
  vue-loader was used without the corresponding plugin. Make sure to include VueLoaderPlugin in your webpack config.
  ```

- 修改配置文件 `webpack.dev.conf.js` 增加`mode=development`，修改配置文件`webpack.prod.conf.js` 增加 `mode=development`

  ```
  const devWebpackConfig = merge(baseWebpackConfig, {
    mode:'development',
    //webpack.dev.conf.js
  }
  
  const webpackConfig = merge(baseWebpackConfig, {
    mode:'production',
    // webpack.prod.conf.js
  }  
  ```

  消除如下警告

  ```
  The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
  You can also set it to 'none' to disable any default behavior.
  ```

- 安装css提取插件 `npm i mini-css-extract-plugin -D` 修改配置文件`webpack.prod.conf.js` , 替换 `extra-text-webpack-plugin`, 同时更新`utils.js`中的内容

  ```
  //webpack.prod.conf.js 直接替换
  const MiniCssExtractPlugin = require('mini-css-extract-plugin') 
  
  //utils.js
  if (options.extract) {
  	return[MiniCssExtractPlugin.loader].concat(loaders)
  } else {
  	return ['vue-style-loader'].concat(loaders)
  }
  ```

  解决如下异常

  ```
  Error: webpack.optimize.CommonsChunkPlugin has been removed, please use config.optimization.splitChunks instead.
  ```
- 增加`webpack.optimize.CommonsChunkPlugin`相关对应的配置在`webpack.prod.conf.js`

  ```
    mode:'production',
    optimization:{
        minimize:true,
        runtimeChunk:{
            name:'manifest'
        },
        splitChunks:{
            name:true,
            cacheGroups:{
                vendor:{
                    reuseExistingChunk:true,
                    test:function (module,chunks) {
                        // any required modules inside node_modules are extracted to vendor
                        return (
                            module.resource &&
                            /\.js$/.test(module.resource) &&
                            module.resource.indexOf(
                                path.join(__dirname, '../node_modules')
                            ) === 0
                        )
                    }
                },
                app:{
                    minChunks:3
                }
            }
        },
        namedModules:true,
        namedChunks:true,
    },
  ```



### 特别注意

- babel 7 配置文件有特殊变化，node_modules中需要转码的，babel配置文件需要使用`babel.config.js`，tong