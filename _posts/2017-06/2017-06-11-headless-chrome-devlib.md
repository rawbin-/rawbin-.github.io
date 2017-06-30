---
layout: post
title: "Web自动化之Headless Chrome开发工具库"
categories: [Web开发,前端开发,JavaScript,自动化]
tags: [JavaScript, Lighthouse, chrome-launcher, Headless Chrome]
---



### 命令行运行Headless Chrome

#### Chrome 安装（需要带梯子）

+ [下载地址](https://www.chromium.org/getting-involved/dev-channel)
+ 几个版本的比较
  + Chromium 不是Chrome，但Chrome的内容基本来源于Chromium，这个是开源的版本，小时级别的更新
  + Canary 是试验版，翻译过来就是金丝雀，金丝雀对瓦斯等毒气很敏感，浓度稍高就会停止鸣叫甚至挂掉，金丝雀是瓦斯等毒气检测的土办法，这个场景在《寻龙诀》中黄渤的操作中也能看到。哈哈 扯远了，这个是daily build 版本。
  + Dev 是开发版，weekly build版本
  + Beta 是测试版，monthly build版本
  + Stable 是稳定版，不定期更新，一般也是一个月左右一次
  + 更新频率 Chromium > Chrome Canary > Chrome Dev > Chrome Beta > Chrome Stable
  + Chrome Dev、Chrome Beta 和 Chrome Stable三者只能同时出现一个
  + Chromium 、Chrome Canary 和 剩下的任意一个可共存 
+ Windows平台下载下来的可能只是一个在线安装的程序，下载离线版在下载页面的URL里面加参数`standalone=1`

#### 命令行快捷配置（Mac环境）

在`~/.bashrc` 中加入

```
alias chrome="/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome"
alias chrome-canary="/Applications/Google\ Chrome\ Canary.app/Contents/MacOS/Google\ Chrome\ Canary"
```

重新打开终端，我们就可以直接通过 `chrome`打开稳定版的Chrome，`chrome-canary`打开试验版的Chrome了。

#### 命令行启动Chrome 

+ 参考[官方说明](http://www.chromium.org/developers/how-tos/run-chromium-with-flags), Headless模式需要Chrome Version >= 59

+ 使用Chrome打开百度首页(带界面)，能看到浏览器的打开

  ```
  chrome https://www.baidu.com
  ```

+ 使用无界面模式启动Chrome打开百度首页(无界面)，但不到浏览器界面打开，但任务栏会有图标

  ```
  chrome --headless https://www.baidu.com   
  ```

+ 使用无界面模式启动Chrome并将页面转为PDF，可以看到`output.pdf`的输出

  ```
  chrome --headless --print-to-pdf https://www.baidu.com
  ```

+ 使用无界面模式启动Chrome并截图，可以看到`screenshot.png`的输出

  ```
  chrome --headless --screenshot --window-size=414,736 https://www.baidu.com/
  ```

+ 使用无界面模式启动Chrome并打开交互环境

  ```
  chrome --headless --repl 
  ```

+ 使用无界面模式启动Chrome，并开启调试Server

  ```
  chrome --headless --remote-debugging-port=9222
  ```

+ 参考 [Chrome命令行参数列表](http://peter.sh/experiments/chromium-command-line-switches/)

#### 命令行操作Headless Chrome

+ 确保已经启动Headless Chrome,并启用了调试Server

  ```
  chrome --headless --remote-debugging-port=9222
  ```

+ 安装`chrome-remote-interface`

  ```
  npm install chrome-remote-interface -g
  ```

+ 查看命令说明，这里可以进行各种相关操作

  ```
  $ chrome-remote-interface

    Usage: chrome-remote-interface [options] [command]

    Commands:

      inspect [options] [<target>] inspect a target (defaults to the first available target)
      list                   list all the available targets/tabs
      new [<url>]            create a new target/tab
      activate <id>          activate a target/tab by id
      close <id>             close a target/tab by id
      version                show the browser version
      protocol [options]     show the currently available protocol descriptor

    Options:

      -h, --help         output usage information
      -t, --host <host>  HTTP frontend host
      -p, --port <port>  HTTP frontend port
      -s, --secure       HTTPS/WSS frontend
  ```

+ 打开一个新页面

  ```
  chrome-remote-interface new https://www.baidu.com
  ```

+ 查看刚打开的页面

  ```
  chrome-remote-interface inspect
  ```

+ 查看当前页面的URL

  ```
  >>> Runtime.evaluate({expression:'location.href'})
  ```

  ​

### 可编程方式运行Headless Chrome

#### 直接通过代码调用命令行启动Chrome 调试Server

可以通过系统调用的方式直接调用上面的命令行执行方式。这种方式在跨平台的情况下会有一些工作需要做。

Google出品的[Lighthouse](https://github.com/GoogleChrome/lighthouse) 这个网页质量检查工具，有一个组件专门做这事，考虑了各种平台的兼容性问题，源码参考[lighthouse-chromelauncher](https://github.com/GoogleChrome/lighthouse/blob/master/chrome-launcher/chrome-finder.ts)，这个组件现在已经单独独立出来，作为一个单独的`NPM`组件`chrome-launcher`,可以直接使用这个在`Node`平台下调用，其他平台的也可以此为参考。

```JavaScript
const chromeLauncher = require('chrome-launcher');
//启用无界面模式并开启远程调试,不同引用版本和方式，调用方式可能有些区别
//chromeLauncher.run({
chromeLauncher.launch({
    // port: 9222,
    chromeFlags: [
        '--headless'
    ]
}).then((chrome) => {
    // 拿到一个调试客户端实例
    console.log(chrome)
    chrome.kill();
});
```



#### 通过客户端的封装组件进行浏览器交互

实现了`ChromeDevTools`协议的[工具库有很多](https://github.com/ChromeDevTools/awesome-chrome-devtools#chrome-devtools-protocol)，`chrome-remote-interface`是NodeJS的实现。

Chrome调试Server开启的是WebSocket交互的相关实现，要用编程的方式实现还需要封装一些WebSocket命令发送、结果接收等这一系列操作，这些`chrome-remote-interface`已经帮我们做了，更多实例可以参考[chrome-remote-interface的wiki](https://github.com/cyrus-and/chrome-remote-interface/wiki)。

```javascript
const chromeLauncher = require('chrome-launcher');
const chromeRemoteInterface = require('chrome-remote-interface')
//启用无界面模式并开启远程调试,不同引用版本和方式，调用方式可能有些区别
//chromeLauncher.run({
chromeLauncher.launch({
    port: 9222,
    chromeFlags: [
        '--headless'
    ]
}).then((launcher) => {
    chromeRemoteInterface.Version({
        host:'localhost',
        port:9222
    }).then(versionInfo => {
        console.log(versionInfo)
    });

    chromeRemoteInterface({
        host:'localhost',
        port:9222
    }).then((chrome) => {
        //这里调用ChromeDevToolsProtocol定义的接口
        const {Network,Page} = chrome;

        Network.requestWillBeSent((params) => {
            let {request}  = params;
            let {url} = request;
            console.log(url)
        });
        Promise.all([
            Network.enable(),
            Page.enable()
        ]).then(() => {
            Page.navigate({
                url:'https://www.baidu.com'
            })
        });

        setTimeout(() => {
            launcher.kill()
        },5000);

    })
});
```

