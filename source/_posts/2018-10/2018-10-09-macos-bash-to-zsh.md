---
layout: post
title: "MacOS命令行bash切换为zsh"
categories: [前端开发,Web开发,JavaScript,代码规范]
tags: [JavaScript,NodeJS,TypeScript,代码规范]
---



为了更高效的使用命令行，将默认Shell从`bash` 切换为`zsh`，具体的可以简单参考下[这里](https://www.zhihu.com/question/21418449)

变更比较多的话可以用[这个](https://github.com/zsh-users/antigen) 来管理

# 安装

## 安装zsh

````
brew install zsh
````

## 安装 ohmyzsh  一个增强的配置框架

可能需要梯子，参考[这里](https://rawbin-.github.io/web%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/javascript/2017/12/01/webkit-src-overview/)

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 安装额外的自定义插件

```
brew install zsh-syntax-highlighting
brew install zsh-autosuggestions
```

# 配置

## 配置zsh插件

```
vim ~/.zshrc
```

到[这里](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins), 凭感觉选就行，

```
plugins=(
        colored-man-pages
        git
        git-prompt
        gitfast
        node
        npm
        nvm
        osx
        screen
        sudo
        urltools
        vscode
        yarn
        z
        )
```

 将下面几行加在`.zshrc `的末尾

```
source /usr/local/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

##  迁移配置

将`~/.bashrc` 中的内容，迁移到`~/.zshrc`中，删除针对`bash`的 配置

## 使配置生效

```
source ~/.zshrc
```



