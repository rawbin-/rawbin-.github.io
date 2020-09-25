---
layout: post
title: "CSS 垂直水平居中实现"
categories: [前端开发,Web开发]
tags: [CSS,HTML,垂直水平居中]
---



```css


main {
    position: absolute;
    top: 50%;
    left: 50%;
    margin-top: -3em; /* 6/2 = 3 */
    margin-left: -9em; /* 18/2 = 9 */
    width: 18em;
    height: 6em;
}

main {
    position: absolute;
    top: calc(50% - 3em);
    left: calc(50% - 9em);
    width: 18em;
    height: 6em;
}

main {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

main {
    width: 18em;
    padding: 1em 1.5em;
    margin: 50% auto 0;
    transform: translateY(-50%);
}

main {
    width: 18em;
    padding: 1em 1.5em;
    margin: 50vh auto 0;
    transform: translateY(-50%);
}



body {
    display: flex;
    min-height: 100vh;
    margin: 0;
}
main {
    margin: auto;
}


main {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 18em;
    height: 10em;
}
```







### 1 参考资料

1. [盘点8种CSS实现垂直居中水平居中的绝对定位居中技术](http://blog.csdn.net/freshlover/article/details/11579669)
2. [解读 CSS 布局之水平垂直居中](http://www.tuicool.com/articles/FZzQreV)
3. [CSS元素水平垂直居中方法总结（主要对大漠以及张鑫旭博客所述方法进行了归纳）](http://www.cnblogs.com/Dudy/p/4085292.html)
4. [CSS 元素垂直居中的 6种方法](http://blog.csdn.net/wolinxuebin/article/details/7615098)
5. [元素垂直居中的-6种方法](http://blog.zhourunsheng.com/2012/03/css-元素垂直居中的-6种方法/)
6. [Absolute Centering in CSS](http://codepen.io/shshaw/details/gEiDt)
7. [用 CSS 实现元素垂直居中，有哪些好的方案？](https://www.zhihu.com/question/20543196)
8. [CSS垂直居中的11种实现方式](http://blog.csdn.net/sinat_27346451/article/details/78807294)
9. [CSS使用技巧](http://www.ruanyifeng.com/blog/2010/03/css_cookbook.html)
10. [CSS居中大全](https://www.jianshu.com/p/94e6f0ac039d)
11. [CSS实现垂直居中的常用方法](http://www.cnblogs.com/yugege/p/5246652.html)
12. [CSS垂直居中的11种实现方式](http://www.cnblogs.com/zhouhuan/p/vertical_center.html)
13. [CSS实现水平垂直居中方式](http://www.cnblogs.com/mengfangui/p/7227486.html)