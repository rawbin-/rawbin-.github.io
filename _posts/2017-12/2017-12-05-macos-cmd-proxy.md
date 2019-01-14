---
layout: post
title: "MacOS 设置命令行或终端代理"
categories: [软件开发,Web开发,前端开发]
tags: [源码阅读,命令行配置,命令行代理,终端代理]
---



 在所有事开始之前我们需要有一个梯\子，我用 [lantern](https://github.com/getlantern/lantern), 我的邀请码 `YPV2DMN`

### 单一命令配置代理

- 如果只是单一的命令不能连接，可以单独设置

  比如执行如下命令

  ```
  git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
  ```

  得到的结果

  ```
  fatal: unable to access 'https://chromium.googlesource.com/chromium/tools/depot_tools.git/': Failed to connect to chromium.googlesource.com port 443: Operation timed out
  ```

- 解决这个问题可以给`git`单独设置代理，编辑`~/.gitconfig` 并加入如下内容

  ```
  ## 配置信息在lanter的设置=>高级设置里面可以拿到，比如我的类似于下面的
  [http]
  proxy = http://127.0.0.1:57047 # http://your-socks5-host:port
  [https]
  proxy = http://127.0.0.1:57047 # http://your-socks5-host:port
  ```

- 配置完成后重新执行相关`git`操作即可

### 执行命令的时候确定是否使用代理

- 上面单一命令的配置存在几个问题

  - 用不用这个代理需要来回改配置文件
  - 这个配置只适合单一的命令

- 可以通过`proxychains-ng` 来实现使用时代理，比如像下面这样

  ```
  $ proxychains4 curl www.google.com
  [proxychains] config file found: /usr/local/etc/proxychains.conf
  [proxychains] preloading /usr/local/Cellar/proxychains-ng/4.13/lib/libproxychains4.dylib
  [proxychains] DLL init: proxychains-ng 4.13
  [proxychains] Strict chain  ...  127.0.0.1:57047  ...  119.28.87.227:80  ...  OK
  ```

- 安装`proxychains-ng`

  ```
  brew install proxychains-ng
  ```

- 然后编辑`/usr/local/etc/proxychains/conf`, 并加入如下内容

  ```
  [ProxyList]
  http 127.0.0.1  57047
  #https 127.0.0.1 57047
  #socks4 127.0.0.1   57048
  ```

- 这样配置完了之后不一定能生效，具体依赖于MacOS的版本，原因可以参考

  - [proxychains-ng](https://github.com/rofl0r/proxychains-ng)
  - [关于 Mac 上的系统完整性保护](https://support.apple.com/zh-cn/HT204899)
  - [Configuring System Integrity Protection](https://developer.apple.com/library/archive/documentation/Security/Conceptual/System_Integrity_Protection_Guide/ConfiguringSystemIntegrityProtection/ConfiguringSystemIntegrityProtection.html)

- 详情可以参考，过程是重启MacOS，按住Command+R 在出现Logo或者进度条的时候松手，进去执行一条命令，禁用系统完整性保护，当然你也可以

  - [通过 ProxyChains-NG 实现终端下任意应用代理](https://www.hi-linux.com/posts/48321.html)
  - [故事：试图不关闭 SIP 在 macOS Sierra 上使用 proxychains-ng](https://www.tcdw.net/post/proxychains-with-sip/)

- 如果你也不想动系统，再来看看其他方法

### 通过配置系统环境变量，设置命令行或终端代理

- 这里面也有几种配置方法

  - 单次配置，只对当前终端生效

    - 直接在当前终端执行如下命令

      ```
      export http_proxy=http://127.0.0.1:57047
      export https_proxy=http://127.0.0.1:57047
      ```

  - 更改配置文件，在所有终端生效

    - 修改`.~/bashrc`或者`~/.zshrc`并加入如下内容

      ```
      export http_proxy=http://127.0.0.1:57047
      export https_proxy=http://127.0.0.1:57047
      
      ```

    - 执行`source ~/.bashrc` 或者 `source ~/.zshrc`让配置在当前终端生效

    - 重新开启新的终端配置可以生效

  - 上面的配置方法在切换的时候还是有点慢，可以通过小脚本的方式来封装一层

    - 修改`.~/bashrc`或者`~/.zshrc`并加入如下内容

      ```
      # Set Proxy
      function setproxy() {
          export {http,https,ftp}_proxy="http://127.0.0.1:57047"
      }
      
      # Unset Proxy
      function unsetproxy() {
          unset {http,https,ftp}_proxy
      }
      ```

    - 执行`source ~/.bashrc` 或者 `source ~/.zshrc`让配置在当前终端生效

    - 这样就可以通过简单的`setproxy`和`unsetproxy`来实现代理的开和关

- 那么问题来了，老师，这个系统环境变量到底从哪冒出来的，为啥搞搞就好使了呢？

### 系统环境变量追本溯源

- 从哪儿来？
  - 翻了[Bash的文档](https://www.gnu.org/software/bash/manual/bash.html) 没找着
  - 搜了[ABS](http://www.tldp.org/LDP/abs/html/) 也没搜到





### 参考资料

0. [Linux 内核源代码](https://www.kernel.org/)
1. [GNU Bash](https://www.gnu.org/software/bash/)
2. [Bash 参考手册](https://www.gnu.org/software/bash/manual/bash.html)
3. [Advanced Bash-Scripting Guide](http://www.tldp.org/LDP/abs/html/)
4. [Linux Proxy Server Settings – Set Proxy For Command Line](https://www.shellhacks.com/linux-proxy-server-settings-set-proxy-command-line/)
5. [Bash configure proxy for corporate network](https://gist.github.com/patik/6d40ded40bf93c2f381b)
6. [Proxy server](https://wiki.archlinux.org/index.php/Proxy_server)
7. [命令行配置代理服务器](https://yevon-cn.github.io/2017/05/05/set-proxy-of-cmd.html)
8. [让终端走代理的几种方法](https://blog.fazero.me/2015/09/15/%E8%AE%A9%E7%BB%88%E7%AB%AF%E8%B5%B0%E4%BB%A3%E7%90%86%E7%9A%84%E5%87%A0%E7%A7%8D%E6%96%B9%E6%B3%95/)
9. [Linux bash终端设置代理（proxy）访问](https://aiezu.com/article/linux_bash_set_proxy.html)
10. [[Bash Script - Setting Local Environment Variables (Proxy)](https://unix.stackexchange.com/questions/400733/bash-script-setting-local-environment-variables-proxy)](https://unix.stackexchange.com/questions/400733/bash-script-setting-local-environment-variables-proxy)