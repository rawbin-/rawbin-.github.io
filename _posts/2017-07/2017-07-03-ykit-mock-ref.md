---
layout: post
title: "YKit简明参考手册"
categories: [Web开发,前端开发,前端工程化,开发工具]
tags: [YKit,Mock,模拟数据,测试数据]
---

[TOC]

### YKit Mock配置

#### 设计

+ 使用[Mockjs](https://github.com/nuysoft/Mock)
  + YKit Mock数据使用 [Mockjs](https://github.com/nuysoft/Mock), 相应的[Mockjs文档和示例](http://mockjs.com/)
  + [Mockjs的语法规范](https://github.com/nuysoft/Mock/wiki/Syntax-Specification)
  + Mockjs数据规则参考[mockJSON](https://github.com/mennovanslooten/mockJSON), FEKit也使用了这个规则

#### YKit Mock启动方式

+ `ykit server`启动时如果配置了`mock`插件会自动启动mock server
+ `ykit server mock=false` 方式启动可以不启动mock server
+ 如果使用了ykit server并配置了mock，直接访问mock接口无法访问，需要先访问mock文件对应项目的某个资源，让ykit加载到对应的mock配置，这种情况会在项目上一层启动ykit server的时候出现。



#### YKit Mock配置文件

+ 默认搜索项目根目录下的`mock.js` 或者 `mock.json`,推荐使用`mock.js`

+ 可以自定义配置文件的路径，在YKit的配置文件`ykit.js`或者`ykit-xxx.js`中修改

  ```javascript
  module.exports = {
      plugins: [{
          name: 'mock',
          options: {
              confPath: './tests/mockdata/mockconf.js'
          }
      }],
      // ...
  };
  ```

  ​

+ mock文件支持两种配置方式

  + FEKit原来支持的方式

    - `key`=>`value`形式, 其中`key`为路径的匹配规则，`value`为对应的mock数据指向；
    - `pattern`=>`respondwith`形式，其中`pattern`为路径匹配规则,`respondwith`为对应的mock数据指向；

    ```javascript
    module.exports = {
      '/uri/to/api':'relative/path/to/mock/data/file',
      rules:[{
        pattern: "/uri/pattern/to/match",
        respondwith:'relative/path/to/mock/data/file',
      }]
    }
    ```

    ​

  + YKit 增加的方式

    ```javascript
    module.exports = [{
        // pattern:'/api/list.json',
        // responder:'./data/listData.json'
    }, {
        pattern: /^\/api\/(\w+)\.json.*$/,
        responder: './data/$1Data.mockjson'
    }, {
        pattern: /^\/api\/(\w+)\.json.*$/,
        responder: './data/$1Data.js'
    }, {
        pattern: '/api/list.json',
        responder: {
            "ret": true,
            "data": [{
                "name": "Li Lei",
                "email": "lilei@test.com",
                "registerDateTime": "2020-10-01 22:11:11"
            }, {
                "name": "Han Meimei",
                "email": "hanmeimei@test.com",
                "registerDateTime": "2020-10-01 22:11:11"
            }]
        }
    }]
    ```

    + json文件的配置跟上面直接出的数据一样

    + mockjson的数据有些区别，如下

      ```json
      {
        "ret|0-1":true,
        "data|2-5":[{
          "name":"@last @first",
          "email":"@email",
          "registerDateTime": "@date('yyyy-MM-dd') @time('HH:mm:ss')"
        },{
          "name":"@last @first",
          "email":"@email",
          "registerDateTime": "@date('yyyy-MM-dd') @time('HH:mm:ss')"
        }]
      }
      ```

      ​

+ 目前(2017-07)`ykit-config-mock`插件还不支持jsonp的mock配置

+ YKit Mock比 FEKit Mock强大的地方

  + 可以支持多个工程的mock文件，fekit只能同时用一个
  + YKit匹配的URL支持正则匹配到数据路径，这样可以批量造数据而配置一个规则，不用一个一个配
  + Mockjs 比 MockJSON强大得多，支持中文mock数据，可谓青出于蓝而胜于蓝



### YKit Proxy 配置

#### 设计

+ YKit Proxy 使用了蛟神写的一个牛逼的代理服务[Jerry Proxy](https://github.com/Ellery0924/Jerry),主要功能
  + HOST 管理
  + 代理服务
  + 请求日志和断点
  + 网络限速

#### YKit Proxy 代理配置

##### Jerry Proxy 启动

+ 使用`sudo ykit server -x` 启动带代理服务的ykit server
+ 使用`ykit server`启动日志中的`[JerryProxy] Available on: http://127.0.0.1:\d+/jerry`中的URL访问代理服务器的界面

##### Jerry Proxy 配置

###### 服务器组配置

+ 这里配置环境以及每套环境对应的机器，方便在HOST管理的地方被引用到

###### URL MAP

+ 请求转发的配置，支持转到本地文件，或者转发到其他页面

###### 代理面板

+ 配置是否使用HTTPS代理和网络限速
+ 配置HOST分组，Jerry Proxy会为每一个发现的YKit工程新建一个对应的分组
+ HOST 规则的配置，参考添加规则的说明



### 代码地址

+ 所有的代码可以在**[这里](https://github.com/rawbin-/ykit-demos)**找到。

### 参考资料

1. [YKit mock 插件源码](https://github.com/roscoe054/ykit-config-mock)
2. [Mockjs github](https://github.com/nuysoft/Mock)
3. [Mockjs 文档](http://mockjs.com/)
4. [Jerry Proxy](https://github.com/Ellery0924/Jerry)

