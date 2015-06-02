---
layout: post
title:  "使用Github 搭建自己的博客"
categories: [应用技术]
tags: [GitHub, Jekyll ,博客, 站点, Jekyll Bootstrap]
---

利用github pages 搭建自己的博客，搭建完成之后，可以直接用markdown来写博客，之后直接push到github
就可以在网页上看到自己的博客了。

我喜欢这一点的一个很大的原因是，不需要一张一张的向网页上插图了，直接放入目录，一次提交就完成。

###大致原理是：  
1. github pages 用来给用户或者项目建主页的  
2. github pages 是用jekyll 这个引擎来解析的  
3. 我们可以利用这个来解析我们自己的markdown  
4. 通过jekyll 可以配置一些自动生成的样式和模板  

### 基本部署过程

#### 普通的过程：  
1. 安装Ruby，Devkit，Jekyll [参考][5]
2. 用jekyll 自动生成一个目录结构  (jekyll new xxx)
3. 将你的md 文件按照_post下的文件命名方式重命名  
4. 将整个目录一并提交到github  
5. 通过github pages访问你的博客  

#### 定制页面分隔（带归档分类）
1. 安装Ruby，Devkit，Jekyll [参考][5]
2. 部署 Jekyll Bootstrap,参考[Jekyll Bootstrap][11]
Jekyll Bootstrap 附带了几个实用的命令，可以用rake来构建，具体可参考主目录下的Rakefile
这样就可以得到一个，能加标签和分类并能分类展示的博客。
3. 同样是提交到username.github.io这个代码库中

###参考文档：
0. [github pages jekyll][-1]
1. [github pages help][0]
2. [github pages wizard][1]
1. [使用 GitHub, Jekyll 打造自己的独立博客][2]
2. [Github+Jekyll构建个人博客][3]
3. [通过GitHub Pages建立个人站点（详细步骤）][4]
4. [使用 GitHub, Jekyll 打造自己的免费独立博客][10]
4. [在 Windows 上安装 Jekyll][5]
5. [如何将你的github仓库部署到github pages][6]
6. [jekyllrb 官方网站][7]
7. [jekyll安装][8]
8. [搭建一个免费的无流量的博客][9]
9. [Jekyll-Bootstrap Github][11]
10. [Jekyll-Bootstrap][12]

[0]: https://help.github.com/categories/github-pages-basics/ "github pages jekyll"
[-1]: https://help.github.com/articles/using-jekyll-with-pages/ "github pages help"
[1]: https://pages.github.com/ "github pages wizard" 
[2]: https://github.com/minixalpha/minixalpha.github.io/blob/source/_posts/2014-02-15-github-jekyll-markdown.md "使用 GitHub, Jekyll 打造自己的独立博客"
[3]: http://aboutchen.org/blog/blog-with-github-and-jekyll/ "Github+Jekyll构建个人博客"
[4]: http://www.cnblogs.com/purediy/archive/2013/03/07/2948892.html " 通过GitHub Pages建立个人站点（详细步骤）"
[5]: http://cn.yizeng.me/2013/05/10/setup-jekyll-on-windows/ "在 Windows 上安装 Jekyll"
[6]: http://os.51cto.com/art/201409/450260.htm " 如何将你的github仓库部署到github pages"
[7]: http://jekyllrb.com/docs/installation/ " jekyllrb 官方网站"
[8]: http://hustlei.github.io/2014/08/jekyll-install.html " jekyll安装"
[9]: http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html " 搭建一个免费的无流量的博客"
[10]: http://blog.csdn.net/on_1y/article/details/19259435 "使用 GitHub, Jekyll 打造自己的免费独立博客"
[11]: https://github.com/plusjade/jekyll-bootstrap/ "Jekyll-Bootstrap Github"
[12]: http://jekyllbootstrap.com/ "Jekyll-Bootstrap"