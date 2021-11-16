---
layout: post
title: "前端三剑客JavaScript、CSS、HTML模块化那些事"
cagegories: [Web开发,前端开发,移动端]
tags: [JavaScript,CSS,HTML,模块化]
---

# JavaScript模块化

## JavasScript模块发展历史

在ES2015（ES6 )之前，ECMAScript(俗称JavaScript)没有标准的模块，从最初对JavaScript的设计预期来说，也不是要做多少事情的，比如就做做表单校验以解决当时网速超级慢的情况下客户端与服务端交互等待时间太长的问题，谁知道后来Web技术的发展让JavaScript肩上的责任越来越重大，预期也越来越高。

### 全局变量

早期不做任何管理的情况下，多个不同的JS的内容都通过`script`标签引用，不同`js`文件中的内容挂放在全局对象window的属性上，这样问题很明显，不同的js文件（可能是不同人写的）中有相同变量名的话，后面加载的会覆盖前面加载的，可能出现同名不同功能的函数被覆盖导致功能异常，这个前两年在维护老系统的时候还遇到过，同名的表单校验函数被各种CopyAndPaste到很多地方，然后有的地方需要这个规则，有的地方需要那个规则，然后你懂的，操碎了心心！！于是就有了下面的命名空间的事。

### 命名空间

最早接触到命名空间来解决类似的问题是在[yui](https://clarle.github.io/yui3/) 和 [extjs](https://www.sencha.com/products/extjs/) 中，在我们经常玩的[MathType Web](https://ckeditor.com/mathtype/demo/) 中也还能看到类似的身影`com.wiris.js.xxx` 对于从`Java`转过来的小伙伴一点也不会陌生，这不就是那个包名么，而且早期也没有啥前端工程师，基本都是Web开发，也就是说前端也是后端写的，这样看来就不足为奇了。在前端内容越来越多、前端技术逐渐细分的情况下，原有Web开发也存在搞不定的事了（我碰到过一个JavaWeb哥们，调一个Table样式搞了一天尝试了N多方法，恁是没搞出来，然后我们的前端小姐姐出马分分钟搞定），这也就是专业的人干专业的事，才有了下面的事情。下面这些我们都称之为规范，但规范也分级别，这些都是社区规范或者说是民间规范，但比起前面这种命名约定来说，已经有了大幅度的发展了。

### AMD + CMD + KMD + UMD

- [AMD(Asynchronous Module Definition)](https://github.com/amdjs/amdjs-api/blob/master/AMD.md)——[RequireJS](https://github.com/requirejs/requirejs)
- [CMD(Common Module Definition)](https://github.com/seajs/seajs/issues/242)——[SeaJS](https://github.com/seajs/seajs)
- [KMD(Kissy Module Definition)](http://docs.kissyui.com/1.4/docs/html/guideline/kmd.html)——[Kissy](http://docs.kissyui.com/)
- [UMD(Universal Module Definition)](https://github.com/umdjs/umd)——上面的包括CommonJS在内的AllInOne



## JavaScript模块标准化

详情见【JavaScript模块标准化】

### CommonJS

参考[wiki](http://wiki.commonjs.org/wiki/CommonJS)这里说标准也不那么恰当，但在`Node.js`环境中，比起前面的各种MD来说，又更近了一步，更加统一，以至于体验上会更加规范，但还不是规范，至少可以做到只要是Node，都遵守一致的模块定义，所有使用者也能达成共识。



### ECMAScript

伴随着ES2015(ES6)的发布，JavaScript标准憋了十年的大招放出来，总算解决了模块标准化的问题，区别于以往任何的模块化系统，又是一套全新的规则，但标准总归是标准，各大平台相继支持Node，Deno等等





# CSS 模块化

## CSS-in-JS



## CSS Modules

### BEM

### :global() :local()

### 样式组合

### 样式覆盖

- 全局穿透样式
- data属性来选择



# HTML模块化

## 相关文档

- [html modules 提的 issue](https://github.com/WICG/webcomponents/issues/645)
- [html modules 提的 proposal](https://github.com/WICG/webcomponents/blob/gh-pages/proposals/html-modules-proposal.md)
- [html modules 浏览器厂商的反馈](https://github.com/MicrosoftEdge/MSEdgeExplainers/blob/main/HTMLModules/i2i.md)
- [html modules 技术设计文档](https://github.com/MicrosoftEdge/MSEdgeExplainers/blob/main/HTMLModules/designDoc.md)
- [html modules 示例](https://github.com/MicrosoftEdge/MSEdgeExplainers/blob/main/HTMLModules/explainer.md)
- [html modules 争议](https://github.com/WICG/webcomponents/blob/gh-pages/proposals/html-modules-explainer.md)
- [html modules 后续状态](https://chromestatus.com/feature/4854408103854080)





# 模块化包管理工具

## [corepack](https://nodejs.org/docs/latest-v17.x/api/corepack.html)

- NodeJS 官方的包管理工具的管理工具

## pnpm

- [github](https://github.com/pnpm/pnpm)

## yarn

- [github](https://github.com/yarnpkg/yarn)

## cnpm

- [github](https://github.com/cnpm/cnpm)

## npm

- [github](https://github.com/npm/npm)

## bower

- [github](https://github.com/bower/bower)

## component

- [github](https://github.com/componentjs/component)

## spm

- [github](https://github.com/spmjs/spm)



# 模块化构建工具

## vite

- [github](https://github.com/vitejs/vite)

## rollup

+ [github](https://github.com/rollup/rollup)
+ [rollup doc](https://rollupjs.org/)

##  ykit——去哪儿网

- [github](https://github.com/YMFE/ykit)


- [qunar ued ykit doc](http://ued.qunar.com/ykit/index.html)

##  webpack

- [github](https://github.com/webpack/webpack)
- [webpack doc](http://webpack.github.io/)

##  fastpack

- [github](https://github.com/fastpack/fastpack)
- [fastpack doc](https://fastpack.sh/)

##  parcel

- [github](https://github.com/parcel-bundler/parcel)

##  fis——百度

+ [github](https://github.com/fex-team/fis)
+ [fis doc](http://fis.baidu.com/)

## feflow——腾讯

- [github](https://github.com/Tencent/feflow)

## fekit——去哪儿网

+ [github](https://github.com/rinh/fekit)
+ [qunar ued fekit doc](http://ued.qunar.com/fekit/index.html)
+ [前端工程化工具Fekit分析](https://my.oschina.net/honchy/blog/403559)

## WeFlow——腾讯

- [github](https://github.com/Tencent/WeFlow)

## coolie

- [github](https://github.com/cooliejs/coolie.js)
- [coolie doc](https://coolie.ydr.me/)
- [fed coolie doc](http://frontenddev.org/column/introduce-coolie/)

##  老牌通用自动化工具

- [grunt](https://gruntjs.com/)
- [gulp](https://gulpjs.com/)



# JavaScript转义器

在ES6发布前后，出现了一大堆相关的JavaScript转义器，主要解决新特性和[兼容性](https://kangax.github.io/compat-table/es6/)问题，甚至也有Google的工具在其中，但最后因为各种各样的原因类似于更新不及时，支持不及时等等，最后babel 胜出，才有了现在的情况。

- [babel](https://babeljs.io/)
- [esm](https://github.com/standard-things/esm)
- [traceur-compiler](https://github.com/google/traceur-compiler)
- [es6-module-transpiler](https://github.com/esnext/es6-module-transpiler)
- [jstransform](https://github.com/facebookarchive/jstransform)



# JavaScript预处理+类型增强

- [typescript](https://www.typescriptlang.org/)
- [flow](https://flow.org/)

