---
layout: post
title: "TypeScript简明参考指南"
sticky: 98
cagegories: 
- 前端开发
tags: 
- TypeScript
- 编程语言
---



# 类型系统

## 相关界定/定义

### 类型定义

- 类型别名的声明（`type alias`）
- 接口声明（`interface`）
- 类声明（`class`）
- 枚举声明（`emum`）
- 指向类型的导入声明（`import` ）

### 值的定义

- 变量声明（`let`,`const`,`var` 声明）
- 包含值的名称空间或者模块的声明（`namespace`,`module`）
- 枚举声明（`emun`）
- 类声明（`class`）
- 指向值的导入声明（`import`）
- 函数声明（`function`）

## 类型分类

### 简单类型

- 类型字面量（数字，字符串）
- `object`
- `string`
- `number`
- `boolean`
- `bigint`
- `symbol`
- `Function`
- `null`
- `undefined`
- `unknown`
- `never`
- `void`
- `any`
- `X[]` 基础类型X的数组
- [X,Y] 基础类型的元组
- `any` 任意类型

### 复合类型

- 对象类型（`object`）
- 联合类型（`union`）
- 类型别名（`type alias`）
- 接口类型（`interface`）
- 枚举类型（`enum`）
- 交集类型（`intersection`）
- 泛型（`generics`）`<Type>`
- 数组类型`Array<Type>`
- 只读数组`ReadOnlyArray<Type>`



### 内置实用类型

- `Partial<Type>` 所有属性可选
- `Required<Type>`所有属性必选
- `ReadOnly<Type>`所有属性只读
- `Record<Keys,Type>` 用`Keys`限制的`key`和用`Type`限制的`value`的对象类型
- `Pick<Type,Keys>` 从`Type`中选择`Keys`中的字段作为`key`的对象
- `Omit<Type,Keys>`从`Type`中移除`Keys`中的字段剩下的作为字段作为属性的对象
- `Exclude<Type,ExcludedUnion>`从`Type`中排出不能赋值给`ExcludedUnion`剩下的
- `Extract<Type,Union>`从`Type`中取能赋值给`Union`的
- `NonNullable<Type>`排除`null`和`undefined`
- `Parameters<Type>`从函数`Type`参数中取
- `ConstructorParameters<Type>`从构造函数`Type`参数中获取 
- `ReturnType<Type>`从函数`Type`返回值获取
- `InstanceType<Type>`从构造函数`Type`获取实例类型
- `ThisParameterType<Type>`取函数类型的`this`参数的类型，没有的话就用`unknown`
- `OmitThisParameter<Type>` 从`Type`中删除`this`参数，没有就用`Type`，有就重新生成一个函数类型
- `ThisType<Type>`使用了`noImplicitThis`要打开，标注`this`
- `Uppercase<StringType>`
- `Lowercase<StringType>`
- `Capitalize<StringType>`
- `Uncapitalize<StringType>`

## 类型应用

### 函数中应用

#### 参数中应用

- 可调用参数`Function`

- 函数类型表达式 `(a: string) => boolean`

- 函数类型别名 `type OneFunType = (a: string) => boolean`

- 调用签名（`call signatures`）

  ```javascript
  type DescribableFunction = {
    description: string;
    (someArg: number): boolean;
  };
  function doSomething(fn: DescribableFunction) {
    console.log(fn.description + " returned " + fn(6));
  }
  ```

- 构造签名（`construct signature`）

  ```javascript
  type SomeConstructor = {
    new (s: string): SomeObject;
  };
  function fn(ctor: SomeConstructor) {
    return new ctor("hello");
  }
  
  interface CallOrConstruct {
    new (s: string): Date;
    (n?: number): number;
  }
  ```

- 泛型函数

  ```javascript
  function firstElement<Type>(arr: Type[]): Type | undefined {
    return arr[0];
  }
  ```

- 函数重载

- `this声明`

  ```javascript
  interface DB {
    filterUsers(filter: (this: User) => boolean): User[];
  }
  ```

  

#### 工厂方法中用

使用 `new` 关键字一起声明

```javascript
function create<Type>(c: { new (): Type }): Type {
  return new c();
}
```

### 类中应用



# 代码组织

## 模块 `module`

### 导出 `export`

- `export let/const/var/interface/class/  XX`
- `export {XX,YY}`
- `export {XX as YY} from 'in-module.ts'`
- `export default XX`
- `export * as XX from 'in-module.ts'`
- `export = XX`



### 导入 `import`

- `import {XX} from 'in-module.ts'`
- `import {XX as YY} from 'in-module.ts'`
- `import * as XX from 'in-module.ts'`
- `import 'in-module.ts'`
- `import type {XX} from 'in-module.ts'`
- `import XX = require('../in-module.ts')`

## 名称空间 `namespace`

`namespace XXX{}` 可以实现

- 在模块内对模块进行逻辑分区 
- 跨模块对名称空间内的内容进行合并