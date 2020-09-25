---
layout: post
title: "Shell字符串处理"
categories: [前端开发,Web开发]
tags: [Shell,字符串处理]
---



### 参数替换

#### `${param}`

- 避免参数后面的字符导致的冲突
- 访问`>=10` 的位置参数



#### `${string:position}`

- 从 `position`的位置开始截取



#### `${string:position:length}`

- 从`position`的位置开始截取长度为`length`的

 

#### `${string#substring}`

- 从前往后，截取最短匹配`substring`的



#### `${string##substrng}`

-  从前往后，截取最长匹配`substring`的



#### `${string%substring}`

- 从后往前，截取最短匹配`substring`的



#### `${string%%substrng}`

-  从后往前，截取最长匹配`substring`的



#### `${string/substring/replacement}`

- 替换第一个匹配`substring`的为`replacement`



#### `${string//substring/replacement}`

- 替换所有匹配`substring`的为`replacement`



#### `${string/#substring/replacement}`

- 如果`substring`能匹配首部，就替换成`replacement`



#### `${string/%substring/replacement}`

- 如果`substring`能匹配尾部，就替换成`replacement`



### 案例解析

```
#!/usr/bin/env bash

hello="this is a hello from code ode"

## 变量的长度
echo ${#hello}

## 从截取从10开始的子串
echo ${hello:10}

## 从10开始截取长度为5的子串
echo ${hello:10:5}

## 从前往后截掉最短的*is的匹配
echo ${hello#*is}

## 从前往后截掉最长的*is的匹配
echo ${hello##*is}

## 从后往前截掉最短的ode*的匹配
echo ${hello%ode*}

## 从后往前截掉最长的ode*的匹配
echo ${hello%%ode*}

## 把第一个is换成are
echo ${hello/is/are}

## 把所有is换成are
echo ${hello//is/are}

## 从前往后匹配this的话就替换成THIS
echo ${hello/#this/THIS}

## 从后往前匹配ode的话就替换成ODE
echo ${hello/%ode/ODE}

```



```
#!/usr/bin/env bash

## 处理逗号分隔的字符串
## 把内容复制一遍并输出

## 把第一个参数中的所有逗号替换成空格，便于后面的循环
arg_info=${1//,/ }

result=""
## 
for arg in $arg_info
do
result="$result,$arg$arg"
done

## 去掉第一个逗号并输出
echo ${result#?}
```

