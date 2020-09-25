---
layout: post
title: "Win7 Win8 Win10非Administrator管理员获取管理员权限 打开应用需要开启账户控制"
categories: [操作系统]
tags: [管理员权限,非内置管理员,Administrator]

---

参考 [微软的官方文档](https://technet.microsoft.com/zh-cn/library/dd834795.aspx)

权限管理在每个操作系统里面都有，是一种比较安全的控制，微软的UAC，相当于类Unix系统中的sudo

### 1 内置管理员 Administrator 使用完整管理员权限
看[这个说明](https://technet.microsoft.com/zh-cn/library/dd834795.aspx)   
将 用户帐户控制：对内置管理员帐户使用管理员批准模式 禁用 则获取完整的管理员权限

当然 [这个选项](https://technet.microsoft.com/zh-cn/library/dd851609.aspx)    
用户帐户控制: 管理员批准模式中管理员的提升提示行为  不提示直接提升 才行

### 2 非内置管理员 获取管理员权限，同时开启账户控制（不开启不能使用内置的应用）

用户帐户控制:以管理员批准模式运行所有管理员  启用 则每次都需要经过权限批准 

有地方说设成 “禁用” [Win8非内置系统管理员获得完整权限的方法](http://it.oyksoft.com/post/6176/)，就可以获取完整的至高无上的管理员权限。

然而在使用内置应用的时候，会提示“在关闭了用户账户控制的情况下 无法打开xxxx”,这样包括Edge浏览器在内的许多应用都将无法使用，即使在控制面板中开启提示也没用

所以这个还是需要开启的。

当然 [这个选项](https://technet.microsoft.com/zh-cn/library/dd851609.aspx)    
用户帐户控制: 管理员批准模式中管理员的提升提示行为  不提示直接提升 才行

这样看来，开启账户控制，设置成批准不提示提升，才是解决问题的关键。

### 3 参考资料
0. [Win8非内置系统管理员获得完整权限的方法](http://it.oyksoft.com/post/6176/)
1. [Windows 用户帐户控制循序渐进指南](https://technet.microsoft.com/zh-cn/library/cc709691\(v=ws.10\).aspx#BKMK_1)
3. Windows 用户帐户控制循序渐进指南(https://technet.microsoft.com/zh-cn/library/cc709691\(v=ws.10\).aspx#BKMK_1)
2. [用户帐户控制：对内置管理员帐户使用管理员批准模式](https://technet.microsoft.com/zh-cn/library/dd834795.aspx)
