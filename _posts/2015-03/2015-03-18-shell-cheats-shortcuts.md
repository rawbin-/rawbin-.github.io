---
layout: post
title:  "Bash Shell 命令行操作快捷键"
categories: [开发环境,开发工具,编程语言]
tags: [Linux, Shell,快捷键]
---



快速、高效 ^_^

---

## 方向移动
+ **Ctrl+A** 光标移动到行首，相当于Home键
+ **Ctrl+E** 光标移动到行尾，相当于End键
+ **Ctrl+B** 光标左移一个字符，相当于←方向键
+ **Ctrl+F** 光标右移一个字符，相当于→方向键
+ **Ctrl/Alt+←** 光标移动到前一个单词的开头
+ **Ctrl/Alt+→** 光标移动到后一个单词的结尾
+ **ESC+B** 光标移动到前一个单词的开头
+ **ESC+F** 光标移动到后一个单词的结尾
+ **Ctrl+X+X** 光标在光标所在字符和光标上一次所在字符之间切换 



## 字符删除/恢复（删除为剪切）
+ **Ctrl+D** 删除光标所在处的字符，并向右删除，相当于Del键；最后一次可退出当前Shell
+ **Ctrl+H** 删除光标左侧的字符，相当于Backspace
+ **Ctrl+K** 删除从光标所在处字符及其后的所有字符，相当于长按Del键
+ **Ctrl+U** 删除从光标所在处字符前面（到行首）的所有字符，相当于长按Backspace键
+ **Ctrl+L** 清屏，删除屏幕的所有输出，相当于Linux clear命令 或Windows cls命令
+ **Ctrl+W** 删除光标左侧的一个单词
+ **ESC+D**  删除光标右侧的一个单词
+ **Ctrl+?** 撤销前一次操作
+ **Ctrl+Y** 粘贴上一次剪切的内容

## 交换
+ **Ctrl+T** 交换光标前两个字符位置
+ **ESC+T**  交换光标前两个单词位置

## 命令相关快捷键
+ **Ctrl+C** 结束当前命令
+ **Ctrl+R** 在历史命令中查找,输入关键字过滤，重复命令进行滚动查找
+ **Ctrl+Z** 挂起/停止命令，前后台切换，可用fg，bg命令恢复
+ **Ctrl+P** 上一条命令，相当于↑方向键
+ **Ctrl+N** 下一条命令，相当于↓方向键

## 命令相关快捷操作
+ !! 执行上一条命令
```
apt-get install xxx
sudo !! #equals 'sudo apt-get install xxx'
```

+ !xxx 执行最近的以xxx开头的命令
+ !xxx:p 打印最近的以xxx开头的命令
+ !$ 上一条命令的最后一个参数
```
cat /etc/passwd
vim !$ #equals 'vim /etc/passwd'
```
+ !$:p 打印上一条命令的最后一个参数
+ !* 上一条命令的所有参数
+ !*:p 打印上一条命令的所有参数
+ ^xxx 删除上一条命令中的第一个xxx并执行
```
cat /etc/password
^or #equals 'cat /etc/passwd'
```
+ ^xxx^yyy 将上一条命令中的第一个xxx替换为yyy并执行                 
+ ^xxx^yyy^ 将上一条命令中的所有xxx替换为yyy并执行

## 其他

+ **Ctrl+S** 锁定终端
+ **Ctrl+Q** 解锁终端
+ **Ctrl+I** 自动补全，相当于Tab键
+ **Ctrl+J** 相当于Enter
+ **Ctrl+O** 相当于Enter
+ [explainshell.com shell命令文档解释](https://explainshell.com/)

## 参考文档：
0. [高级Bash编程指南](http://tldp.org/LDP/abs/html/index.html)
1. 《Linux与Unix Shell编程指南》
2. 《Linux 命令行和Shell脚本编程宝典》
3. [Linux Shell 快捷键1][1]
4. [Linux Shell 快捷键2][2]


[1]: http://blog.chinaunix.net/uid-361890-id-342066.html Linux Shell 快捷键
[2]: http://blog.sina.com.cn/s/blog_7556be720100ryp2.html linux下的shell 快捷键 