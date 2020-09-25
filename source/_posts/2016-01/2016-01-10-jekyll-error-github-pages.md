---
layout: post
title: "jekyll 3.1.2 导致的github pages 静态资源无效"
categories: [前端开发,Web开发]
tags: [node-inspector,fekit,javascript]
---

## 1 问题

+ 原来好好的github上的博客，打开样式乱了，静态资源找不到 404了
+ 查看页面源代码，发现缺少了主题字段的路径

## 2 解决
+ github目录搜索了一下 "assets/themes" 这个关键字
+ 原有的配置文件中默认添加主题名称的，但这里明星没有添加
+ 原来的配置文件

        # By default, the asset_path is automatically defined relative to BASE_PATH plus the enabled theme.
        # ex: [BASE_PATH]/assets/themes/[THEME-NAME]
        #
        # Override this by defining an absolute path to assets here.
        # ex: 
        #   http://s3.amazonaws.com/yoursite/themes/watermelon
        #   /assets
        #
        ASSET_PATH : false  
    
+ 修改后的配置文件
    
        # By default, the asset_path is automatically defined relative to BASE_PATH plus the enabled theme.
        # ex: [BASE_PATH]/assets/themes/[THEME-NAME]
        #
        # Override this by defining an absolute path to assets here.
        # ex: 
        #   http://s3.amazonaws.com/yoursite/themes/watermelon
        #   /assets
        #
        ASSET_PATH : /assets/themes/bootstrap3
+ 上传后就ok了

## 3 参考资料
0. [升级 Jekyll 3 之前你一定要注意的一件事情](https://rebornix.com/engineering/2015/11/16/Jekyll3Breaks/)
0. [Github page使用jekyll 3.0 以后导致的问题](http://www.zhihu.com/question/40298290)