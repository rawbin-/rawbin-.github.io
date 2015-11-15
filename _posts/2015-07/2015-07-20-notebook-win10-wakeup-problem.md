---
layout: post
title: "升级Win10 后 休眠 睡眠 无法唤醒的问题解决"
categories: [应用技术,操作系统]
tags: [睡眠,休眠,无法唤醒]

---


解决办法参考 [惠普官方论坛](http://h30471.www3.hp.com/t5/forums/forumtopicpage/board-id/Notebook/message-id/335162#M335162)

### 操作
+ 到[Intel Management Engine下载](https://downloadcenter.intel.com/search?keyword=intel+management+engine) 下载最新版的驱动，下载后解压待用
+ 在命令行输入 devmgmt.msc 打开设备管理器，或者我的电脑右键管理，设备管理器
+ 在 系统设备 下找到 Intel(R) Management Engine Interface,右键更新驱动
+ 找到刚刚解压的驱动目录，安装即可，可能需要重启
+ 参考版本 2015/8/31 11.0.0.1166 版本好使

### 参考资料
0. [惠普客服支持](http://support.hp.com/cn-zh/document/c04775412/)
1. [HP电脑-睡眠和休眠的问题](http://support.hp.com/cn-zh/document/c03544205)
2. [惠普电脑驱动下载](http://support.hp.com/cn-zh/document/c03544205)
4. [Win10 无法关机和无法唤醒问题解决](http://h30471.www3.hp.com/t5/forums/forumtopicpage/board-id/Notebook/message-id/335162#M335162)
5. [Intel Management Engine下载](https://downloadcenter.intel.com/search?keyword=intel+management+engine)