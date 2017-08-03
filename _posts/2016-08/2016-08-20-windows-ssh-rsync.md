---
layout: post
title: "windows ssh rsync 环境配置问题"
categories: [开发工具,开发环境]
tags: [ssh,rsync]
---

```
>rsync -rzcv ./ 192.168.237.72:/home/q/test_rsync
```

完整的命令:
```
rsync -rzcv -e 'ssh -i c:/users/liao.zhang/.ssh/id_rsa -l liao.zhang'  --rsync-path='sudo rsync' ./ 192.168.237.72:/home/q/test_rsync
```

## 1 ssh找不到对应的认证key文件


#### 1.0.1 错误信息如下：
```
Could not create directory '/home/liao.zhang/.ssh'.
The authenticity of host '192.168.237.72 (192.168.237.72)' can't be established.
RSA key fingerprint is 3d:dd:8c:32:31:c0:20:b9:96:f0:71:3e:79:a7:ae:2e.
Are you sure you want to continue connecting (yes/no)? yes
Failed to add the host to the list of known hosts (/home/liao.zhang/.ssh/known_hosts).
```

#### 1.0.2 解决办法：

windows中默认ssh 认证文件路径在C:/users/USERNAME/.ssh/中，而默认的路径是/home/username/.ssh，所以找不着，这时需要手动指定ssh认证文件路径
```
 ssh -i c:/users/USERNAME/.ssh/id_rsa
```

命令演变成：
```
rsync -rzcv -e 'ssh -i c:/users/liao.zhang/.ssh/id_rsa -l liao.zhang' ./ 192.168.237.72:/home/q/test_rsync
```

### 1.1 rsync没有权限


#### 1.1.1 错误信息如下
```
rsync: change_dir#1 "/home/q/test_rsync" failed: Permission denied (13)
rsync error: errors selecting input/output files, dirs (code 3) at main.c(534) [receiver=3.0.6]
rsync: connection unexpectedly closed (176 bytes received so far) [sender]
rsync error: error in rsync protocol data stream (code 12) at io.c(235) [sender=3.1.1]
```

#### 1.1.2 解决办法：

rsync 需要远程rsync进程做同步功能，远程的进程需要相关的权限，比如创建文件夹的权限。
在rsync中添加相关的授权信息，
```
rsync --rsync-path='sudo rsync'  src dst
```
这样远程的rsync进程就有相关的权限。

命令演变成：
```
rsync -rzcv -e 'ssh -i c:/users/liao.zhang/.ssh/id_rsa -l liao.zhang'  --rsync-path='sudo rsync' ./ 192.168.237.72:/home/q/test_rsync
```


### 1.2 Git Bash 下rsync 不能同步数据


#### 1.2.1 错误信息如下
```
$ fekit sync
[LOG] [调用] rsync -rzcv --chmod=a='rX,u+w' --rsync-path='sudo rsync' ./ 192.168.237.75:/home/q/www/qunarzz.com/package_b2c_admin/  --exclude=.svn --exclude=.git --temp-dir=/tmp
[LOG] [提示] 如遇问题参见 http://wiki.corp.qunar.com/display/fe/8+Trouble+shooting

C:\Users\liao.zhang\AppData\Roaming\npm\node_modules\fekit\lib\commands\sync.js:77
        throw err;
              ^
Error: Command failed: dup() in/out/err failed
rsync: connection unexpectedly closed (0 bytes received so far) [sender]
rsync error: error in rsync protocol data stream (code 12) at io.c(235) [sender=3.1.1]

    at ChildProcess.exithandler (child_process.js:648:15)
    at ChildProcess.emit (events.js:98:17)
    at maybeClose (child_process.js:756:16)
    at Socket.<anonymous> (child_process.js:969:11)
    at Socket.emit (events.js:95:17)
    at Pipe.close (net.js:465:12)
```

#### 1.2.2 解决过程

fekit sync 执行的命令如下
```
rsync -rzcv --chmod=a='rX,u+w' --rsync-path='sudo rsync' ./ 192.168.237.75:/home/q/www/qunarzz.com/package_b2c_admin/  --exclude=.svn --exclude=.git --temp-dir=/tmp
```

查看ssh命令的位置：
```
$ where ssh
C:\Program Files\Git\usr\bin\ssh.exe
D:\Applications\cwRsync_5.4.1_x86_Free\ssh.exe
```

在git bash下尝试指定ssh位置：
```
rsync -rzcv -e "d:/Applications/cwRsync_5.4.1_x86_Free/ssh.exe" --chmod=a='rX,u+w' --rsync-path='sudo rsync' ./ 192.168.237.75:/home/q/www/qunarzz.com/package_b2c_admin/  --exclude=.svn --exclude=.git --temp-dir=/tmp
```
执行能够成功。 说明这两个ssh 命令有区别，需要使用rsync自带的ssh。

通过调整环境变量的位置，发现对Git Bash使用的ssh 路径没有影响。

#### 1.2.3 解决办法

- 修改fekit的 ``` C:\Users\liao.zhang\AppData\Roaming\npm\node_modules\fekit\lib\commands\sync.js ```，将命令里面的ssh 替换为rsync 自带的ssh的绝对路径；
- 删除或者替换Git安装目录下的ssh 命令文件（记得备份）

### 1.3 ssh key 权限不对


#### 1.3.1 错误信息类似 permissions are too open，bad owner or permissions
```
Permissions 0770 for '/home/username/.ssh/id_rsa' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/home/username/.ssh/id_rsa": bad permissions
```

#### 1.3.2 解决办法

在windows 中各种chmod 不好使。
把.ssh目录的权限设置为只用当前用户有完全控制的权限即可。

具体操作办法:
- .ssh目录右键==>属性==>安全
  点击==>高级==> 禁用继承==>将以继承的权限 转换为此对象的显式权限
- .ssh目录右键==>属性==>安全
  点击==>编辑
  删除所有组或用户名下面的信息
  点击==>添加==>高级==>立即查找==>选中需要使用的用户（一般为当前用户）==>双击或者选中后确定
  将选中的用户勾选 完全控制 权限
  一路确定 完工。
