---
layout: post
title: "前后端PDF报表导出最佳实践"
sticky: 100
categroies: [Web开发,前端开发]
tags: [PDF报表,Selenium,Puppeteer]
---



# 缘起

2017年那会，探索过一些关于[Web自动化的内容](http://rawbin-.github.io/dev-tools/2018-12-13-puppeteer-review/),当时 `Headless Chrome`还只是一个不能用于生产的一个玩具，现在早已经完全可以生产应用了。

## 相关问题
回到PDF报表的主题，当我们讨论这个话题的时候，不可避免地要回答几个问题：
- 如果是后端来做报表排版，是不是鸭梨有点大，还原度美观度是不是能不打折？
- 如果前端提供模板后端来填数据渲染生成，这个过程是不是需要来回倒腾才能验证？
- 如果前端也要阅览报表相关内容，为什么需要为后端单独出一个模板或者单独做一份？
- 如果报表预览和报表下载本身是同一个事情，为什么要重复做成两份？

## 解决方案
为了回答上面的几个问题，于是就有了如下的基于浏览器PDF打印能力的报表方案，优势在于：
- 专业的人做专业的事，前端对于排版布局来做都是基操，放给后端可能无形中增加成本
- 通过模板渲染的方式，不可避免地增加了重复工作量，同时引入了前后端过程依赖，来回沟通折腾
- 可以将重复工作降到最低，前端可以最大限度复用组件复用页面
- 不模糊，高保真，前后端都轻松~

后文附上各种实现方案，不管是前端还是后端，不管是Java、Python，Ruby，C#等等还是JavaScript 都能搞。

# 借助浏览器打印实现PDF报表生成
- 为了能够实现一些定时自动化的步骤，一般报表生成都是在服务端做的，这样做完之后就可以有各种通知，发邮件等等自动化的操作

## 设计
- 可参考后文的【PDF报表导出方案】
- 前端在当前系统中或者单独的报表系统中实现报表页面（常规操作）
- 使用浏览器工具或者浏览器访问这个报表页面，自动化实现PDF打印或者导出（一定是要程序实现的）
	- 如果是单独的报表系统，可以带权限认证可以不带
	- 如果是当前系统中做，那么一般是要带权限认证的，需要把对应请求的认证信息同时提交给自动化的浏览器
- 使用流程
	- 用户界面操作，发出报表生成请求（这个时候是有登录认证信息的）
	- 后端接口收到这个请求，携带这些认证信息访问报表相关的浏览器，并用浏览器访问报表页面，然后生成PDF
	- 生成的PDF回传给后端接口，传共享的文件地址可以简化URL操作
	- 然后是下载、通知、发邮件等等

## 方案选型
- 报表页面就正常开发，不管是什么技术栈，只要最终生成合适的数据展示页面就行
	- 这样比起其他方案来效率更高，前端可以独立完全负责报表页面需求的对接，不需要把需求切图成模板，模板给后端渲染，过程中来回沟通，有问题又是长流程的依赖更新等等
- 浏览器工具选择`HeadlessChrome + puppeteer` 作为单独的服务
	- 上面两个组件都是官方实现，配套服务，作为首选
	- 可以用其他替代方案，比如`selenium + Headless Browser`的方案，这个时候就可以用`Python`，`Java`，`C#`等来调用selenium（当然也听过WebDriver，它是selenium用来操作浏览器的API和协议）
	- 其他方案，因为更新不及时，不支持最新的W3C标准特性等等，不建议使用，请自行探索
- 文件回传，可以是各种共享存储，各种对象存储，甚至各种CDN都行，文件流回传也不是不行

## 技术关键点
### 页面布局
- 导出PDF或打印，跟浏览器的页面显示来说，多出了一些打印相关的处理比如分页，比如渲染快切割，显示相关渲染等等，可能遇到的一些问题
	- Chrome本身的问题 比如[这个](https://bugs.chromium.org/p/chromium/issues/detail?id=1298795)，可以通过改变代码的方式规避掉，比如[antd List 套用chart 问题](https://github.com/ant-design/ant-design/issues/34104)
	- 定位问题，比如Antd WaterMark 组件的绝对定位，需要打印的时候同时勾选打印【背景图像】
	- 宽度问题，比如 [这个demo](https://github.com/rawbin-/chrome-webkit-svg-print-debug)l里面，页面显示OK，响应式也OK，但打印预览或者导出PDF一行本来俩图的就变成一个图了，然后页面页数又固定了，有一部分内容就给挤出去到最后没了
	- 切割问题，可以用[CSS控制分页](https://www.w3.org/TR/css-break-3/#break-between)l来避免一些不正确的分页和切割，比如图分隔到两页了，头尾分割了等等
	- 其他奇奇怪怪的问题，比如是否打开开发者工具打印预览结果不一样，以及页面是否调整了打印预览缩放的参数等等可能引起的问题
	- 如果是使用的标准的浏览器样式问题还好，如果是非标准的浏览器更新也不及时，那问题可能就不少了，比如不支持flex，布局怪异等等，这个时候需要多用`block`和`inline-block`布局额外的成本就上来了，所以建议用标准浏览器+最新配套调用库（驱动）
### [[排版布局印刷物相关基础]]
- 这个单独在后续内容里面分享


## 方案实现
### 页面实现
- 跟正常页面实现一样，需要专门验证跟打印相关的布局，样式可以写到 `@media print`里面
- 需要考虑PDF导出方方案实现的兼容性，比如不支持某些最新的W3C特性等
- AntdPro 需要设置`layout:false`，根据情况考虑是否去除水印
- 参考前面的【技术关键点】

### 服务端实现
#### NodeJS [完整源码地址](https://github.com/rawbin-/node-pdf-report-exporter)
##### 方式一 使用[puppeteer](https://github.com/puppeteer/puppeteer)实现  
- 使用Chrome官方的puppeteer NodeJS调用Chrome的方法  
- 安装依赖`yarn`  
- 运行 `node selenium-demo.js`  
  
##### 方式二 使用 [Selenium](https://github.com/SeleniumHQ/selenium)实现  
- 使用NodeJS 调用 Selenium  
- 安装依赖`yarn`  
- 运行 `node selenium-demo.js`

#### Python实现 [完整源码地址](https://github.com/rawbin-/python-pdf-report-exporter)
##### 方式一 使用[pyhtml2pdf](https://pypi.org/project/pyhtml2pdf/#description)实现  
- 安装依赖 `pip install -r requirements.txt`  
- 直接运行 pyhtml2pdf-demo.py  
- 底层原理是调用了 Selenium，可以直接看 site-packages下的源码  
  
##### 方式二 使用[pdfkit](https://github.com/JazzCore/python-pdfkit)  实现
- 安装依赖 `pip install -r requirements.txt`  
- macOS `brew install homebrew/cask/wkhtmltopdf`  
- 直接运行 pdfkit-demo.py  
- 底层原理是调用了 wkhtml2

##### 方式三 直接使用 [Selenium](https://github.com/SeleniumHQ/selenium) 实现  
- 安装依赖 `pip install -r requirements.txt`  
- 直接运行 selenium-demo.py

#### Java实现 [完整源代码](https://github.com/rawbin-/java-pdf-report-exporter)
-  WebDriverManager + 直接调用Selenium实现

#### Kotlin实现 [完整源代码](https://github.com/rawbin-/kotlin-pdf-report-exporter)
- WebDriverManager + 直接调用Selenium实现

#### C#实现 [完整源代码](https://github.com/rawbin-/csharp-pdf-report-exporter)
- 直接调用 Selenium 实现

#### Ruby实现 [完整源代码](https://github.com/rawbin-/ruby-pdf-report-exporter)
- 直接调用 Selenium实现

## 优化拓展
###  优化点
- 可能需要增加MQ来进行异步通信，具体看报表响应时间
- 需要进行必要的浏览器进程池化，避免每次访问都冷启动浏览器
- 可能需要有定时的重启池化的浏览器进程，避免因为浏览器和组件本身内存泄露带来的应用内存泄露
### 拓展点
- 这里方案给了传递cookie的方式，如果需要传递其他自定义头等，需要一些技术调整，如果使用puppeteer 可以直接事件监听实现，如果使用Selenium 可以通过加一个代理层的方式来实现，自定义的头加代理层上，因为Selenium目前不支持这个。


# PDF报表导出常见方案
## 纯浏览器端方案
### html2canvas + jsPDF
- 使用html2canvas将页面转换为canvas，然后将canvas渲染到pdf
- 存在的问题
	- 图片不清晰问题
	- 分页位置不对问题（可以通过计算单页高度和长图位置截取来进行多页，断页切面无法控制）
	- 要精准控制和分页，需要算内容高度，算页高，无法支持内容动态变化


## 借助浏览器打印实现
### puppeteer
- 通过浏览器端请求服务器端，将对应的认证相关信息发送到puppeteer端，设置cookie，然后请求专门的报表页面，打印为pdf
- 可能存在的问题
	- 使用canvas画的图还是会不清晰
	- 页面中如果有绝对定位的内容，需要设置打印背景（否则为空白），比如antdpro的水印设置
	- [antdpro dev server 打印svg图形空白问题](https://github.com/ant-design/ant-design/issues/34104)

### selenium
- 同样的流程，同样的原理
- 支持 Safari,Opera,Firefox,Edge,Chrome
- 支持的技术栈 Java,Python,C#,Ruby,JavaScript,Kotlin

### headless browser（无界面浏览器）
- headless chrome
- headless Firefox
- [headless Edge](https://docs.microsoft.com/zh-cn/microsoft-edge/webdriver-chromium/?tabs=c-sharp)



## 服务端实现的方案
### 借助浏览器打印实现
- 参考前面

### html模板转pdf
- 先生成html模板，然后填充数据
- 根据html生成pdf，比如 iText,wkhtmltopdf, phantomjs，pandoc等
- wkhtmltopdf,phantomjs 等由于webkit内核版本较低，会有各种新特性不支持的情况
- 可以使用puppeteer等

### 报表软件 iReport 、JasperReport，FineReport等
- 先要设计模板，然后进行数据组装
- 同上

### 通过iText组装pdf
- 同上

### 截图方案
- JFreeChart + JFreePDF
- 图不清晰的问题一定存在

### 其他收费报表系统
- unknown