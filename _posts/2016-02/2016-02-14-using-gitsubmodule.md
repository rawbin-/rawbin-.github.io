---
layout: post
title: "使用git submodule管理公共项目"
categories: [开发环境,Git]
tags: [git,submodule]
---

###  引子

svn 有一个引用外部版本库的方式，svn:external；把另外一个版本库作为当前版本库的资源（一部分）。默认开启自动检出外部引用，这个外部引用可以指向一个分支的某个版本，也可以一直指向最新版本。

git submodule跟这个类似，两个不同点：一个是默认不自动检出，另一个是只能指向某个特定的版本。

进入子模块目录，可以向主工程信息一样进行git操作。

### 操作方法

#### 查看外部版本库信息

`git submodule`  类似于`git branch`

#### 添加外部库

`git submodule add REPOSITORY_URL PATH_TO_SUBMODULE`

在当前目录中生成一个`.gitmodules`的文件，里面记录了每个submodule额仓库和路径信息

#### 查看外部库状态

`git submodule status`

#### 初始化外部库

`git submodule init` 这个命令修改.git/config中的内容，对外部库（子模块）进行注册。

#### 更新外部库信息

`git submodule update` 这个命令会按照主项目中包含的子模块的信息更新指定版本的子模块的内容，如果子模块目录中存在未提交的更改，内容可能会丢失。

#### 删除外部库

 `git rm --cached PATH_TO_SUBMODULE`然后删除.gitmodules和.git/config中响应的module的内容。



### 应用

#### 检出一个带有外部版本库的项目

```
git clone REPOSITORY_URL NAME
git submodule init
git submodule update
```

#### 修改外部版本库的引用版本

```
cd PATH_TO_SUBMODULE
git chechout REVISION
cd PATH_TO_MAIN_PROJECT
git add .
git ci -m "update submodule revision"
```



###　参考资料

1. [Git Submodule使用完整教程](http://www.kafeitu.me/git/2012/03/27/git-submodule.html)
2. [git submodule](https://www.kernel.org/pub/software/scm/git/docs/git-submodule.html)
3. [git 子模块](https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)
4. [git 子树合并](https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A0%91%E5%90%88%E5%B9%B6)