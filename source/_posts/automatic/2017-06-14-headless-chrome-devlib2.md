---
layout: post
title: "Web自动化之Headless Chrome开发工具库更新—更高层级的API"
categories: [Web开发,前端开发,JavaScript,自动化]
tags: [JavaScript,Xvfb, PhantomJS, Selenium, Headless Chrome]
---



### 1 引言

在 《Web自动化之Headless Chrome开发工具库》 一文中我们使用`chrome-remote-interface`这个ChromeDevTools协议的NodeJS实现做了一个简单的实例，并在《Web自动化之Headless Chrome编码实战》一文中使用简单封装的库做了一个获取性能参数和一个抓取搜索引擎搜索结果的示例。

我们可以看到`chrome-remote-interface`本身是对ChromeDevTools的一个相对底层的包装，我们在使用的过程中很少有比较贴近浏览器层面的API调用，在开发过程中对底层Websocket命令了解更多，却比如类似于PhantomJS，SlimerJS那样的API来调用，可以简单比较下`ChromeDevTools Protocol` API和`puppeteer` API的区别。

Chrome 团队专门针对这个搞了一个NodeJS库`puppeteer`,专门针对Headless Chrome的NodeJS 开发库。

### 2 问题的解决

在上面示例代码抓取搜素引擎结果的例子中，需要填入搜索词，触发搜索按钮的点击操作，然后等结果返回后，抓取页面结果，这里使用的方式是setTimeout，这种方式实际应用就不太靠谱了，时间长了浪费时间效率低，时间短了拿不到数据，即使时间合适了，网络状况的变化也会变成时间长或者短的问题，针对和这个问题提了一个[issue](https://github.com/ChromeDevTools/devtools-protocol/issues/33),没有什么简单快捷的好办法。直到·puppeteer·的出现，让我们可以重审这个问题(其实这几个月也出了些其他的类似的工具，[参考](https://news.cnblogs.com/n/576898))。

#### 2.1 要实现的需求

+   打开百度首页
+   搜索 `Web自动化 headless chrome`
+   抓取搜索结果




在新的库下

```javascript
const puppeteer = require('puppeteer');

puppeteer.launch({
    headless:false
}).then(async browser => {
    const page = await browser.newPage();

    await page.goto('https://www.baidu.com');

    await page.evaluate(() => {
        const inputElement = document.getElementById('kw');
        inputElement.value = 'Web自动化 headless chrome';

        const submitElement = document.getElementById('su');
        submitElement.click();
    });

    // 这部分代码真正有效，但不太靠谱
    // setTimeout(() => {
    //     page.evaluate(() => {
    //         let resultList = [];
    //         const linkBlocks = document.querySelectorAll('div.result.c-container');
    //         for (let block of Array.from(linkBlocks)) {
    //             let targetObj = block.querySelector('h3');
    //             resultList.push({
    //                 title: targetObj.textContent,
    //                 link: targetObj.querySelector('a').getAttribute('href')
    //             });
    //         }
    //         return resultList;
    //     }).then((searchResult) => {
    //         console.log(searchResult)
    //         browser.close();
    //     });
    // },3000);


    // 这部分代码真正靠谱 但不太有效，有异常，但异常跟站点相关。 
    // https://github.com/GoogleChrome/puppeteer/issues/597 
    // await page.waitFor('div.result.c-container').then(() => {
    //     page.evaluate(() => {
    //         let resultList = [];
    //         const linkBlocks = document.querySelectorAll('div.result.c-container');
    //         for (let block of Array.from(linkBlocks)) {
    //             let targetObj = block.querySelector('h3');
    //             resultList.push({
    //                 title: targetObj.textContent,
    //                 link: targetObj.querySelector('a').getAttribute('href')
    //             });
    //         }
    //         return resultList;
    //     }).then((searchResult) => {
    //         console.log(searchResult)
    //         browser.close();
    //     });
    // })
});
```



实现的代码量减少了一半多，这就是高级别API带来的优势。

然而，暂时还是没有摆脱使用setTimeout这种前面谈到的不太靠谱的方式，对于可以用API操控的URL跳转，完全可以用带`waitUntil`的options参数的API来调用（到API文档里面去搜`waitUntil`）。

如下使用的例子

```javascript
const puppeteer = require('puppeteer');

puppeteer.launch({
    headless:false
}).then(async browser => {
    const page = await browser.newPage();


    page.on('load',() => {
        console.log('load event fired');
    });

    await page.goto('https://dujia.qunar.com').catch(() => {

    });

    await page.evaluate(() => {
        const inputElement = document.getElementById('arriveSearchText');
        inputElement.value = '三亚';

        const submitElement = document.querySelector('[item="commit"]');
        submitElement.click();
    });

    await page.waitFor('.dj-list-content').then(() => {
        page.evaluate(() => {
            let resultList = [];
            const linkBlocks = document.querySelectorAll('.unit.calendar-list-con');
            for (let block of Array.from(linkBlocks)) {
                let targetObj = block.querySelector('.has_title_dom');
                resultList.push({
                    title: targetObj.textContent,
                    link: targetObj.getAttribute('href')
                });
            }
            return resultList;
        }).then((searchResult) => {
            // console.log(searchResult)
            // browser.close();
        });
    })
});
```



### 3 问题解决

#### TimeoutError: Navigation Timeout Exceeded

- page.setDefaultTimeout



#### Error: Protocol error (Page.setLifecycleEventsEnabled): Target closed

- close 前加 waitFor延时 [ref](https://www.cnblogs.com/muou2125/p/11352746.html)
- 手动改改源码 [ref](https://lucifaer.com/2018/12/14/如何解决Pyppeteer的Target closed错误/)

###  参考文档

1.  [ChromeDevTools Protocol](https://github.com/ChromeDevTools/devtools-protocol)

2.  [chrome-remote-interface](https://github.com/cyrus-and/chrome-remote-interface)

3.  [puppeteer](https://github.com/GoogleChrome/puppeteer)

4.  [Google Puppeteer加入到headless Chrome的工具行列](https://news.cnblogs.com/n/576898/)

5.  [Google's Puppeteer Joins Crowd of Headless Chrome Tools](https://www.infoq.com/news/2017/08/google-puppeteer-headless-chrome)

    

