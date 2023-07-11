# LDAP介绍
[LDAP(Lightweight Directory Access Protocol)](https://zh.wikipedia.org/wiki/%e8%bd%bb%e5%9e%8b%e7%9b%ae%e5%bd%95%e8%ae%bf%e9%97%ae%e5%8d%8f%e8%ae%ae)是一个基于X.500(早期国际电信联盟(ITU-T)指定的目录服务标准)的协议，在各个平台或者系统中有自己的实现。可以类似理解为咱们的目录和文件树。
基于LDAP协议的产品有不少，比如微软的Active Directory，IBM 的 Domino Directory，以及LDAP的开源实现 OpenLDAP等。
### LDAP的结构定义 objectClass
结构定义为对象的属性信息，也就是可以出现的字段。
- olcGlobal 全局配置文件类型
- top 顶层的对象
- organization 组织，比如公司
- organizationalUnit 组织单元，比如分组，部门等
- inetOrgPerson 用户节点 叶子节点，表示用户或者具体的信息
- groupOfNames 分组的group类型，标记一个group节点
- olcModuleList 配置模块的对象
#### 字段信息（树形结构）
- dc(Domain Component) 域名的几个部分 不如baidu.com 拆为dc=baidu,dc=com，表示公司层级
- ou(Organization Unit) 组织单元，类似于目录的子目录，可以包含其他目录或者文件，表示部门层级
- uid 用户ID 比如 lilei
- cn(Common Name) 公共的名称 比如 jim green，一般是邮箱前缀
- sn(Surname) 姓 green
- dn(Distinguished Name) 唯一识别名称，类似于目录的绝对路径，全局唯一
- rdn(Relative Distinguished Name) 相对识别名称，比如uid=lilei或者cn=jim green
- c(Country) 国家信息
- o(Organization) 组织名称
#### 认证匹配过程
- 配置LDAP服务
- 用户传递cn(比如aa)给LDAP服务认证，读取LDAP信息，匹配cn字段获得dn(cn=aa,ou=dev,dc=baidu,dc=com)
- 根据dn和密码再次认证，验证通过即为认证通过
- o(dc=baidu,dc=com)
	- ou(dev)
		- cn=aa
		- cn=bb
		- cn=cc
	- ou(market)
	- ou(hr)

# OpenLDAP介绍
## OpenLDAP 安装
### macOS 
#### 安装
macOS上安装基础技术组件相对比较简单，先安装一个包管理器
- homebrew https://brew.sh/
- macports https://www.macports.org/
这里以brew 为例，按文档装上之后就可以获得一个brew命令。
- 安装
```
brew install openldap
```
#### 配置
- 生成配置文件
```
cd /etc/openldap/
sudo cp slapd.conf.default slapd.conf
```
- 生成密码
```
slappasswd
```
- 将生成的密码填入 /etc/openldap/slapd.conf 对应位置
```
rootpw xxxx #上面的密码
```
- 其他的不用动 最后核心的内容大致这样
```
database        bdb
suffix          "dc=my-domain,dc=com"
rootdn          "cn=Manager,dc=my-domain,dc=com"
# Cleartext passwords, especially for the rootdn, should
# be avoid.  See slappasswd(8) and slapd.conf(5) for details.
# Use of strong authentication encouraged.
rootpw          {SSHA}Ny6u1rsdp0DIOGhIltypAWhchYlfjxXXt
```
#### 启动
```
sudo /usr/libexec/slapd
```
- 校验服务是否正确启动
```
netstat -an | grep 389
```
- 详细日志启动(如果有问题启动失败，用这个排查)
```
sudo /usr/libexec/slapd -d3 -s1 # 带调试信息说明的
```
#### 测试验证
##### 命令行
```
ldapsearch -x -D "cn=Manager,dc=my-domain,dc=com" -W -h 127.0.0.1
```

##### 可视化客户端
- 安装JDK
```
brew install --cask temurin
```
- 安装ApacheDirectoryStudio
```
brew install apache-directory-studio
```