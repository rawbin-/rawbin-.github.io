---

layout: post
title: "windows 下 使用Beyond Compare作为git diff merge 工具"
categories: [开发环境]
tags: [git,diff,merge,beyondcompare]

---


### 直接修改.gitconfig 一般修改全局路径下的
    
    [diff]
        tool = bc
    [difftool "bc"]
        path = C:\\Program Files\\Beyond Compare 4\\BCompare.exe
    [merge]
        tool = bc
    [mergetool "bc"]
        path = C:\\Program Files\\Beyond Compare 4\\BCompare.exe


[Using Beyond Compare with Version Control Systems](http://www.scootersoftware.com/support.php?zz=kb_vcs#gitwindows)
[Git下使用Beyond Compare作为比较和合并工具](http://sinojelly.blog.51cto.com/479153/633495/)