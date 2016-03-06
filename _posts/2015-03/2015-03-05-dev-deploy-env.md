---
layout: post
title: "Linux 自定义动态链接库 32位，64位"
categories: [操作系统]
tags: [动态链接库, IA32]
---

### 解决的问题
在64位linux运行可执行文件的时候，出现no such file or directory，文件明明存在，肯定有其他问题。

### 解决路径
+ 查看文件的依赖: `ldd xxx` [ldd][0]，发现找不到动态链接库
+ 查看文件信息： `file xxx` [file][4]，发现时32位环境编译的
+ 查看文件加载信息： `readelf -l xxx |grep interpreter` [readelf][4]，发现是存在的
+ 把没有的库加上，安装ia32libs 或者到其他机子上把想要的动态库拿过来
+ 在/etc/ld.so.conf中加入文件路径，最好在/etc/ld.so.conf.d/下新建一个文件，然后将我们的动态链接库的路径写进去
+ 运行`ldconfig` [ldconfig][5]，将我们的库加载进来 
+ 查看文件的依赖: `ldd xxx` [ldd][0]，看看还有没有not found
+ happy 运行~

建议： 部署这样程序的时候，自己带上自己需要的库，并加载到系统中



### 参考
0. [ bash: ***: No such file or directory][2]
1. [cannot open shared object file: No such file or directory解决][1]
2. [Linux 共享库][6]

[0]: http://man.linuxde.net/ldd "ldd"
[1]: http://www.2cto.com/os/201306/222628.html "cannot open shared object file: No such file or directory解决"
[2]: http://blog.csdn.net/dlutxie/article/details/8645051 " bash: No such file or directory"
[4]: http://man.linuxde.net/file "file" "readelf"
[5]: http://man.linuxde.net/ldconfig "ldconfig"
[6]: http://blog.csdn.net/ming470612141/article/details/3348063 "Linux 共享库"