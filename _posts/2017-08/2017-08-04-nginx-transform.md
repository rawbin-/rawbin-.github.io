---
layout: post
title: "nginx 路径转发配置小结"
categories: [Web开发,前端开发,开发环境]
tags: [nginx,反向代理,静态服务器]
---

## 基本的转发规则
### location 
#### 匹配优先级(更明确的结果优先)  [参考](http://nginx.org/en/docs/http/ngx_http_core_module.html#location)
- 先处理直接（前缀）匹配的内容，匹配长度最长的优先作为目标配置备选
- 如果直接（前缀）匹配的内容 使用了 ^~ 或者 =,就直接使用匹配的配置作为目标配置
- 再处理正则匹配的内容，按在配置文件中出现顺序匹配，找到第一个匹配的配置是目标配置
- 正则没找到就用前缀匹配的配置作为目标配置

#### 匹配规则
- /xxx/ 会正常匹配，/xxx 会返回301到/xxx/(注意)

### alias [参考](http://nginx.org/en/docs/http/ngx_http_core_module.html#alias)
- 用于在URL和文件系统路径之间实现映射

- 无默认值

- 使用环境 location

  ​
```
location /i/{
    alias /path/to/images/
}

# 不能用于含有正则表达式的location，可以使用rewrite和root组合实现
# /i/top.gif => /path/to/images/top.gif

```

### root [参考](http://nginx.org/en/docs/http/ngx_http_core_module.html#root)
- 用来指定请求的文档根目录

- 默认值 root html

- 使用环境 http ,server ,location, if in location

  ​
```
locateion /i/{
    alias /path/to/images/
}

# /i/top.gif => /path/to/images/i/top.gif

```

### rewrite [参考](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html#rewrite)
- 根据表达式来重定向URI，或者修改字符串
- 重写字符串只对相对路径有效，如果想匹配主机名，需要使用if
- rewrite 按照在配置文件中的出现顺序执行
- 以https://, http://, $scheme 开头的重写结果将直接返回给客户端
    - last 终止处理当前的rewrite块，开始匹配新的块
    - break 终止处理当前的rewrite块
    - redirect 返回302， 在不以上面几个绝对地址头返回的情况
    - permanent 返回301 
 - 重写参数默认会拼在替换的url后面，如果不要的话，在替换的后面加上?   


### if 条件 [参考](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html#if)
- 变量名 false值为 空字符串或者以0开始的字符串
- 变量比较使用 = 或者 !=
- ~* 和 ~ 表示正则表达式匹配，~*表示不区分大小写，~表示不区分大小写
- !~* 和 !~与上面两个刚好相反，表示不匹配
- -f 和 !-f 用来判断文件是否存在
- -d 和 !-d 用来判断目录是否存在
- -e 和 !-e 用来判断文件或目录是否存在
- -x 和 !-x 用来判断文件是否可执行
- 正则在()分组内的值可以通过$1-$99取到


## 一些已知的坑（敲黑板，注意事项）
- rewrite 规则执行后，还会从头走一遍匹配规则（当做新请求进来吧），一不小心就写成死循环了
- proxy_pass 后面不带/会拼上location匹配的路径，带上/会干掉匹配的路径
- alias 后面不带/不表示目录，会直接拼上
- root 后面带不带/都表示目录
- URL重写的时候默认会带上查询字符串，最终URL带上？表示不拼参数 