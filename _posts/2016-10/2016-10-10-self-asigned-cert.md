---
layout: post
title: "使用OpenSSL生成多域名自签名证书进行HTTPS开发调试"
categories: [Web开发,前端开发]
tags: [证书,自签名证书,openssl]
---

[TOC]

### 1 证书生成过程介绍

证书的目的是建立特定密钥对与特定实体之间的联系。

自签名根证书是指一堆密钥对的私钥对自己相应的公钥生成的证书请求进行签名而颁发的证书，证书的申请人和签发人都是同一个。

+ 需要一对密钥对；
  + 常用的是RSA，DSA密钥，ECDSA密钥，用于密钥交换的证书不能用DSA，只能用RSA，因为DSA不能加解密也不能做密钥交换，只能做签名；ECDSA还没有被大部分的CA支持；
  + 生成密钥可以使用OpenSSL的`genrsa` 和 `gendsa`指令，也可以使用req指令；
+ 证书认证请求文件(csr文件)
  + 这是按照一定格式生成的文件，里面包含实体信息
  + 将实体信息和公钥一起用相应的私钥签名，是CA（证书认证）能确认这些信息是用户发送的（证书请求中的公钥拿出来验证这个证书请求的签名）
  + 通过OpenSSL的`req`指令填写和生成这个csr文件
  + 不同的CA要求，可能需要更改默认的配置文件`openssl.cnf`
+ 将证书请求文件(csr文件)给CA签发
  + 验证证书请求上的签名是否正确，确保公钥对应的私钥就在申请者手中并且申请信息是正确没被更改的
  + CA对某些字段信息可能有特殊的要求，比如要求国家，身份等信息要求跟CA本身设定的一样
  + 如果CA是在OpenSSL指令的基础上，那么它使用的签发指令可能是`ca`，也可能是`x509`



### 2 常用指令

#### 2.1 req 指令

`req`指令既可以直接生成一个新的自签名证书，也可以根据现有的证书请求和其相应私钥生成自签名根证书。如果是根据现有证书请求生成自签名根证书，那么一定要`-key`选项指定相应的私钥指令才能执行成功。

`req` 指令也可以生成密钥对，但在使用`req` 同时生成密钥对是对密钥对保存和格式有限制（只能是PEM编码，DES3-CBC模式加密）。如果需要更灵活的处理，可以使用`genrsa`或者`gendsa`先生成密钥然后使用`-key`选项指定。

##### 2.1.1 参数选项

+ `-new` 指定执行生成新的证书请求，此时会忽略`-in` 指定的内容
+ `-x509` 根据现有的证书请求生成自签名根证书（要求使用`-key`指定证书请求里面的公钥相应的私钥，以便对自签名根证书进行签名）
+ `-key` 指定输入的密钥，如果不指定此选项会根据`-newkey`选项的参数生成密钥对
+ `-newkey` 指定生成一个新的密钥对，只有在没有`-key` 选项的时候才生效，参数形式为`rsa:numbits` 或者 `dsa:file`
+ `-subj`直接从指令行指定证书请求的主体名称，格式为`/`分割的键值对字符串，如果没有此选项，那么会弹出交互提示；
+ `-days` 设定了生成的自签名根证书的有效期，单位为天；该选项只有在使用了`-x509`选项生成自签名证书的时候才生效，默认为30天。
+ `-config` 指定`req`指令在生成证书请求的时候使用的OpenSSL配置文件，一般默认为`openssl.cnf` 
+ `-extensions` 选项指定了生成自签名根证书的时候使用的扩展字段，其参数为OpenSSL配置文件中的某个字段名
+ `-reqexts` 选项指定了生成证书请求是使用的扩展字段，该字段参数也是配置文件中的某个字段名
+ `-text` 让指令输出证书请求或者自签名根证书内容的明文解析，默认情况下，它将输出所有可能输出的内容，如果使用了`reqopt`选项，则输出内容取决于`reqopt`选项
+ `-reqopt` 指定`text` 选项输出的内容,可以为多个，每个之间使用`,`分隔
+ `set_serial`指定生成的自签名根证书的序列号，默认情况下生成的自签名根证书序列号是0；该选项也只有在生成自签名根证书的时候有效。
+ `-keyout` 置顶新生成的私钥的输出（仅在使用了`-newKey` 或` -new` 选项导致生成新密钥对的时候才有效，如果使用了`-key`则此选项被忽略）
+ `-keyform` 指定输入密钥的编码格式（比如PEM，DER，PKCS#12，Netscape，IIS SGC，Engine等）
+ `-in` 指定输入证书请求文件，如果使用了`-new` 或者 `-newkey`选项，此选项被忽略
+ `-inform` 指定输入证书请求文件的编码格式（比如PEM，DER）
+ `-out` 指定输出证书请求文件或自签名证书文件
+ `-noout` 使用此选项后，指令将不会输出编码的证书请求或者自签名根证书到`-out`选项指定的文件中，一般用来测试指令或者查看证书请求的信息
+ `-outform` 指定输出证书请求文件或自签名证书的编码格式（比如PEM,DER）
+ `-pubkey` 使用此选项活 指令将输出PEM编码的公钥到`-out`选择指定的文件中，默认情况下只输出私钥到`-keyout`指定的文件，并不输出公钥。 
+ `-passin` 指定读取`-key` 选项指定的私钥所需要的解密口令，如果没有指定，私钥又有密钥的话，会弹出交互提示
+ `-passout` 指定`-keyout` 选项输出私钥时使用的加密口令
+ `-nodes` 表示不对私钥进行加密，如果指定此选项，则忽略`-passout`指定的口令；如果没有此选项，却指定了`-passout`则会有交互提示。
+ `-digest` 指定生成证书请求或者自签名根证书是使用的信息摘要算法，一般在生成数字签名的时候使用。
+ `-verify` 使用此选项对证书请求中的数字签名进行验证操作，并给出失败或者成功的提示信息，其验证的过程是从证书请求里面提取公钥，然后使用该公钥对证书请求的数字签名进行验证。
+ 如果没有`-key`选项也没有`-newkey`选项，则会根据`openssl.cnf`中`req`字段的`default_bits`选项的参数，生成一个RSA密钥
+ 如果没有使用`-nodes`选项，并且生成了新的私钥，私钥会被输出到`-keyout`指定的文件中时将被以DES3的CBC模式加密。



##### 2.1.2 使用实例

+ 生成一个新的证书请求，使用新的`rsa`2048位密钥，输出证书请求到`request.pem`，密钥到`private.pem`,私钥口令为`12345678`

```
openssl req -new -newkey rsa:2048 -keyout private.pem -passout pass:12345678 -out request.pem
```

+ 对证书请求签名进行验证

```
openssl req -in request.pe -verify -noout
```

+ 生成一个自签名的根证书

```
openssl req -x509 -newkey rsa:2048 -keyout private.pem -passout pass:12345678 -out selfsign.crt
```

+ 显示证书请求内容

```
openssl req -text -in fd.csr -noout
```



#### 2.2 ca 指令

`ca`指令模拟一个完整的CA服务器，它包括签发用户证书，吊销证书，产生CRL及更新证书库等管理操作

##### 2.2.1 参数选项

+ `-config` 指定要使用的配置文件，如果没有此选项，则会先查找`OPENSSL_CONF`或者`SSLEAY_CONF`定义的文件名，如果这两个环境变量都没有定义，就使用OpenSSL安装的默认路径，一般是`/usr/local/openssl/openssl.cnf`，具体看安装配置
+ `-startdate` 设置证书的生效时间 格式为`YYMMDDHHMMSSZ`指定`年月日时分秒`，如果没有则使用主配置文件中的`default_startdate`
+ `-enddate` 格式跟`-startdate`一样
+ `-days` 设置证书的有效天数，生效时间到到期时间之间的天数，如果使用了`-enddate`，此选项被忽略
+ `-name` 指定配置文件中CA选项的名称
+ `-notext` 不输出明文信息到证书文件
+ `-subj`直接从指令行指定证书请求的主体名称，格式为`/`分割的键值对字符串，如果没有此选项，那么会弹出交互提示；
+ `-cert`  参数是一个可以包含路径的文件名，该文件是一个PEM编码的X.509证书文件
+ `-keyfile`  参数是一个包含路径的文件名，文件格式可以为PEM，DER，PKCS#12，Netscape，IIS SGC，Engine，但需要通过`-keyform`指定到底是哪种格式
+ `-policy` 指定CA的匹配策略
+ `-extensions` 指定`x509 v3`扩展字段的字段名，如果没有这个选项就由`-extfile`中内容
+ `-extfile` 指定`x509 v3`扩展的配置文件，如果没有`-extensions`字段，则由CA主配置文件中的`x509_extensions`选项指定
+ `-in` 指定一个可以包含路径的证书请求文件名，应该是PEM变得PKCS#10格式的证书请求
+ `-infiles`指定一系列包含PEM编码证书请求的文件，包含多个，只能作为指令的最后一个选项，其后的参数都被认为是证书请求文件
+ `-out` 选项指定了输出签发好的证书或者新生成的CRL的文件，如果没有使用`-notext`选项，那么证书的明文信息也会输出到`-out`选项指定的文件中
+ `-outdir`选项指定了新生成的证书的输出目录，默认输出到`newecerts`目录，并使用`.pem`作为后缀，都是PEM编码。

#### 2.3 x509

`x509`指令能已各种方式显示一个证书的内容，也可以对一个证书的格式进行转换，还可以签发证书

##### 2.3.1 参数选项

+ `-in`
+ `-inform`
+ `-out`
+ `-outform`
+ `-keyform`
+ `-CA` 指定签发证书或者转换证书格式的时候需要的CA证书文件
+ `-CAkey` 指定签发证书或者转换证书格式时需要的CA证书对应的私钥文件路径
+ `-CAform` 指定CA证书文件的格式
+ `-CAkeyform` 私钥文件格式
+ `-startdate`
+ `-enddate`
+ `-pubkey`
+ `-noout`

##### 2.3.2 使用实例

+ 使用现有证书生成证书请求文件 

```
openssl x509 -x509toreq -in fd.crt -out fd.csr -signkey fd.key
```

+ 使用证书请求文件生成自签名证书

```
openssl x509 -req -days 365 -in fd.csr -signkey fd.key -out fd.crt
```

+ 显示证书内容

```
openssl x509 -text -in fd.crt -noout
```



#### 2.4 crl指令

`crl`指令用于显示、处理和验证CRL文件信息

##### 2.4.1 参数选项

+ `-in`
+ `-inform`
+ `-out`
+ `-outform`
+ `-noout`



### 3 开发测试使用自签名证书实例

#### 3.1 生成认证私钥 私钥 私钥

要输入密码

 ```
openssl genrsa -des3 -out qunarzz-dev-enc.key 2048
 ```

可以通过如下方式解密(输入上面的密码)

```
openssl rsa -in qunarzz-dev-enc.key -out qunarzz-dev.key
```



#### 3.2 生成带有SAN(Subject Alt Name)的自签名证书

#### 3.3 生成对多个主机有效的自签名证书

#### 3.4 missing_subjectAltName 问题解决

原有的简单自签名证书在chrome里面不好使了，提示 `missing_subjectAltName`



需要全局找一份openssl.cnf ，或者下载一份openssl 那里有

```
openssl req -new -sha256  \
    -x509 \
    -days 10000 \
    -key qunarzz-dev.key \
    -subj "/C=CN/ST=BeiJing/L=Beijing/O=QUNAR/OU=FE/CN=qunarzz.com" \
    -extensions SAN \
    -config <(cat ./openssl.cnf \
        <(printf "[SAN]\nsubjectAltName=DNS.1:qunarzz.com,DNS.2:q.qunarzz.com,DNS.3:*.qunarzz.com")) \
    -out qunarzz-dev.crt
```

相关字段的信息

```
C  => 国家 Country 
ST => 省 State
L  => 市 City
O  => 机构 Organization
OU => 部门 Organization Unit
CN => 域名 Common Name (证书所请求的域名)
emailAddress => main administrative point of contact for the certificate
```



### 4 参考资料

0. 《OpenSSL与网络信息安全-基础、结构和指令》
1. 《OpenSSL攻略》
2. 《OpenSSL编程》
3. [OpenSSL文档](https://www.openssl.org/docs/manmaster/)
4. [OpenSSL命令](https://www.openssl.org/docs/manmaster/man1/openssl.html)
5. [OpenSSL 给自己颁发根证书，由根证书签发下级证书的步骤。](http://blog.csdn.net/xu_0705/article/details/34435445)
6. [Nginx 使用 openssl 的自签名证书](https://infong.me/openssl-self-signed-certificate-and-nginx-config/?utm_source=tuicool&utm_medium=referral)
7. [使用 OpenSSL 制作一个包含 SAN（Subject Alternative Name）的证书](http://liaoph.com/openssl-san/?utm_source=tuicool&utm_medium=referral)
8. [使用openssl为ssl证书增加“使用者备用名称（DNS）”](http://colinzhouyj.blog.51cto.com/2265679/1566438)
9. [openssl证书添加多个IP](http://blog.csdn.net/linsanhua/article/details/16986701)
10. [使用openssl为ssl证书增加“使用者备用名称（DNS）”](http://colinzhouyj.blog.51cto.com/2265679/1566438)
11. [Multiple SSL Sites Using SubjectAltName](https://www.linode.com/docs/websites/ssl/multiple-ssl-sites-using-subjectaltname)
12. [*Multiple Names on One Certificate*](http://apetec.com/support/GenerateSAN-CSR.htm)
13. [多个SSL站点使用SubjectAltName教程](http://blog.sina.com.cn/s/blog_bcb1f6440102xwx9.html)
14. [[openssl生成证书链多级证书](http://www.cnblogs.com/gsls200808/p/4502044.html)](http://www.cnblogs.com/gsls200808/p/4502044.html)
15. [Subject Alt Name](https://www.openssl.org/docs/man1.0.2/apps/x509v3_config.html)
16. [完全图解 HTTPS](https://juejin.im/post/5c441073e51d455226654d60?utm_source=gold_browser_extension)

