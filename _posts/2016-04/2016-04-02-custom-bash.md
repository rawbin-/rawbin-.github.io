---
layout: post
title: "自定义Mac Bash补全和高亮显示"
categories: [操作系统,开发环境]
tags: [Bash,Git,Git Bash]
---

### Git命令自动补全

- 找到 git-completion.bash

  ​

  ```
  # 得到git completion脚本，拷贝一份放到自己的位置
  find / -name git-completion.bash 

  # 将脚本加入用户bash环境变量
  vim ~/.bashrc

  # 填入内容并保存
  . PATH_TO_git-completion.bash
    
  # 使配置生效，而不用重启或者重新登录
  source ~/.bashrc
  ```

- 输入git 然后用tab补全    


-  [参考资料中文](https://git-scm.com/book/zh/v2/%E5%85%B6%E5%AE%83%E7%8E%AF%E5%A2%83%E4%B8%AD%E7%9A%84-Git-Bash-%E4%B8%AD%E7%9A%84-Git) [参考资料英文](https://git-scm.com/book/en/v2/Git-in-Other-Environments-Git-in-Bash)

### Git显示分支信息

- 同样的方法和路径找到git-prompt.sh

- 在同样的文件中加入`.bashrc` 内容如下

  ```
  # Git AutoComplete
  . ~/CustomScripts/git-completion.bash

  # Git Prompt
  . ~/CustomScripts/git-prompt.sh
  export GIT_PS1_SHOWDIRTYSTATE=1
  export GIT_PS1_SHOWSTASHSTATE=1
  export GIT_PS1_SHOWUNTRACKEDFILES=1
  export GIT_PS1_SHOWUPSTREAM="auto"
  export GIT_PS1_SHOWCOLORHINTS=1
  export PS1='\w$(__git_ps1 " (%s)")\$ '
  ```


-  [参考资料中文](https://git-scm.com/book/zh/v2/%E5%85%B6%E5%AE%83%E7%8E%AF%E5%A2%83%E4%B8%AD%E7%9A%84-Git-Bash-%E4%B8%AD%E7%9A%84-Git) [参考资料英文](https://git-scm.com/book/en/v2/Git-in-Other-Environments-Git-in-Bash)



### Bash 终端设置显示颜色

- 执行命令

  ```
  brew install xz coreutils
  gdircolors --print-database > ~/.dir_colors
  ```

  ​


- 编辑 ~/.bash_profile 并加入如下内容

  ```
  if brew list | grep coreutils > /dev/null ; then
    PATH="$(brew --prefix coreutils)/libexec/gnubin:$PATH"
    alias ls='ls -F --show-control-chars --color=auto'
    eval `gdircolors -b $HOME/.dir_colors`
  fi
  alias grep='grep --color'
  alias egrep='egrep --color'
  alias fgrep='fgrep --color'
  ```

  ​


- [让Mac OS X的终端多姿多彩](http://blog.csdn.net/windows1989/article/details/8882642)
- [让Mac OS X的终端多姿多彩](http://linfan.info/blog/2012/02/27/colorful-terminal-in-mac/)
- [参考资料](http://jyhong.blog.ustc.edu.cn/2015/02/bash-colors-setting/)
- [LS_COLORS](http://www.cnblogs.com/xlmeng1988/archive/2013/01/08/shell_color.html)



### VIM 设置

- 编辑.vimrc

  ```
  syntax on
  set nu
  ```

  ​

### Bash 命令提示符显示颜色

 习惯了gitbash 的彩色，在这里面用着总是不舒服，设置一个彩色的命令提示符。

- 编辑.bashrc

  ```
  export PS1='\n\[\033[32m\]\u@\h\[\033[33m\]\w\[\33[36m\]$(__git_ps1 " (%s)") \[\033[0m\]\n\$ '
  ```


- 编辑.bash_profile

  ```
  if [ -f ~/.bashrc ];then
     . ~/.bashrc
  fi
  ```

  ​

- [参考资料](http://jyhong.blog.ustc.edu.cn/2014/05/bash-it%E7%AE%80%E4%BB%8B%EF%BC%88%E7%BB%88%E7%AB%AF%E7%BE%8E%E5%8C%96%EF%BC%89/)

- [bash documentation](https://www.gnu.org/software/bash/manual/bashref.html)

- [表示的含义Controlling-the-Prompt](https://www.gnu.org/software/bash/manual/bashref.html#Controlling-the-Prompt)