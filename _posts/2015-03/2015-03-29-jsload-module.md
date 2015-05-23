---
layout: post
title: "JavaScript 模块加载工具 JSLoader"
categories: [开发技术]
tags: [seajs,requirejs,AMD, CMD]
---

随着Web应用的内容不断扩充，Web开发工作的不断细分，前端的模块化的呼声也越来越高。
了解前端模块化可以看[前端模块化--高效重构][0],以及[前端模块化思路][1]。

总的来说还是高内聚，低耦合，减少依赖和耦合，提高合作效率。

模块化在发展的过程中产出了一些规范，如AMD，CMD等，可以看看[JavaScript模块规范][5],以及[区别1][6]和[区别2][7].


也有一些关于AMD和CMD及其相关实现requirejs和seajs的讨论[为什么我推荐requirejs 而不是seajs?][8]

JavaScript模块加载工具，解决我们在页面一次性加载所有需要用到的库文件，导致的性能和体验问题；做到按需加载。

不管怎样，抓到老鼠就是好猫，能有明显的优化效果就行。

### 其他参考资料
0. [seajs 官网][2]
0. [seajs 文档][3]
0. [使用SeaJS实现模块化JavaScript开发][4]
0. [SeaJS所为何][11]
0. [为什么 SeaJS 模块的合并这么麻烦][12]
0. [requirejs 英文][9]
0. [requirejs 中文][10]
0. [利用browserify or requirejs 来构建angularjs]("http://www.tuicool.com/articles/UNrq22","利用browserify or requirejs 来构建angularjs")


[0]: http://kb.cnblogs.com/page/146573/ "前端模块化--高效重构"
[1]: http://kb.cnblogs.com/page/63687/ "前端模块化思路"
[2]: http://seajs.org/docs/ "seajs 官网"
[3]: http://seajs.org/docs/#docs "seajs 文档"
[4]: http://blog.codinglabs.org/articles/modularized-javascript-with-seajs.html "使用SeaJS实现模块化JavaScript开发"
[5]: http://blog.chinaunix.net/uid-26672038-id-4112229.html "JavaScript模块规范"
[6]: http://www.zhihu.com/question/20351507/answer/14859415 "AMD和CMD区别"
[7]: http://zhangyaochun.iteye.com/blog/1702625 "CMD和AMD区别"
[8]: http://blog.3gcnbeta.com/2014/05/27/%E4%B8%BA%E4%BB%80%E4%B9%88%E6%88%91%E6%8E%A8%E8%8D%90requirejs-%E8%80%8C%E4%B8%8D%E6%98%AFseajs/ "为什么我推荐requirejs 而不是seajs?"
[9]: http://requirejs.org/ "requirejs 英文网"
[10]: http://www.requirejs.cn/ "requirejs 中文网"
[11]: http://cyj.me/why-seajs/zh/ "Why SeaJS"
[12]: http://chaoskeh.com/blog/why-its-hard-to-combo-seajs-modules.html "为什么 SeaJS 模块的合并这么麻烦"