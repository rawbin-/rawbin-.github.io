---
layout: post
title: "docker安装配置管理简明手册"
categories: [Web开发,前端开发,自动化]
tags: [部署,自动化,运维]
---



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



## `dockerfile`指南

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

### 使用`dockerfile`创建镜像

```
docker [image] build [OPTIONS] PATH | URL | -
```

该命令会读取指定路径（包括子目录）下的`dockerfile`，并将改路径下所有数据作为上下文发给`docker`服务端，服务端在校验`dockerfile`格式通过后，逐条执行其中定义的指令，碰到`ADD`，`COPY`，`RUN`指令生成一层新的镜像，最终创建成功返回镜像ID。

上下文文件过大，会延缓创建过程，非必须文件不要放到上下文路径下，如果使用非上下文路径下的`dockerfile`可以使用`-f`来指定路径，也可以通过`.dockerignore`文件来指定忽略的文件，支持通配符

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

#### 基于dockerfile 创建

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



