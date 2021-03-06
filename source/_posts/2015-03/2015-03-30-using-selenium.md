---
layout: post
title:  "Selenium 浏览器截图，网页截图，浏览器操作"
categories: [开发技术]
tags: [Selenium, Python 网页截图, 自动截图]
---

Selenium 是一个Web自动化测试的套件
使用前需要安装selenium包:

    #! /usr/bin/env python
    # -*- coding: UTF-8 -*-
    
    from selenium import webdriver
    
    browser = webdriver.Firefox()
    browser.set_window_size(1024,768)
    browser.get('http://www.baidu.com/s?wd=selenium')
    
    browser.save_screenshot('page.png')
    browser.quit()

需要注意的是，使用chrome需要安装相应的驱动，叫chromedriver的可执行文件，放入$PATH

截图是针对浏览器页面，即使浏览器窗口被其他应用程序窗口覆盖也没有关系。


### 1 参考资料

0. [ReadTheDoc文档][1]
1. [selenium官网][2]
2. [selenium官方文档][3]
3. [selinium pypi文档说明][4]
4. [selinium on GitHup][5]
---
[1]: https://selenium-python.readthedocs.org/ ReadTheDoc文档

[2]: http://www.seleniumhq.org/  官网

[3]: http://www.seleniumhq.org/docs/ 官方文档

[4]: https://pypi.python.org/pypi/selenium  pipy实例

[5]: https://github.com/SeleniumHQ/selenium/tree/master/py  GitHub