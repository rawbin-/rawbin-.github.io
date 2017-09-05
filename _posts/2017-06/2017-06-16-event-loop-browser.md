---
layout: post
title: "深入理解JavaScript事件循环之浏览器源码分析"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,事件循环,浏览器,源码分析]
---

### Chrome 版本比较

-   Chromium 不是Chrome，但Chrome的内容基本来源于Chromium，这个是开源的版本，小时级别的更新
-   Canary 是试验版，翻译过来就是金丝雀，金丝雀对瓦斯等毒气很敏感，浓度稍高就会停止鸣叫甚至挂掉，金丝雀是瓦斯等毒气检测的土办法，这个场景在《寻龙诀》中黄渤的操作中也能看到。哈哈 扯远了，这个是daily build 版本。
-   Dev 是开发版，weekly build版本
-   Beta 是测试版，monthly build版本
-   Stable 是稳定版，不定期更新，一般也是一个月左右一次
-   更新频率 Chromium > Chrome Canary > Chrome Dev > Chrome Beta > Chrome Stable
-   Chrome Dev、Chrome Beta 和 Chrome Stable三者只能同时出现一个
-   Chromium 、Chrome Canary 和 剩下的任意一个可共存

### Chrome/Chromium源码获取

+   可以说Chromium是Chrome的开源版本，但Chrome本身不开源

+   从[Chromium官网](https://www.chromium.org/) 点击`Chromium` 到[Chromium首页](https://www.chromium.org/Home)

+   选择[Get the Code: Checkout, Build, & Run Chromium](https://www.chromium.org/developers/how-tos/get-the-code)

+   这里我们以[Mac](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md) 环境为例，介绍源码获取

+   我们只需要获取源码不进行编译，可以简单点，如果需要编译可以参考[这里](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md)进行操作。

    +   首先配置好梯子，编辑`~/.gitconfig`，配置VPN代理

        ```
        [http]
        proxy = socks5://your-socks5-host:1080
        [https]
        proxy = socks5://your-socks5-host:1080
        ```

    +   首先获取搞源码的工具（需要自带梯子），`git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git`

    +   编辑``~/.bashrc` 或者 `~/.zshrc` （具体看到底用的哪个，都用可以都搞上），添加上面的工具路径

        ```
        export PATH=$PATH:/path/to/depot_tools
        ```

    +   找一个地方放Chromium的源码
        ```
        mkdir chromium && cd chromium
        fetch chromium --no-history
        ```

    +   更新现有的搞出来的Chromium源码

        ```
        git rebase-update
        gclient sync
        ```


### 源码结构







### 参考资料

0.  [Chromium 官网](https://www.chromium.org/)
1.  [Chromium 文档汇总](https://chromium.googlesource.com/chromium/src/+/master/docs/README.md#Preview-local-changes-using-md_browser)
2.  [Chromium 设计文档](https://sites.google.com/a/chromium.org/dev/developers/design-documents)
3.  [Chromium 开发者文档](https://sites.google.com/a/chromium.org/dev/developers)
4.  [Chromium 源代码结构](https://sites.google.com/a/chromium.org/dev/developers/how-tos/getting-around-the-chrome-source-code)