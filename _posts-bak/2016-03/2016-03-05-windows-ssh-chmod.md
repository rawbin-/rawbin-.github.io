---
layout: post
title: "Windows SSH chmod 600"
categories: [开发环境,开发技术,应用技术]
tags: [windows,ssh,chmod]
---

### 错误提示

在rsync使用过程中，配置相关SSH信息的时候得到的错误信息：

+ Permissions are too open
+ Bad owner or permissions

### 解决办法

在windows 中各种chmod 不好使。

把.ssh目录的权限设置为只用当前用户有完全控制的权限即可。

具体操作办法:

+ .ssh目录右键==>属性==>安全
  + 点击==>高级==> 禁用继承==>将以继承的权限 转换为此对象的显式权限
+ .ssh目录右键==>属性==>安全
  + 点击==>编辑
  + 删除所有组或用户名下面的信息
  + 点击==>添加==>高级==>立即查找==>选中需要使用的用户（一般为当前用户）==>双击或者选中后确定
  + 将选中的用户勾选 完全控制 权限
  + 一路确定 完工。



### 参考资料

1. [SSH Keys](http://bodhizazen.net/Tutorials/SSH_keys)
2. [ssh “permissions are too open” error [closed]](http://stackoverflow.com/questions/9270734/ssh-permissions-are-too-open-error)
3. [Windows CHMOD 600](http://stackoverflow.com/questions/5264595/windows-chmod-600)
4. [windows7 下 Bad owner or permissions 问题](http://wenzhixin.net.cn/2014/01/15/windows7_rsync_error)
5. [Bad owner or permissions on /Users/username/.ssh/config](https://github.com/ddollar/heroku-accounts/issues/15)
6. [Why cannot chmod in cygwin on Windows 8 CP?](http://stackoverflow.com/questions/9561759/why-cannot-chmod-in-cygwin-on-windows-8-cp)
7. [cygwin在windows8.1上的chmod无法修改权限](http://zengrong.net/post/2048.htm)
8. [“Bad owner or permissions” error using Cygwin's ssh.exe](http://superuser.com/questions/348694/bad-owner-or-permissions-error-using-cygwins-ssh-exe)