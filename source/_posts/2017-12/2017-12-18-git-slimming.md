---
layout: post
title: "git 仓库瘦身方法及相关命令解析"
categories: [Web开发,前端开发,JavaScript,自动化]
tags: [Git,仓库,瘦身]
---

### 前言

其实有更简单和高效的工具能搞这事[BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/), 本文纯粹是为了用Git来解决这事，而不是单纯的解决这事。 

### 相关命令

#### Shell 相关

-  ```` `` 反引号(ESC旁边)表示命令替换 ,`$()`表达同样的意思，在需要命令执行结果的时候使用，比如 `echo $(whoami)`
-  `|` 管道运算符，将上一条命令的执行结果

#### **`git-verify-pack`** [ref](https://git-scm.com/docs/git-verify-pack)  获取所有文件信息，可用`git verify-pack --help`查看帮助

- `-v` `--verbose` 展示输出结果

```
git verify-pack [-v|--verbose] [-s|--stat-only] [--] <pack>.idx
```

命令输出文件列含义和顺序如下:

```
SHA-1 type size size-in-packfile offset-in-packfile
```

#### **`sort`**  `bash`命令，对输入进行排序，可用`sort --help`查看帮助

- `-n` `--numeric-sort` 用数值来比较
- `-r` `--reverse` 倒序
- `-u` `--unique`
- `-k`  `--key=KEYDEF` ，`KEYDEF`的格式是`F[.C][OPTS][,F[.C][OPTS]]` , `F(field)`表示是第几列（默认是`1`），`C(character)`表示当前列的第几个字符（默认是`1`），`OPTS`表达排序顺序，跟`sort`本身的排序顺序表达一致(可选这些参数`[bdfgiMhnRrV]`)，
- 我们这里用到的是第三列(size)并且用数值比较，所以命令会是`sort -k 3 -n`，命令也可以合写 `sort -k3n`


#### **`head`** / **`tail`** 命令  截取输入中的部分结果，可用`head --help` 和`tail --help`查看帮助

- `head`和`tail`参数含义相似

- `head`参数 ，`-n`,` --lines=[-]NUM`  行数前面没有减号，表示取前`n`行; 行号前面带减号表示从头开始取到倒数第`n`行止

- `tail`参数 ，`-n`,` --lines=[+]NUM`  行数前面没有加号，表示取后`n`行; 行号前面带加号表示从第`n`行开始取到结尾

  

#### **xargs** 构建参数
- `-n <number>` 一次取几个作为参数



#### **`git-rev-list`** 按时间倒序列出提交对象

- `--all` 列出所有能显示的引用
- `--objects` 列出提交对象引用的对象ID



#### **`awk`** 命令行下强大的文本处理工具 [ref](https://www.gnu.org/software/gawk/manual/gawk.html), 同时也是一个脚本解析命令，有自己的脚本语法规则

- 常用模式`awk '[<pattern>]{ <action> }'`
- 使用`$+列序号`来引用每一行对应列的数据，比如` $1`为第一列内容，`$2`为第二列内容
- `NF`特殊表示列数，`$NF`表示最后一列内容; `NR`是已经处理的行数



#### **`git-filter-branch`**  [ref](https://git-scm.com/docs/git-filter-branch), 重写分支版本历史信息

- `--tree-filter <command>` 用来重写树及相关内容的过滤器，参数会经过shell解析
- `--index-filter <command>` 用来重写`index`的过滤器，跟`tree-filter`相似，但不`迁出`树对象，所以会更快；经常与`git rm --cached --ignore-unmatch ...`联用  
- `--tag-name-filter <command>`  `--tag-name-filter cat`更新tag相关信息
- `prune-empty` 删除由于`<command>` 操作产生的空提交
- 注意文档中这一小节`CHECKLIST FOR SHRINKING A REPOSITORY`



#### **git-for-each-ref**  处理每一个引用点

- `--format=<format>`   `%(refname)` 模板字符串



#### **git-update-ref** 更新引用对象名称

- `-d` 删除引用



#### **`git-rm`**  [ref](https://git-scm.com/docs/git-rm) 从工作区和暂存区删除文件

- `-f` `--force` 强制删除
- `-r` 递归删除目录下的内容
- `--cached` 仅仅从暂存区删除，工作区的内容不变
- `--ignore-unmatch` 没有匹配到文件也算成功执行



#### **git-fsck** 检查数据对象的连接性和有效性

- `--full` 检查所有的数据对象
- `--unreachable` 打印不可达的数据对象



#### **`git-reflog`** 引用日志信息管理

- `expire` 子命令
  - `--all` 处理所有引用的引用日志
  - `--expire=<time>` 处理比设置的时间旧的入口



#### **`git-gc`** 清理不必要的文件，优化本地仓库

- `--aggressive`  强化本地仓库优化，执行时间更长
- `--prune=<date>` 处理指定时间之前的



#### **git-clone** [ref](https://git-scm.com/docs/git-clone) 克隆远端仓库

- `--bare` 不带这个选项是创建一个目标目录，同时目录下包含一个`.git`的目录，记录仓库相关信息，其他代码相关内容跟`.git`目录在同一目录下；使用这个选项，就没有其他代码相关内容了，也没有`.git`这个目录了，源来`.git`下的内容直接在这个目标目录下。所有`refs/remotes/origin`远端分支直接拷贝作为本地引用在`refs/heads`，就没有`refs/remotes/`了，其他跟这个相关的内容也没了。
- `--mirror` 这也是 `—bare`模式的一种，但有所有远程跟踪分支引用相关的映射关系，并可以通过`git remote update`来更新。
- `-o <name>` `--origin <name>` 改变默认的`origin`命名
- `-b <name>`  `--branch <name>` 切换到指定的分支
- `<repository>` 仓库路径，支持`ssh`,`git`,`http[s]`,`ftp[s]` 和类`scp`的服务器路径，当然也支持本地路径绝对路径或者`file`协议的路径
- `<directory>` 目标目录名



#### **git-push** 推送到远端仓库

- `<repository>` 目标远端仓库地址
- `<src>:<dst>` 本地源（可以是分支也可以是任意版本引用）和远程目标分支，均不写的话均为当前分支；如果`<src>`不写而只写`:<dst>`表示删除远端`dst`分支
- `--all` 推送所有本地存在的分支到远程，本地分支在`refs/heads`
- `--purne` 清理没有本地分支的远程分支
- `--mirror` 将所有引用(所有`refs`下的内容，包括但不限于`refs/heads`,`refs/remotes`,`refs/tags`，注意区别`--all`)镜像到远端
- `-n` `--dry-run` 走个过程，不搞实质性内容，在搞尝试性内容时很管用，很多地方都有这个选项

### 利用命令进行处理

#### 拿到相关文件路径

- 列出 git 对象及相关信息 `git verify-pack -v .git/objects/pack/*.idx`
- 将 git 对象按实际大小从小到大排列 `git verify-pack -v .git/objects/pack/*.idx | sort -k3n`
- 根据输出的情况，看需要选取的条件
  - 比如取最大20个`git verify-pack -v .git/objects/pack/*.idx | sort -k3n | tail -20`
  - 我这里取大于2M的看看情况  `git verify-pack -v .git/objects/pack/*.idx | sort -k3n | awk '$3 > 2 * 1024 * 1024 {print}'`
- 从提交对象中过滤出符合条件的提交 `git rev-list --objects --all | grep "$(git verify-pack -v .git/objects/pack/*.idx | sort -k3n | awk '{if(NF > 2 && ($3 > 2 * 1024 * 1024) && length($1) == 40) {print $1}}')"` (加入了过滤无关数据的条件) 
- 把响应结果写入到文件`result.txt`  `git rev-list --objects --all | grep "$(git verify-pack -v .git/objects/pack/*.idx | sort -k3n | awk '{if(NF > 2 && ($3 > 2 * 1024 * 1024) && length($1) == 40) {print $1}}')" > result.txt` 得到的结果是包含 hash 和路径的数据列表
- 取出大文件的路径结果，看看哪些可以删掉的 `cat result.txt | awk '{print $2}' | sort -u`

#### 删除相关路径的历史提交

- 从上面的路径里面找出可以删除的路径，通过下面的方法删除
- 通提交历史中删除对应路径的命令 `git filter-branch -f --prune-empty --index-filter 'git rm -rf --cached --ignore-unmatch <path-to-remove-from-log>' -- --all`

#### 后续相关清理

- `git reflog expire --expire=now --all` 重新整理所有日志
- `git gc --prune=now --aggressive` 重新整理仓库文件存储

#### 同步最新结果到远端

- `git push --all --force`   仓库大会很慢，耐心等
- `git push --tags --force`

#### 完整的命令列表

```
git rev-list --objects --all | grep "$(git verify-pack -v .git/objects/pack/*.idx | sort -k3n | awk '{if(NF > 2 && ($3 > 2 * 1024 * 1024) && length($1) == 40) {print $1}}')" > result.txt
cat result.txt | awk '{print $2}' | sort -u
git filter-branch -f --index-filter 'git rm -rf --cached --ignore-unmatch dist*.zip dist/*.zip output.zip node_modules dist' --prune-empty --tag-name-filter cat -- --all
git for-each-ref --format="%(refname)" refs/original/ | xargs -n 1 git update-ref -d ## 这步搞完就没有上一步的备份了，最好先单独备份一把，也只有搞了瘦身成效才明显
git reflog expire --expire=now --expire-unreachable=all --all
git fsck --full --unreachable
git repack -A -d
git gc --prune=now --aggressive
git push --all --force 
git push --tags --force
```



### 然并卵(有插曲)

#### 插曲现象

- 可以`clone` 这个`rawbin-/git-rewrite-history-test`仓库做测试，一套搞完没有效果，数据对象还在

  ```
  git clone git@github.com:rawbin-/git-rewrite-history-test.git
  cd git-rewrite-history-test
  git rev-list --objects --all  ## 查看对象，包含了dist目录和dist.zip的内容
  git filter-branch -f --index-filter 'git rm -rf --cached --ignore-unmatch dist*.zip dist/*.zip output.zip node_modules dist' --prune-empty --tag-name-filter cat -- --all
  git reflog expire --expire=now --expire-unreachable=all --all
  git fsck --full --unreachable
  git repack -A -d
  git gc --prune=now --aggressive
  git rev-list --objects --all
  
  ```

- (测出来一个可行结果) ，只需要把命令执行两遍，数据对象能被删掉

  ```
  git clone git@github.com:rawbin-/git-rewrite-history-test.git
  cd git-rewrite-history-test
  git rev-list --objects --all  ## 看看对象列表的情况 
  git filter-branch -f --index-filter 'git rm -rf --cached --ignore-unmatch dist*.zip dist/*.zip output.zip node_modules dist' --prune-empty --tag-name-filter cat -- --all
  git rev-list --objects --all  ## 对象列表中还是存在的
  git filter-branch -f --index-filter 'git rm -rf --cached --ignore-unmatch dist*.zip dist/*.zip output.zip node_modules dist' --prune-empty --tag-name-filter cat -- --all
  git rev-list --objects --all  ## 对象列表中已经被删掉了
  
  ```

#### 解释原因

- 一套搞完数据对象还在的原因是`git filter-branch`执行结果后再`.git/refs/original`里面还有备份，这里面的备份还会引用原有的数据对象，哪怕是经过了日志整理和垃圾回收，这些引用都会一直在以备恢复。
- 执行两遍能删除数据对象，是因为强制模式下的`git filter-branch`第二次会覆盖前一次的备份，覆盖完了之后就没有了备份，也就没有了引用，也就能全部删掉了，也就被删掉了。但整体的空间还是没有回收回来，还需要日志整理和垃圾回收



### 后记

Git的几个核心概念：

- `Blob` 存储的文件内容，文件名和文件系统相关系统没放在这，在整个仓库里只要文件内容相同，在Git中只存储一份内容，通过ID引用
- `Tree` 包含了对整个目录和目录下的`Blob`相关的名称和权限信息
- `Commit` 是对`Tree`的引用，同时包含作者、提交人、提交日志及相关的父提交等信息
- `Tag` 是对Commit的引用，同时包含Tag名称、备注和打Tag的人等信息
- `refs` 对其他对象的引用，一般是`Commit`, 使用对应`Commit`的ID引用



### 参考资料

- [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)
- [Removing sensitive data from a repository](https://help.github.com/en/articles/removing-sensitive-data-from-a-repository)
- [Maintaining a Git Repository](https://confluence.atlassian.com/bitbucket/maintaining-a-git-repository-321848291.html)
- [git filter-branch](https://git-scm.com/docs/git-filter-branch)
- [Git Tools - Rewriting History](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History)
- [git remove from history](https://blog.ostermiller.org/git-remove-from-history)
- [remove files from git history](Remove files from git history)


