---
layout: post
title: "使用Beyond Compare作为git diff merge 工具"
categories: [开发环境]
tags: [git,diff,merge,beyondcompare]

---

### 1 直接修改.gitconfig 一般修改全局路径下的

#### Windows下配置

    [diff]
        tool = bc
    [difftool "bc"]
        path = C:\\Program Files\\Beyond Compare 4\\BCompare.exe
    [merge]
        tool = bc
    [mergetool "bc"]
        path = C:\\Program Files\\Beyond Compare 4\\BCompare.exe

#### Mac下配置

要生效的话需要在beyond compare 菜单中，选择 install command line tool 安装可在命令行调用的可执行文件

```
[diff]
    tool = bc
[difftool]
    prompt = false
[difftool "bc"]
    path = /usr/local/bin/bcomp
[merge]
    tool = bc
[mergetool "bc"]
    path = /usr/local/bin/bcomp
```



[Using Beyond Compare with Version Control Systems](http://www.scootersoftware.com/support.php?zz=kb_vcs#gitwindows)
[Git下使用Beyond Compare作为比较和合并工具](http://sinojelly.blog.51cto.com/479153/633495/)