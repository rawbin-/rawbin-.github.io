---
layout: post
title: "Git 概念详解"
categories: [前端开发,Web开发]
tags: [开发工具,Git]
---



### 分布式版本管理系统

#### 优点

- 能将代码库完整地复制到本地
- 运行速度快
- 临时作业的提交易于管理
- 分支、合并简单方便
- 可以不受地点的限制进行协作开发

#### 缺点

- 系统中没有真正意义上的最新版本
- 没有真正意义上的版本号
- 工作流程的配置过于灵活，易于产生混乱
- 思维方式的习惯需要一定的时间



### Git概念

- 配置文件（优先级依次降低）
  - .git/config 为特定版本库的配置文件
  - ~/.gitconfig 为当前用户的配置文件 `--global`
  - /etc/gitconfig 为当前系统的配置文件 `--system`
- 版本库
  - 裸版本库，没有工作目录，没有检出分支，不能用于正常开发
  - 开发版本库，最常用的
  - 工作中使用的是本地版本库或者当前版本库，共享和交换文件的版本库为远程版本库
- 分支
  - 远程追踪分支与版本库关联，追踪远程分支中的变化`refs/remotes`
  - 本地追踪分支和远程追踪分支对应，用于收集本地开发和远程追踪分支的变更
- 文件分类
  - tracked（已追踪的）
  - ignored（被忽略的）
  - untracked（未追踪的）
- 对象类型（commit可以打tag，commit包含tree，tree包含至少一个blob）
  - blob
  - tree
  - commit
  - tag
- 索引（index）
- 提交（commit）
  - 相对提交
    - 除了跟提交外，其他每一个提交都有至少一个父提交
    - 使用`^x`来选择第`x`个父提交（多个父提交来源于`merge`）,单独的`^`表示父级提交
    - 使用`~`来选择上一代提交，`~n`表示向上`n`代提交，单独的`~`表示父级提交
    - 使用`@`来表示，相对的版本,比如`HEAD@{10}`
  - 提交范围
    - `..`表示一个范围，`start..end`表示`start`可达的但`end`不可达的内容（结合差）,`start` 和 `end`分别为能表示提交的标识，比如`commitid` 或者 相对提交等，范围的结果是前开后闭的区间，不包括`start`但包括`end`；也可以省略`start`或者`end`，省略的一方默认HEAD
    - `...`同样表示一个范围，`start...end`表示`start`和`end`不同时可达的内容
- 引用（reference）`.git/refs/`
  - 本地分支名 `.git/refs/heads/`
  - 远程跟踪分支名 `.git/refs/remotes`
  - 标签名 `.git/refs/tags`
  - HEAD 当前分支的最新提交
  - ORIG_HEAD 类似`merge`、`reset`这样的操作留下的操作前HEAD的保存
  - FETCH_HEAD `fetch`操作是获取的所有分支HEAD的列表
  - MERGE_HEAD `merge`操作时要写到HEAD里面的内容
  - MERGE_MSG 记录合并失败的信息
  - MEGE_MODE 合并标识状态





### Git 操作

- 获得帮助
  - 输入`git` 并回车，看到git相关帮助和提示
  - 输入`git help xxx`来查看子命令相关的帮助
- 初始化仓库`git init`
  - 会在当前目录下创建一个`.git`的目录，包含了相关的版本库相关的信息
- 版本库克隆 `git clone`
  - clone过程原始版本库中的`refs/heads`下的本地分支，会成为新的克隆版本库`refs/remotes`下的远程跟踪分支，原始版本库中的`refs/remotes`下的远程分支不会克隆
  - `refs/tags`会被克隆，hooks、配置文件、reflog、stash 不会被克隆
- 添加跟踪 `git add`
- 删除跟踪 `git rm`
- 带跟踪的移动或者改名 `git mv`
- 切换分支、拉取文件 `git checkout`
  - `git checkout branch -- path/to/file` 拉取branch中的文件
- 日志查看 `git log`
- 比较差异 `git diff`
- 单行最后一次变更查看 `git blame`
- 提交内容 `git commit`
  - `git commit --amend` 修改最后一次提交
- 二分查找 `git bisect`
- 分支管理 `git branch`
- 更详细的分支信息`git show-branch`
  - 展示的是二位矩阵，破折号上面表示分支的信息，破折号下面表示提交的信息
  - `+`表示当前行的提交存在于当前列的分支中
  - `*`表示存在于活动分支中的提交
  - `-`表示当前提交为一个合并提交
- 分支合并 `git merge`
  - 冲突文件状态 `git status --merge`或者 `git ls-files -u`
- 重置状态 `git reset`
  - `--soft`, `index`和`working copy`保持不变，只改变HEAD
  - `--mixed` `working copy` 保持不变，HEAD和`index`改变
  - `--hard`  `working copy`,`index`,HEAD都会改变
- 引入提交的变更 `git cherry-pick`
  - `git cherry-pick commitid` 在当前分支引入`commitid`的变更
  - `git cherry-pick start..end` 新版的git中，支持引入一个范围的变更
- 以新的提交抵消特定提交 `git revert`
  - `git rebase branch` 让当前分支基于最新的 branch 
  - `git rebase base target` 使 `target` 分支基于 `base` 分支的最新提交
- 临时保持状态  `git stash`
- 引用日志 `git reflog`
- 获取引用信息 `git rev-parse`
- 远程版本信息管理 `git remote`
- 抓取远程信息 `git fetch`
- 抓取远程信息并合并 `git pull`
- 推送本地信息到远程 `git push`
- 修改版本库信息 `git filter-branch`
- `git rev-parse`
- `git rev-list`






### 参考资料

0. [Git Glossary](https://git-scm.com/docs/gitglossary/)