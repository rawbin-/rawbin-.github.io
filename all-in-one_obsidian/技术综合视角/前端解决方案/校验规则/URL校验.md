# 使用组件库校验
## antd
- 配置 表单 rule type=url

## element ui
- 配置 表单 rule type=url

## semi ui
-  配置 表单 rule type=url


# 使用npm包校验
- 安装url-regex 包 (也是antd等包的校验async-validator 最终使用的包)
- 使用urlRegex({exact:true}).test(url)来校验


# 自定义校验
## 直接实现
- 参考url规范
	- https://www.rfc-editor.org/rfc/rfc1738
	- https://www.w3.org/Addressing/rfc1808.txt
	- https://www.rfc-editor.org/rfc/rfc1945.txt
- 实现对应的校验规则参考（网上找到相对有代表性的）
```
export const isUrlValid = (url) => {

const urlregex = /^(https?|ftp):\/\/([a-zA-Z0-9.-]+(:[a-zA-Z0-9.&%$-]+)*@)*((25[0-5]|2[0-4][0-9]|1[0-9]{2}|[1-9][0-9]?)(\.(25[0-5]|2[0-4][0-9]|1[0-9]{2}|[1-9]?[0-9])){3}|([a-zA-Z0-9-]+\.)*[a-zA-Z0-9-]+\.(com|edu|gov|int|mil|net|org|biz|arpa|info|name|pro|aero|coop|museum|[a-zA-Z]{2}))(:[0-9]+)*(\/($|[a-zA-Z0-9.,?'\\+&%$#=~_-]+))*$/;

return urlregex.test(url);

}


```

## 利用标准对象实现
- 参考
	- https://developer.mozilla.org/zh-CN/docs/Web/API/URL
	- https://url.spec.whatwg.org/#constructors
```
export const isUrlValid = (url) => {
	try{
		new URL(url);
		return true;
	}catch(e){
		return false
	}
}

```

##  校验匹配IP和域名的HTTP地址
- 参考 
	- 域名 https://www.rfc-editor.org/rfc/rfc1035.txt
	- 主机名 https://www.rfc-editor.org/rfc/rfc1123.txt
	- HTTP https://www.rfc-editor.org/rfc/rfc1945.txt
## 拆解分析
- 协议头
```
http?:\/\/
```
- 域名
```
 ([a-zA-Z0-9-]+\.)*[a-zA-Z0-9-]+\.(top|xyz|xin|vip|red|wang|com|edu|gov|int|mil|net|org|biz|arpa|info|name|firm|nom|rec|store|web|pro|xxx|cat|art|aero|coop|jobs|museum|travel|mobi|asia|arpa|root|tel|post|fans|ren|club|city|[a-zA-Z]{2,4}))

```
- IP v4 正则 
```
(25[0-5]|2[0-4][0-9]|1[0-9]{2}|[1-9][0-9]?)(\.(25[0-5]|2[0-4][0-9]|1[0-9]{2}|[1-9]?[0-9])){3}
```
- IP v6正则
```

```
- 端口 0-65535
```
(6553[0-5]|655[0-2]\d|65[0-4]\d{2}|6[0-4]\d{4}|[1-5]\d{4}|[1-9]\d{1,3}|[0-9])

```
- 路径
```

```