---
layout: post
title: "命令行命令错误解决"
categories: [应用技术]
tags: [命令行,rsync,ssh]

---

### SSH 无法认证，找不到认证文件
windows中默认ssh 认证文件路径在C:/users/USERNAME/.ssh/中，而默认的路径是/home/username/.ssh，所以找不着，这时需要手动指定ssh认证文件路径
    ssh -i c:/users/USERNAME/.ssh/id_rsa 私钥文件路径

### rsync 远程sudo权限
rsync 需要远程rsync进程做同步功能，远程的进程需要相关的权限，比如创建文件夹的权限。
在rsync中添加相关的授权信息，
    rsync --rsync-path='sudo rsync'  src dst

这样远程的rsync进程就有相关的权限。



## 参考资料
0. [Linux Man rsync](http://linux.die.net/man/1/rsync)
1. [rsync documentation](https://rsync.samba.org/documentation.html)
2. [rsync use sudo](http://serverfault.com/questions/151464/how-can-make-rsync-use-sudo)
3. [rsync and sudo over ssh](https://crashingdaily.wordpress.com/2007/06/29/rsync-and-sudo-over-ssh/)
4. [rsync add sudo](http://www.theodo.fr/blog/2011/01/transferring-read-protected-files-with-rsync-and-sudo/)
5. [rsync permission visia sssh](http://askubuntu.com/questions/91838/rsync-permission-issues-via-ssh)
6. [rsync permission denied](http://www.linuxquestions.org/questions/linux-server-73/rsync-permission-denied-906421/)
7. [rsync remote over ssh](http://stackoverflow.com/questions/21639906/rsync-remote-files-over-ssh-to-my-local-machine-using-sudo-privileges-on-local)
8. [rsync root and sudo](http://www.pplux.com/2009/02/07/rsync-root-and-sudo/)
9. [rsync with root permission on remote machine](http://superuser.com/questions/270911/run-rsync-with-root-permission-on-remote-machine)