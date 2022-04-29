---
layout: post
title: "前端单仓库(Monorepo)那些事"
categories: [前端开发,Web开发]
tags: [单仓库,Monirepo]
---



# 简介

在软件开发过程中，代码的版本控制必不可少。代码仓库的管理也有不同的策略，[Monirepo](https://en.wikipedia.org/wiki/Monorepo) 就是其中一种，当下在前端主流仓库中挺流行的方式(React, Babel, Vue,VSCode等等都是这种方式)，它的特点就是所有相关联的代码（第三方依赖除外）都是在同一个代码仓库中（比如git仓库）；而跟这种模式相对应的就是多仓库(multirepo 姑且这么叫吧)模式，典型的就是将将相关联的内容分属多个代码仓库（比如git仓库），然后通过一定的方式关联起来(比如git submodule)。



# Monorepo特点

简单的翻译一下wiki上面的内容

## 优势

- 代码复用简单
- 依赖管理简化
- 提交原子化
- 利于大规模重构
- 便于协作管理

## 劣势

- 丢失版本信息
- 无法进行单项目访问控制
- 一般需要占用更大的存储空间



## 优劣势的平衡

- ["分"与"合"的哲学碰撞，Monorepo vs Multirepo](https://juejin.cn/post/6949882490324516894)



## 在前端项目中的优势

- 每个子项目包的依赖不需要重复安装
- 统一各个子项目的公共配置比如TypeScript，Babel，ESLint等
- 统一和复用脚本命令 [scripty](https://www.npmjs.com/package/scripty)



# Monorepo的实现方案

## rushstack（Microsoft）

- 内置pnpm实现依赖管理

## pnpm

## nx

## turborepo

## bit

## yarn workspace

## lerna(不维护了,还是好用)



# 参考资料

- [Monorepo 的过去、现在、和未来](https://zhuanlan.zhihu.com/p/504554070)
- [前端工程化：如何使用monorepo进行多项目的高效管理](https://juejin.cn/post/7043990636751503390)
- [Monorepo在前端工程里为什么越来越重要？](https://juejin.cn/post/7071463186112725022)
- [现代前端工程为什么越来越离不开 Monorepo?](https://juejin.cn/post/6944877410827370504)
- [前端 Monorepo 实践](https://juejin.cn/post/7036016254385127431)
- [基于pnpm搭建monorepo项目](https://juejin.cn/post/7084582387060834340)
- [rushjs+pnpm之monorepo初探](https://juejin.cn/post/6958360065577320485)
- [基于 Rush 的 Monorepo 多包发布实践](https://juejin.cn/post/7032229406592630815)
- [Monorepo](https://juejin.cn/post/7042244561845878821)
- [Monorepos.2022你该知道的](https://juejin.cn/post/7088239964105736200)
- [代码项目管理](https://juejin.cn/post/7084573116776579085)
- [Monorepo 下的模块包设计实践](https://zhuanlan.zhihu.com/p/456483953)
- [2021年管理Monorepo代码库的11种出色工具](https://juejin.cn/post/6913497232687759367)
- [Monorepo学习与Rush.js实践](https://juejin.cn/post/7082663524899504142)
- [从 Turborepo 看 Monorepo 工具的任务编排能力](https://juejin.cn/post/7065323766640967716)
- [使用Monorepo管理前端微前端项目](https://juejin.cn/post/6950561371394146318)
- [All in one：项目级 monorepo 策略最佳实践](https://juejin.cn/post/6924854598268108807)
- [基于 Lerna 管理 packages 的 Monorepo 项目最佳实践](https://juejin.cn/post/6844903911095025678)
- [几分钟了解前端 Monorepo - Lerna 的使用](https://juejin.cn/post/7064118504982577160)
- [Changesets: 流行的 monorepo 场景发包工具](https://juejin.cn/post/7024827345059971080)
- [lerna+yarn workspace+monorepo项目的最佳实践](https://juejin.cn/post/6844903918279852046)
- [开源项目都在用 monorepo，但是你知道居然有那么多坑么？](https://juejin.cn/post/6950082433647640612)
- [Monorepo 的这些坑，我们帮你踩过了！](https://juejin.cn/post/6972139870231724045)
- [年终盘点：2022基于Monorepo的首个大趋势-TurboRepo](https://juejin.cn/post/7051929587852247077)