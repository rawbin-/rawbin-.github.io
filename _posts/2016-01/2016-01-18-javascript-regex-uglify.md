---
layout: post
title: "JavaScript正则表达式应用 移除注释"
categories: [前端开发,Web开发,JavaScript]
tags: [JavaScript,正则表达式,移除注释]
---

### 1 使用JavaScript正则表达式匹配注释

#### 1.1 匹配velocity注释

+ 单行注释 `/##[^\r\n]*/g`
+ 多行注释 `/#*[\s\S]*?*#/g`

#### 1.2 匹配HTML注释

+ 匹配HTML注释保护IE条件注释 `/<!--(?!\s*\[if)[\s\S]*?-->/g`

#### 1.3 匹配CSS

+ 匹配CSS 标签 `/<(style)[\w\W]*?\1/g`


+ 匹配CSS 注释 `/\/*[\s\S]*?*\//g`

  #### 匹配JavaScript

+ 匹配JavaScript标签 `/<(script)[\w\W]*?\1>/g`

+ 匹配单行注释 `/(^|\b|\s|[^'":\w\d])(\/\/[^\r\n]*)/g`

+ 匹配多行注释 `/\/*[\s\S]*?*\//g`

  ​

### 2 参考资料

1. [JavaScript正则表达式及其应用](http://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/%E5%BC%80%E5%8F%91%E6%8A%80%E6%9C%AF/javascript/2015/08/03/javascript-regex/)
1. [Finding Comments in Source Code Using Regular Expressions](http://blog.ostermiller.org/find-comment)
2. [Finding Comments in HTML Source Code Using Regular Expressions](http://blog.ostermiller.org/find-comments-html)
3. [正则表达式匹配多行注释](http://nasta.is-programmer.com/posts/41249.html?utm_source=tuicool&utm_medium=referral)
4. [正则表达式匹配多行注释](http://www.tuicool.com/articles/rqEBb2)
5. [【正则表达式 去掉注释】_正则表达式 去掉注释的相关文章，教程，源码](http://www.xuebuyuan.com/zt/28388153.html)
6. [IE条件注释详解](http://www.admin10000.com/Document/21.html)