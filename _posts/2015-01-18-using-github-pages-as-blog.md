---
layout: post
title:  "Using Github Pages as Blog"
date:   2015-01-18 20:36:38
categories: Other
---

利用github pages 搭建自己的博客，搭建完成之后，可以直接用markdown来写博客，之后直接push到github
就可以在网页上看到自己的博客了。

我喜欢这一点的一个很大的原因是，不需要一张一张的向网页上插图了，直接放入目录，一次提交就完成。

####大致原理是：  
1. github pages 用来给用户或者项目建主页的  
2. github pages 是用jekyll 这个引擎来解析的  
3. 我们可以利用这个来解析我们自己的markdown  
4. 通过jekyll 可以配置一些自动生成的样式和模板  

####操作过程如下：  
1. 安装Ruby，Devkit，Bundler，Jekyll，github-pages  
2. 用jekyll 自动生成一个目录结构  
3. 将你的md 文件按照_post下的文件命名方式重命名  
4. 将整个目录一并提交到github  
5. 通过github pages访问你的博客  



###参考文档：
[1] github pages: https://pages.github.com/， https://help.github.com/categories/github-pages-basics/， https://help.github.com/articles/using-jekyll-with-pages/

[2] 使用 GitHub, Jekyll 打造自己的独立博客: https://github.com/minixalpha/minixalpha.github.io/blob/source/_posts/2014-02-15-github-jekyll-markdown.md

[3] Github+Jekyll构建个人博客: http://aboutchen.org/blog/blog-with-github-and-jekyll/

[4] 通过GitHub Pages建立个人站点（详细步骤）: http://www.cnblogs.com/purediy/archive/2013/03/07/2948892.html

[5] 在 Windows 上安装 Jekyll: http://cn.yizeng.me/2013/05/10/setup-jekyll-on-windows/

[6] 如何将你的github仓库部署到github pages: http://os.51cto.com/art/201409/450260.htm
