---
layout: post
title: "Vue Router History Mode 适配填坑实战"
categories: [前端开发,Web开发,前端框架]
tags: [Webpack,Vue,Cli]
---









### 缘由

- 使用Hash Mode开发的一套大系统，完全成型
- 由于各种跳转的问题，nginx的转发会导致#后面的路径丢失
- 想试试History Mode
- 环境 Vue-Cli 早期版本，webpack 2.6



### 相关配置

#### 基础配置

- 将Vue Router mode设置为history,并将跳转路径中带#的`hash`路由改为`history`对应的方式
- [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware) 不带`dev-server`相关内容，只处理内容的映射,需要配合[connect-history-api-fallback](https://github.com/bripkens/connect-history-api-fallback)来做，默认给了一个默认的映射，但多级路由页面刷新还是会出现404

#### historyApiFallback日志

- 配置connect-history-api-fallback

  ```javascript
  // handle fallback for HTML5 history http
  app.use(require('connect-history-api-fallback')({
      verbose:true  //开启日志信息
  }))
  ```
#### 本地路由404
- 即使配置了这样的情况，还是会出现路由点击跳转好使，刷新页面404的情况，原因是刷新页面的地址被`proxyTable`匹配到了，找去了服务端（因为`ProxyMiddleware`在`connect-history-api-fallback`中间件前面），没有走Fallback，解决办法是把所有服务相关的转发都加上api后缀或者使用`connect-history-api-fallback`的`rewrites`配置来做，前提是中间件的加载位置要在代理中间件前面。



#### 本地图片404

- 原有图片是直接在Vue组件里面利用相对路径写的，对于history模式来说，总会根据当前路径加上莫须有的前缀，这里直接将所有图片路径写上绝对路径就好了 `/static/xxx`

#### Web Server 路由404

- 将打包后的内容部署到Web Server根目录，刷新页面路由会出现404，根据[Vue Router官网文档](https://router.vuejs.org/zh/guide/essentials/history-mode.html)配置 Web Server即可解决相关问题，比如nginx 配置，就会再找不到的情况下转到 index.html 通过router 解析路由

  ```
  location / {
    try_files $uri $uri/ /index.html;
  }
  ```

- 然而，更多时候我们的应用不是部署在根目录的，而是在某个路径下比如 app-test，这个时候最好的方式是我们引用的资源路径都跟着走相对路径，打包出来自带光环，但看看前面图片路径的情况则不尽然。这个时候我们加载相关页面各种图片资源404，刷新页面路由会报500，上面的nginx转发规则已经不适用了。

#### Web Server 路由500

- 上面官方文档里面的nginx配置已经不适合带路径目录的项目了，需要干掉。



#### 应用增加base ` /app-test/`

- 路由首先加上一个base ,这样路由跳转的时候才不会越界

  ```javascript
  export default new Router({
      mode:'history',
      base:'/app-test/',
      routes
  })
  ```

- build.assetsPublicPath 值改为 `/app-test/`，这样打包路径相关的资源会加上这个前缀

- dev.assetsPublicPath 值改为 `/app-test/`，这样本地dev server 路径能跟服务端访问一致

- 配置本地Server 转发
 ```javascript
// handle fallback for HTML5 history http
app.use(require('connect-history-api-fallback')({
    verbose:true,
    index:'/app-test/index.html',
}))
 ```

- Vue组件模板中使用`@/../static/images/xxx.png`引用图片, 其中 `@`为`src`的别名 [参考 vue-loader](https://vue-loader.vuejs.org/zh/guide/asset-url.html#%E8%BD%AC%E6%8D%A2%E8%A7%84%E5%88%99)

- Vue组件中SCSS style 使用`~/../static/images/xxx.png`引用图片，其中`~`使后面的解析为模块路径， `~@/`代表`src`[参考 sass-loader](https://github.com/webpack-contrib/sass-loader#imports) 或者 [css-loader](https://github.com/webpack-contrib/css-loader)

- 在 js 中使用的话，按相对于`src`的路径写，`import`或者`require`都ok，

- 对应nginx配置需要改为

  ```
  location /app-test {
    try_files $uri $uri/ /app-test/index.html;
  }
  ```

- 实例代码在 [这 github](https://github.com/rawbin-/vue-cli-2.x-imgpath/commit/7d1abda6a6221a20f34f79fb3eb8e972851d79a7)


















