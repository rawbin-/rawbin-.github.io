---
layout: post
title: "前端页面静态资源缓存和不缓存填坑实战"
categories: [前端开发,Web开发,前端框架]
tags: [缓存,浏览器,HTTP]
---





## 缓存控制关键点

### HTTP协议头

#### HTTP 1.0 协议头

- Expires  绝对时间点，0或者无效时间建议解释为立即过期
- If-Modified-Since 绝对时间点，GET请求中的条件过期，如果有满足条件的变更则返回新资源，否则返回304
- Last-Modified 绝对时间点，上一次数据变更的时间点
- Pragma 'no-cache'，扩展字段为`key=value`的形式

#### HTTP 1.1协议头
- Cache-Control
	- public  任何缓存都能存
	- private 共享缓存不能存
	- no-cache 任何缓存都不能存
	- no-store 不能存相关数据
	- max-age=delta-seconds 保质期
- ETag 实体标签名称，W/前缀表示弱校验
- Expires 过期时间点
- If-Modified-Since 绝对时间点，GET请求中的条件过期，如果有满足条件的变更则返回新资源，否则返回304
- If-Unmodified-Sice 绝对时间点
- If-Match 逗号分隔的多个EntityTag或者*
- If-None-Match 逗号分隔的多个EntityTag或者*
- Last-Modified 绝对时间点，上一次数据变更的时间点
- Pragma  'no-cache'，扩展字段为`key=value`的形式

### HTTP缓存机制
#### 过期机制 减少网络交互次数
- 优先级 
	- Cache-Control:max-age > Expires，都没有的话由实现端来定
	- 多个过期时间，Date字段最新的优先

#### 验证机制 减少网络带宽占用
- Last-Modified Dates 之后没改就不用传
- Entity Tag，时间精度要求高，不便于用修改时间或者用时间有问题的场景
- 强验证和弱验证，有点变化就要更新的算强验证
- Entity Tag本身为强验证，可以设置为弱验证
- HTTP/1.1服务器 推荐的是设置一个强EntityTag 和一个Last-Modified值
- HTTP/1.1客户端
	- 有EntityTag就必须用If-Match 或者 If-None-Match去校验
	- 只有Last-Modified就应该用If-Modified-Since
	- 都有的话都要	 	










### 参考资料

0. [实践这一次，彻底搞懂浏览器缓存](https://juejin.im/post/5c4528a6f265da611a4822cc?utm_source=gold_browser_extension)
1. [RFC 搜 HTTP](https://www.rfc-editor.org/search/rfc_search_detail.php?page=All&title=HTTP&pubstatus[]=Any&pub_date_type=any&sortkey=Number&sorting=ASC)
2. [RFC 1945 HTTP 1.0](https://www.rfc-editor.org/info/rfc1945)
3. [RFC 2068 HTTP 1.1](https://www.rfc-editor.org/info/rfc2068)
4. [RFC 2616 HTTP 1.1](https://www.rfc-editor.org/info/rfc2616)
5. [RFC 7234 HTTP Caching](https://www.rfc-editor.org/info/rfc7234)
6. [RFC 7540 HTTP 2](https://www.rfc-editor.org/info/rfc7540)
