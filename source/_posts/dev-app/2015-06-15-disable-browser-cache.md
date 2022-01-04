---
layout: post
title: "浏览器禁用缓存配置"
cagegories: [开发技术]
tags: [浏览器,禁用缓存]

---

## 1 绕过浏览器缓存
强制更新，可按Ctrl+F5刷新页面。参考[绕过浏览器缓存](https://zh.wikipedia.org/zh-hans/Help:%E7%BB%95%E8%BF%87%E6%B5%8F%E8%A7%88%E5%99%A8%E7%BC%93%E5%AD%98)

## 2 Chrome

### 2.1 HTTP缓存
F12 打开开发者工具，点开小齿轮，有一个"Disable Cache"

### 2.2 DNS缓存 
在地址栏输入: chrome://net-internals/#dns 点击Clear


## 3 Firefox

### 3.1 开发者工具
按F12 打开Firebug，切换到“网络”标签，打开旁边的小三角，选中“禁用浏览器缓存”

### 3.2 Web开发者
在Firefox的“工具”菜单，找到“Web开发者”，然后打开“Web控制台”，点开小齿轮，将设置中的“禁用缓存”选中。

### 3.3 Firefox配置
在浏览器地址栏中输入: about:config

#### 3.3.1 HTTP缓存
+ 搜索 browser.cache.disk.enable  将值设为false （双击即可）
+ 搜索 network.http.use-cache 将值设为false
+ 搜索 browser.cache.check_doc_frequency 将值改为 1，[参考](http://www.imasuper.com/2008/10/07/firefox-caching-get-latest-page-every-time/)

#### 3.3.2 DNS缓存
+ 搜索 network.dnsCacheExpiration 将值设为0
+ 搜索 network.dnsCacheEntries 将值设为0

### 3.4 清除缓存
在工具->选项->隐私中进行设置和操作。 [参考](https://support.mozilla.org/zh-CN/kb/%E6%80%8E%E4%B9%88%E6%B8%85%E9%99%A4%E7%BC%93%E5%AD%98%EF%BC%9F)

### 3.5 查看缓存
在地址栏中输入: about:cache 

## 4 Opera
点击网络标签清除缓存，选择“网络选项”-> “禁用所有缓存”

## 5 Internet Explorer
在Internet 选项中，找浏览历史记录中的设置，将检查存储的页面的较新版本选项改为“每次访问网页时”

## 6 参考
0. [Chrome 禁用缓存图文](http://jingyan.baidu.com/article/574c5219cbedec6c8d9dc1bd.html)
1. [浏览器开发工具的秘密](http://jinlong.github.io/2013/08/29/devtoolsecrets/)
2. [在Firefox中关闭缓存](http://blog.csdn.net/violet_day/article/details/18734421)
3. [13种方法来清除浏览器缓存](http://zh.wikihow.com/%E6%B8%85%E9%99%A4%E6%B5%8F%E8%A7%88%E5%99%A8%E7%BC%93%E5%AD%98)
4. [Firefox每次检查网页更新的设置](http://www.bizeway.net/read.php?429)
5. [Setting your Browser Cache](https://www.udel.edu/sakai/help/content/current/sakai_iframe_myworkspace/browser_cache.html)
6. [禁用Firefox DNS缓存](http://www.truevue.org/firefox/clear-disable-dns-cache)
7. [Firefox删除DNS缓存](http://www.cnblogs.com/sink_cup/archive/2010/10/28/compare_hosts_dns_cache_firefox_addon.html)