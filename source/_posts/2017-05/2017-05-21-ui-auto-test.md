---
layout: post
title: "前端自动化测试&&持续集成"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript, selenium, appium,phantomjs,slimerjs,casperjs]
---

[TOC]

### 1 测试工具

#### 1.1 操作浏览器

+  [selenium](https://github.com/SeleniumHQ/selenium)
+  [appium](https://github.com/appium/appium)
+  [dalekjs](http://dalekjs.com/)

#### 1.2 无界面浏览器 [参考](https://github.com/dhamaniasad/HeadlessBrowsers) 

+ [phantomjs](https://github.com/ariya/phantomjs) Webkit (维护难度大)

+ [slimerjs](https://github.com/laurentj/slimerjs) Gecko

+ [triilejs](http://triflejs.org/) V8

+ [casperjs](https://github.com/casperjs/casperjs) Webkit + Gecko

  + JavaScript~
  + 自带测试框架，这。。。
  + 可以通过NPM 安装，但不是真正意义上的NPM模块，要当成NPM模块来用的话 ，需要用SpookyJS

+ [nightwatch](https://github.com/nightwatchjs/nightwatch)

+ [nightmare](https://github.com/segmentio/nightmare) Electron

  + NodeJS开发，首选

+ [zombie](https://github.com/assaf/zombie)

+ [Chrome 59+](https://chromium.googlesource.com/chromium/src/+/lkgr/headless/) Webkit HeadLess Mode `--headless`

  + 未来的发展

  ​

### 2 自动化工具

+ [grunt](https://gruntjs.com/)
+ [gulp](http://gulpjs.com/)

#### 2.1 测试框架 [参考](http://phantomjs.org/headless-testing.html)

+ [robot framework](http://robotframework.org/)
+ [mocha](http://mochajs.org/)
+ [Jasmine](https://jasmine.github.io/)



### 3 持续集成

+ [strider](https://github.com/Strider-CD/strider)
  + NodeJS 开发，首选


+ [jenkins](https://jenkins.io/index.html)
+ [travis CI](https://docs.travis-ci.com/)
+ [teamcity](https://www.jetbrains.com/teamcity/)



### 4 选型结果

+ Web测试用CasperJS，移动端用Appium
+ 集成Jenkins



### 5 参考资料

1. [w3c webdriver](https://github.com/w3c/webdriver)

2. [webdriver io](http://webdriver.io/)

3. [appium github](https://github.com/appium/appium)

4. [HeadlessBrowsers](https://github.com/dhamaniasad/HeadlessBrowsers)

5. [selenium github](https://github.com/SeleniumHQ/selenium)

6. [selenium doc](https://seleniumhq.github.io/docs/index.html)

7. [dalekjs](http://dalekjs.com/)

8. [casperjs github](https://github.com/casperjs/casperjs)

9. [phantomjs github](https://github.com/ariya/phantomjs)

10. [slimerjs github](https://github.com/laurentj/slimerjs)

11. [nightwatch github](https://github.com/nightwatchjs/nightwatch)

12. [Chrome Headlesss Mode](https://chromium.googlesource.com/chromium/src/+/lkgr/headless/)

13. [zombie github](https://github.com/assaf/zombie)

14. [How to install and use Headless Chrome on OSX](https://objectpartners.com/2017/04/13/how-to-install-and-use-headless-chrome-on-osx/)

15. [SpookyJS github](https://github.com/SpookyJS/SpookyJS)

16. [robot framework](http://robotframework.org/)

17. [grunt](https://gruntjs.com/)

18. [grunt 中文](http://www.gruntjs.net/)

19. [gulp](http://gulpjs.com/)

20. [gulp 中文](http://www.gulpjs.com.cn/)

21. [mocha](http://mochajs.org/)

22. [jasmine](https://jasmine.github.io/)

23. [测试框架参考](http://phantomjs.org/headless-testing.html)

24. [strider](https://github.com/Strider-CD/strider)

25. [jenkins](https://jenkins.io/index.html)

26. [travis CI](https://docs.travis-ci.com/)

27. [teamcity](https://www.jetbrains.com/teamcity/)

28. [HTMLUnitDriver & PhantomJS for Selenium Headless Testing](http://www.guru99.com/selenium-with-htmlunit-driver-phantomjs.html)

    ​