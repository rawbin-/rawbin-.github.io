---
layout: post
title: "使用Jenkins进行持续集成和自动部署"
categories: [Web开发,前端开发]
tags: [Jenkins,CI,Deploy]
---

## Jenkins部署

### 简单的部署过程

+ 下载Jenkins包
+ 配置Java环境
+ 启动Jenkins包

CentOS下的脚本如下:

```shell
yum install -y wget screen java
if [ ! -f "jenkins.war"]; then
	wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war
fi

screen java -jar jenkins.war 
```



### 简单的进入过程

+ 打开浏览器，访问 `http://192.168.1.2:8080`,IP地址换成你自己的主机
+ 进入登录界面，使用上面命令行控制台里面的密码进行登录
+ 安装推荐的插件或者自己选择插件安装
+ 设定管理员的账号和密码
+ 进入Jenkins配置主界面



## Jenkins 配置










### 参考资料

1. [Jenkins Gitlab持续集成打包平台搭建](http://skyseraph.com/2016/07/18/Tools/Jenkins%20Gitlab%E6%8C%81%E7%BB%AD%E9%9B%86%E6%88%90%E6%89%93%E5%8C%85%E5%B9%B3%E5%8F%B0%E6%90%AD%E5%BB%BA/)
2. [Jenkins官方文档](https://www.w3cschool.cn/jenkins/)
3. [Use Jenkins](https://wiki.jenkins.io/display/JENKINS/Use+Jenkins)
4. [Gitlab Jenkins CI](http://docs.gitlab.com/ee/integration/jenkins.html#jenkins-ci-deprecated-service)
