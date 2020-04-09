---
layout: post
title: "NodeJS 服务端技术调研"
categories: [前端开发,Web开发,JavaScript]
tags: [JavaScript,Node,Node.js,NodeJS]
---



### 技术框架选型

#### Node特点及适用性

##### 特点

- 从2009年3月开始亮相，目前发展10+年
- 事件驱动，轻量、松耦合、只关注事务点
- 单线程（`JavaScript`代码执行），
  - 没有同步问题，没有死锁，没有线程切换的影响
  - 无法利用多核，一个错误会引起整个应用退出，健壮性有风险
    - 通过`child_process`、`Cluster`、`Cluster2`来解决
- 异步非阻塞I/O，减少昂贵的I/O对CPU计算的影响，充分利用硬件资源

##### 应用场景

- 不太适合CPU密集型业务（也不差，可以通过分解业务解决），适合I/O密集型业务
  - 通过编写C/C++扩展或者`child_process`来解决
- 前后端公用模板的系统

#### Web开发框架

| 包名称               | github Star数 | 周下载量   | 上次发布时间 | 备注             |
| -------------------- | ------------- | ---------- | ------------ | ---------------- |
| express              | 46.4k         | 10,638,972 | 6 months ago |                  |
| koa                  | 28k           | 388,602    | a month ago  | 备选             |
| nest                 | 25.6k         | 100,465    |              |                  |
| hapi                 | 11.9k         | 148,441    | 3 months ago |                  |
| restify              | 9.6k          | 103,575    | 7 days ago   |                  |
| fastify              | 12.7k         | 60,139     | a day ago    | 文档中有对比数据 |
| loopback             | 13k           | 48,179     | 6 months ago |                  |
| egg                  | 14.4k         | 6,750      | 2 days ago   | 基于koa，备选    |
| sails                | 21k           | 37,670     | 6 months ago |                  |
| micro                |               | 60,466     | 5 months ago | 微服务框架       |
| @feathersjs/feathers | 11.9k         | 38,304     | 3 days ago   |                  |
| derby                | 4.5k          | 156        | 6 days ago   |                  |
| seneca               | 3.6k          | 8664       | 3 months ago | 备选             |



- Connect

  2010年开始出现，只是一个中间件层，后被用在Express和Koa中

- Express

  2010年开始出现，继承了Connect的思想并内置了很多中间件，MEAN（MongoDB+Express+Angular+Node）非常流行

- Koa

  2013年底，Express原班人马打造，解决Express异步中间件层级调用等问题，内部没提供中间件

- Hapi

- restify

- fastify

- LoopBack

- eggjs

- thinkjs

- sails

- micro

- @feathersjs/feathers

- derby

- Seneca Node微服务框架

#### 模板引擎

| 包名称       | github Star数 | 周下载量  | 上次发布时间 | 备注     |
| ------------ | ------------- | --------- | ------------ | -------- |
| art-template | 8.7k          | 30,801    | a year ago   | 备选     |
| velocityjs   | 506           | 166,655   | 2 months ago |          |
| pug          | 18.8k         | 543,078   | 6 months ago | 原名jade |
| handlebars   | 15k           | 9,244,577 | 21 days ago  | 备选     |
| mustache     | 13.5k         | 1,343,511 | 2 days ago   |          |
| ejs          | 4k            | 4,601,778 | 15 days ago  | 备选     |
| dot          | 4.4k          | 172,329   | 2 days ago   |          |
| nunjucks     | 6.4k          | 242,099   | 9 months ago |          |
|              |               |           |              |          |



- art-template
- velocityjs
- Jade/pug
- Handlebars
- Mustache
- Embedded JavaScript
- dot
- numjucks

这里有个[对比](https://blog.csdn.net/weixin_43648947/article/details/102544602) `art-template` 性能表现好

#### 数据库框架

| 包名称    | github Star数 | 周下载量 | 上次发布时间 | 备注 |
| --------- | ------------- | -------- | ------------ | ---- |
| typeorm   | 16.5k         | 201,756  | 4 days ago   |      |
| sequelize | 20.6k         | 465,897  | a month ago  |      |
| knex      | 11k           | 412,470  | a day ago    |      |
|           |               |          |              |      |

- typeorm
- Sequelize
- knex

#### 服务端渲染

- Vue 
  - vue-server-renderer
  - nuxtjs
  - quasar
- React
  - react-dom/server
  - nextjs

#### 实时通信

- Websocket
- WebRTC
- Socket.IO

### 开发测试

#### 编码规范

- eslint
- 安全编码
  - xss 转义输出
  - csrf 验证token

#### 异常处理

- try catch
  - 无法捕捉try 中异步异常
- server on error
- process on uncaughtException
- domains(pending deprecated)
- 结束进程
  - 避免抛出新异常
  - process.exit 结束进程
  - timmer.unref 避免timmer阻止进程退出
  - server.close 停止接收新连接
  - cluster.work.disconnect 关闭IPC通道

#### 流程控制

- async
- step
- wind
- bagpipe

#### 自动化测试

- [相关概念参考](https://docs.python.org/3.6/library/unittest.html)
- Nock HTTP相关测试
- Nyc 覆盖度测试
- Selenium 浏览器相关自动化测试
- Headless Browser 
  -  [puppeteer](https://github.com/puppeteer/puppeteer)
  - 汇总 https://github.com/dhamaniasad/HeadlessBrowsers
- 压测
  - ab
  - JMeter

##### 测试框架

- 汇总 https://phantomjs.org/headless-testing.html
- Karma
- mocha
- ava
- jasmine
- Vows async + BDD风格
- SuperTest  RESTful  API
- Sinon.JS 
- 端到端测试
  - CasperJS
    - PhantomJS SlimerJS
  - Protractor
  - Nightwatch
  - TestCafe
  - CodeceptJs

##### 断言库

- chai  BDD风格+TDD风格
- should.js  BDD风格
- expect.js  BDD风格
- assert TDD风格

##### 覆盖率

- istanbul
- nyc

### 部署维护

#### 服务进程管理

- [简单比较](http://strong-pm.io/compare/)
- pm2
- nodemon
- forever
- strong-pm
- node-supervisor
- upstart
- init.d
- systemd

#### 服务监控

- PM2
- Keymetrics
- 监控指标
  - 响应时间
  - 进程监控
  - 磁盘监控
  - 内存监控
  - CPU监控
  - 心跳监测
- Node自带, process 相关信息
- REPL 《node项目实践》
- 《Node调试指南》
- alinode



#### 日志追踪

- winston
- bunyan
- log
- log4js
- Patertrail
- 中间件
  - morgan
  - express-winston

#### 安全相关

- 防止命令注入



#### 持续集成和持续部署

- Travis CI
- Jenkins
- strider

#### 部署容器

- Docker
- kubernetes
- chef
- vagrant