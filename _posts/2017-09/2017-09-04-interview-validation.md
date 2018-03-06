---
layout: post
title: "当我问表单校验的面试题时，我期望得到什么样的答案"
categories: [前端开发,Web开发,JavaScript]
tags: [JavaScript,正则表达式,校验,面试题,面试]
---



 ## 面试题

校验用户名表单，长度为8-10位的只包含数字和字母的字符串，用JavaScript实现一个校验函数。



## 1 解决过程

### 1.1 首先确认题目需求（几乎没有人确认过，当然也没有人写对过）

#### 1.1.1 题目要求

- 长度8-10位
- 只包含数字和字母
- JS校验函数



#### 1.1.2 Tips

- 几乎没人确认过
- 没人写对过
- 如果这里有问题，后面肯定对不了



### 1.2 其次分析思路（转换为可以写代码的等价逻辑表达，也没人写对过）

#### 1.2.1 等价逻辑转换一

- 包含字母

- 包含数字

- 只能是数字和字母

- 长度8-10位

  ​

#### 1.2.2 等价逻辑转换二

- 不 全为数字
- 不 全为字母
- 只能是数字和字母
- 长度8-10位



#### 1.2.3 等价逻辑转换三

- 所有字符ASCII码在数字和字母的范围内
- 长度8-10位



#### 1.2.4 Tips

- 即使前面需求理解清楚，这里转换不等价也得不到正确的结果
- 有了这里面的等价分解，最基本的TestCase也就有了，便于后面做校验
- 即使写不出代码来，这里能说清楚也行
- 说不清楚也行，需要能看到不断尝试，积极思考的过程



### 1.3 然后是核心代码实现（清一色的正则，我们也先说正则）

#### 1.3.1 使用零宽正向先行断言

##### 1.3.1.1 代码实现

```
/^(?=.*\d.*)(?=.*[a-zA-Z].*)[0-9a-zA-Z]{8,10}$/.test(str)
```



##### 1.3.1.2 代码解释

+ `(?=)`表达正向先行断言，满足条件的其他匹配结果才为真，即括号内的表达式匹配整个匹配结果才为真
+ 可以出现在代码的任意位置
+ 不占用最终的匹配宽度
+ 这里表达既包含数字又包含字母的只包含数字和字母的8-10位的字符串



##### 1.3.1.3 逻辑表达

+ 包含数字
+ 包含字母
+ 8-10位的数字和字母的组合（全匹配）



#### 1.3.2 使用零宽负向先行断言

##### 1.3.2.1 代码实现

```
/^(?!\d+$)(?![a-zA-Z]+$)[0-9a-zA-Z]{8,10}$/.test(str)
```



##### 1.3.2.2 代码解释

- `(?!)` 表达负向先行断言，满足非条件的其他匹配结果才为真，即括号内的表达式不匹配整个匹配结果才为真
- 可以出现在代码的任意位置
- 不占用最终的匹配宽度
- 这里表达不全为数字且不全为字母的只包含数字和字母的8-10位的字符串



##### 1.3.2.3 逻辑表达

- 不全为数字的（全匹配）
- 不全为字母的（全匹配）
- 8-10位的数字和字母的组合（全匹配）

##### 1.3.2.4 

#### 1.3.3 如果不知道上面的方式，可以拆分一下

##### 1.3.3.1 代码实现

```
!/^\d+$/.test(str) && !/^[a-zA-Z]+$/.test(str) && /^[0-9a-zA-Z]{8,10}$/.test(str)
```



##### 1.3.3.2 代码解释

- 不解释了，直接的逻辑表达



##### 1.3.3.3 逻辑表达

- 不全为数字的（全匹配）
- 不全为字母的（全匹配）
- 8-10位的数字和字母的组合（全匹配）



#### 1.3.4 如果不知道正则怎么玩，也可以用字符判断的方式

##### 1.3.4.1 代码实现

```
//考虑记不住ASCII码
var rangeChars = '09azAZ';
var char0Code = rangeChars.charCodeAt(0),
char9Code = rangeChars.charCodeAt(1),
charaCode = rangeChars.charCodeAt(2),
charzCode = rangeChars.charCodeAt(3),
charACode = rangeChars.charCodeAt(4),
charZCode = rangeChars.charCodeAt(5);

Array.from(str).every(char => {
	return '0' <= char && char <= '9' || 'a' <= char && char <= 'z' || 'A' <= char && char <= 'Z' 
});

Array.from(str).some(char => {
    return '0' <= char && char <= '9'
});

Array.from(str).some(char => {
	return 'a' <= char && char <= 'z' || 'A' <= char && char <= 'Z' 
});

8 <= str.length && str.length <= 10
```



### 1.4 最后是结果的输出

```
export const validationUtil = {
    isNameValid:(str) => {
            //调用isNameValid 的同时，不应该有判断undefind,判断null的过程，表单取出来的不会有这俩值
            str += '';
            str = str.trim();
            return str.indexOf('_') === -1 && /^\w{8,10}$/.test(str);
    }，
    isNameValid2:(str) => {
        //调用isNameValid 的同时，不应该有判断undefind,判断null的过程，表单取出来的不会有这俩值
        str += '';
        str = str.trim();
        return /^(?!\d+$)(?![a-zA-Z]+$)[0-9a-zA-Z]{8,10}$/.test(str);
    }
}
```



## 2 常见问题

- 校验不写trim
- 正则不写首尾匹配
- /^[0-9a-zA-Z]{8,10}$/ 作为题目结果
- 自己写出来的正则，自己也不知道啥意思



## 3 参考资料

### 3.1 正则书籍

+ 基础
  + 学习正则表达式
  + 正则表达式必知必会
  + 神奇的匹配
+ 进阶
  + 精通正则表达式
  + 正则指引



### 3.2 正则工具

+ 分析调试工具
  + [Regex Buddy, Regex Magic](http://www.regular-expressions.info/tools.html)
  + [Match Tracer](http://www.regexlab.com/zh/mtracer/)
  + [正则表达式101](http://regex.zjmainstay.cn/)
  + [regex101](https://regex101.com/)
  + [regexr](https://regexr.com/)
+ 可视化分析
  - [regulex](https://jex.im/regulex/)
  - [regexper](https://regexper.com/)

