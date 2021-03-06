---
layout: post
title: "nginx 路径转发、反向代理配置小结"
categories: [Web开发,前端开发,开发环境]
tags: [nginx,反向代理,静态服务器]
---

## 1 基本的转发规则
### 1.1 location 
- 匹配之前先做uri的标准化，解码，解析相对路径和可能的压缩，以及多个斜杠处理为单斜杠
- 可以用正则匹配或者前缀匹配，正则匹配用`~`和`~*`表示大小写敏感和不敏感
- 先检查前缀匹配的，选出最长的，再检查正则的，取第一个匹配的，如果没有正则匹配的就用前缀的
#### 1.1.1 匹配优先级(更明确的结果优先)  [参考](http://nginx.org/en/docs/http/ngx_http_core_module.html#location)
- 先处理直接（前缀）匹配的内容，匹配长度最长的优先作为目标配置备选
- 如果直接（前缀）匹配的内容 使用了 ^~ 或者 =,就直接使用匹配的配置作为目标配置
- 再处理正则匹配的内容，按在配置文件中出现顺序匹配，找到第一个匹配的配置是目标配置
- 正则没找到就用前缀匹配的配置作为目标配置

#### 1.1.2 匹配规则
- `=`  严格匹配，匹配上就终止搜索
- `^~` 前缀匹配，优先级高于正则匹配
- `~` 正则匹配，区分大小写匹配
- `~*`正则匹配， 不区分大小写匹配  
- /uri 前缀匹配，优先级在正则之后
- /通用匹配
- 带`@`前缀的是一个具名`location`的定义，被用来作为请求重定向，不能嵌套，也不能包含嵌套的`location`，不能用正则
- 反向代理的时候，`/xxx/` 会正常匹配，`/xxx` 会301重定向到`/xxx/`，如果不想这样，需要定义一个严格匹配来处理

### 1.2 alias [参考](http://nginx.org/en/docs/http/ngx_http_core_module.html#alias)
- 用于在URL和文件系统路径之间实现映射

- 使用环境 location

- 别名路径的值可以是除了`$document_root`和`$realpath_root`中的

- 如果`location`指定了正则表达式，正则表达式需要有捕获组，同时`alias`中需要有对捕获组的引用

- 如果`location`匹配了别名路径的最后一段，最好用`root`来替代`alias`

```
location /i/{  	# 不能用于含有正则表达式的location，可以使用rewrite和root组合实现
    alias /path/to/images/; #/i/top.gif => /path/to/images/top.gif
}

```

### 1.3 root [参考](http://nginx.org/en/docs/http/ngx_http_core_module.html#root)
- 用来指定请求的文档根目录

- 默认值 root html

- 使用环境 http ,server ,location, if in location

- 别名路径的值可以是除了`$document_root`和`$realpath_root`中的

- `root`只是直接将请求路径加到根路径上，如果有改动需要用`alias`

  ​
```
locateion /i/{
    root /path/to/images/; #/i/top.gif => /path/to/images/i/top.gif
}
```

### 1.4 rewrite [参考](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html#rewrite)
- 根据表达式来重定向URI，或者修改字符串
- 重写字符串只对相对路径有效，如果想匹配主机名，需要使用if
- `rewrite` 按照在配置文件中的出现顺序执行
- 以`https://`,` http://`,` $scheme` 开头的重写结果将直接返回给客户端
    - last 终止处理当前的`rewrite`相关指令，会对`rewrite`所在的`server`重新发起请求（容易导致死循环，所以一般在根location中或者直接在server中写rewrite推荐用last，非跟location使用break），浏览器地址不变；使用alias指令时必须使用last
    - break 终止处理当前的rewrite块，不再匹配后面的规则，浏览器地址不变；使用proxy_pass指令时要用break;在`location`中的设置的`break`,location中后续的请求处理还会继续
    - redirect 临时重定向返回302， 浏览器地址会显示跳转后的URL，爬虫不会更新URL，在重写URL不是以`http://`,`https://`,`$scheme`开头的情况下使用。
    - permanent 永久重定向返回301，浏览器地址会显示跳转后的URL，爬虫会更新URL
 - 重写参数默认会拼在替换的url后面，如果不要的话，在替换的后面加上?   
 - 正则如果带有大括号，需要将这个匹配规则放入单引号或者双引号中


### 1.5 if 条件 [参考](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html#if)
- 变量名 false值为 空字符串或者以0开始的字符串
- 变量比较使用 = 或者 !=
- ~* 和 ~ 表示正则表达式匹配，~*表示不区分大小写，~表示不区分大小写
- !~* 和 !~与上面两个刚好相反，表示不匹配
- -f 和 !-f 用来判断文件是否存在
- -d 和 !-d 用来判断目录是否存在
- -e 和 !-e 用来判断文件或目录是否存在
- -x 和 !-x 用来判断文件是否可执行
- 正则在()分组内的值可以通过$1-$99取到


### 1.6 try_files
- 路径从root 和 alias指令指定的地方找，
- 加一个/在后面的话可以找目录
- 前面都找不到就内部重定向到最后一个指定的uri
- 最后一个uri可以是一个命名的 @xxx的uri，也可以是 =404 这样的code


### 1.7 一些已知的坑（敲黑板，注意事项）
- rewrite 规则执行后，还会从头走一遍匹配规则（当做新请求进来吧），一不小心就写成死循环了
- proxy_pass 后面不带/会拼上location匹配的路径，带上/会干掉匹配的路径
- alias 后面不带/不表示目录，会直接拼上
- root 后面带不带/都表示目录
- rewrite URL重写的时候默认会带上查询字符串，最终URL带上？表示不拼参数 

## 2 反向代理
###  反向代理 proxy_pass 出现301的问题

- 需求 将 `/apiprefix/` 反向代理到 `/` 下面

  ```
  ## nginx.conf
  location /apiprefix/ {
      proxy_pass http://127.0.0.1/;
  } 
  ```

- 现象

  - 浏览器出现访问了`/apiprefix/path/to/api`然后变为`/path/to/api`
  - nginx日志里面出现 `/apiprefix/path/to/api`  301，`/path/to/api` 301 的日志

- 解决办法

  ```
  ## nginx.conf
  location /apiprefix {
      proxy_pass http://127.0.0.1/;
      proxy_set_header Host $http_host;
  } 
  ```

- 原因分析

  - `proxy_set_header` 默认值为 `$proxy_host`





### 参考资源

0. [nginx docs proxy_set_header](https://nginx.org/en/docs/http/ngx_http_proxy_module.html?&_ga=1.74997266.187384914.1443061481#proxy_set_header)
1. [proxy_pass反向代理配置中url后面加不加/的说明](https://www.cnblogs.com/kevingrace/p/6566119.html)
2. [nginx reverse proxy odd 301 redirect](https://forum.nginx.org/read.php?2,243458,243466#msg-243466)
3. [Nginx reverse proxy with 301 redirect under https](https://stackoverflow.com/questions/39434674/nginx-reverse-proxy-with-301-redirect-under-https)
4. [nginx负载均衡（5种方式）、rewrite重写规则及多server反代配置梳理](https://www.cnblogs.com/kevingrace/p/6398488.html)
5. [Nginx 日志、Rewrite及403报错信息处理等](https://blog.csdn.net/mr_rsq/article/details/79632034)