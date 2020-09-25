---
layout: post
title: "Web自动化之Headless Chrome编码实战"
categories: [Web开发,前端开发,JavaScript,自动化]
tags: [JavaScript,Xvfb, PhantomJS, Selenium, Headless Chrome]
---



### 1 API 概览 && 编码Tips

#### 1.1 文档地址

+ [github Chrome DevTools Protocol](https://github.com/ChromeDevTools/devtools-protocol)  协议本身的仓库 有问题可以在这里提issue
+ [github debugger-protocol-viewer](https://github.com/ChromeDevTools/debugger-protocol-viewer)  协议API文档的仓库
+ [API 文档地址](https://chromedevtools.github.io/devtools-protocol/) API展示的地方，这个经常用

#### 1.2 常用API

+ Network 网络请求、Cookie、缓存、证书等相关内容
+ Page 页面的加载、资源内容、弹层、截图、打印等相关内容
+ DOM 文档DOM的获取、修改、删除、查询等相关内容
+ Runtime JavaScript代码的执行，这里面我们可以搞事情~~

#### 1.3 编码Tips

+ 我们这里不会直接调用Websocket相关的内容来调用chrome的调试命令，而是用[chrome-remote-interface](https://github.com/cyrus-and/chrome-remote-interface) 这个封装的库来做，它是基于Promise风格的
+ 每一个功能块成为一个单独的`domain`,像Network，Page，DOM等都是不同的`domain`
+ 几乎每一个个头大的`domain`都有`enable`方法，需要先调用这个方法启用之后再使用
+ 各个`domain`的接口方法参数都是第一个对象或者说一个Map，不用考虑参数的位置了
+ 各个`domain`的接口返回值也是一个对象，取对应的key就行
+ 参数值和返回值经常是meta信息，经常是各种对象的id信息，而不是具体的对象内容（这里可能需要切一下风格）



### 2 编码实例

首先做一个简单的封装，准备API的执行环境，具体可参考前一篇关于工具库的。

```javascript
const chromeLauncher = require('chrome-launcher');
const chromeRemoteInterface = require('chrome-remote-interface');

const prepareAPI = (config = {}) => {
    const {host = 'localhost', port = 9222, autoSelectChrome = true, headless = true} = config;
    const wrapperEntry = chromeLauncher.launch({
        host,
        port,
        autoSelectChrome,
        additionalFlags: [
            '--disable-gpu',
            headless ? '--headless' : ''
        ]
    }).then(chromeInstance => {
        const remoteInterface = chromeRemoteInterface(config).then(chromeAPI => chromeAPI).catch(err => {
            throw err;
        });
        return Promise.all([chromeInstance, remoteInterface])
    }).catch(err => {
        throw err
    });

    return wrapperEntry
};

```



#### 2.1 打开百度，获取页面性能数据，参考 [Navigation Timing W3C规范](https://www.w3.org/TR/navigation-timing/)

```javascript
const wrapper = require('the-wrapper-module');

const performanceParser = (perforceTiming) => {
    let timingGather = {};
    perforceTiming = perforceTiming || {};
    timingGather.redirect = perforceTiming.redirectEnd - perforceTiming.redirectEnd-perforceTiming.redirectStart;
    timingGather.dns = perforceTiming.domainLookupEnd - perforceTiming.domainLookupStart;
    timingGather.tcp = perforceTiming.connectEnd - perforceTiming.connectStart;
    timingGather.request = perforceTiming.responseStart - perforceTiming.requestStart;
    timingGather.response = perforceTiming.responseEnd - perforceTiming.responseStart;
    timingGather.domReady = perforceTiming.domContentLoadedEventStart - perforceTiming.navigationStart;
    timingGather.load = perforceTiming.loadEventStart - perforceTiming.navigationStart;
    return timingGather;
};

const showPerformanceInfo = (performanceInfo) => {
    performanceInfo = performanceInfo || {};
    console.log(`页面重定向耗时:${performanceInfo.redirect}`);
    console.log(`DNS查找耗时:${performanceInfo.dns}`);
    console.log(`TCP连接耗时:${performanceInfo.tcp}`);
    console.log(`请求发送耗时:${performanceInfo.request}`);
    console.log(`响应接收耗时:${performanceInfo.response}`);
    console.log(`DOMReady耗时:${performanceInfo.domReady}`);
    console.log(`页面加载耗时:${performanceInfo.load}`);
};

wrapper.prepareAPI().then(([chromeInstance, remoteInterface]) => {
    const {Runtime,Page} = remoteInterface;

    Page.loadEventFired(() => {
        Runtime.evaluate({
            expression:'window.performance.timing.toJSON()',
            returnByValue:true  //不加这个参数，拿到的是一个对象的meta信息,还需要getProperties
        }).then((resultObj) => {
            let {result,exceptionDetails} = resultObj;
            if(!exceptionDetails){
                showPerformanceInfo(performanceParser(result.value))
            }else{
                throw exceptionDetails;
            }
        });
    });

    Page.enable().then(() => {
        Page.navigate({
            url:'http://www.baidu.com'
        })
    });
});
```



#### 2.2 打开百度 搜索`Web自动化 headless chrome`，并爬取首屏结果链接

```javascript
const wrapper = require('the-wrapper-module');
//有this的地方写成箭头函数要注意，这里会有问题
const buttonClick = function () {
    this.click();
};

const setInputValue = () => {
    var input = document.getElementById('kw');
    input.value = 'Web自动化 headless chrome';
};

const parseSearchResult = () => {
    let resultList = [];
    const linkBlocks = document.querySelectorAll('div.result.c-container');
    for (let block of Array.from(linkBlocks)) {
        let targetObj = block.querySelector('h3');
        resultList.push({
            title: targetObj.textContent,
            link: targetObj.querySelector('a').getAttribute('href')
        });
    }
    return resultList;
};


wrapper.prepareAPI({
    // headless: false  //加上这行代码可以查看浏览器的变化
}).then(([chromeInstance, remoteInterface]) => {
    const {Runtime, DOM, Page, Network} = remoteInterface;
    let framePointer;
    Promise.all([Page.enable(), Network.enable(), DOM.enable(),Page.setAutoAttachToCreatedPages({autoAttach:true})]).then(() => {
        Page.domContentEventFired(() => {
            console.log('Page.domContentEventFired')
            Runtime.evaluate({
                expression:`window.location.href`,
                returnByValue:true
            }).then(result => {
                console.log(result)
            })
        });
        Page.frameNavigated(() => {
            console.log('Page.frameNavigated')
            Runtime.evaluate({
                expression:`window.location.href`,
                returnByValue:true
            }).then(result => {
                console.log(result)
            })
        })
        Page.loadEventFired(() => {
            console.log('Page.loadEventFired')
            Runtime.evaluate({
                expression:`window.location.href`,
                returnByValue:true
            }).then(result => {
                console.log(result)
            })
            DOM.getDocument().then(({root}) => {
                //百度首页表单
                DOM.querySelector({
                    nodeId: root.nodeId,
                    selector: '#form'
                }).then(({nodeId}) => {
                    Promise.all([
                        //找到 搜索框填入值
                        DOM.querySelector({
                            nodeId: nodeId,
                            selector: '#kw'
                        }).then((inputNode) => {

                            Runtime.evaluate({
                                // 两种写法
                                // expression:'document.getElementById("kw").value = "Web自动化 headless chrome"',
                                expression: `(${setInputValue})()`
                            });


                            //这段代码不起作用 日狗
                            // DOM.setNodeValue({
                            //     nodeId:inputNode.nodeId,
                            //     value:'Web自动化 headless chrome'
                            // });

                            //上面的代码需求要这么写
                            // DOM.setAttributeValue({
                            //     nodeId:inputNode.nodeId,
                            //     name:'value',
                            //     value:'headless chrome'
                            // });
                        })
                        //找到 提交按钮setInputValue
                        , DOM.querySelector({
                            nodeId,
                            selector: '#su'
                        })
                    ]).then(([inputNode, buttonNode]) => {

                        Runtime.evaluate({
                            expression: 'document.getElementById("kw").value',
                        }).then(({result}) => {
                            console.log(result)
                        });

                        return DOM.resolveNode({
                            nodeId: buttonNode.nodeId
                        }).then(({object}) => {
                            const {objectId} = object;
                            return Runtime.callFunctionOn({
                                objectId,
                                functionDeclaration: `${buttonClick}`
                            })
                        });
                    }).then(() => {
                        setTimeout(() => {
                            Runtime.evaluate({
                                expression: `(${parseSearchResult})()`,
                                returnByValue: true
                            }).then(({result}) => {
                                console.log(result.value)
                                //百度的URL有加密，需要再请求一次拿到真实URL
                            })
                        },3e3)
                    });
                })

            });
        });
        Page.navigate({
            url: 'http://www.baidu.com'
        }).then((frameObj) => {
            framePointer = frameObj
        });
    })

});
```

