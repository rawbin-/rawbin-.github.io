# 免密登录
假设`主机A`需要用`acountA`（非`root`）免密登录到`主机B`

首先需要在`主机A`上使用`ssh-keygen` 生成对应的公钥私钥对，比如是`id_rsa.pub`（公钥）和`id_rsa`（私钥），可以使用`ssh-copy-id`这个工具拷贝，也可以直接将`id_rsa.pub`中的文本内容追加到目标机器（`主机B`）的对应的路径`/home/accountA/.ssh/authorized_keys`中，确保`sshd`服务启动即可通过SSH进行免密登录。

一般我们用普通用户（非`root`）做SSH登录，这样可以降低安全风险。如果要用`root`用户的话，这里需要修改sshd的配置（`/etc/ssh/sshd_config`），允许`root`用户远程登录。