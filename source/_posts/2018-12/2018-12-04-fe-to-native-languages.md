---
layout: post
title: "前端视角切入原生之语言特性——鸿蒙、Android、iOS、Flutter"
cagegories: [Web开发,前端开发,移动端]
tags: [原生,Native,鸿蒙,harmonyos,安卓,Android,苹果,iOS,Flutter]
---



# Flutter——Dart

## 语言特性

- 函数是一级对象
- 支持动态类型，支持类型系统

## 注释

- `//` 单行注释
- `///`  `/**/`  多行注释

## 变量

### 变量声明

- `var` 关键字定义变量，分号分隔语句。
- `int a = null;` 或`int ? a;`支持 `null`初始值 `null safe`
- `Object` 或者 `dynamic`  用于可变类型
- `final` 或者 `const` 表示不可修改
- `late` 修饰符表示现在不初始化
- `num` 类型表示 `int` 或者 `double`



## 运算符

- `?`  变量定义时初始为`null` ，如`int ? a;`
- `?` 函数定义时为最后的可选形参(命名形参`String ? name`和位置形参`[int ?b, int ? c]`)
- `?.` 属性访问时避免`null`被取值, 如 `obj?.prop`
- `??` 只有前面值为`null`才起作用 如`a ?? = 3; print(null ?? 5);`
- `..` 调用方法放弃函数返回值直接返回对象引用，实现类jq的级联效果
- `typedef` 可以重新定义类型



## 数据结构

- 列表 List `[1,2,3]`
- 集合 Set {1,2,3}
- 键值对 Map {'a':1,'b':2}
- 范型 `<T>` 支持参数限制 `<T extends BaseClass>`



## 模块

### 模块定义

- 函数定义带返回值类型，支持箭头函数
- `mixin` 定义的模块，可以用`with` 在类之间共享，可以多个
- 支持类单继承使用`extends` 和 多接口实现 `implements`
- 用类来充当接口，可以用`abstract`关键字定义抽象类

### 模块导入

- `import 'dart:math' 导入系统模块`
- `import 'package:path/to/file' 导入第三方模块`
- `import 'path/to/file' 导入文件`
- `import 'path' as xx`



## 异步

- `Future`表达`Promise`
- 支持 `await-async`



## 异常捕获

- `rethrow 可以抛出`

```
try{

} on IOException catch(e){

} catch (e){
	rethrow;
} finally{

}
```

