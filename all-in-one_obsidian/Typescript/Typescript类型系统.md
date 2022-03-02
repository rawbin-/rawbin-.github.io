# 核心概念
- [参考](https://www.typescriptlang.org/docs/handbook/declaration-files/deep-dive.html)
## 类型
- 类型别名声明`type sn = string | number`
- 接口声明 `interface INum {x:number[]} `
- 类声明 `class X {}`
- 枚举声明 `enum E {A,B,C}`
- 引用类型的`import`声明

## 值
- 变量定义 `let` `const` `var`声明
- 名称空间或模块声明 `namespace` `module`声明（带有值的）
- 枚举声明 `enum`声明
- 类声明 `class`声明
- 引用值的`import`声明
- 函数`function`声明

## 名称空间
- 模块内的代码组织方式，可以不是值或者类型

## 声明合并
- 声明以上三种类型，`类型`，`值`，`名称空间`
### `interface`内合并
- 非函数的成员应该唯一，不唯一的话，必须要类型相同
	- 两个以上的同名接口定义了类型不同的相同成员会报错
- 函数成员会被当做重载，一致的会被覆盖还是报错？

### `namespace`内合并
- 类型合并
- 值合并
- 非`export`成员在合并后的结果中不可见

### `namespace`和 `class`合并
- `namespace`可以给 `class`添加静态成员（合并后的结果）

### `namespace`和`function`合并
- `namespace`可以给 `function`添加静态成员（合并后的结果）
### `namespace`和`enum`合并
- `namespace`可以给 `enum`添加静态成员（合并后的结果）

### 模块扩展
```ts
// observable.ts
export class Observable<T> {
  // ... implementation left as an exercise for the reader ...
}
// map.ts
import { Observable } from "./observable";
declare module "./observable" {
  interface Observable<T> {
    map<U>(f: (x: T) => U): Observable<U>;
  }
}
Observable.prototype.map = function (f) {
  // ... another exercise for the reader
};
// consumer.ts
import { Observable } from "./observable";
import "./map";
let o: Observable<number>;
o.map((x) => x.toFixed());
```

### 全局扩展
```ts
// observable.ts
export class Observable<T> {
  // ... still no implementation ...
}
declare global {
  interface Array<T> {
    toObservable(): Observable<T>;
  }
}
Array.prototype.toObservable = function () {
  // ...
};
```


# 类型系统

## 相关界定/定义

### 类型定义
- 名称空间中类型声明（`namespace`）
- 类型别名的声明（`type alias`）
- 接口声明（`interface`）（可合并，有readonly）
- 类声明（`class`）
- 枚举声明（`emum`）（可合并）
- 指向类型的导入声明（`import` ）
- 类型数组 `type[]`
- 元组类型 `[typeA,typeB]`
- 特殊类型 `any null undefined void unknown`
- 范型 `<T>`
- 联合类型 `typeA | typeB`
- 交叉类型 `typeA & typeB`
- 内置其他复合类型
- 强制类型转换 （会导致不会有编译器类型提示）
	- `<Type>xxxValue`
	- `xxx as Type`
```ts
function handler(event: Event) {
  const element = (event as any) as HTMLElement; // ok
}
```
- 可调用类型 
	```ts
	interface ReturnString{
		(): string
	}

	const a: ReturnString
	const a: () => string

	```
- 可实例化类型
```ts
	interface newAble{
		new (): string
	}

```
- 索引类型
```ts
	const indexObj = {
		[index: string] = {message:string}
	}

```

### 值的定义
- 名称空间中类型声明（`namespace`）
- 变量声明（`let`,`const`,`var` 声明）
- 包含值的名称空间或者模块的声明（`namespace`,`module`）
- 枚举声明（`emun`）
- 类声明（`class`）
- 指向值的导入声明（`import`）
- 函数声明（`function`）
- 特殊值 `null undefined void`

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

### 类型保护
- `typeof`
- `keyof`
- `instanceof`
- `in`
- `is`
- 字面量类型保护（类型推断）
- 自定义类型保护

### 应用注意
#### 不要用
- 不要用`Number`,`String`,`Boolean`,`Symbol`,`Object`

#### 尽量不要用
- `any`
- `Function`

#### 推荐用
- `number`,`string`,`boolean`,`symbol`,`object`
- `void`

## 类型系统扩展
类型声明可以用来作为类型注解，变量声明可以用来作为变量
定义的既可以是类型也可以是变量
可以用来声明类型的有`名称空间，类，枚举，接口，类别名`
可以用来声明变量的有`名称空间，类，类别名，函数，变量(let const var)`
`枚举，接口`只能用来声明类型

### 声明全局类型
- 使用`declare`声明名称空间、接口、全局变量等等，让Typescript知道有
- `global.d.ts` 是一种扩充 `lib.d.ts` 很好的方式
- 全局类型声明是一个很好的处理代码迁移的方式

```
// global.d.ts
declare module 'foo' {
  // some variable declarations
  export var bar: number;
}
```

```
// anyOtherTsFileInYourProject.ts
import * as foo from 'foo';
// TypeScript 将假设（在没有做其他查找的情况下）
// foo 是 { bar: number }
```

```
import foo = require('foo');
```

### 继承已有类型



# 代码组织

## 模块 `module`

### 导出 `export`

- `export let/const/var/interface/class/type  XX`
- `export {XX,YY}`
- `export {XX as YY} from 'in-module.ts'`
- `export default XX`
- `export * as XX from 'in-module.ts'`
- `export = XX` (Typescript 特有)
- `export type Type = XXX`（Typescript 特有）



### 导入 `import`

- `import {XX} from 'in-module.ts'` // 类型或者模块
- `import {XX as YY} from 'in-module.ts'`
- `import * as XX from 'in-module.ts'`
- `import './in-module.ts'`
- `import type {XX} from 'in-module.ts'`
- `import XX = require('../in-module.ts')` // 仅仅是导入类型
- `import XX = AA.BB.CC` // 创建别名或引入内层

## 名称空间 `namespace`

`namespace XXX{}` 可以实现
- 在模块内对模块进行逻辑分区 
- 跨模块对名称空间内的内容进行合并

### 以下情况禁用名称空间
- 唯一顶层声明是`export namespace Foo{...}`的文件，往上抽取一层，去掉`namespace`的定义
- 多个有同样声明`export namespace Foo{}`在顶层的文件，他们不会按预期进行合并

## 名称空间和模块区别
- 模块可以同时包含代码和声明，包含模块依赖，推荐默认优先使用模块组织代码
- 名称空间是Typescript特定的组织代码的方式，是在全局命名的JavaScript对象，名称空间可以跨文件，可以用`outFile`串起来，是一个好的代码组织方式，但依赖难以明确管理

# 系统迁移
## 操作检查
-   添加一个 `tsconfig.json` 文件；
-   把文件扩展名从 `.js` 改成 `.ts`，开始使用 `any` 来减少错误；
-   开始在 TypeScript 中写代码，尽可能的减少 `any` 的使用；
-   回到旧代码，开始添加类型注解，并修复已识别的错误；
-   为第三方 JavaScript 代码定义环境声明。[参考](https://github.com/DefinitelyTyped/DefinitelyTyped),使用`@types/xxx`安装
- 其他非JavaScript资源处理
```
declare module '*.css';
declare module '*.html';

```
- 从js文件中创建`.d.ts`文件 [参考](https://www.typescriptlang.org/docs/handbook/declaration-files/dts-from-js.html)
- [`.d.ts`文件模板](https://www.typescriptlang.org/docs/handbook/declaration-files/templates/module-plugin-d-ts.html)

# 简明参考
- [cheetsheet](https://www.typescriptlang.org/cheatsheets)