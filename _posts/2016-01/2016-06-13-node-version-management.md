---
layout: post
title: "Node JS版本管理，多版本切换，多套开发环境"
categories:[开发技术,前端开发,开发环境]
tags: [nvm,n,nvmw,node]

---



## 缘由

NodeJS 跟Python类似，版本间差异较大，版本中组件的依赖关系较严格，所以我们不能用一套NodeJS环境解决所有项目的问题；最常用是不同的项目或者不同的组件或者不同的工具，可能使用不同的Node版本环境；所以，我们需要在不同的NodeJS版本间进行自由快速切换。



### 工具

NodeJS在类Unix环境中使用问题较少，在Windows中问题稍多，但我们必须直面Windows下使用的问题。

我们需要一个比较符合国情的NodeJS管理工具，nvm比较适合，nvmw比较适合，因为他们可以配置国内镜像的安装路径。其实nvm-windows更加简单，但无法配置国内镜像，在NodeJS和Github网络环境较好的时候使用为佳。

#### Linux

+ [n](https://github.com/tj/n)

+ [nvm](https://github.com/creationix/nvm)

  ​

#### Windows

+ [nvmw](https://github.com/hakobera/nvmw)


+ [nvm-windows](https://github.com/coreybutler/nvm-windows)
+ [nvm-for-windows](https://github.com/magicdawn/nvm-for-windows)
+ [nodlist](https://github.com/marcelklehr/nodist)

### nvm-windows使用

这里使用nvm-windows来做一个全程使用示例，其他类似。

使用nvm来管理，就不需要手动安装NodeJS了，直接使用nvm来安装和卸载

####  原理

+ 有一个公用的NodeJS版本的代理路径
+ 每一个NodeJS版本放入一个目录（包括相应的npm）
+ 每次切换NodeJS版本的时候，就将这个代理路径指向目标NodeJS版本对应的目录
+ 这样外部访问路径都是同一个，不同的版本进入不同目录

#### 安装

到 [这里](https://github.com/coreybutler/nvm-windows) 去下载最新的[nvm-windows](https://github.com/coreybutler/nvm-windows)版本，然后安装。

+ 安装之前卸载之前安装的NodeJS，避免安装后版本错乱问题


+ 注意使用右键 管理员权限
+ 如果有系统防护类软件报警，需要允许
+ 安装完成之后，打开命令行，输入`nvm` 便可以看到简单的几条命令说明（悄悄看）

#### 配置

如果使用nvm-windows默认的配置，会到nodejs.org上去下载相应的资源，这样会很慢，或者根本下不下来

+ 可以选择使用一个VPN或者能连接nodejs.org的环境，这个是nvm-windows的软肋，
+ nvm和nvmw都有配置国内镜像源的环境变量，但我使用了nvmw配置了镜像源，然并卵

#### 使用

+ `nvm ls`查看当前的node环境列表
+ `nvm list avaliable`  查看哪些可以安装的版本列表
+ `nvm install x.xx.xx` 安装这个版本
+ `nvm uninstall x.xx.xx` 卸载这个版本
+ `nvm use x.xx.xx` 切换到这个版本

#### 在这之上

上面我们已经有了基础的node环境了，我们可以使用`nvm install`安装不同的NodeJS环境，然后使用`nvm use `切换到不同的版本，然后再当前版本下安装适合当前NodeJS版本的Node模块。

+ `nvm use 0.12.12`
+ `npm install fekit@0.2.85 -g`
+ `nvm use  4.4.0`
+ `npm install fekit -g`

这样我们就在本机安装了两个`fekit`版本，可以根据需要使用`nvm use`来切换NodeJS环境，从而达到切换`fekit`版本的目的

#### 一些优化配置

[npm官网](https://www.npmjs.com/)访问速度相当的慢，安装npm 组件包，很可能就失败了。一个比较有效的方法:

+ `npm install nrm -g --registry=http://registry.npm.taobao.org/`
+ 这样我们就装了一个npm 镜像管理的组件，然后可以很方便的切换npm组件源
+ `nrm use taobao`切换到淘宝npm源
+ `nrm use cnpm`切换到cnpm的源
+ `nrm use npm` 切回官方的npm源


### 参考资料

1. [nvm管理node，并加速nvm、加速npm](http://www.gowhich.com/blog/673)
2. [快速搭建 Node.js 开发环境以及加速 npm](http://cnodejs.org/topic/5338c5db7cbade005b023c98)
3. [快速搭建 Node.js 开发环境以及加速 npm](https://segmentfault.com/a/1190000000454456)