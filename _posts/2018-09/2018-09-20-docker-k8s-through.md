---
layout: post
title: "docker+kubenetes开发上线运维"
categories: [Web开发,前端开发,自动化]
tags: [部署,容器化,可伸缩,自动化,运维]
---



# 概念理解

## 两大生态

### Docker

- 将精力专注在应用开发上，包揽环境部署问题(Debug you app, not your environment)
- 将环境部署定义在稳定的自动化的基础上，从过程上降低运维成本

#### 相关概念 [完整的概念参考](https://docs.docker.com/glossary/)

##### Docker 本身

代指`Docker` 这个思想，这个项目，相关的产品，工具、社区等等，不同语境代指不一样。

Docker 是一种可以提供操作系统级别虚拟化（也称作容器）的软件技术。

#####  Repository （仓库）

存放和管理镜像的地方，比如`DockerHub`

##### Image （镜像）

是一个配置和数据的集合，是容器的初始状态

##### Container（容器）

容器是镜像的实例，是可移植的动态调度单元

##### Dockerfile

一个用来生成镜像的配置文件

### Kubernetes

- 已经是解决`Docker`编排需求的行业标准，超过了包括`Docker`官方的`Docker Compose`，用于管理容器化的工作负载和服务
- 高可用、弹性扩容、强大的生态系统，从架构上降低运维成本

#### 相关概念 [完整的概念参考](https://kubernetes.io/zh/docs/reference/glossary/?fundamental=true)

Pod是管理容器的，Service来管理Pods，Pod上的标签Label作为Pod和Service的关联，解决服务扩容和升级的问题只需要目标Pod上创建一个Replication Controller(RC)

##### Container（容器）

容器是可移植、可执行的轻量级的镜像，镜像中包含软件及其相关依赖

##### Pod（容器组）

Pod 是 Kubernetes 的原子对象， 表示集群上一组正在运行的容器，Pod与Pod之间可以直接通信，包含

- Pause容器 
- 其他业务容器 多个业务容器共享Pause容器的IP，共享Pause容器挂载的Volume卷，简化了通信问题，也解决了文件共享问题

##### Service（服务）

将运行在一组 Pods 上的应用程序公开为网络服务的抽象方法，是服务的入口，通过Label Selector跟 Pod副本机器的无缝对接

##### Node（工作节点）

是执行工作的机器，是集群中的工作负载节点，它可以是一个虚拟机或者物理机器，每个节点都包含用于运行Pods的必要服务，并由主控组件管理，最小的管理单元是Pod，除Master外的集群其他机器的统称，包含的服务

- `kubelt` 负责Pod的管理，与Master节点的通信，实现集群管理

- `kube-proxy` 实现Kubernetes Service的通信和负载均衡

  `·Container Runtime`，docker或其他容器运行时，容器引擎，负责本机容器的管理

##### Master(管理节点)

Kubernetes集群控制管理节点,负责整个集群的管理和控制，所有的控制命令都会发送到这里，包含的服务

- `kube-apiserver`  用于暴露Kubernetes API，提供HTTP Rest接口的管理服务，是集群资源操作的唯一入口，任何资源请求或者调用都是通过`kube-apiserver`  提供的接口进行的
- `kube-controller-manager` `Kubernetes` 集群中所有资源对象的自动化控制中心，他们是集群中处理常规任务的后台线程，逻辑上每个控制器都是一个单独的进程，为了降低复杂度，他们都被编译成单个二进制文件并在单个进程中运行
  - `Node Controller`  节点控制器，检查云端节点，以确保节点在停止响应之后在云中是否删除
  - `Route Controller`  路由个控制器，用于在底层云基础架构中设置路由
  - `Service Controller`  服务控制器，用于创建、更新和删除云提供商的负载均衡器
  - `Volume Controller`  卷控制器，用于创建、附加和装载卷，以及与云提供商交互以协调卷
- `cloud-controller-manager`  云控制器管理器
- `kube-scheduler`  负责负载的调度（Pod调度）进程，为Pod选择Node以供运行
- `etcd-server`  是Kubernetes高可用的一致性键值存储系统，也是默认的存储系统，用于存储所有的集群数据，使用时需要为`etcd`提供数据备份计划

##### Volume（卷）

定义在Pod上，被一个Pod中的多个容器挂载到具体的文件目录下，当容器终止或者重启时，Volume中的数据也不会丢失，是Pod中能够呗多个容器访问的共享目录。

##### Label（标签）和 Label Selector（标签选择器）

标签是附加到对象上的键值对，随后可以对这个对象用标签选择器进项查询和筛选拥有特定标签的对象，是Kubernetes实现的简单通用的对象查询机制。

##### Replication Controller（RC 复制控制器）

保障Pod的副本数量在任意时刻都符合预期值，多了就杀掉，少了就创建。

##### Replica Set（RS 副本集控制器）

是下一单 `RC`，跟`RC`的唯一区别是，`RS` 支持基于集合的`Label Selector` 而`RC`只支持基于等式的，`RS`很少单独使用，主要被`Deployment`这个更高层次的资源对象所使用，从而形成一整套Pod创建、删除、更新的编排机制。

##### Deployment（部署控制器）

为Pod和RS 提供声明式更新

##### 插件

- DNS 支持Kubernetes集群系统中各服务之间的发现和调用
- `Web UI(Dashboard)` 是Kubernetes集群基于Web的通用UI，允许用户管理集群以及管理集群运行中的应用程
- `Container Resource Monitoring ` 提供UI监测容器、Pods、服务以及整个集群中的数据，用于检查Kubernetes集群中应用程序的性能
- `Cluster-level Logging` 集群级日志记录和存储，并且提供了搜索/浏览界面。



# 环境部署 

## 开发环境部署 [参考](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-macos)

### macOS

#### 安装kubectl

##### 使用Homebrew

```
brew install kubectl
kubectl version --client
```

##### 使用Macports

```
sudo port selfupdate
sudo port install kubectl
kubectl version --client
```

##### 通用命令行安装

```
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

#### 安装Minikube [参考](https://kubernetes.io/docs/tasks/tools/install-minikube/)

##### 检查是否支持虚拟化，不支持需要安装一种

```
sysctl -a | grep -E --color 'machdep.cpu.features|VMX'
```

##### 使用Homebrew

```
brew install minikube
```

##### 通用命令行安装

```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64 
chmod +x minikube
sudo mv minikube /usr/local/bin
```

#### 启动

这个过程要下载较大体积的内容，可能需要爬墙，请泡杯茶或者来杯咖啡

```
minikube start 
```

- 创建新的集群

  ```
  minikube start -p testcluster
  ```

- 打开可视化面板

  ```
  minikube dashboard
  ```

  

## 测试环境

### CentOS环境

#### 集群规划

| 主机名称                  | 操作系统        | IP             | 系统配置 | 备注         |
| ------------------------- | --------------- | -------------- | -------- | ------------ |
| vm-130.8-centos-k8smaster | centos-7-x86_64 | 192.168.130.8  | 8核32G   | 作为主节点   |
| vm-130.20-centos-k8snode  | centos-7-x86_64 | 192.168.130.20 | 8核32G   | 作为工作节点 |
|                           |                 |                |          |              |

##### 配置主机名 `/etc/hostname`

```
hostname
hostnamectl set-hostname  HOSTNAME
```



##### 配置host `/etc/hosts`

这些内容就是从默认里面拷出来然后改改的得到的就是一个全量

```
127.0.0.1 HOSTNAME HOSTNAME
127.0.0.1 localhost.localdomain localhost
127.0.0.1 localhost4.localdomain4 localhost4

::1 HOSTNAME HOSTNAME
::1 localhost.localdomain localhost
::1 localhost6.localdomain6 localhost6
```

##### 主机配置脚本

```
HOSTNAME="vm-130.8-centos-k8smaster" 
# 设置hostname
hostnamectl set-hostname $HOSTNAME
# 查看hostname
hostname

echo "
127.0.0.1 $HOSTNAME $HOSTNAME
127.0.0.1 localhost.localdomain localhost
127.0.0.1 localhost4.localdomain4 localhost4

::1 $HOSTNAME $HOSTNAME
::1 localhost.localdomain localhost
::1 localhost6.localdomain6 localhost6
192.168.130.8  vm-130.8-centos-k8smaster
192.168.130.20 vm-130.20-centos-k8snode
"> /etc/hosts

# 查看结果
cat /etc/hosts
```

##### 配置动态/静态IP `/etc/sysconfig/network-scripts/ifcfg-eth0`

- 动态IP

  ```
  BOOTPROTO=dhcp
  DEVICE=eth0
  HWADDR=52:54:00:22:47:30
  NM_CONTROLLED=no
  ONBOOT=yes
  PERSISTENT_DHCLIENT=yes
  TYPE=Ethernet
  USERCTL=no
  ```

- 静态IP

  ```
  BOOTPROTO=static
  DEVICE=eth0
HWADDR=52:54:00:22:47:30
  NM_CONTROLLED=no
  ONBOOT=yes
  TYPE=Ethernet
  IPADDR=
  PREFIX=24
  NETMASK=
  NETWORK=
  GATEWAY=
  BROADCAST=
  DEFROUTE=yes
  ONBOOT=yes
  USERCTL=yes
  IPV4_FAILURE_FATAL=yes
  ```
  

##### 配置网络 `/etc/sysconfig/network`

```
NETWORKING=yes
HOSTNAME=
```

##### 配置DNS `/etc/resolv.conf`

```
nameserver 114.114.114.114
nameserver 8.8.8.8
```

##### 重启和查看网络

```
systemctl restart network.service
systemctl status network.service
```



#### 安装Docker

##### 分步安装

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

##### 脚本安装

```
curl -fsSL https://get.docker.com -o get-docker.sh
## 注意安全 最好检查下
sudo sh get-docker.sh
```



#### 安装kubeadm kubelet kubectl

官方的源需要梯子，改成阿里云的

```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
exclude=kube*
EOF

# Set SELinux in permissive mode (effectively disabling it)
setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

systemctl enable kubelet
```

- CentOS 7 配置防火墙

  ```
  cat <<EOF > /etc/sysctl.d/k8s.conf
  net.bridge.bridge-nf-call-ip6tables = 1
  net.bridge.bridge-nf-call-iptables = 1
  EOF
  sysctl --system
  ```

- 启动kubelet

  ```
  systemctl daemon-reload
  systemctl enable kubelet && systemctl start kubelet
  ```

#### 镜像获取

- 查看需要的镜像列表

  ```
  kubeadm config images list
  ```

  

- 获取镜像

  通过国内能够访问的方式来获取，缺点是可能不是最新的包
  
  - 使用脚本的方式来做
  
  ```
  # 下载需要的镜像
  kubeadm config images list |sed -e 's/^/docker pull /g' -e 's#k8s.gcr.io#docker.io/mirrorgooglecontainers#g' -e 's/:/-amd64:/g' |sh -x  
  
  # 重命名镜像
  docker images |grep mirrorgooglecontainers |awk '{print "docker tag ",$1":"$2,$1":"$2}' |sed -e 's#mirrorgooglecontainers#k8s.gcr.io#2' |sh -x  

  # 删除mirrorgooglecontainers镜像
  docker images |grep mirrorgooglecontainers |awk '{print "docker rmi ", $1":"$2}' |sh -x 
  ```
  
  `coredns`不在`mirrorgooglecontainers` 里面，需要单独下载
  
  ```
  docker pull coredns/coredns:1.6.7
  docker tag coredns/coredns:1.6.7 k8s.gcr.io/coredns:1.6.7
  docker rmi coredns/coredns:1.6.7
  ```
  
  - 使用配置文件，镜像命名完全一致可用
  
    ```
    # 生成配置文件
    kubeadm config print init-defaults | sed -e 's#k8s.gcr.io#docker.io/mirrorgooglecontainers#g'> kubeadm.conf
    kubeadm config images list --config kubeadm.conf
    kubeadm config images pull --config kubeadm.conf
    kubeadm init --config kubeadm.conf
    ```
  
- 查看镜像

  ```
  docker images
  ```

#### 主节点配置

- 启动主节点

  ```
  kubeadm init 
  ```

  可以使用`kubeadm init --dry-run`  来查看初始化过程

  ```
  kubeadm init --kubernets-version=xxx --aiserver-advertise-address=xxx --Pod-network-cidr=xxxx ==service-cidr=xxx
  ```

  集群创建成功后，类似于下面的命令需要保存备用

  ```
  kubeadm join IP:PORT --token xxx --discovery-token-ca0cert-hash xxx
  ```

- 认证

  - root 用户

    ```
    export KUBECONFIG=/etc/kubernetes/admin.conf
    ```

  - 非root用户

    ```
    mkdir -p $HOME/.kube
    cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    chown $(id -u):$(id -g) $HOME/.kube/config
    ```

- 安装 `flannel`网格插件

  ```
  kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
  ```

- 检查集群状态

  ```
  kubectl get cs
  ```

- 确认相关Pod已经正常运行

  ```
  kubectl get Pods -n kube-system -o wide
  ```

#### 工作节点配置

- 使用上面保存备用的命令加入集群

  ```
  kubeadm join IP:PORT --token xxx --discovery-token-ca0cert-hash xxx
  ```

- 在主节点上拷贝配置文件到工作节点

  ```
  scp /etc/kubernetes/admin.conf IP:/etc/kubernetes/admin.conf
  ```

- 配置`kubeconfig`文件

  ```
  export KUBECONFIG=/etc/kubernetes/admin.con f
  echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> ~/.bash_profile
  ```

- 查看集群状态

  ```
  kubectl get nodes
  ```

#### 安装dashboard

- 安装

```
docker pull mirrorgooglecontainers/kubernetes-dashboard-amd64:v1.10.1
docker tag mirrorgooglecontainers/kubernetes-dashboard-amd64:v1.10.1 k8s.gcr.io/kubernetes-dashboard-amd64:v1.10.1
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```





## 线上环境

- 参考测试环境配置

# 应用配置

## 使用Deployment对象来运行应用程序

使用yaml文件来定义Deployment对象。

### Deployment配置规范

`deployment-demo.yaml`

```
# API版本对象，可以通过	`kubectl api-versions`命令查看
apiVersion: apps/v1 
# 资源类型，区分大小写，可通过`kubectl api-resources` 命令查看，这里使用Deployment
kind: Deployment  
# 标准元数据
metadata:
	# 当前Deployment对象名称，同一个命名空间下必须唯一
	name: first-demo-deployment
# 部署规范，Deployment控制器会根据此模板调整当前Pod到最终的期望状态
spec: 
	# Pod数量，这里指运行5个Pod
	replicas: 5
	# 选择器，定义Deployment控制器如何找到要管理的Pod
	selector:
		# 匹配标签
		matchLabels:
			app: first-demo
		# Pod的模板定义
		template:
			标准的元数据
			metedata:
				# Pod标签
				labels:
					# 定义Pod标签的键值对
					app: first-demo
			# Pod规范		
      spec:
      	# 容器列表，Pod中至少有一个容器
      	containers:
      		# 容器名称
      		name: first-demo-con
      		# 镜像地址
      		image: node
      		# 端口列表
      		ports: 
      			# 设置容器端口
      			containerPort: 80
      		
```



### 使用`kubectl create`创建资源

```
kubectl create -f deployment-demo.yaml
```

创建和更新资源可以用

```
kubectl apply -f deployment-demo.yaml
```

- 检查资源创建状态

  ```
  kubectl get Deployment first-demo-deployment
  ```

- 查看副本集状态

  ```
  kubectl get ReplicaSets -lapp=first-demo
  ```

- 查看Pod状态

  ```
  kubectl get Pods -lapp=first-demo --show-labels
  ```



## 配置自动伸缩和回滚

- 手动伸缩应用，也可以通过修改配置文件再执行命令的方式来做

  ```
  kubectl scale --replicas=3 deployment/first-demo-deployment
  ```

- 自动伸缩

  ```
  kubectl autoscale deployment/first-demo-deployment --min=2 --max=10
  kubectl autoscale deployment/first-demo-deployment --max=5 --cpu-percent=70
  ```

- ​	快速运行对象

  ```
  kubectl run DEPLOYNAME --image=xxx --env "AA=BB"
  ```

- 更新应用

  ```
  kubectl set env deployment/first-demo-deployment AA=BB
  ```

- 回滚应用

  ```
  kubectl rollout history deplyment/first-demo-deployment
  kubectl rollout status deplyment/first-demo-deployment
  ```

## 服务访问的几种方式

### 通过Service访问应用

- 通过PodIP访问，PodIP会随着Pod的生死而变动

  ```
  kubectl get Pods -lapp=first-demo -o wide # 查Pod状态
  ```

- 通过ClusterIP Service在集群内部访问，Service集群配置文件`clusterIPService.yaml`

  ```
  # api版本信息
  apiVersion: v1
  # 资源类型
  kind: Service
  # 标准元数据
  metadata:
  	# 服务名称
  	name: demo-service
  # 规范定义
  spec:
  	# 服务类型，不填默认为ClusterIP
  	type: ClusterIP
  	# 标签选择器
  	selector: 
  		# 标签键值对
  		app: demo
  	# 端口
  	ports:
  		# 协议，支持TCP和UDP
  		protocol: TCP 
  		# 当前端口
  		port: 80
  		# 目标端口
  		targetPort: 80
  ```

  ```
  kubectl create -f clusterIPService.yaml
  kubectl get services demo-service -o wide
  kubectl get endpoints demo-service -o wide
  ```

### 通过NodePort Service在外部访问集群应用

NodePort类型的Service允许在每个节点的IP上时候用静态端口公开服务，我们可以在集群之外通过请求IP：Port的方式来访问, `nodePortService.yaml`

```
# api版本信息
apiVersion: v1
# 资源类型
kind: Service
# 标准元数据
metadata:
	# 服务名称
	name: nodeport-service
# 规范定义
spec:
	# 服务类型，不填默认为ClusterIP
	type: NodePort
	# 端口列表
	ports:
		# 当前端口
		port: 80
		# 节点端口，默认端口范围为30000-32767
		nodePort: 31001
	# 标签选择器
	selector: 
		# 标签键值对
		app: demo
	# 端口
```

```
kubectl create -f nodePortService.yaml
kubectl get services ndoeport-service
```



### 通过LoadBalancer Service在外部访问集群服务

使用一个负载均衡的IP，来访问集群服务

```
# api版本信息
apiVersion: v1
# 资源类型
kind: Service
# 标准元数据
metadata:
	# 服务名称
	name: demo-service
	# 名称空间
	namespace:default
# 规范定义
spec:
	# 集群IP
	clusterIP:10.100.2.20
	# 负载均衡IP
	loadBalancerIP:10.100.2.2
	# 服务类型，不填默认为ClusterIP
	type: LoadBalancer
	# 端口列表
	ports:
		name: tcp-80-80
		# 当前端口
		port: 80
		# 节点端口，默认端口范围为30000-32767
		nodePort: 31001
		# 协议，支持TCP和UDP
		protocol: TCP 
		# 当前端口
		port: 80
		# 目标端口
		targetPort: 80
	# 标签选择器
	selector: 
		# 标签键值对
		app: demo
		k8s-app: demo
		qcloud-app: demo
```



### 使用Ingress负载分发微服务



## 使用Helm简化Kubernetes应用部署



# 运维场景

## 健康状态检查

### 组件、插件健康状态检查

```
kubectl get componentstatus
```

或者

```
kubectl get cs
```

- 部分插件默认基于`systemd`运行，比如`kubelet`, `Docker`等，我们需要使用以下命令确保其处于活动状态

  ```
  systemctl status kubelet docker
  ```

- 大部分Kubernetes组件运行在命名空间为`kube-system`的静态Pod中，我们可以使用一下命令来查看这些Pod的状态

  ```
  kubectl get Pods -o wide - n kube-system
  ```

### 组件异常分析

- k8s为Master-Slave结构，分为Master组件和节点组件
  - 如果Master组件出问题，就可能导致集群不可访问，Kubernetes API访问出错、各种控制器无法工作等
  - 如果节点组件出问题，就可能会导致该节点异常并且该节点Pod无法正常运行和结束
- kube-apiserver  对外暴露了Kubernetes API，如果`kube-apiserver`出现异常就可能会导致
  - 集群无法访问，无法注册新的节点
  - 资源（Deployment，Service等）无法创建、更新和删除
  - 现有的不依赖Kubernetes API的Pods和Service可以继续正常工作
- etcd用于Kubernetes的后端存储，所有集群数据存在这里，如果etcd出现问题可能会导致
  - kube-apiserver 无法读写集群状态，apiserver无法启动
  - Kubernetes API访问出错
  - kubectl操作异常
  - kubelet无法访问apiserver，仅能继续运行已有的Pod
- kube-controller-manager和kube-scheduler分别用于控制器管理和Pod的调度，如果出现问题就可能导致：
  - 相关控制器无法工作
  - 资源（Deployment，Service 等 ）无法正常 工作
  - 无法注册新的节点
  - Pod无法调度，一直处于Pending状态
- kubelet是主要的代理节点，如果节点宕机或者kubelet出现异常，可能会导致
  - 该节点上Pod无法正常运行，如果节点关机，那么当前节点上所有Pod都将停止运行
  - 已运行的Pod无法伸缩，也无法正常终止
  - 无法启动新的Pod
  - 节点会标识为不健康状态 
  - 副本控制器会在其他节点上启动新的Pod
  - kubelet有可能会删掉当前运行的Pod
- coredns是k8s的默认的DNS服务器，如果出现问题可能导致
  - 无法注册新的节点
  - 集群网络出现问题
  - Pod无法解析域名
- kube-proxy是每个节点上运行的网络代理，如果出现异常，就可能导致该节点Pod通信异常

### 节点健康状态检查

```
kubectl get nodes
```



### Pod健康状态检查

```
kubectl get Pods -o wide
```



### Service 健康状态检查

```
kubectl get svc -o wide
```



## 系统相关检查

### 查看日志

- 查看系统服务日志

  ```
  journalctl -u docker
  ```

- 查看并追踪kubelet的日志

  ```
  journalctl -u kubelet -f
  ```

- 查看容器日志

  ```
  kubectl logs PODNAME
  ```

### 查看资源详情和事件

- 查看资源详情

  ```
  kubectl describe
  ```

  - 查看节点详情

    ```
    kubectl describe nodes [NODENAME]
    ```

  - 查看Pod详情

    ```
    kubectl describe Pods PODNAME
    ```

- 查看资源配置

  ```
  kubectl get
  ```

  - 查看Pod的配置

    ```
    kubectl get Pods PODNAME -o yaml
    ```

  - 查看所有Pod

    ```
    kubectl get Pods -o json
    ```

  - 查看服务配置

    ```
    kubectl get svc SVCNAME  -o yaml
    ```
  - 查看部署配置

    ```
    kubectl get deployments DEPNAME  -o yaml
    ```
    



### 容器状态检查

- 进入容器检查

  ```
  kubectl exec
  ```

- 使用调试工具`kubectl-debug`

  ```
  kubectl debug 
  ```

### 常见问题

- 镜像源问题

  - 可以使用亚马逊国内源 http://mirror.azure.cn
  - 可以使用中科大镜像源 http://mirror.ustc.edu.cn

- coredns添加节点长久等待

  - 先查看服务状态

  ```
  kubectl get Pods -n kube-system  -o wide
  ```

  - 查看 容器日志

    ```
    kubectl log -f CONTAINERNAME -n kube-system
    ```

  - 处理防火墙规则

    ```
    systemctl stop kubelet
    systemctl stop docker
    iptables --flush
    iptables  -tnat --flush
    systemctl start kubelet
    systemctl start docker
    ```

- 添加工作节点，提示token过期

  ```
  kubeadm token generate
  kubeadm token create <token> --print-join-command --ttl=0
  ```

- kubectl 执行命令报错 `the connection to the server host:8080 was refused`

  问题原因是kubectl命令需要使用 kubernetes-admin的身份来运行，在`kubeadm init`启动集群的步骤就生成了`/etc/kubernetes/admin.conf` 需要将主节点中的`/etc/kuubernetes/admin.conf`文件复制到工作节点相同目录下，然后在工作节点配置环境变量，参考工作节点配置

- 网络组建flannel无法完成初始化

  ```
  kubectl get Pods -n kube-system -o wide
  kubectl logs -f NAME -n kube-system
  journactl -u kubelet -f
  ```

- 部分节点无法初始化Pod

  查看日志，然后处理flannel网格出现的问题

  ```
  kubeadm reset
  systemctl stop kubelet && systemctl stop docker && rm -rf /var/lib/cni/ && rm -rf /var/lib/kubelet/* && rm -rf /var/lib/etcd && rm -rf /etc/cni/ && ifconfig cni0 down && ifconfig flannel.1 down && ifconfig docker0 down && ip link delete cni
  systemctl start kubelet
  systemctl start docker0
  ```

  

# 常用内容

## Shell相关

- Shell 脚本调试

  ```
  sh -xv script-to-run.sh
  ```


## 系统相关

- CentOS查看主机基本硬件信息

  - 查看系统信息

    ```
    cat /etc/redhat-release
    uname -a
    ```

  - 查看CPU信息

    ```
    grep "model name" /proc/cpuinfo
    ```

  - 查看内存信息

    ```
    grep MemTotal /proc/meminfo
    ```

  - 查看硬盘信息

    ```
    df -h
    ```


## 安装配置相关

- 主机名错误（只能包含小写字母，中划线和点）

  ```
  could not convert cfg to an internal cfg: nodeRegistration.name: Invalid value: "k8s_master": a DNS-1123 subdomain must consist of lower case alphanumeric characters, '-' or '.', and must start and end with an alphanumeric character (e.g. 'example.com', regex used for validation is '[a-z0-9]([-a-z0-9]*[a-z0-9])?(\.[a-z0-9]([-a-z0-9]*[a-z0-9])?)*')
  ```

  - 解决办法

    ```
    hostnamectl set-hostname xxx
    ```

    



# 参考资料

0. [Dockerfile 官方最佳实践](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
1. [Kubernetes Handbook](https://jimmysong.io/kubernetes-handbook/)
2. [熟悉Docker的参考](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)
3. [Kubernetes 配置参考](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#deployment-v1-apps)

