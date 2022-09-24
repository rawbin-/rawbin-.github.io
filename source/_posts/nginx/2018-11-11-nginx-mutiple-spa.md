---
layout: post
title: "nginx SPA 多环境和多项目配置参考"
categories: [前端开发,Web开发,服务配置]
tags: [nginx,history mode,spa,前后端分离]
---


# 需求场景
## 线上环境配置
- 线上环境一个项目一个配置，也就是行业各种推荐的nginx配置，如下
- 这样有M个项目就写M个配置就OK，频次不高
```
    location /projectName/ {
      root path/to/projectName;
      index index.html;
      try_files $uri $uri/ /projectName/index.html;
    }
```

## 测试环境配置
- 测试环境不太一样，只有一台机器
- 在这一台机器上面通过目录的方式划分出N个环境（也就是对应N个目录）
- 在每一套环境上面，分出M个项目来，同一个项目可能再不同时间会部署到不同的环境（比如原来的环境被占用了）
- 测试环境需要N*M个配置来处理类似的需求，这样就有点蛋疼了，尤其是在nginx配置上面

# 解决方案
## 诉求
- 根据前面的场景，针对每个环境每个项目去写对应的配置，已经不太现实
- 我们希望写一套配置针对一个环境的多个项目通用，不用重复配置
- 最好是一套配置将不同环境的多个项目都搞定这样就更舒心了

## 解法
- 经探索，搞出来一套配置
```
    location ~* ^/(env1|env2|envx|envtest)\/(\w+)/ {
        root /usr/local/var/www;
        index index.html index.htm; # 这个地方不写分号在我的nginx版本不报错
        try_files $uri $uri/ /$1/$2/index.html;
    }
```
## 适用场景
- 上面这套配置，适用于如下的目录结构

```
-----env1
-----------projectA
-----------------------index.html
-----------------------static
------------------------------css
------------------------------js
-----------projectB
-----------------------index.html
-----------------------static
------------------------------css
------------------------------js
-----env2
-----------projectA
-----------------------index.html
-----------------------static
------------------------------css
------------------------------js
-----------projectB
-----------------------index.html
-----------------------static
------------------------------css
------------------------------js
#....此处省略更多.....

```

# 相关操作
## Nginx相关配置变更
- 这个上面已经讲过啦

## SPA 项目配置调整
- 如果使用的是hash模式，本身相对简单直接能兼容
- 如果是history模式的话，需要关注路由在nginx找不到的问题，需要重定向到对应的index.html就行，上面nginx部分已经做了
- 本地dev-server也有找不到路径的问题
- 项目部分还需要做一些配置，以适配目录的部署，也就是history的baseName，下面是一个相对通用的解决
  

React Router  或者 Vue Router使用如下方式进行获取  

```
const getBasePath = () => {
  // 任意目录部署需要指定容器目录 `xxx/index.html`
  const parentDirectory = 'xxx/'
  const path = location.pathname
  const identifierIndex = path.lastIndexOf(parentDirectory)
  // 这里可以自定义处理规则
  if (identifierIndex === -1) {
    // 比如这里兼容原有的直接部署到环境根目录的情况
    const secondSlashIndex = path.indexOf('/', 1)
    if (secondSlashIndex !== -1) { // 存在 `/aa/` `/aa/bb`等情况，那就用一级目录
      return path.slice(0, secondSlashIndex + 1)
    } else {
      return path // 不存在上面情况就只有一级目录或者直接根目录，那就直接用
    }
  }
  return path.slice(0, identifierIndex + parentDirectory.length)
}

```


## jenkins配置调整
- 基本不需要大调整
- 但如果是使用了 Publish over SSH，这里参数化有一些问题
- 我这里遇到的就是参数化不生效，直接变量给到的没有被支持，看起来代码是被先解析了一道，同时字符串截取也没生效

```
# 代码写成这样的
hostEnvValue=${hostEnv%-*}
echo '$hostEnv='$hostEnv
echo '$hostEnvValue='$hostEnvValue
echo '$hostEnvValue='${hostEnv%-*}

# 控制台输出的代码是这样的
hostEnvValue=${hostEnv%-*}
echo 'envx-justtest='envx-justtest
echo '$hostEnvValue='$hostEnvValue
echo '$hostEnvValue='${hostEnv%-*}

# 控制台输出的内容是这样的
envx-justtest=envx-justtest
$hostEnvValue=
$hostEnvValue=
```
简单用一个变量周转一下就好了
```
# 代码写成这样
tmpHostEnv=$hostEnv
hostEnvValue=${tmpHostEnv%-*}
echo $tmpHostEnv
echo $hostEnvValue
echo '$hostEnv='$hostEnv
echo '$tmpHostEnv='$tmpHostEnv
echo '$hostEnvValue='$hostEnvValue
echo '$hostEnvValue='${hostEnv%-*}

# 控制台输出的代码
tmpHostEnv=envx-justtest
hostEnvValue=${tmpHostEnv%-*}
echo $tmpHostEnv
echo $hostEnvValue
echo 'envx-justtest='envx-justtest
echo '$tmpHostEnv='$tmpHostEnv
echo '$hostEnvValue='$hostEnvValue
echo '$hostEnvValue='${hostEnv%-*}

# 控制台输出
envx-justtest
envx
envx-justtest=envx-justtest
$tmpHostEnv=envx-justtest
$hostEnvValue=envx
$hostEnvValue=
```
这样一切就正常了

# 相关参考
## 调试nginx的方法
- 有时候规则多了，不知道到底匹配哪一个，
- 比如一方面涉及到规则优先级，另一方面又涉及到正则本身太强大不容易控制

### 可以直接在规则里面写return 

```
    location ~* ^/(env1|env2|envx|envtest)\/(\w+)/ {
        root /usr/local/var/www;
        index index.html index.htm; # 这个地方不写分号在我的nginx版本不报错
        return 200 "这个是xxx规则";
        try_files $uri $uri/ /$1/$2/index.html;
    }
```

### 安装echo模块并echo，简单理解为console
- 这个最好先把配置的响应类型改一下，不用流，不然直接就下载了，有点讨厌

```
    default_type text/plain; # 这一行放到http合适的地方

    location ~* ^/(env1|env2|envx|envtest)\/(\w+)/ {
        root /usr/local/var/www;
        index index.html index.htm; # 这个地方不写分号在我的nginx版本不报错
        echo -n  $1;
        echo $2
        try_files $uri $uri/ /$1/$2/index.html;
    }
```

### 直接开启nginx的调试模式
- 如果说上面的是小打小闹，这个就是放大招
- 首先要安装或者配置可调试的nginx
    - 服务器上直接源码编译开启调试选项就ok
    - MacOS上可以简单用brew实现
- 在离目标最近的合适位置，开启调试

```
error_log logs/error.log debug;
```

- 开启调试模式后，可以看到如下日志,瞬间瞎了的眼都亮了

```
2020/12/30 18:35:00 [debug] 98824#0: *36 rewrite phase: 1
2020/12/30 18:35:00 [debug] 98824#0: *36 test location: "/"
2020/12/30 18:35:00 [debug] 98824#0: *36 test location: "manage/"
2020/12/30 18:35:00 [debug] 98824#0: *36 test location: "admin/"
2020/12/30 18:35:00 [debug] 98824#0: *36 test location: "50x.html"
2020/12/30 18:35:00 [debug] 98824#0: *36 test location: ~ "^/(271|272|envx|envtest)/"
2020/12/30 18:35:00 [debug] 98824#0: *36 using configuration "^/(271|272|envx|envtest)/"
2020/12/30 18:35:00 [debug] 98824#0: *36 http cl:-1 max:1048576
2020/12/30 18:35:00 [debug] 98824#0: *36 rewrite phase: 3
2020/12/30 18:35:00 [debug] 98824#0: *36 post rewrite phase: 4
2020/12/30 18:35:00 [debug] 98824#0: *36 generic phase: 5
2020/12/30 18:35:00 [debug] 98824#0: *36 generic phase: 6
2020/12/30 18:35:00 [debug] 98824#0: *36 generic phase: 7
2020/12/30 18:35:00 [debug] 98824#0: *36 access phase: 8
2020/12/30 18:35:00 [debug] 98824#0: *36 access phase: 9
2020/12/30 18:35:00 [debug] 98824#0: *36 post access phase: 10
2020/12/30 18:35:00 [debug] 98824#0: *36 generic phase: 11
2020/12/30 18:35:00 [debug] 98824#0: *36 try files handler
2020/12/30 18:35:00 [debug] 98824#0: *36 http script var: "/271/index.html"
2020/12/30 18:35:00 [debug] 98824#0: *36 trying to use file: "/271/index.html" "/usr/local/var/www/271/index.html"
2020/12/30 18:35:00 [debug] 98824#0: *36 http script var: "/271/index.html"
2020/12/30 18:35:00 [debug] 98824#0: *36 trying to use dir: "/271/index.html" "/usr/local/var/www/271/index.html"
2020/12/30 18:35:00 [debug] 98824#0: *36 http script copy: "/"
2020/12/30 18:35:00 [debug] 98824#0: *36 http script capture: "271"
2020/12/30 18:35:00 [debug] 98824#0: *36 http script copy: "/index.html"
2020/12/30 18:35:00 [debug] 98824#0: *36 trying to use file: "/271/index.html" "/usr/local/var/www/271/index.html"
2020/12/30 18:35:00 [debug] 98824#0: *36 internal redirect: "/271/index.html?"
2020/12/30 18:35:00 [debug] 98824#0: *36 rewrite phase: 1
2020/12/30 18:35:00 [debug] 98824#0: *36 test location: "/"
2020/12/30 18:35:00 [debug] 98824#0: *36 test location: "manage/"
2020/12/30 18:35:00 [debug] 98824#0: *36 test location: "admin/"
2020/12/30 18:35:00 [debug] 98824#0: *36 test location: "50x.html"
2020/12/30 18:35:00 [debug] 98824#0: *36 test location: ~ "^/(271|272|envx|envtest)/"
2020/12/30 18:35:00 [debug] 98824#0: *36 using configuration "^/(271|272|envx|envtest)/"
```


## MacOS开启nginx调试
- 首先一定要安装`nginx-full` 才可以定制，`nginx`这个包我没玩起来过
- 执行如下命令等操作完成，就可以玩前面的配置和验证了

```
brew install nginx-full --with-echo-module --with-debug ## 第一次~

brew reinstall nginx-full --with-echo-module --with-debug ## 老司机专用

```



# Content-length 为空 反向代理无效的问题

```
# proxy_set_header Host $proxy_host;
# proxy_set_header Host $http_host;
proxy_set_header Host $proxy_host;
```

http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_set_header

上面两条都可能出现反向代理返回为空的情况，如果HOST设置为了其他的，可能走了CORS的配置，然后给了个OPTIONS的结果body全为空，可以调整为上面的配置，或者删掉配置

```
curl 'http://mytest.bybit.com:8080/path/to/your/api' \
  -H 'Connection: keep-alive' \
  -H 'Pragma: no-cache' \
  -H 'Cache-Control: no-cache' \
  -H 'Authorization: ' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36' \
  -H 'Accept: */*' \
  -H 'Referer: http://mytest.bybit.com:8000/pressure/plan/PressPlan?planId=13' \
  -H 'Accept-Language: zh-CN,zh;q=0.9' \
  -H 'Cookie:' \
  --compressed \
  --insecure
  
HTTP/1.1 200 OK
Server: nginx/1.19.10
Date: Mon, 17 Jan 2022 08:42:28 GMT
Content-Type: application/octet-stream
Content-Length: 0
Connection: keep-alive

```



https://www.cnblogs.com/Qooo/p/13853465.html

https://blog.51cto.com/u_15127669/4002499

https://blog.csdn.net/yihanzhi/article/details/107002881

https://cuiqingcai.com/8443.html

https://www.cnblogs.com/faberbeta/p/nginx008.html

https://www.cnblogs.com/Qooo/p/13853465.html