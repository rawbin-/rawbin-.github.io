---
layout: post
title: "Git常用操作命令梳理"
categories: [开发技术,开发环境,Git]
tags: [git,github]
---

### 1 基本操作

#### 1.1 仓库生成	

```shell
git init [DIR-NAME] //将当前目录，或者指定的DIR-NAME目录初始化为仓库
git clone REPOSITORY-URL [DIR-NAME] //将远端工程放入当前目录，或者重命名为指定的DIR-NAME的目录
```

#### 1.2 分支操作

```
git branch 显示本地分支，-r 显示远程分支 -a 显示所有分支
git branch NEW-BRANCH 以当前分支为基础创建一个分支
git branch NEW-BRANCH BASE-BRANCH 以BASE-BRANCH为基础创建一个分支
git checkout -b NEW-BRENCH 以当前分支为基础创建一个分支
git checkout -b NEW-BRANCH BASE-BRANCH 以BASE-BRANCH为基础创建一个分支
git checkout BRANCH-NAME 切换到分支
git branch -d BRANCH-NAME 删除BRANCH-NAME指定的分支（如果要删除的分支有内容未合并到当前分支，不能删除） -D表示无条件删除
```

#### 1.3 内容整合

```
git fetch [REPOSITORY-URL REMOTE-BRANCH] 取回远端分支内容
git merge ANOTHER-BRANCH 将ANOTHER-BRANCH的内容合并到当前分支
git pull [REPOSITORY-URL REMOTE-BRANCH:LOCAL-BRANCH] 将远端分支的内容取回并尝试合并
git mergetool 使用合并工具进行合并
```

#### 1.4 内容提交

```
git status  查看当前改动提交状态
git diff 对比工作区和暂存区，--cached 为比较暂存区和HEAD
git add . 添加所有改动到git 跟踪，指定FILEPATH则只添加指定内容
git commit -m "commit log" 提交所有改动到本地仓库， --amend 表示调整上一次提交
git push [REPOSITORY-URL LOCAL-BRANCH:REMOTE-BRANCH] 推动本地仓库信息到远端仓库
```



### 2 使用实践

#### 2.1 当前工作未完成，临时切换到其他分支

```
git stash 暂存当前未提交的内容
git stash save "stash log" 暂存内容并提供描述信息
git stash pop 将最后一个暂存的内容取出
git stash list 查看所有暂存
git stash pop [stash@{n}] 取出一个特定的暂存
```



#### 2.2 查找某处变更是哪次提交生成的（bug是谁在什么时候写的~）

##### 2.2.1 方法一

```
git blame FILE-NAME -L m,n 查看m到n行之间的最后一次变更的信息
```

##### 2.2.2 方法二

```
git log -L m,n:FILE-NAME 	查看文件的m到n行之间的变化
```

##### 2.2.3 方法三

```
git bisect start 开始一个二分查找
git bisect bad COMMIT-ID 设置一个已经出问题的版本
git bisect good COMMIT-ID 设置一个没有问题的版本
git bisect good|bad 持续的给结果，最后会定位到一个版本，结果跟方法二的类似
```

##### 2.2.4 方法四

```
git show :/KEY-WORD 查找关键信息的变更，这里给出的是一个大致信息，比如是在其他分支做出的改动
```

##### 2.2.5 方法五

```
git log -p -S "字符串" 查找字符串在整个提交历史的出现， -G "正则表达式"
```

#### 2.2   查看文件的历史 包括被删除的文件
```
git log -- FILE_NAME
git log --diff-filter=D --summary | grep FILE_NAME -C 5
git log --follow --graph --summary -- FILE_NAME
```



#### 2.3 检查当前分支内容是否包含了最新的主干内容

```
git cherry origin/master origin/BRANCH_NAME
git log origin/master ^origin/BRANCH_NAME –oneline
```







### 3 修改历史

+ 版本信息的引用

  + 相对一个版本 比如 HEAD^
  + 相对N个版本 比如 HEAD@{10}
  + 相对N个版本 比如 HEAD~10
  + 相对N个版本 比如 HEAD@{5}^~5

+ 版本区间 

  + A..B, A不可达&&B可达的的范围，如果是同一条主线上，则是前开后闭区间，否则视情况而定
  + ^A B 等同于 A..B
  + B —not A 等同于 A..B
  + A…B  A可达&&B可达除掉AB都可达的范围，前后闭区间

  

#### 3.1 修改已经提交的log信息

##### 3.1.1 修改上次的log

```
git commit --amend
git push -f 谨慎谨慎，只有提交了远端仓库才需要这个
```

##### 3.1.2 合并上几次的log,原来五条

```
git reset HEAD^^~3 在当前分支回退五个版本的提交历史
git commit -m "combined commit" 
git push -f git push -f 谨慎谨慎，只有提交了远端仓库才需要这个
```

#### 3.2 修改提交内容的历史

+ 现有的版本A,B,C,D,E
+ 删掉C的提交内容

##### 3.2.1 使用revert

```
git revert C 简单，但会增加一次提交，而且C原来的提交也会留在历史中
```

##### 3.2.2 使用rebase

```
git rebase -i B  删掉想要删掉的C提交，然后保存
```

##### 3.2.3 使用cherry-pick

```
git checkout B 先切到D之前的提交
git cherry-pick C..E 将后面的提交再拉进来
git checkout -b xxx 放入新的分支中
```





### 4 参考资料

1. [git docs](https://git-scm.com/docs)
2. [git docs](<https://mirrors.edge.kernel.org/pub/software/scm/git/docs/>)
3. [Pro Git 2nd](https://git-scm.com/book/en/v2)
4. [git community book中文](http://git.seyren.com/index.html)
5. [Git - Tutorial](http://www.vogella.com/tutorials/Git/article.html)
6. 《Git权威指南》
7. 《版本控制之道-使用Git》
8. [git 相关书籍](https://git-scm.com/doc/ext)
9. [github漫游指南](http://github.phodal.com/)
10. [github漫游指南](https://github.com/phodal/github-roam)
11. [github秘籍](http://blog.csdn.net/x805433354/article/details/41214895)
12. [git工作流指南](http://blog.jobbole.com/76843/)
13. [github help](https://help.github.com/)
14. [怎样使用github](http://www.zhihu.com/question/20070065)
15. [github使用指南](https://github.com/NeuOL/neuola-legacy/wiki/github%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97https://github.com/NeuOL/neuola-legacy/wiki/github%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97)
16. [github中fork和更新原作者变更](http://my.oschina.net/u/2306127/blog/369167?fromerr=TmdohiO0)


