---

layout: post
title: "docker安装配置生态全览"
categories: [Web开发,前端开发,自动化]
tags: [部署,自动化,运维]
---

# 基本概念

## 虚拟化

- 虚拟机和容器都属于软件虚拟化的范畴，软件虚拟化=>平台虚拟化=>操作系统虚拟化=>容器技术(docker为其中一种)
- 容器虚拟化可以更高效的构建应用，也更容易管理维护

## 镜像

- 是docker容器的基础，是docker容器的初始状态
- 包含数据和必要的元数据（JSON描述文件，描述镜像信息、数据之间的关系，容器配置等）

## 容器

- 是一个可移植的运行单元，是docker镜像的运行实例
- 是基于docker镜像创建、包含运行某一特定应用程序所需要的的OS、软件、配置文件和数据的集合，是docker运行态的体现

## 仓库

- 镜像的存取的地方

# 安装

## CentOS 下安装

### 分步安装

- (如果有)删除旧版本

  ```
   sudo yum remove docker \
                    docker-client \
                    docker-client-latest \
                    docker-common \
                    docker-latest \
                    docker-latest-logrotate \
                    docker-logrotate \
                    docker-engine
  ```

  

- 添加必要组件

  ```
  sudo yum update
  sudo yum install -y yum-utils device-mapper-persistent-data lvm2
  ```

- 添加`docker`软件源

  ```
  sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
  ```

- 更新软件源缓存并安装docker

  ```
  sudo yum update
  sudo yum install -y docker-ce
  sudo systemctl start docker
  ```

### 脚本安装

```
curl -fsSL https://get.docker.com -o get-docker.sh
## 注意安全 最好检查下
sudo sh get-docker.sh
```

## 其他环境

- 参考客户端安装和脚本安装

# 配置

## 数据管理

### 数据卷

`docker volumn`命令及其子命令来管理

- `create`
- `inspect`
- `ls`
- `prune`
- `rm`

可以在`docker run` 的时候绑定数据卷

```
docker run [OPTIONS] --mount type=[volumn|bind|tmpfs],source=PATH,destination=PATH CONTAINER COMMAND [ARGS...]
```

### 数据卷容器

```
docker run -it -v /dbdata --name dbdata ubuntu ## 创建数据容器
docker run -it --volumes-from dbdata --name db1 ubuntu
docker run -it --volumes-from dbdata --name db2 ubuntu ## 其他容器挂载数据卷
```

#### 利用数据卷容器迁移数据

##### 备份

```
docker run --volumes-from dbdata -v $(pwd):/backup --name worker ubuntu tar cvf /backup/backup.tart /dbdata ## 启动容器并备份到 backup.tar
```

##### 恢复

```
docker run -v /dbdata --name dbdata2 ubuntu /bin/bash ## 创建带数据卷的容器dbdata2
docker run --volumes-from dbdata2 -v $(pwd):/backup busybox tar xvf /backup/backup.tar
```

## 端口映射

### 随机端口

```
docker run -d -P NAME
```

### 指定端口

```
docker run -d -p IP:HOST_PORT:CONTAINER_PORT|IP::CONTAINER_PORT|HOST_PORT:CONTAINER_PORT
```

映射结果区别

- `IP:HOST_PORT:CONTAINER_PORT` 指定地址的指定端口
- `IP::CONTAINER_PORT` 指定地址的任一端口
- `HOST_PORT:CONTAINER_PORT` 所有地址的指定端口

### 查看端口映射

```
docker port NAME PORT
```



## `Dockerfile`指南

### 主体结构

- 基础镜像信息

   ```
  FROM NAME[:TAG]
  ```

- 维护者信息

  ```
  LABEL maintainer docker_user<xxx@email.com>
  ```

- 镜像操作指令

  ```
  RUN xxx
  RUN yyy ## 每一条RUN指令镜像添加一层并提交
  ```

- 容器启动时指令

  ```
  CMD ["nginx","-g","deamon off;"]
  ```

### 指令参考

- 配置指令
  - `ARG` 定义创建镜像过程使用的变量
  - `FROM` 指定所创建镜像的基础镜像
  - `LABEL` 为生成的镜像添加元数据标签信息
  - `EXPOSE` 声明镜像内服务监听的端口，只起声明作用
  - `ENV` 声明环境变量
  - `ENTRYPORT` 指定镜像的默认入口命令
  - `VOLUME` 创建一个数据挂载点
  - `USER` 指定运行容器时的用户名或者UID
  - `WORKDIR` 配置工作目录
  - `ONBUILD` 创建子镜像时指定自动执行的操作指令
  - `STOPSIGNAL` 指定退出的信号值
  - `HEALTHCHECK` 配置所启动容器如何进行健康检查
  - `SHELL` 指定默认shell类型
- 操作指令 
  - `RUN` 运行指定命令
  - `CMD` 启动容器时指定默认执行的命令
  - `ADD` 添加内容到镜像
  - `COPY` 复制内容到镜像

### 使用`Dockerfile`创建镜像

```
docker [image] build [OPTIONS] PATH | URL | -
```

该命令会读取指定路径（包括子目录）下的`Dockerfile`，并将改路径下所有数据作为上下文发给`docker`服务端，服务端在校验`Dockerfile`格式通过后，逐条执行其中定义的指令，碰到`ADD`，`COPY`，`RUN`指令生成一层新的镜像，最终创建成功返回镜像ID。

上下文文件过大，会延缓创建过程，非必须文件不要放到上下文路径下，如果使用非上下文路径下的`Dockerfile`可以使用`-f`来指定路径，也可以通过`.dockerignore`文件来指定忽略的文件，支持通配符

- `*` 表示任意多个字符
- `?` 代表单个字符
- `!` 表示不匹配



# 命令参考

## Docker操作

### 获取镜像

```
docker [image] pull NAME[:TAG]
```

### 查看镜像信息

```
docker images  或者 docker image ls
```

### 搜索镜像

```
docker search [option] keyword
```

### 删除镜像

```
docker rmi NAME 或者 docker image rm NAME
```

### 创建镜像

#### 基于已有容器创建

```
docker [container] commit [OPTIONS] CONTAINER [REPOSITORY:[:TAG]]
```

#### 基于本地模板导入

```
docker [image] import [OPTIONS] file|URL|-[REPOSITORY[:TAG]]
```

#### 基于Dockerfile 创建

```
FROM REPOSITORY:TAG

LABEL version="1.0" maintainer="xxx"

RUN xxxx
```

### 存储和导入

#### 存储

```
docker [image] save -o xxx REPOSITORY:TAG
```

#### 导入

```
docker load -i xxx
```

### 上传镜像

```
docker [image] push NAME[:TAG] | [REPOSITORY[:RETISTRY_PORT]NAME[:TAG]
```



## 容器操作

### 创建容器

```
docker [container] create [OPTIONS] NAME[:TAG]
```

#### 创建后启动

```
docker [container] start [OPTIONS] NAME[:TAG]
```

#### 创建并启动

```
docker [container] run [OPTIONS] NAME[:TAG]
```

相当于先执行`docker create` ，再执行`docker start`

**常见错误**

- 125 Docker daemon 执行出错，比如指定了不支持的docker命令参数
- 126 所指定命令无法执行，例如权限出错
- 127 容器内命令无法找到



**常用参数**

- `-i` 让容器的标准输入保持打开
- `-t` 分配一个伪终端
- `-d` 守护状态运行

#### 等待退出并打印返回结果

```
docker [container] wait CONTAINER [CONTAINER...]
```

#### 查看容器输出

```
docker [container] logs NAME[:TAG]
```

#### 暂停/恢复容器

```
docker [container] pause/unpause CONTAINER [CONTAINER]
```

### 停止容器

```
docker [container]  stop [-t|--time[=10]] CONTAINER [CONTAINER...]
docker [container]  kill  CONTAINER [CONTAINER...] ## 强行停止
docker [container] prune ## 清理停止状态的容器
```

### 进入容器

#### attach命令

```
docker [container] attach [--detach-keys[=[]]] [--no-stdin] [--sig-proxy[=true]] CONTAINER
```

多个窗口同时attach的时候，多个窗口会同步显示，一个阻塞其他的也无法操作

#### exec命令

```
docker [container] exec [-d|--detach] [--detach-keys[=[]]] [-i|--interactive] [--privileged] [-t|--tty] [-u|--user[=USER]] CONTAINER COMMAND [ARGS]
```

### 删除容器

```
docker [container] rm [-f|--force] [-l|--link] [-v|--volumes] CONTAINER [CONTAINER...]
```

只能删除已经处于终止或者退出状态的容器，并不能删除处于运行状态的容器，`-f`会先发`SIGKILL`给容器终止应用然后删除。

### 导入和导出

```
docker [container] export [-o|--output[="xx.tar"]] CONTAINER
```

```
docker [container] import [-c|--change[=[]]] [-m|--message[=MESSAGE]] file|URL|-[REPOSITORY[:TAG]]
```

### 查看容器

#### 查看容器详情

```
docker [container] inspect [OPTIONS] CONTAINER [CONTAINER...]
```

#### 查看容器内进程

```
docker [container] top [OPTIONS] CONTAINER [CONTAINER...]
```

### 其他命令

#### 查看命令帮助

```
docker container help
docker help
```

#### 复制文件

```
docker [container] cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
```

#### 查看容器变更

```
docker [container] diff CONTAINER
```

#### 查看端口映射

```
docker [container] port CONTAINER [PRIVATE_PORT[/PORT]]
```

#### 更新配置

```
docker [container] update [OPTIONS] CONTAINER [CONTAINER...]
```



# 集群运维管理

## 容器编排调度

业界的一些组合

### Swarm+Compose - Docker

### Kubernetes - Google

### Fleet - CentOS

### Marathon+Mesos - Apache

### Rancher

### Nomad

### DC/OS

## Docker集群管理

### docker-compose 容器编排

- `dockder-compose -f docker-compose.yml` 指定配置文件
- `docker-compose config` 检查`docker-compose.yml`的语法问题 也可以输出内容
- `docker-compose -p PREFIX up ` 指定启动是的服务前缀
- `docker-compose build MIRROR_NAME` 构建镜像
- `docker-compose bundle`  生成`.dab`包，给`docker deploy`来部署
- `docker-compose create` 创建服务容器
- `docker-compose down` 清理项目,停止容器并删除包括容器、网络、数据卷等内容(跳过正在使用的)，对应`up`
- `docker-compose events`  查看配置相关的服务的事件
- `docker-compose exec` 进入服务
- `docker-compose kill` kill服务
- `docker-compose logs` 查看服务容器日志
- `docker-compose pause` 暂停服务容器
- `docker-compose port` 查看服务容器端口状态
- `docker-compose ps` 查看容器列表
- `docker-compose images` 查看服务镜像列表
- `docker-compose pull` 拉取项目镜像
- `docker-compose push` 推送项目镜像
- `docker-compose restart` 重启服务容器
- `docker-compose rm` 删除项目容器
- `docker-compose run` 执行一次命令
- `docker-compose scale SERVICE_NAME=NUM` 设置服务容器数量
- `docker-compose start` 启动服务容器 
- `docker-compose stop` 停止容器服务
- `docker-compose top` 查看容器服务状态
- `docker-compose unpause` 取消暂停状态
- `docker-compose up` 启动项目，包括构建，创建(重新创建)、启动和链接服务容器



#### compose 配置文件

`Compose`的配置文件是一个YML格式的文件，它定义了包括服务（容器）、网络、数据卷在内的一系列项目组件，默认路径是`./docker-compose.yml`。

使用配置文件定义的服务在启动时就跟使用`Docker Client`的`docker run`一样，同样的配置文件重定义的网络、数据卷也相当于`Docker Client`中使用`docker network create`和`dockder volume create`一样。实际上，`Compose`并不会真正操作容器，它只适合解析配置文件的定义，然后发送给`Docker Client`。

`Compose` 配置文件中定义的每个服务都必须通过`image`标签指定镜像或`build`标签来执行构建(上下文中存在Dockerfile)，Dockerfile中定义的变量可以在`docker-compose.yml`文件中使用，不必在`docker-compose.yml`文件中重新定义。

##### 文件语法

###### version

```
version "VERSION"
```

###### services

如果同时指定`build`和`image`，会先执行构建，然后命名为`image`指定的名字

- `image` 指定镜像名称或者镜像ID
- `build` 可以基于Dockerfile，构建需要的镜像
  - 直接指定`Dockerfile`的相对或者绝对路径
  - `context` 指定上下文根目录
  - `dockerfile`  指定`Dockerfile`的名称 
  - `cache_from` 指定构建缓存
  - `labels`
  - `cap_add`
  - `cap_drop`
  - `command` 可以覆盖容器启动后默认执行的命令，可以直接写，也可以写成数组的形式
- `configs` 设置config文件，在集群部署时可以方便调度配置文件
  - `source` Docker中存在的配置的名称
  - `target` 容器内部的目标路径
  - `gid/uid` 指定配置文件存放时的用户归属
  - `mode` 配置文件的权限
- `cgroup_parent` 为容器指定一个可选的父`cgroup`
- `container_name` 默认容器名称格式为<PROJECT_NAME><SERVICE_NAME><SEL_NUM>
- `deploy` 仅在使用`docker stack`部署到集群是生效，`docker-compose up`和`docker-compose run`等命令无效
  - `mode` `global`和 `replicated`(默认)二选一
  - `replicas` 数量
  - `update_config` 配置更新策略
    - `parallelism` 每次更新的容器数量
    - `delay` 更新一组容器时等待的时间
    - `failure_action` 如果更新失败后续操作,`continue`或者`pause`(默认)
    - `monitor`每个任务更新失败后监视的时间间隔(ns|us|s|m|h)默认为0
    - `max_failure_ratio` 允许更新过程中更新失败的最大容器数量
  - `restart_policy`重启策略
    - `condition` 可选值有 `none`,`on-failure`,`any`（默认）
    - `delay` 每次尝试重启要等待多时时间，默认为0
    - `max_attempts` 尝试次数，默认永不放弃
    - `window` 重启后等待多时时间才能确定启动成功,默认立即
  - `resources` 配置资源约束
    - `limits`
    - `reservations`
    - `cpu_shares`
    - `cpu_quota`
    - `cpuset`
    - `mem_limit`
    - `memswap_limit`
    - `mem_swappiness`

- `args` 指定构建过程中的环境变量，可以使用冒号或者等号分隔，可以省略来允许空值

- `devices`设备映射列表

- `depends_on` 规定服务的前后依赖顺序

- `dns` 指定`dns`配置

- `dns_search` 自定义搜索域

- `tmpfs` 挂载临时目录到容器

- `entrypoint` 定义接入点，覆盖`Dockerfile`中的定义

- `env_file` `.env`文件设置的Compose的变量，对宿主机的Compose而言，不会用在构建中

- `environment` 设置镜像变量，可以将变量保存到镜像中，启动的容器都会包含这些变量设置，与Dockerfile的ENV一样

- `expose` 用于暴露端口,与Dockerfile中的EXPOSE一样

- `external_links` 为了使Compose能够连接这些不在`docker-compose.yml`中定义的容器

- `extra_hosts` 添加主机名的标签，往`/etc/hosts`中添加一些记录

- `healthcheck` 检查容器状态的命令

  - `test` 命令
  - `interval`
  - `timeout`
  - `retries`

- `labels` 向容器添加元数据，与Dockerfile中的LABEL命令类似

- `links` 连接到其他服务的容器中

- `logging` 配置日志服务

  - `driver`
  - `options`

- `network_mode` 指定服务或者容器的网络

- `networks` 也可以设置不同网络及相关映射，用于服务升级

- `pid`设置PID模式，跟主机系统共享进程命名空间

- `ports` 映射端口的标签，使用HOST:CONTAINER格式时，端口小于60可能会报错，YAML将会解析xx:yy为60进制

- `secrets` 每个服务配置独立的`secrets`

- `security_opt` 为每个容器覆盖默认的标签，管理全部服务的标签

- `stop_signal` 设置另一个信号来停止容器，默认情况下使用SIGTERM来停止容器

- `volumes` 挂载一个目录或者一个已存在的数据卷容器，可以直接使用

- `volumes_from` 从其他容器或者服务挂载数据卷

- `extends` 可以扩展另一个服务，扩展内容来自当前文件，也可以来自其他文件

  - `file`
  - `service`

  

```
version "VERSION"
services
  SERVICE_NAME
    image:IMAGE_NAME
networks
  
```



### swarm 集群管理

- 可以把多个`docker`主机组成的系统转换为单一的虚拟`docker`主机，使得容器可以组成跨主机的子网网络
- `docker 1.23`版本之后自带的官方支持的集群管理工具，支持 `swarm mode` 集群管理模式
- `docker swarm`模式不是指`docker swarm`命令，`docker swarm`模式包含了` docker node`、`docker service`、`docker stack`、`docker deploy`、`docker network`等命令在内的一整套完整的集群部署管理体系，是融与`docker`中的集群管理方案

#### docker swarm 命令

- `docker swarm init` 初始化swarm集群
- `docker swarm join` 加入一个集群，包括普通节点和管理节点
- `docker swarm join-token`  管理加入集群 的口令(tokens)
- `docker swarm leave` 离开当前集群 
- `docker swarm unlock` 解锁集群
- `docker swarm unlock-key` 管理解锁集群的密钥
- `docker swarm update` 更新集群



#### docker node 命令

- `docker node demote` 将一个或者多个节点降级为普通节点
- `docker node inspect` 显示节点的详细信息
- `docker node ls` 查看集群的所有弱点
- `docker node promote` 将普通节点提升为管理节点
- `docker node ps` 显示一个或多个节点的正在运行的任务列Shell, 默认为当前节点
- `docker node rm` 移除一个或者多个节点
- `docker node update` 更新节点



#### docker stack 命令

- `docker stack deploy` 部署一个新的Docker栈或更新现有的Docker栈
- `docker stack ls` 显示所有的Docker栈
- `docker stack ps` 显示指定栈的任务
- `docker stack rm` 删除指定的Docker栈
- `docker stach service` 显示指定栈的服务列Shell



### docker-machine 集群网络

#### 建立跨主机网络

- `docker-machine create` 创建虚拟机
- `docker-machine env` 查看虚拟机的环境变量信息
- `docker-machine ls` 查看虚拟机列表
- `docker-machine ssh` 操作虚拟机
- `dockek-machine ssh MANAGER_NAME docker swarm init` 初始化集群
- `docker-machine ssh MANAGER_NAME docker node ls` 查看集群管理节点相关的节点
- `docker-machine ssh WORKERNAME docker swarm join` 将worker加入集群
- `docker-machine ssh MANAGER_NAME docker swarm join-token` 获取manager的集群token

#### 在跨主机网络上部署应用

- `docker-machine ssh MANAGER_NAME docker pull` manager的集群节点获取镜像
- `docker service create` 创建服务使用的跨主机网络
- `docker service ls` 查看服务状态
- `docker service ps` 查看服务详情
- `docker-machine ssh MANAGER_NAME docker ps -a` 到节点上查看服务状态
- `docker-machine ssh NODE_NAME docker exec -i` 进入机器，可以互ping 查看联通状态
- `dockder-machine ssh NODE_NAME ping` 集群的ping



## Kubernetes 

与Docker集群管理的一套类似，Kubernetes是一个用于容器集群的自动化部署、扩容以及运维的开源平台，是Google内部Borg集群管理平台的开源版本。

比起前面的组合，部署更加方便，在微服务管理上更加出色，市场份额占比增长很快。Kubernetes基于容器的应用部署和维护，可靠的高可用机器可用机制使得服务滚动升级不再艰难，集成负载均衡和服务发现功能，拥有跨机器甚至跨地区的集群调度能力，自动根据用户期望伸缩服务于和规模，兼容有、无状态服务，支持Volume等，通过插件机制保证扩展性。

使用Kubernetes可以快速高效地响应客户需求，快速并且无意外的部署应用，并且可以动态对应用进行扩容，无缝的升级应用。

### 基本概念

#### 资源

`Node`，`Pod`， 各种`Controller`，`Service` 等都可以看做一种"资源对象"，而这些资源都可以通过Kubernetes提供的`kubectl`命令行工具执行管理操作，`etcd`会记录并保持这些资源的状态。Kubernetes通过与`etcd`的记录比对，当实际的服务状态与期望状态不符合时，会自动调度资源去实现期望的状态。

#### 调度中心：Master

Master就是管理节点，是集群控制中心，每个Kubernetes集群里都至少需要有一个Master节点来负责集群的管理和控制，Kubernetes几乎所有的控制命令都是发给Master的。Master节点作为集群的大脑，通常都位于一个高性能服务中，以保证集群运行正常。

#### 工作节点：Node

在Kubernetes集群中，Node作为一种资源，它表示所有在集群中的机器，包括Master也是Node中的一员（如果Master运行服务）。Node节点并不局限于物理主机，所有部署了Kubernetes agent的环境都可以称为节点（例如虚拟机）。Node节点是集群中的工作和负载节点，每个Node都会被Master分配一些工作和负载，当某个Node宕机时，其工作会被Master自动转移到其他节点上去。

Node节点可以动态添加到Kubernetes集群中，当Kubernetes在Node节点配置完成并启动时，Node节点中的`kubelet`(agent程序)便会想Master注册。当Node被Master认证通过时，Node节点的kubelet进程就会定时地向Master节点发送自身的信息（例如操作系统，Docker版本，机器的CPU和内存情况）以及哪些Pod在运行等。这样Master就可以获知每个Node的资源使用情况，并根据用户的期望高效地实现资源调度。

当某个Node超过设定时间没有发送信息给Master时，Master便把该Node的状态标记为不可用（Not Ready）,然后Master开启工作负载转义流程，把失联Node节点的工作交给其他Node节点，这是一个完全自动的流程。

相关命令：

- `kubectl get node` 获取节点列表信息

- `kubectl describe node NODE_NAME` 查看节点详细信息

#### 最小调度单位：Pod

Pod是Kubernetes里面抽象出来的一个概念，他是Kubernetes的最小调度单位，每个Pod都有一个独立的IP（不推荐用IP去访问），并至少由一个容器构成，同一个Pod中的容器会自动分配到同一个物理机或者虚拟机上，一个Pod内的容器共享Pod的Volumes 、网络、端口等资源（联合命名空间包括PID、网络、IPC、UTS等）。

同一个Pod中的容器可以自由访问，共享内存，一般情况下，我们把有"亲密关系"的容器放到一个Pod中，使得他们可以通过localhost互相访问，例如在一个PHP服务器中，前端使用nginx作为Web服务，后端使用php-fpm执行任务，那么将这两个容器部署在一个Pod内就非常合理了。

Kubernetes的Pod和Docker Container区别很大，Pod里包含多个Container，主要考虑两方面，一方面是自身解耦和Docker解耦，另一方面通过Pod打包多个Container使之共享同一个生命周期，有利于多个服务有强依赖的场景。

#### 资源标签：Label

Label是附加在Kubernetes资源对象上的键值对（由key/value组成），以传达使用者所定义的可识别属性。他可以在创建的时候指定，也可以随时修改，一个资源对象上面可以有任意多个Label。

Label可以用来组织和选择某个资源的子集合，Kubernetes最终会索引并且反向索引Label以获得更高效的查询和监视，把他们用到UI或者CLI中进行排序或者分组等。

相关命令：

- `kubectl get nodes --show-labels` 获取节点标签
- `kubectl label node NODE_NAME LABEL=VALUE` 新增标签
-  `kubectl label node NODE_NAME LABEL=-` 删除标签
- `kubectl get pods -l environment=production`
- `kubectl get pods -l 'environment in (production)'` 



#### 弹性伸缩：RC与RS

Replication Controller简称RC，用来确保同一时间内存在指定数量的副本Pod（Pod Replicas）在运行中。RC允许用户调整（也成为scaling）副本数量，RC可以再机器故障或其他原因导致部分Pod关闭时重新创建指定数量的Pod副本（异常多出来的Pod也会自动回收）。典型应用场景包括确保健康Pod的数量，弹性伸缩，滚动升级以及应用多版本发布跟踪等。

Replica Set简称RS，是新版本的RC，本质是一样的，区别在于ReplicaSet支持集合式的选择器，RC仅支持等式。



#### 部署对象：Development

Deployment和ReplicaSet用来替代以前的Replication Controller，以实现更高效的Pod管理流程。可以简单理解为以前的RC分为现在的两个资源Deployment和RS，其中Development可以创建Pod和ReplicaSet，而ReplicaSet知负责管理Pod副本数量不再参与到Pod整个生命周期过程中。

自动扩容案例

`kubectl autoscale deployment CONFIG_NAME --min=MIN --max=MAX --cpu-percent=80`



#### 水平扩展：HPA

HPA全称为Horizontal Pod Autoscaling即Pod的水平自动扩展，他可以根据当前系统的负载来自动水平扩容，如果系统负载超过预定值，就开始增加Pod的个数，如果低于某个值，就自动减少Pod的个数。

HPA根据CPU使用率或者应用自定义metrics自动扩展Pod数量，使用Heapster去收集CPU的使用情况。

`kubectl autoscale (-f FILENAME | TYPE NAME |TYPE/NAME) [--min-MINPODS] --max=MAXPODS [--cpu-percent=CPU] [flags] [options]`



#### 服务对象：Service

Service是Kubernetes中最核心的资源对象之一，我们前面所说的Pod、RC、RS、Deployment、Label等都是为Service服务的，例如Pod副本实际上是通过Label选择器来实现与Service对接的——作为Service负载均衡的后端，而RC/RS的作用实际上是保证Service的服务能力和服务质量始终处于预期的标准。Service作为Kubernetes里面抽象出来的一层，它定义了由多个Pods组成的逻辑组，Service可以管理组内的Pod。

Service有三大作用：对外暴露流量，做负载均衡，服务发现。



#### 数据卷资源：Volume

用在Kubernetes中持久化数据，一个Kubernetes Volume拥有明确的生命周期，与所在的Pod的生命周期相同。Volume独立于任何容器，只与Pod有关，所以数据卷在Pod或者容器重启过程中还会保留。

Kubernetes Volume支持多种类型，任何容器都可以使用多个Kubernetes Volume。数据卷是文件目录，不是所有的数据卷都是持久化的，emptyDir,secret,gitRepo等随着Pod的消失而消失。



#### 数据持久化：Persistent Volume

Persistent Volume和Persistent Volume Claim是Kubernetes提供的两种API资源，拥有抽象存储细节。

PV只是网络存储，不与Pod共享生命周期，而是独立于Pod之外；PVC是用户存储的请求，他类似于Pod，Pod消耗节点资源，而PVC消费PV资源，例如Pod可以向Node请求特定级别的资源（CPU和内存），而PVC可以向PV请求特定的大小和访问模式。



#### 命名空间：Namespace

Namespace是对一组资源和对象的抽象集合，比如可以用来将系统内部的对象划分为不同的项目或用户组。常见的Pods、Services、RC和Development等都是属于某个Namespace的（默认是default），但Node和Persistent Volume等资源不属于任何Namespace。

在一个Kubernetes集群中可以用Namespace创建多个“虚拟集群”，这些Namespace之间可以完全隔离。因为Namespace可以提供独立的命名空间，因此可以实现部分的环境隔离。当你的项目和人员众多的时候可以考虑根据项目属性划分不同的Namespace比如（开发、测试、生产）。

相关命令：

- `kubectl get namespaces`
- `kubectl create namespace NAMESPACE_NAME`



#### 注释：Annotation

Annotation使用键值对存储元数据，与Label相似，但没有Label那么严格的命名规则。

Annotation主要用于信息检索、标记等，给人看的内容，可以灵活填写，像构建信息，版本信息，Docker镜像信息，日志存储位置等可能对管理有帮助的内容都可以写进去，以便管理人员后续管理时能够理解整个资源的用途和其他相关信息。



###  部署方式

#### Minikube 意见部署单机版

Minikube使用操作系统中的虚拟机技术，在本地简历一个虚拟机集群，模拟集群环境，整个过程一键部署，不需要了解部署细节，适合入门学习或者体验Kubernetes的用户。

#### Kubeadm 自动化部署版本

如果已经使用Minikube安装了Kubernetes，那么需要删除$HOME/.kube目录

#### 手动部署二进制版本



### 命令参考

`kubectl [子命令] [资源类型] [资源名称] [选项] ` 

#### 常见资源类型缩写表

- `service` =>`svc`
- `serviceaccounts` => `sa`
- `resourcequotas` => `quota`
- `replicationcontrollers` => `rc`
- `replicasets` => `rs`
- `podsecuritypolicies` => `psp`
- `pods` => `po`
- `poddisruptionbudgets` => `pdb`
- `persistentvolumes` => `pv`
- `persistentvolumeclains` => `pvc`
- `nodes` => `no`
- `networkpolicies` => `netpol`
- `namespace` => `ns`
- `limitranges` => `limits`
- `ingresses` => `ing`
- `horizontalpodautoscalers` => `hpa`
- `events` => `ev`
- `endpoints` => `ep`
- `deployments` => `deploy`
- `daemonsets` => `ds`
- `configmaps` => `cm`
- `componentstatuses` => `cs`
- `certificatesigningrequests` => `csr`

#### 基本命令

##### create

通过文件名或控制台输入创建资源

- `clusterrole` 创建一个新的集群角色
- `clusterrolebinding` 为特定的集群角色创建集群角色绑定
- `configmap` 从本地文件、目录或者字符值创建一个configmap，可以用来保存键值对的配置数据
- `deployment` 创建具有指定名称的`deployment`
- `namespace`  创建具有指定名称的`namespace`
- `poddisruptionbudget` 创建具有指定名称的`poddisruptionbudget`
- `quota` 创建具有指定名称的`quota` （资源配额）
- `role` 使用单一规则创建角色
- `rolebinding` 为特定角色或集群角色创建一个角色绑定
- `secret` 使用指定的子命令创建一个`secret`,子命令有`docker-retistry` `generic`和`tls`三个
- `service` 使用指定的子命令创建一个服务，有四个子命令，`clusterip`，`externalname`，`loadbalancer`，`nodeport`
- `serviceaccount` 创建具有指定名称的服务账户

##### expose

将一个资源（RC，Service，Deployment，Pod）作为新的Kubernetes服务。

`expose`按名称查找`deployment`，`service`，`RC`，`RS`或`Pod`，并将该资源的选择器用作指定端口上新的服务的选择器。`deployment`或`RS`只有当选择器可转换为服务支持的选择器时，即当选择器仅包含matchLabels组件时，才会暴露服务。



##### run

创建并允许特定镜像，通过`deployment`或`job`来管理创建的容器。

##### set

配置应用资源，这个命令可以更改现有的应用程序资源。

- `image` 更新现有容器镜像的资源。
-  `resources` 为Pod模板指定计算资源请求(包括CPU和内存)，如果一个Pod被成功部署，这个命令将保证Pod请求的资源数量
- `selector` 在资源对象上设置选择器。注意如果资源在调用`set selector`之前已经有选择器，则新的选择器将覆盖旧的选择器。
- `subject` 在RoleBinding/ClusterRoleBinding中更新`user`，`group`或`ServiceAccount` 



##### get

显示一个或者多个资源，默认隐藏已停止的资源，无论运行成功还是启动失败，只要状态不是活动的都不显示。可以通过`--show-all`来显示完整结果 。

##### explain

资源记录，获取资源及其字段的文档

##### edit

在服务器上使用默认编辑器编辑资源，使用这个命令可以编辑多个对象，然后一次性地应用更改。

##### delete

通过指定文件名、stdin、资源名称、资源标签选择器来删除资源。

#### 部署命令

##### rollout

管理资源的部署，有效的资源类型包括:`deployments`,`daemonsets`

- `history` 查看以前部署的版本和配置
- `pause` 将指定的资源标记为已暂停，已暂停资源将不会由控制器调度。目前支持`deployment`
- `resume` 恢复暂停资源，暂停的资源将不会被控制器调度，通过恢复资源，我们允许它再次调度。
- `status` 显示部署的状态，默认情况下，“部署状态”将监视最新部署的状态，知道完成为止。
- `undo` 回滚先前的部署

##### rolling-update

执行指定的RC进行滚动更新，使用新的RC控制器替换旧的RC控制器，逐步使用新的Pod Template替换旧的Pod。新旧RC必须在同一个Namespace中，并覆盖其选择器至少一个标签（通常是版本号），以便选择器可以区分新旧版本RC。

##### scale

为`Deployment`，RS，RC或`Job`设置新的规模。`scale`允许用户为伸缩操作指定一个或多个前提条件。如果制定了`--current-replicas`或`--resource-version`则在尝试伸缩规模之前进行验证，并确保将 规模伸缩命令发送到服务器时前提条件不变。

##### autoscale

创建自动规模伸缩器，可自动调整并设置在Kubernetes集群中运行的Pod数量，通过名称查找`Development`,`ReplicaSet`,`ReplicationController`,并创建一个使用指定资源状态作为参考的自动规模伸缩器，autoscale可以根据需要自动增加或者减少系统中部署的`Pod`数量

#### 集群管理命令

##### certificate

修改证书资源

- approve 批准证书签名请求，`kubectl certificate `准许管理人员提交的证书签名请求(CSR)，此操作告诉证书签名控制器向请求者颁发证书，其中包含CSR中请求的属性。
- deny 拒绝签名请求，`kubectl certificate`拒绝管理人员发送的证书签名请求（CSR），此操作会告知证书签名控制器不想请求者颁发证书。

##### cluster-info

使用标签显示Master服务器和Service的地址，如果要进一步调试和诊断集群问题，可以使用`kubectl cluster-info dump`命令

##### top

显示资源（CPU、内存、存储）的使用情况。top命令用于查看节点或Pod的资源消耗，此命令需要Heapster正确配置并在服务器上正常工作。

- node 显示节点的资源信息
- pod 显示指定pod的信息

##### cordon

标记节点为不可调度，不可调度的节点将从集群的调度列表中删除，不会调度任何资源到该节点中，一般用于节点维护升级服务。标记为不可调度并不会删除节点上的Pod，但是失联的Pod 也无法与集群通信。

##### uncordon

标记节点为可调度。

##### drain 

drain标记节点，表示准备维护。该命令会删除该节点上的所有Pod通常用于节点维护。

##### taint

当节点被标记为taint(变质)时，除非Pod被标识为可以忍受污染(toleration)，否则不会有任何Pod呗调度到该节点。之所以把taint标记到节点而不是标记到Pod，是因为在这种情况下绝大多数的Pod都 不应该部署到taint节点上。



#### 故障排除和调试命令

##### describe

显示特定资源或资源组的详细信息，与其他API调用合并在一起，以显示指定资源或资源组的详细描述。

`kubectl describe TYPE NAME_PREFIX`

##### logs

打印指定资源中容器的日志，如果Pod只有一个容器，容器名称是可选的。

##### attach

附加到已经在现在容器中运行的进程，从指定的Pod中获取输出，默认依附到第一个容器。

##### exec

在容器中执行命令。

##### port-forward

将一个或多个本地端口转发到Pod。比如本地监听`5000`和`6000`端口，并把数据转发到Pod的`5000`和`6000`端口

`kubectl port-forward NAME 5000 6000`

##### proxy

在localhost和Kubernetes API Server之间创建代理服务器或应用层网关。proxy允许通过指定的HTTP路径提供静态内容。所有传入数据通过一个端口进入，并被转发到远程Kubernetes API服务端口（除了与静态内容路径匹配的路径）中。

##### cp

将文件和目录拷贝到容器中，cp命令要求容器中存在tar命令，如果tar不存在，`kubectl cp`将执行失败。

##### auth

检查授权，can-i 子命令用户检查用户是否允许执行某些操作。

#### 高级命令

##### apply 

通过文件名或stdin将apply配置到资源中，文件可以使用JSON和YAML格式。资源名称必须制定，如果资源不存在，将创建资源。

##### patch

使用补丁更新资源的字段，文件可以是JSON和YAML格式

##### replace

用指定的文件或stdin替换资源，如果替换现有资源，则必须提供完整的资源定义，可以通过`kubectl get TYPE NAME -o yaml`获得

##### convert

在不同API版本中转换配置文件，YAML和JSON格式都可以，参数可以是文件名，目录或URL。



#### 设置命令

##### label

更新资源上的标签，标签规则是以字母或数字开头，可以包含字母、数字、连字符、点和下划线，最多为63个字符。

##### annotate

更新一个或多个资源上的注释

##### completion

输出指定shell的shell完整代码，必须对shell代码进行评估，以提供`kubectl`命令的交互

#### 其他命令

##### api-versions

在服务器上输出支持的API版本

##### config

使用`kubectl config set current-context my-context`之类的子命令修改`kubeconfig`文件

- `set-cluster` 在`kubeconfig`文件中设置一个集群项
- `set-context` 在`kubeconfig`配置文件中上设置一个环境项
- `set-credentials` 在`kubeconfig`配置文件中上设置一个用户项
- `set` 在`kubeconfig`配置文件中上设置一个单独的值
- `unset` 在`kubeconfig`配置文件中上清除一个单独的值
- `use-context` 使用`kubeconfig`中一个环境项作为当前配置
- `view` 显示合并后的`kubeconfig`设置，或者一个指定的`kubeconfig`配置文件

##### help

查看所有命令的帮助

##### options

输出某个命令的所有选项

##### plugin

运行一个命令行插件，插件命令不属于`kubectl`子命令，可以由第三方提供

##### version

打印输出当前上下文环境中的客户端和服务端版本信息

