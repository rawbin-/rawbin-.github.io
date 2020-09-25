---
layout: post
title: "Win10 耳机 扬声器自动切换，插入耳机关闭扬声器"
categories: [应用技术,操作系统]
tags: [Win10,VIA,耳机,扬声器]
---

### 1 现象

Win10中的VIA驱动，插入耳机无法自动关闭扬声器，耳机没有声音；手动切换耳机为默认设备之后，插入插入耳机可以正常播放，但拔掉耳机无法自动切换到扬声器。

这个也是Win10自以为牛逼的改进功能，将扬声器和耳机作为了独立的设备，可以不共享音源。



### 2 解决办法

+ 自然是安装原版VIA驱动，安装完成之后就会有 VIA HD Audio Deck这个可以管理音频配置

+ 打开配置之后，都是一些属性配置，看看边边角角（我的在右下角）有没有带扳手或者齿轮或者设置的东东

+ 打开之后可能发现只能设置一个语言选项，别灰心

+ 将如下内容另存为，xxx.reg，然后管理员权限运行。

  ```
  Windows Registry Editor Version 5.00

  [HKEY_CURRENT_USER\System\CurrentControlSet\Services\VIA HD Audio Codec Default\VDeckINI\VDeck\NB_CASE]
  "Show_HP_Mode_Change_Option"="1"

  ```


+ 关闭VIA HD Audio Deck（右下角右键退出），然后再打开
+ 在同样的设置的地方（如果没有请重启或者过会再试，注册表有生效延迟），会有“喇叭和耳机设为同一音源”的选项，选中这个即可。



### 3 参考资料
0. [【麦·技术】解决WIN10系统 VIA HD驱动无法自动切换外置](http://tieba.baidu.com/p/4243795752)