---
layout: post
title: "FEKit Mock 简明参考手册"
categories: [Web开发,前端开发,前端工程化,开发工具]
tags: [FEKit,Mock,模拟数据,测试数据]
---



[TOC]

### FEKit Mock 配置

#### FEKit Mock 启动方式

+ FEkit Mock是`fekit server`的一个额外功能
+ 在工程目录的上一级目录启动
+ `fekit server -m ./projectA/path/to/mockconf.js` 启动mock server。	
+ 可以通过`fekit server -h` 查看相应的帮助，`-m`参数跟`--mock`一致

#### FEKit Mock配置文件

+ 配置文件后缀可以自己定义，比如常见的`.conf` 或者`.js`，推荐使用`.js`这样在各大编辑器里面能得到更好的高亮支持。
+ 配置规则支持两种方式
  + `key`=>`value`形式, 其中`key`为路径的匹配规则，`value`为对应的mock数据指向；
  + `pattern`=>`respondwith`形式，其中`pattern`为路径匹配规则,`respondwith`为对应的mock数据指向；
+ 配置的规则（`key`或`pattern`）支持全匹配和正则匹配，mock数据指向支持`.json`,`.mockjson`,`.js`以及带`http`开头的全URL接口数据代理，规则文件的相对路径是相对于mock配置的路径。
+ 规则从前至后匹配，但`key`=> `value`形式的规则顺序无法保证（但优先级始终低于rules规则），如果有交叉规则需要通过顺序来控制，建议用rules形式的配置。
+ 配置文件会被解析为CommonJS模块，同时支持更详尽的`rules`规则配置，所以一个配置文件内容大致如下:

```javascript
module.exports = {
  '/uri/to/api':'relative/path/to/mock/data/file',
  rules:[{
    pattern: "/uri/pattern/to/match",
    respondwith:'relative/path/to/mock/data/file',
    jsonp:'jsCallback'
  }]
}
```

#### FEKit Mock支持的Mock数据类型及配置方式

这里我们通过不同的支持方式来模拟一个list接口(`/api/list.json `)的数据(`listData.json`)，数据如下:

```json
{
  "ret":true,
  "data":[{
    "name":"Li Lei",
    "email":"lilei@test.com",
    "registerDateTime": "2020-10-01 22:11:11"
  },{
    "name":"Han Meimei",
    "email":"hanmeimei@test.com",
    "registerDateTime": "2020-10-01 22:11:11"
  }]
}
```

##### 对于URL（`/api/list.json`）的匹配规则的配置

**以下四种任意一种配置方式都可以匹配到这个规则：**

```javascript
module.exports = {
    '/api/list.json':'./data/listData.json',
    '^\\/api\\/\\w+\\.json.*^^^':'./data/listData.json',
    rules:[{
        pattern:'/api/list.json',
        respondwith:'./data/listData.json'
    },{
        pattern:/^\/api\/\w+\.json.*$/,
        respondwith:'./data/listData.json'
    }]
}
```

##### jsonp规则的配置

+ 在mock的规则中需要处理jsonp的配置默认为`callback`，可以自定义
+ 在请求的url中需要增加相关的参数`callback`,这两者需要一致

```javascript
module.exports = {
    rules:[{
        pattern:'/api/list.json',
        respondwith:'./data/listData.json',
        jsonp:'thecallback'
    },{
        pattern:/^\/api\/\w+\.json.*$/,
        respondwith:'./data/listData.json',
        jsonp:'thecallback',
    },{
        pattern:/^\/api\/\w+\.json.*$/,
        respondwith:function(req,res,context){
            //这里可以有更多其他的处理过程
            var callbackKey = 'thecallback'
            var dataStr = JSON.stringify({
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
            });
            var jsonpCallback = req.query[callbackKey];
            if(jsonpCallback){
                res.end([jsonpCallback,'(',dataStr,')'].join(''))
            }else{
                res.end(dataStr)
            }
        }
    }]
}
```





##### 对于数据匹配规则的配置

###### json 规则

直接返回原数据文件(`listData.json`)内容

###### mockjson 规则

参考[mockJSON github](https://github.com/mennovanslooten/mockJSON),网站挂掉了，直接`clone`代码看文档，这个可以更加灵活的配置数据模板，根据模板动态生成随机数据，比如我们这里的规则（`listData.mockjson`）如下:

```
{
  "ret|0-1":true,
  "data|2-5":[{
    "name":"@LAST_NAME @MALE_FIRST_NAME",
    "email":"@EMAIL",
    "registerDateTime": "@DATE_YYYY-@DATE_MM-@DATE_DD @TIME_HH:@TIME_MM:@TIME_SS"
  },{
    "name":"@LAST_NAME @FEMALE_FIRST_NAME",
    "email":"@EMAIL",
    "registerDateTime": "@DATE_YYYY-@DATE_MM-@DATE_DD @TIME_HH:@TIME_MM:@TIME_SS"
  }]
}
```

###### js规则

直接控制`response`对象传入数据，这里可以有更灵活的处理

```
module.exports = function (req, res, context) {
    //这里可以有更多其他的处理过程
    res.end(JSON.stringify({
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
    }))
}
```

###### http 数据转发规则

通过直接获取远端接口数据并将数据作为mock数据返回，这里只支持http，不支持https，因为没有考虑相关的证书输入。

**数据的匹配规则如下:**

```JavaScript
module.exports = {
    '/api/list.json':'./data/listData.json',
    '/api/list.json':'./data/listData.mockjson',
    '/api/list.json':'./data/listData.js',
    '/api/list.json':'http://testdomain.com.cn/resp/listapi.json',
    rules: [{
        pattern:'/api/list.json',
        respondwith:'./data/listData.json'
    }, {
        pattern:'/api/list.json',
        respondwith:'./data/listData.mockjson'
    }, {
        pattern:'/api/list.json',
        respondwith:'./data/listData.js'
    }, {
        pattern:'/api/list.json',
        respondwith:'http://testdomain.com.cn/resp/listapi.json'
    },{
        pattern: '/api/list.json',
        respondwith: function (req, res, context) {
            //这里可以有更多其他的处理过程
            res.end(JSON.stringify({
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
            }))
        }
    }]
}
```



### FEKit Proxy 代理配置

+ 配置文件在~/fekit.hosts，使用`fekit server -o `启动代理，会有相应的提示

+ 启动端口在10180，将浏览器的代理设置到这个端口上面就可以玩起来了，用这里面的host和转发规则

+ 配置的内容参考

  ```
  # 简单的host配置
  127.0.0.1 test.test.com
   
  # 请求转发的配置
  proxy_pass http://q.qunarzz.com/(.*) http://127.0.0.1:7778/$1
  ```

  ​

  ### 代码地址


+ 相关的代码可以在[这里](https://github.com/rawbin-/fekit-demos)找到



### 参考资料

---
0. [FEKit Mock 文档](https://github.com/rinh/fekit/tree/master/docs/mock)
1. [FEKit 使用教程](http://ued.qunar.com/fekit/usage.html)
2. [FEKit Mock源码](https://github.com/rinh/fekit/blob/master/lib/middleware/mock.js)

