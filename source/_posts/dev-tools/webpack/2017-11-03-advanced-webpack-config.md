---
layout: post
title: "Webpack 4 配置实战疑点详解"
categories: [前端开发,打包工具]
tags: [Vue,Node,Webpack]
---



### 引言

本文不是一个完整的配置指南，而是在一个完整配置的基础上进行优化或者实现一些特殊需求的内容，不是一个完整的配置参考，但能给出参考入口，同时也能将文档没写清楚的内容做一下详细解释。



### 基本配置

#### `optimization`

- [文档参考](https://webpack.js.org/plugins/split-chunks-plugin/)--

- webpack本身会自动对代码块进行分割，条件规则如下

  - 新的可共享的块或者从`node_modules`里面来的模块
  - 新的大约`30KB`（压缩前大小）的块
  - 按需加载最大并行请求数量要求不超过5个
  - 初始页面最大并行请求数量要求不超过3个

- 默认值如下

  ```
  module.exports = {
    //...
    optimization: {
      splitChunks: {
        chunks: 'async',
        minSize: 30000,
        maxSize: 0,  // 优先级 maxInitialRequest/maxAsyncRequests < maxSize < minSize
        minChunks: 1,
        maxAsyncRequests: 5,
        maxInitialRequests: 3,
        automaticNameDelimiter: '~',
        name: true,
        cacheGroups: {
          vendors: {
            test: /[\\/]node_modules[\\/]/,
            priority: -10
          },
          default: {
            minChunks: 2,
            priority: -20,
            reuseExistingChunk: true
          }
        }
      }
    }
  };
  ```

- `optimization.runtimeChunk` 可选值 `true`和`multiple`,还有 single [参考](https://webpack.js.org/configuration/optimization/#optimization-runtimechunk)

  - `true`和`multiple`是一个意思，
  - 这里需要注意下，在配置`html-webpack-plugin`的时候，需要配置`runtime-${entry}` 或者是 `runtime`

- `optimization.splitChunks.chunks` 制定需要优化的代码块

  - `all` 同步和异步模块都进行打包优化，在所有块中共享块
  - `async` 只针对异步模块进行打包优化，在异步块中共享块
  - `initial` 只针对同步块进行打包优化，在同步块中共享块
  - Function , 

- `optimization.splitChunks.cacheGroups` 作为代码块分组的内容，作用替代`CommonsChunkPlugin`,`test`、`priority`、`reuseExistingChunk` 只能在`cacheGroups`下面每个`group`中设置

  - 多页打包，提取公共内容

    ```
    cacheGroups: {
        commons: {
            name: 'commons',
            chunks: 'initial',
            minChunks: 2
        }
    }
    ```

  - `node_modules`下面的内容单独打包

    ```
    cacheGroups: {
        commons: {
            test: /[\\/]node_modules[\\/]/,
            name: 'vendors',
            chunks: 'all'
        }
    }
    ```

- 注意事项

  - 默认会有`vendors-${entry}`这个注入块
  - `vendors-${entry}`在配置了多个 `cacheGroups`的情况下，不一定会出这个注入块（有优先级、size、minChunks等条件的约束）
  - 配置成`html-webpack-plugin` chunks的时候需要注意这个问题

- 待解决 default 控制了什么内容

#### webpack-dev-server

- [文档参考](https://webpack.js.org/configuration/dev-server/#devserver)

- 增加一个API Mock 中间件的配置，[插件源码参考](https://github.com/rawbin-/webpack-api-mock-plugin), 放`setup` ,`before`配置里面都可以

  ```
  module.exports = {
    //...
    devServer: {
      setup: function(app, server) {
        if (config.dev.withMock) {
            app.use(mockPlugin({
                mockConf: './mock/mock-conf.js'
            }))
        }
      }
    }
  };
  ```

- `host`配置为`0.0.0.0`可以支持外部访问，比如用IP地址来访问

- `historyApiFallback` 配置 `history` 模式下的路由解析

  ```
  historyApiFallback: {
              rewrites: [
                  {from: /.*/, to: path.posix.join(config.dev.assetsPublicPath, 'index.html')},
              ],
  },
  ```

- `hot` `webapck-dev-server` 内置了热更新，只需要配置`hot=true`即可，使用`webpack-dev-middleware`的话还需要单独配置热更新的内容

- `https` 配置启用https，可以使用`webpack-dev-server`内置的证书，也可以自己提供证书

- `overlay` 报错直接在页面上显示

  ```
  module.exports = {
    //...
    devServer: {
      overlay: {
        warnings: true,
        errors: true
      }
    }
  };
  ```

#### html-webpack-plugin 打包js注入模板

- [文档参考](https://webpack.js.org/plugins/html-webpack-plugin/)
- 默认可以解析 [lodash templates](https://lodash.com/docs#template), 常用的如下：
  - 使用 `<%= variable %>` 或者 `<% ${variable} %>` 来展示变量值
  - 使用`<%- variable %>` 来对变量值进行`HTML`转义
  - 使用 `<% //js code %>` 来嵌入`JavaScript`代码
- 问题1，使用chunks 设置内容的时候，同样的配置在`npm run dev`下，会多出来一个`vendors-${entry}`的注入项，如果chunks中没设置，会导致页面无法渲染，没有报错
  - 原因：默认有vendors组，会将node_modules 里面的自动打包，并且根据根据entry命名 `vendors-${entry}`类似于`runtime`
  - 解决：直接不设置chunks，使用自动的配置；或者禁用vendors 组（设置cacheGroups.vendors:false），

#### web-webpack-plugin  针对多页打包有优化

- [web-webapck-plugin](https://github.com/gwuhaolin/web-webpack-plugin)



####  script-ext-html-webpack-plugin 增强的html-webpack-plugin 更多精细控制

- [文档参考](https://github.com/numical/script-ext-html-webpack-plugin)



### 优化
#### 分析工具
- 打包时间衡量 [speed-measure-webpack-plugin](https://github.com/stephencookdev/speed-measure-webpack-plugin)

- 打包模块依赖查看 [webpack-bundle-analyzer]

- 命令行看板 [webpack-dashboard](https://github.com/FormidableLabs/webpack-dashboard)

- 增强版打包看板 [jarvis](https://github.com/zouhir/jarvis)

#### 性能优化

- 启用babel-loader cacheDirectory

- 增加happypack（再Webpack4中启用babel-loader Cache后引入几乎无改善）
  - 修改`webpack.base.conf.js` 将`babel-loader`缓存 `happypack-loader`

  ```
  {
                  test: /\.js$/,
                  // loader: 'babel-loader?cacheDirectory',
                  loader:'happypack/loader?id=babelloader',
                  // use: [{
                  //     loader:'happypack/loader',
                  //     options:{
                  //         id:'babelloader'
                  //     }
                  // }],
  }
  
  // 增加对vue-loader的配置
  Object.assign(vueLoaderConfig.loaders, {
      js: 'happypack/loader?id=babelloader',
  })
  ```

  - 修改`webpack.prod.conf.js` 增加`babel-loader` 插件配置

  ```
  new HappyPack({
             id:'babelloader',
             loaders:['babel-loader?cacheDirectory']
  }),
  ```

  - 异常解决 `Error: options/query provided without loader (use loader + options) in`

  将上面的改动use的写法，改成loader的写法

- dll打包的方式，优化效果也不明显，使用`optimization.splitChunks.cacheGroups` 来优化浏览器长期缓存







### 参考资料

0. [加快Vue项目的开发速度](https://juejin.im/post/5c106485e51d450e657571a6?utm_source=gold_browser_extension)
0. [Vue项目Webpack优化实践，构建效率提高50%](https://juejin.im/post/5c1fa158f265da613c09cb36?utm_source=gold_browser_extension)
0. [Webpack4优化之路](https://www.colabug.com/2639835.html)
0. [从实践中寻找webpack4最优配置](https://segmentfault.com/a/1190000015032321)
0. [手摸手，带你用合理的姿势使用webpack4（下）](https://segmentfault.com/a/1190000015919928)
0. [webpack SplitChunksPlugin实用指南](https://juejin.im/post/5b99b9cd6fb9a05cff32007a)
0. [Webpack 4 配置最佳实践](http://web.jobbole.com/94842/)
0. [Webpack 4 配置最佳实践](https://zxc0328.github.io/2018/06/19/webpack-4-config-best-practices/)
0. [Webpack 打包优化之速度篇 vue-loader-happypack](https://www.jeffjade.com/2017/08/12/125-webpack-package-optimization-for-speed/)
0. [使用happypack将vuejs项目webpack初始化构建速度提升50%](https://flyyang.me/2017/03/09/%E4%BD%BF%E7%94%A8happypack%E5%B0%86vuejs%E9%A1%B9%E7%9B%AEwebpack%E5%88%9D%E5%A7%8B%E5%8C%96%E6%9E%84%E5%BB%BA%E9%80%9F%E5%BA%A6%E6%8F%90%E5%8D%8750/)
0. [React 16 加载性能优化指南](https://zhuanlan.zhihu.com/p/37148975)
0. [webpack4之splitChunks拆拆拆](https://juejin.im/post/5c00916f5188254caf186f80?utm_source=gold_browser_extension)
0. [一步一步的了解webpack4的splitChunk插件](https://juejin.im/post/5af1677c6fb9a07ab508dabb)
0. [趁webpack5还没出，先升级成webpack4吧](https://www.cnblogs.com/imwtr/p/9189670.html)
0. [webpack4.0各个击破（4）—— Javascript & splitChunk](https://www.cnblogs.com/dashnowords/p/9545482.html)
0. [Webpack4之SplitChunksPlugin规则](https://blog.csdn.net/napoleonxxx/article/details/81975186)
0. [Webpack4学习笔记（三）——代码分割（多入口）](https://www.jianshu.com/p/741d9c98c395)
0. [WebpackOptions.json](https://github.com/webpack/webpack/blob/master/schemas/WebpackOptions.json)
0. [手摸手，带你用合理的姿势使用webpack4（上）](https://www.jianshu.com/p/1fcc2f8ed8bb)
0. [手摸手，带你用合理的姿势使用webpack4（上）](https://segmentfault.com/a/1190000015919863)
0. [高性能webpack配置](https://github.com/xitu/gold-miner/blob/master/TODO/keep-webpack-fast-a-field-guide-for-better-build-performance.md)
0. [chunks 详解-Webpack 4 — Mysterious SplitChunks Plugin](https://medium.com/dailyjs/webpack-4-splitchunks-plugin-d9fbbe091fd0)
0. [Webpack 4 Course Code Spliting with SplitChunksPlugin](https://wanago.io/2018/06/04/code-splitting-with-splitchunksplugin-in-webpack-4/)
0. [webpack 4: 模式(mode)和优化项(optimization)说明](http://forgetting.me/?p=210)