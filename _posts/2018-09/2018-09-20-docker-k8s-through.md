---
layout: post
title: "docker+kubenetes开发上线运维"
categories: [Web开发,前端开发,自动化]
tags: [部署,容器化,可伸缩,自动化,运维]
---



# 概念理解

## 两大生态

### Docker

- 将精力专注在应用开发上，包揽环境部署问题(Debug you app, not your environment)
- 将环境部署定义在稳定的自动化的基础上，从过程上降低运维成本

#### 相关概念 [完整的概念参考](https://docs.docker.com/glossary/)

##### Docker 本身

代指`Docker` 这个思想，这个项目，相关的产品，工具、社区等等，不同语境代指不一样。

Docker 是一种可以提供操作系统级别虚拟化（也称作容器）的软件技术。

##### 仓库（Repository）

存放和管理镜像的地方

##### 镜像（Mirror）

是一个配置和数据的集合，是容器的初始状态

##### 容器（Container）

容器是镜像的实例，是可移植的动态调度单元

##### Dockerfile

一个用来生成镜像的配置文件

### Kubernetes

- 已经是解决`Docker`编排需求的行业标准，超过了包括`Docker`官方的`Docker Compose`，用于管理容器化的工作负载和服务
- 高可用、弹性扩容、强大的生态系统，从架构上降低运维成本

#### 相关概念 [完整的概念参考](https://kubernetes.io/zh/docs/reference/glossary/?fundamental=true)

##### Pod

Pod 是 Kubernetes 的原子对象， 表示集群上一组正在运行的容器

##### Service

将运行在一组 Pods 上的应用程序公开为网络服务的抽象方法

##### 容器

##### 容器

容器是可移植、可执行的轻量级的镜像，镜像中包含软件及其相关依赖

##### 节点（Node）

是执行工作的机器，它可以是一个虚拟机或者物理机器，每个节点都包含用于运行Pods的必要服务，并由主控组件管理



# 环境部署

# 应用配置

# 运维场景







# 参考资料

0. [Dockerfile 官方最佳实践](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
1. [Kubernetes Handbook](https://jimmysong.io/kubernetes-handbook/)