---
layout: post
title: "Nest Web框架实战简明参考手册"
categories: [前端开发,Web开发,JavaScript,NodeJS]
tags: [JavaScript,NodeJS,Nest,TypeScript]
---



# 核心概念

## Nest本身

- 是一个基于Node.js的高效可扩展的服务端应用的Web框架
- 完全支持TypeScript，本身就是用TypeScript实现的
- 结合面向对象编程（OOP），函数式编程（FP）和函数响应式编程（FRP）
- 底层是基于Express实现的，也能轻松集成Fastify
- 集成度高，致力于提供可测试、可扩展、松耦合和易维护的应用框架，思想大体来源于Angular
  - 有一套完整的项目结构生成模板，有每一个项目组件的生成模板
  - 有各种微服务组件的集成，大小型项目都能轻易玩起来
  - 等等

### 应用分类

#### 网络应用

- 基于HTTP的平台
- 基于Websocket的应用
- 微服务（基于TCP和UDP）

#### 脱机/单机/独立 应用(不带网络监听的)

### 混合应用

- 同时包含了微服务和其他网络应用的应用



### Decorators 装饰器

- Nest 构建的基础，是ES2016的语言特性，从代码表达形式上很容易看出来
- 可以用来传递数据，可以跟管道联合使用
- 可以将多个装饰器构建为一个装饰器来使用
- Nest 提供的对应Web框架的参数装饰器，对应原生Web框架的对象
  - `@Request ` => `req`
  - `@Response` => `res`
  - `@Nest` => `next`
  - `@Session` => `req.session`
  - `@Param(param?: string)` => `req.params`/`req.params[param]`
  - `@Body(param?: string)` => `req.body`/`req.body[param]`
  - `@Query(param?: string)` => `req.query`/`req.query[param]`
  - `@Headers(param?: string)` => `req.headers` / `req.headers[param]`
  - `@Ip` => `req.ip`



### Module 模块

- 是标注`Module`装饰器的一个类
- 是Nest组织项目结构的一部分，是用来组织程序部件的有效方式，模块组织以下内容
  - providers 提供者 ，要被Nest注入器实例化的提供者，会被至少在本模块共享
  - controllers 控制器，本模块定义的要被实例化的控制器
  - imports 本模块依赖的模块
  - exports 本模块要暴露的模块，是providers的子集



### 应用生命周期

####  启动过程

- 引导启动
  - `onModuleInit`事件，针对每一个模块，在模块初始化完成后执行
    - 等待子控制器和提供者的`onModuleInit`方法执行完成
    - 等待模块的`onModuleInit`方法执行完成
  - `onApplicationBootstrap` 事件， 针对每一个模块
    - 等待子控制器和提供者`onApplicationBootstrap`方法执行完成
    - 等待模块的`onApplicationBootstrap`方法执行完成

- 初始化监听器（
  - 针对HTTP Server，WS Server和每一个微服务
    - 等待连接打开或者就绪

- Nest整个应用启动完成



#### 关闭过程

- `onModuleDestory` 针对每一个模块
  - 等待子控制器和提供者`onModuleDestory`方法执行完成
  - 等待模块的`onModuleDestory`方法执行完成
- `beforeApplicationShundown` 针对每一个模块
  - 等待子控制器和提供者`beforeApplicationShundown`方法执行完成
  - 等待模块的`beforeApplicationShundown`方法执行完成
- 关闭监听器
  - 针对HTTP Server，WS Server和每一个微服务
    -  等待连接关闭
- `onApplicationShundown`  针对每一个模块
  - 等待子控制器和提供者`onApplicationShundown`方法执行完成
  - 等待模块的`onApplicationShundown`方法执行完成



### 请求生命周期

#### 收到请求

#### middleware 中间件

- 全局绑定的中间件
- 模块绑定的中间件

#### guard 守卫

- 全局守卫
- 控制器守卫
- 路由守卫

#### interceptor 拦截器

- 全局拦截器（pre-controller）
- 控制器拦截器（pre-controller）
- 路由拦截器（pre-controller）

#### pipe 管道

- 全局管道
- 控制器管道
- 路由管道
- 路由参数管道

#### controller 控制器（方法处理器）

#### service 服务（如果存在）

#### interceptor 拦截器

- 路由拦截器（post-request）
- 控制器拦截器（post-request）
- 全局拦截器（post-request）

#### filter 过滤器

- 路由过滤器
- 控制器过滤器
- 全局过滤器

#### 服务端响应



## MVC架构相关概念

### Middleware 中间件

- 在路由控制器之前调用的函数
- 可以访问`request`和`response`对象，调用`next`函数继续请求响应流程，返回响应内容终止流程 



### Guards 守卫

- 一个使用`Injectable`装饰器，同时实现了`CanActivate` 接口的类
- 只负责根据特定的条件（比如权限，角色，访问控制列表等）决定请求是否需要被路由处理器处理
- 能访问`ExecutionContext`执行上下文，明确知道接下来会执行什么，



### Interceptors 拦截器

- 一个使用`Injectable`装饰器，同时实现了`NestInterceptor` 接口的类
- 受AOP思想的启发，提供了实现如下行为的能力：
  - 在方法执行前后绑定额外的逻辑
  - 转换函数执行的结果
  - 转换函数抛出的异常
  - 扩展基本的函数行为
  - 根据一定的条件重新函数的功能，比如根据缓存来重写



### Pipes 管道

- 一个使用`Injectable`装饰器，同时实现了`PipeTransform` 接口的类
- 管道典型应用场景：
  - 数据转换，将输入转到预期的输出
  - 数据校验，校验输入数据是否有效，校验通过就直接透传，不通过就抛异常

- 管道在控制器方法调用前被调用，接收方法的入参；如果管道抛异常，不会再执行控制器方法



### Exception Filter 异常过滤器

- Nest内置一个异常层来负责全局处理所有未处理的异常
- 可以使用标准的异常，也可以过滤部分或者全部异常来自定义异常处理，





### Controller 控制器

- 负责处理请求和给出响应
- 请求是从路由分配过来的
- 每一个控制器可以通过`Controller`装饰器来定义路由
- 每一个控制器下的方法
  - 可以通过HTTP对应的方法装饰器，如`Get`,`Post`等定义下级路由
  - 可以通过`Res`,`Req`装饰器注入框架原生的请求响应实例



### Provider 提供者

- 提供依赖注入的能力，使得对象直接可以创建多样的关系
- 依赖注入是IoC控制反转的一种实现方式，Nest运行时本身是一个IoC容器
- 本质上是一个使用了`Injectable`装饰器类
- 很多Nest中的基本类都可以作为提供者，比如Service、Repository、Factory、Helper等等
- 标准提供者方式是给模块（module）配置提供者（provider）信息，自定义的场景如下
  - 创建自定义的实例而不是Nest提供的
  - 重用已有的类来做另一个依赖
  - 重写用来测试的模拟类
- 自定义提供者的实现方式`useValue`,`useClass`,`useFactory`,`useExisting`,`Inject`装饰器等等

#### Service 服务

- 服务是常见的提供者，直接通过控制器的构造函数注入到控制器





# 应用解析

## Workspace Project（工作区项目）

- `project`（项目）在这里的定义可以为如下两种中的任一一种
  - `application`（应用） ，标准的Nest 应用，也是常见的Web应用结构
  - `library`（库），是一种打包通用功能特性（比如模块，提供者，控制器等等）的方式，无法独立运行
- 所有的工作区项目都有一个默认项目，在`nest-cli.json`中定义，被`nest start`和`nest build`作为默认值调用
- `application`和`library`有两个区别可以从配置文件看出来：
  - `type`分别为`application`和`library`，这不是废话
  - `entryFile` 分别为`main` 和 `index`



### Application 应用

- 也叫应用型项目，在单库模式常用，就是标准的Nest 应用结构，使用`nest g app` 或者`nest g sub-app`生成的
- 可以用`nest start xxx` 启动应用型项目



### Library 库

- 也叫库型项目，使用`nest g lib`生成



### standard 标准模式（默认）

- 用来组织单独的以项目为中心的应用
- 这些应用不需要优化模块共享，也不需要优化复杂的打包过程等等

### monorepo 单库模式

- `monorepo`是一种代码组织的思想，怎么翻译不重要，对应`multirepo`，将多个紧密关联又相互独立的项目放到一个仓库里面来管理，使用工作区的概念

- 在标准模式的基础上（只有一个应用）,添加一个子应用(`nest g app xx`)或者一个库(`nest g lib yy`)，则自动转为单库模式，转换过程就是当前应用（这会应该叫工作区间了）放了多个应用，看一眼就懂了

- 这种模式带来的好处

  - 将代码资源当做轻量级单独的一部分来对待，更适合多开发人员的团队或者多项目的环境

  - 将打包过程部分自动化，更容易创建和管理模块化的组件

  - 带来更好的代码重用

  - 使集成测试更容易

  - 项目范围内的资源共享更容易，比如配置策略， 过滤规则，钩子函数等等，比类似于`git submodules`更好用

    



## 命令行工具 nest

### 功能概览

```
$ nest -h
Usage: nest <command> [options]

Options:
  -v, --version                                   Output the current version.
  -h, --help                                      Output usage information.

Commands:
  new|n [options] [name]                          Generate Nest application.
  build [options] [app]                           Build Nest application.
  start [options] [app]                           Run Nest application.
  info|i                                          Display Nest project details.
  update|u [options]                              Update Nest dependencies.
  add [options] <library>                         Adds support for an external library to your
                                                  project.
  generate|g [options] <schematic> [name] [path]  Generate a Nest element.
    Available schematics:
      ┌───────────────┬─────────────┐
      │ name          │ alias       │
      │ application   │ application │
      │ class         │ cl          │
      │ configuration │ config      │
      │ controller    │ co          │
      │ decorator     │ d           │
      │ filter        │ f           │
      │ gateway       │ ga          │
      │ guard         │ gu          │
      │ interceptor   │ in          │
      │ interface     │ interface   │
      │ middleware    │ mi          │
      │ module        │ mo          │
      │ pipe          │ pi          │
      │ provider      │ pr          │
      │ resolver      │ r           │
      │ service       │ s           │
      │ library       │ lib         │
      │ sub-app       │ app         │
      └───────────────┴─────────────┘
```

### 命令详解

#### new 简写 n

- 生成标准模式的项目结构的所有文件

#### build 

- 按nest的方式打包

#### start

- 编译并运行应用（或则工作区的默认项目）

#### info

-  输出项目相关的部件及版本相关信息

#### update

- 更新`nest`相关的依赖到最新版本

#### add

- 导入一个已被打包为nest 库的库，执行它的安装过程

#### generate

- application 创建一个标准结构的应用，不安装依赖包（nest new 会安装）
- class  生成一个空的类及测试文件
- configuration 生成一个`nest-cli.json` ,暂时还不好用
- controller 生成一个空的controller及测试文件
- decorator 生成一个空的装饰器文件
- filter 生成一个空的过滤器及其测试文件
- gateway 生成一个空的网关及其测试文件
- guard 生成一个空的守卫及其测试文件
- interceptor 生成一个空的拦截器及其测试文件
- interface 生成一个空的接口文件
- middleware 生成一个空的中间件及其测试文件
- module 生成一个空的模块文件
- pipe 生成一个空的管道及其测试文件
- provider 生成一个空的提供者及其测试文件
- resolver 生成一个graphql的解析器及其测试文件
- service 生成一个空的服务及其测试文件
- library 生成完整的lib结构放入libs目录，如果当前项目是标准模式会转换为单库模式
- sub-app 生成完整的app结构放入apps目录，如果当前项目是标准模式会转换为单库模式



## 常用实现参考

### 参考入口

- [相关资料](https://docs.nestjs.cn/7/awesome)
- [techniques](https://docs.nestjs.com/techniques/authentication)
- [recipes](https://docs.nestjs.com/recipes)

### 常用功能特性

####  静态站点

- [@nestjs/serve-static](https://docs.nestjs.com/recipes/serve-static)
- [mvc](https://docs.nestjs.com/techniques/mvc)

#### 权限控制

- [authentication](https://docs.nestjs.com/techniques/authentication)
- [nestjsx/nest-access-control](https://github.com/nestjsx/nest-access-control)

#### CURD

- [中文参考](https://docs.nestjs.cn/7/recipes?id=crud)
- [nestjsx/crud](https://github.com/nestjsx/crud)
- [cqrs](https://docs.nestjs.com/recipes/cqrs)

#### 项目配置

- [nestjsx/nestjs-config](https://github.com/nestjsx/nestjs-config)
- [@nestjs/config](https://docs.nestjs.com/techniques/configuration)

#### 项目文档

- [compdoc](https://docs.nestjs.com/recipes/documentation)
- [swagger](https://docs.nestjs.com/recipes/swagger)

#### 项目日志

- [logger](https://docs.nestjs.com/techniques/logger)
- [nest-winston](https://github.com/gremo/nest-winston)
- [nest-morgan](https://github.com/mentos1386/nest-morgan)

#### 健康检查

- [Terminus](https://docs.nestjs.com/recipes/terminus)

#### HTTP集成

- [HTTP](https://docs.nestjs.com/techniques/http-module)

#### 文件上传

- [multer](https://docs.nestjs.com/techniques/file-upload)

#### 序列化

- [class-transformer](https://docs.nestjs.com/techniques/serialization)

#### GraphQL转换

- [Prisma](https://docs.nestjs.com/recipes/prisma)
- [nestjs-query](https://github.com/doug-martin/nestjs-query)

#### 数据库

- [database](https://docs.nestjs.com/techniques/database)
- [typeorm](https://docs.nestjs.com/recipes/sql-typeorm)
- [sequelize](https://docs.nestjs.com/recipes/sql-sequelize)
- mongodb
  - [@nest/mongoose](https://docs.nestjs.com/techniques/mongodb)
  - [mongoose](https://docs.nestjs.com/recipes/mongodb)
  - [nestjs-typegoose](https://github.com/kpfromer/nestjs-typegoose)

#### 数据校验

- [class-validator](https://docs.nestjs.com/techniques/validation)

#### 路由

- [nestjsx/nest-router](https://github.com/nestjsx/nest-router)

#### 缓存

- [cache-manager](https://docs.nestjs.com/techniques/caching)

#### 任务队列

- [bull](https://docs.nestjs.com/techniques/queues)

- rabbitmq
  - [nestjsx/nestjs-amqp](https://github.com/nestjsx/nestjs-amqp)
  - [amqplib](https://docs.nestjs.com/microservices/rabbitmq)

#### 安全性

- [helmet](https://docs.nestjs.com/techniques/security)

#### 定时任务

- [@nestjs/schedule](https://docs.nestjs.com/techniques/task-scheduling)



# 项目实战

## 相关概念

- application 标准项目里面代表整个项目，单库项目里面代表一个子应用
- library 生成完整的lib结构放入libs目录，如果当前项目是标准模式会转换为单库模式
- sub-app 生成完整的app结构放入apps目录，如果当前项目是标准模式会转换为单库模式
- controller 控制器，负责处理请求和给出响应
- service 服务，一种是提供者，负责处理业务逻辑
- middleware 在路由处理程序之前调用，可以访问请求和响应对象，通用的过滤函数
- module 模块，用来组织项目结构
- decorator 装饰器，语言特性，用来支持功能增强和功能复用
- provider 提供者，通用的可注入的类，一般会更具体的类，比如`service`
- filter 过滤器，用来处理异常过滤
- pipe 管道，用来做数据转换和验证
- guard 守卫，用来做权限控制
- interceptor 拦截器，面向切面编程的思想，函数前后增加额外逻辑，转换函数的结果或抛出的异常等
- class  通用的纯粹的TypeScript类
- interface 通用的TypeScript接口
- configuration 项目配置`nest-cli.json` 
- gateway 网关，抽象的应用类型之上的
- resolver 解析器，提供把graphql操作转换成数据的指令



## 项目需求

- 访问`RabbitMQ`获取关键信息 或者 通过接口传入
- 通过MQ中拿到的信息查询`MongoDB`
- 提供页面服务和模板渲染
- 提供信息配置
- 提供健康检查
- 提供日志记录
- 提供Web安全防护



## 项目规划

- 从Nest的设计来说，RabbitMQ属于微服务，而页面属于HTTP服务，所以本项目是一个混合应用

### 相关参考

- `rabbitmq`实现 [参考](https://docs.nestjs.com/microservices/rabbitmq)
- `mongodb`实现 [参考](https://docs.nestjs.com/techniques/mongodb) [typegoose](https://github.com/kpfromer/nestjs-typegoose)
- 页面服务 [参考](https://docs.nestjs.com/recipes/serve-static), 模板渲染 [参考](https://docs.nestjs.com/techniques/mvc)
- 信息配置 [参考](https://docs.nestjs.com/techniques/configuration)
- 健康检查 [参考](https://docs.nestjs.com/recipes/terminus)
- 日志信息 [参考](https://docs.nestjs.com/techniques/logger)
- 安全配置 [参考](https://docs.nestjs.com/techniques/security)



### 项目结构设计

- 使用单库模式
- 各个功能以公共模块的方式存在于项目中，可以简单实现插拔

