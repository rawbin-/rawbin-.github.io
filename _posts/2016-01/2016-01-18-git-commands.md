---
layout: post
title: "git操作命令小结"
categories: [开发技术,开发环境,Git]
tags: [git,github]
---

### 基本操作

#### 仓库生成	

```shell
git init [DIR-NAME] //将当前目录，或者指定的DIR-NAME目录初始化为仓库
git clone REPOSITORY-URL [DIR-NAME] //将远端工程放入当前目录，或者重命名为指定的DIR-NAME的目录
```

#### 分支操作

```
git branch 显示本地分支，-r 显示远程分支 -a 显示所有分支
git branch NEW-BRANCH 以当前分支为基础创建一个分支
git branch NEW-BRANCH BASE-BRANCH 以BASE-BRANCH为基础创建一个分支
git checkout -b NEW-BRENCH 以当前分支为基础创建一个分支
git checkout -b NEW-BRANCH BASE-BRANCH 以BASE-BRANCH为基础创建一个分支
git checkout BRANCH-NAME 切换到分支
git branch -d BRANCH-NAME 删除BRANCH-NAME指定的分支（如果要删除的分支有内容未合并到当前分支，不能删除） -D表示无条件删除
```

#### 内容整合

```
git fetch [REPOSITORY-URL REMOTE-BRANCH] 取回远端分支内容
git merge ANOTHER-BRANCH 将ANOTHER-BRANCH的内容合并到当前分支
git pull [REPOSITORY-URL REMOTE-BRANCH:LOCAL-BRANCH] 将远端分支的内容取回并尝试合并
git mergetool 使用合并工具进行合并
```

#### 内容提交

```
git status  查看当前改动提交状态
git diff 对比工作区和暂存区，--cached 为比较暂存区和HEAD
git add . 添加所有改动到git 跟踪，指定FILEPATH则只添加指定内容
git commit -m "commit log" 提交所有改动到本地仓库， --amend 表示调整上一次提交
git push [REPOSITORY-URL LOCAL-BRANCH:REMOTE-BRANCH] 推动本地仓库信息到远端仓库
```



### 使用实践

#### 当前工作未完成，临时切换到其他分支

```
git stash 暂存当前未提交的内容
git stash save "stash log" 暂存内容并提供描述信息
git stash pop 将最后一个暂存的内容取出
git stash list 查看所有暂存
git stash pop [stash@{n}] 取出一个特定的暂存
```



####查找某处变更是哪次提交生成的（bug是谁在什么时候写的~）

##### 方法一

```
git log -L m,n:FILE-NAME 	查看文件的m到n行之间的变化
```

##### 方法二

```
git show :/KEY-WORD 查找关键信息的变更，这里给出的是一个大致信息，比如是在其他分支做出的改动
```

##### 方法三

```
git bisect start 开始一个二分查找
git bisect bad COMMIT-ID 设置一个已经出问题的版本
git bisect good COMMIT-ID 设置一个没有问题的版本
git bisect good|bad 持续的给结果，最后会定位到一个版本，结果跟方法二的类似
```

##### 方法四

```
git blame FILE-NAME -L m,n 查看m到n行之间的最后一次变更的信息
```



### 参考资料

1. [git docs](https://git-scm.com/docs)
2. [github漫游指南](http://github.phodal.com/)
3. [github漫游指南](https://github.com/phodal/github-roam)
4. [github秘籍](http://blog.csdn.net/x805433354/article/details/41214895)
5. [git工作流指南](http://blog.jobbole.com/76843/)
6. [github help](https://help.github.com/)
7. [怎样使用github](http://www.zhihu.com/question/20070065)
8. [github使用指南](https://github.com/NeuOL/neuola-legacy/wiki/github%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97https://github.com/NeuOL/neuola-legacy/wiki/github%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97)
9. [github中fork和更新原作者变更](http://my.oschina.net/u/2306127/blog/369167?fromerr=TmdohiO0)

#### 

