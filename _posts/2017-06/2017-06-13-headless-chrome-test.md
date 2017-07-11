---
layout: post
title: "Web自动化之Headless Chrome测试框架集成"
categories: [Web开发,前端开发,JavaScript,自动化]
tags: [JavaScript,Xvfb, PhantomJS, Selenium, Headless Chrome]
---

[TOC]

### 使用`Selenium`操作headless chrome 推荐

#### 简介

`WebDriver`是一个[W3C标准](https://www.w3.org/TR/webdriver/), 定义了一套检查和控制用户代理（比如浏览器）的远程控制接口，各大主流浏览器来实现这些接口以便调用控制接口来操作浏览器。

`Selenium`是一整套的Web自动化测试解决方案，配合WebDrive规范，实现了对各种用户代理的适配（比如浏览器,PhantomJS等），通过操作浏览器的`WebDriver`接口来实现带浏览器的Web自动化。

####  使用`selenium-webdriver`

+ `selenium`相关的[API文档](http://www.seleniumhq.org/docs/index.jsp)
+ `selenium-webdriver`的[JavaScriptAPI文档](http://seleniumhq.github.io/selenium/docs/api/javascript/index.html)

```
const webdriver = require('selenium-webdriver'),
    By = webdriver.By;

const driver = new webdriver.Builder()
    .forBrowser('chrome')
    .build();


driver.get('https://www.baidu.com').then((args) => {
    // 获取百度搜索按钮的 文本
    driver.findElement(By.id('su')).then((element) => {
        return element.getAttribute('value')
    }).then((btnName) => {
        console.log(btnName)
    });

    // 获取百度首页 title
    driver.getTitle().then((title) => {
        console.log(title);
    });
});


driver.quit();

```



#### 使用`browserstack-webdriver`

只是获取driver的方式不一样，其他调用完全一样

```
const webdriver = require('browserstack-webdriver'),
    By = webdriver.By;

// Input capabilities
const capabilities = {
    'browserName' : 'firefox',
    'browserstack.user' : BROWSERSTACK_USERNAME,
    'browserstack.key' : BROWSERSTACK_KEY
}

const driver = new webdriver.Builder().
usingServer('http://hub.browserstack.com/wd/hub').
withCapabilities(capabilities).
build();


driver.get('https://www.baidu.com').then((args) => {
    // 获取百度搜索按钮的 文本
    driver.findElement(By.id('su')).then((element) => {
        return element.getAttribute('value')
    }).then((btnName) => {
        console.log(btnName)
    });

    // 获取百度首页 title
    driver.getTitle().then((title) => {
        console.log(title);
    });
});


driver.quit();
```



#### 使用 `chromedriver`

`chromedriver`是一个编码辅助，自动配置环境变量，不需要手动下载和配置环境变量，通过安装`chromedriver`同时在代码中引入

```
require('chromedriver')
```



##### 更换获取源的URL（使用如下任意一种就行）

+ 安装过程添加参数，默认下载地址为`http://chromedriver.storage.googleapis.com`

  ```
  npm install chromedriver --chromedriver_cdnurl=https://npm.taobao.org/mirrors/chromedriver
  ```

+ 添加如下内容到`.npmrc`文件

```
chromedriver_cdnurl=https://npm.taobao.org/mirrors/chromedriver
```

+ 添加环境变量`CHROMEDRIVER_CDNURL`


```
CHROMEDRIVER_CDNURL=https://npm.taobao.org/mirrors/chromedriver npm install chromedriver
```

##### 更换安装的`chromedriver`文件路径

+ 安装过程使用配置参数

```
npm install chromedriver --chromedriver_filepath=/path/to/chromedriver_mac64.zip
```

+ 添加如下内容到`.npmrc`文件

```
chromedriver_filepath=/path/to/chromedriver_mac64.zip
```

+ 添加环境变量

```
CHROMEDRIVER_FILEPATH=/path/to/chromedriver_mac64.zip
```



### 使用mocha + chai

#### 简介

`mocha`是一个可以运行在浏览器端和NodeJS环境的JavaScript测试框架，区别于类库，框架定义好了流程，并调用你的代码。

`chai`是一个断言库，判断结果是否符合预期。

#### 实例代码 

```
const chai = require('chai');

const chromeDriver = require('selenium-webdriver/chrome')

const webdriver = require('selenium-webdriver'),
    By = webdriver.By;

const driver = new webdriver.Builder()
    .forBrowser('chrome')
    .setChromeOptions(new chromeDriver.Options().addArguments(['headless']))
    .build();

describe('首页加载测试',function(){
    // 获取百度搜索按钮的 文本
    describe('按钮文本',function(){
        it('按钮文本必须等于',function(done){
            driver.get('https://www.baidu.com').then(function(){
                driver.findElement(By.id('su')).then((element) => {
                    return element.getAttribute('value')
                }).then((btnName) => {
                    console.log(btnName);
                    chai.expect(btnName).to.equal('百度一下');
                    done();
                });
            });
        })

    });

    // 获取百度首页 title
    describe('首页标题',function(){
        it('首页标题应该为',function(done){
            driver.get('https://www.baidu.com').then(function(){
                driver.getTitle().then((title) => {
                    console.log(title);
                    chai.expect(title).to.equal('百度一下，你就知道');
                    done();
                });
            });
        });
    });

    after(function(){
        driver.quit();
    })
});
```



### 使用Karma + mocha + chai

#### 简介

`Karma`是一个用JavaScript实现的测试执行器，实现了如下内容

+ 对各种常见框架、库的适配[参考](https://www.npmjs.com/browse/keyword/karma-adapter)
+ 各种常见代码预处理或转译[参考](https://www.npmjs.com/browse/keyword/karma-preprocessor)
+ 各种执行的测试报告方案[参考](https://npmjs.org/browse/keyword/karma-reporter)
+ 各种浏览器或类浏览器的适配[参考](https://npmjs.org/browse/keyword/karma-launcher)
+ 各种编辑器的适配，内容变更，立即重新执行
+ 覆盖率统计

#### 安装相应的依赖库

```
npm i --save-dev karma karma-chrome-launcher karma-mocha karma-chai
npm i --save-dev mocha chai
```

#### 生成配置文件

在工程目录下执行如下命令

```
./node_modules/.bin/karma init
```

一路按照提示操作即可，生成的配置文件在工程目录下`karma.conf.js`，内容大致如下：

```
// Karma configuration
// Generated on Mon Jul 10 2017 19:49:48 GMT+0800 (CST)

module.exports = function(config) {
  config.set({
    // base path that will be used to resolve all patterns (eg. files, exclude)
    basePath: '',
    // frameworks to use
    // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
    frameworks: ['mocha'],
    // list of files / patterns to load in the browser
    files: [
    ],
    // list of files to exclude
    exclude: [
    ],
    // preprocess matching files before serving them to the browser
    // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor
    preprocessors: {
    },
    // test results reporter to use
    // possible values: 'dots', 'progress'
    // available reporters: https://npmjs.org/browse/keyword/karma-reporter
    reporters: ['progress'],
    // web server port
    port: 9876,
    // enable / disable colors in the output (reporters and logs)
    colors: true,
    // level of logging
    // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
    logLevel: config.LOG_INFO,
    // enable / disable watching file and executing tests whenever any file changes
    autoWatch: true,
    // start these browsers
    // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
    browsers: ['Chrome'],
    // Continuous Integration mode
    // if true, Karma captures browsers, runs the tests and exits
    singleRun: false,
    // Concurrency level
    // how many browser should be started simultaneous
    concurrency: Infinity
  })
}
```



##### 调整配置支持headless chrome

可以到[这里](https://www.npmjs.com/package/karma-chrome-launcher),查看chrome相关的karma-launcher，有`ChromeHeadless`和`ChromeCanaryHeadless`这两个headless驱动可以选择。

##### 调整配置支持ES6，添加webpack

```
npm i webpack karma-webpack babel-core babel-loader babel-preset-es2015
```

##### 调整配置增加测试覆盖度

```
npm i babel-plugin-istanbul
```

##### 最终的到的Karma配置文件

`karma.conf.js`

```
// Karma configuration
// Generated on Mon Jul 10 2017 19:49:48 GMT+0800 (CST)

module.exports = function(config) {
    config.set({
        // base path that will be used to resolve all patterns (eg. files, exclude)
        basePath: '',
        // frameworks to use
        // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
        frameworks: ['mocha','chai'],
        // list of files / patterns to load in the browser
        files: [
            'test/**/*.js'
        ],
        // list of files to exclude
        exclude: [
        ],
        // preprocess matching files before serving them to the browser
        // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor
        preprocessors: {
            'test/**/*.js': ['webpack']
        },
        // test results reporter to use
        // possible values: 'dots', 'progress'
        // available reporters: https://npmjs.org/browse/keyword/karma-reporter
        reporters: ['progress', 'coverage'],
        coverageReporter: {
            type: 'html',
            dir: 'coverage/'
        },
        // web server port
        port: 9876,
        // enable / disable colors in the output (reporters and logs)
        colors: true,
        // level of logging
        // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
        logLevel: config.LOG_INFO,
        // enable / disable watching file and executing tests whenever any file changes
        autoWatch: true,
        // start these browsers
        // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
        browsers: ['ChromeHeadless'],
        // Continuous Integration mode
        // if true, Karma captures browsers, runs the tests and exits
        singleRun: true,
        // Concurrency level
        // how many browser should be started simultaneous
        concurrency: Infinity,
        webpack: {
            module: {
                loaders: [{
                    test: /\.js$/,
                    loader: 'babel-loader',
                    exclude: /node_modules/,
                    query: {
                        presets: ['es2015'],
                        plugins: ['istanbul']
                    }
                }]
            }
        }
    })
}
```



#### 编写代码

`src/index.js`

```
const isType = (data, type) => {
    return ['[object ', type, ']'].join('') === Object.prototype.toString.call(data)
};

const isFunction = (data) => {
    return isType(data, 'Function')
};

const isArray = (data) => {
    return isType(data, 'Array')
};


module.exports = {
    isType,
    isFunction,
    isArray
}
```

#### 编写测试用例

`test/index.js`

```
const typeUtil = require('../src/index')

describe('index.js: ', () => {
  it('isFunction() should work fine.', () => {
    expect(typeUtil.isFunction(function(){})).to.equal(true)
    expect(typeUtil.isFunction(Object.prototype.toString)).to.equal(true)
  });

  it('isArray() should work file.', () => {
      expect(typeUtil.isArray([])).to.equal(true)
      expect(typeUtil.isArray({})).to.equal(false)
  })
});

```



#### 运行测试

+ 在当前目录下运行`./node_modules/.bin/karma start`
+ 或者添加如下代码到`package.json`

```
  "scripts": {
    "test": "karma start"
  }
```

​	然后运行`npm run test`



#### 查看结果

+ 命令行能看到运行结果
+ 在工程目录下的`coverage`目录能看到相应的覆盖率报告



#### 存在的问题

Karma是将测试Case在浏览器中运行并查看结果，当页面的url 改变的时候，会影响到整个Karma的执行，会有类似`Some of your tests did a full page reload!`这样的提示。上面打开百度首页检查按钮和title的例子在Karma中还没有找到合适的方式写出来。

 

### 参考资料

0. [Automated testing with Headless Chrome](https://developers.google.cn/web/updates/2017/06/headless-karma-mocha-chai)
1. [使用HeadlessChrome做单页应用SEO](https://github.com/gwuhaolin/blog/issues/8)
2. [基于HeadlessChrome的网页自动化测试系统-FinalTest](https://github.com/gwuhaolin/blog/issues/7)
3. [使用 headless chrome进行测试](http://www.ctolib.com/topics-116159.html)
4. [使用 headless chrome进行测试](https://segmentfault.com/a/1190000009399609)
5. [UI自动化测试之Headless browser容器化](http://www.jianshu.com/p/4a830d22614d)
6. [初探 Headless Chrome](https://zhuanlan.zhihu.com/p/27100187)
7. [Karma原理及论文](https://karma-runner.github.io/1.0/intro/how-it-works.html)
8. [karma入门](https://segmentfault.com/a/1190000005848842)
9. [karma 测试框架的前世今生](http://taobaofed.org/blog/2016/01/08/karma-origin/)