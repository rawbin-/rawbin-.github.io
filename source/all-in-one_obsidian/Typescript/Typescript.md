

`TypeScript`是`JavaScript`(ECMAScript)的超集，在支持JavaScript的基础上增强了自己的类型系统，强化了作为弱类型语言`JavaScript`在运行前的各种类型检查、依赖检查等等在提高项目代码质量、提升开发效率和降低风险中很有用的编译器特性。

本文基于`TypeScript 3.8`



# 类型系统

## 基本类型

### boolean 布尔值

  ```
  let isDone:boolean = false
  ```

###  number 数值

  ```
  let decimal: number = 6;
  let hex: number = 0xf00d;
  let binary: number = 0b1010;
  let octal: number = 0o744;
  ```

###  string 字符串

  ```
  let color: string = "blue";
  color = "red";
  let fullName: string = `Bob Bobbington`;
  let age: number = 37;
  let sentence: string = `Hello, my name is ${fullName}.
  
  I'll be ${age + 1} years old next month.`;
  ```

###  Array 数组/列表

- 两种指定的方式

  ```
  let list: number[] = [1, 2, 3];
  let list: Array<number> = [1, 2, 3];
  ```

###  Tuple 元组

- 设定特定序列的组合值

  ```
  // Declare a tuple type
  let x: [string, number];
  // Initialize it
  x = ["hello", 10]; // OK
  // Initialize it incorrectly
  x = [10, "hello"]; // Error
  console.log(x[0].substring(1)); // OK
  console.log(x[1].substring(1)); // Error, 'number' does not have 'substring'
  Property 'substring' does not exist on type 'number'.
  x[3] = "world"; // Error, Property '3' does not exist on type '[string, number]'.
  Tuple type '[string, number]' of length '2' has no element at index '3'.
  ```
  
  ```
  console.log(x[5].toString()); // Error, Property '5' does not exist on type '[string, number]'.
  Object is possibly 'undefined'.
  Tuple type '[string, number]' of length '2' has no element at index '5'.
  ```

###  Enum 枚举

- 给值更友好的名称
- 默认值从0开始，可以手动设置开始的值，也可以手动设置每一个值
- 可以通过值来获取值的名称

  ```
  enum Color {
    Red = 1,
    Green,
    Blue,
  }
  let c: Color = Color.Green; 
  
  ```

###  Any 都行

- 在`JS2TS`的迁移过程中常用
- `any`类型可以赋任意值也能调用任意方法，但`object`类型只能赋任意值不能调用任意方法，即使存在也不行

  ```
  let notSure: any = 4;
  notSure = "maybe a string instead";
  notSure = false; // okay, definitely a boolean
  let notSure: any = 4;
  notSure.ifItExists(); // okay, ifItExists might exist at runtime
  notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)
  
  let prettySure: Object = 4;
  prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.
  
  let list: any[] = [1, true, "free"];
  list[1] = 100;
  ```

###  Void 无/空

- 如果变量定义为`void`,没多少用，只能赋值为`null`或者`undefined`(在没配置`--strickNullChecks`)

  ```
  function warnUser(): void {
    console.log("This is my warning message");
  }
  ```

  ```
  let unusable: void = undefined;
  unusable = null; // OK if `--strictNullChecks` is not given
  ```

###  Null and Undefined 空对象/未定义

- `null`和`undefined` 既是值也是类型
- `null`和`undefined`是所有类型的子类型
  - 可以把`null`和`undefined`赋值给其他类型
  - 如果开了`--stricNullChecks`,`null`和`undefined`只能赋值给`any`和他们自己（此时`undefined`还能赋值给`void`）,这是为了避免同一个值多个（`string`或者`null`或者`undefined`）类型

  ```
  // Not much else we can assign to these variables!
  let u: undefined = undefined;
  let n: null = null;
  ```

###  Never 绝非

- 在函数抛异常或者永远不会返回的情况下的返回值类型
- `never`类型是所有类似的子类型，并且除了它自己，它没有其他子类型

  ```
  // Function returning never must have unreachable end point
  function error(message: string): never {
    throw new Error(message);
  }
  
  // Inferred return type is never
  function fail() {
    return error("Something failed");
  }
  
  // Function returning never must have unreachable end point
  function infiniteLoop(): never {
    while (true) {}
  }
  ```

###  Object 对象

- 代表所有的非原始类型 `object`
- 原始类型目前为 `number`,`string`,`boolean`,`symbol`,`null`,`undefined`

  ```
  declare function create(o: object | null): void;
  
  create({ prop: 0 }); // OK
  create(null); // OK
  
  create(42); // Error
  Argument of type '42' is not assignable to parameter of type 'object | null'.
  create("string"); // Error
  Argument of type '"string"' is not assignable to parameter of type 'object | null'.
  create(false); // Error
  Argument of type 'false' is not assignable to parameter of type 'object | null'.
  create(undefined); // Error
  Argument of type 'undefined' is not assignable to parameter of type 'object | null'.
  
  ```

  

## 复合类型

### function 函数类型

  ```
function add(x: number, y: number): number {
  return x + y;
}

let myAdd = function(x: number, y: number): number {
  return x + y;
};
  ```

```
let myAdd: (x: number, y: number) => number = function(
  x: number,
  y: number
): number {
  return x + y;
};
```

- 可选参数（可选参数要在所有必选参数之后）

```
  function buildName(firstName: string, lastName?: string) {
    if (lastName) return firstName + " " + lastName;
    else return firstName;
  }

  let result1 = buildName("Bob"); // works correctly now
  let result2 = buildName("Bob", "Adams", "Sr."); // error, too many parameters
  let result3 = buildName("Bob", "Adams"); // ah, just right
```

- 默认参数（没有位置要求）

  ```
  function buildName(firstName = "Will", lastName: string) {
    return firstName + " " + lastName;
  }

  let result1 = buildName("Bob"); // error, too few parameters
  let result2 = buildName("Bob", "Adams", "Sr."); // error, too many parameters
  let result3 = buildName("Bob", "Adams"); // okay and returns "Bob Adams"
  let result4 = buildName(undefined, "Adams"); // okay and returns "Will Adams"
  ```

- 剩余参数，在参数最后

  ```
  function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + " " + restOfName.join(" ");
  }

  let buildNameFun: (fname: string, ...rest: string[]) => string = buildName;
  ```

- 明确的this指向

  ```
  interface Card {
    suit: string;
    card: number;
  }
  interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
  }
  let deck: Deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    // NOTE: The function now explicitly specifies that its callee must be of type Deck
    createCardPicker: function(this: Deck) {
      return () => {
        let pickedCard = Math.floor(Math.random() * 52);
        let pickedSuit = Math.floor(pickedCard / 13);

        return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
      };
    }
  };

  let cardPicker = deck.createCardPicker();
  let pickedCard = cardPicker();

  alert("card: " + pickedCard.card + " of " + pickedCard.suit);
  ```

- 函数重载

  ```
  let suits = ["hearts", "spades", "clubs", "diamonds"];
  
  function pickCard(x: { suit: string; card: number }[]): number;
  function pickCard(x: number): { suit: string; card: number };
  function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
      let pickedCard = Math.floor(Math.random() * x.length);
      return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
      let pickedSuit = Math.floor(x / 13);
      return { suit: suits[pickedSuit], card: x % 13 };
    }
  }
  
  let myDeck = [
    { suit: "diamonds", card: 2 },
    { suit: "spades", card: 10 },
    { suit: "hearts", card: 4 }
  ];
  let pickedCard1 = myDeck[pickCard(myDeck)];
  alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);
  
  let pickedCard2 = pickCard(15);
  alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
  ```

  

### class 类类型

- 类有方法和属性，可以继承，ECMAScript 2015或者ES6开始支持
- 类的属性和方法可以有`public`, `private`,`protected`三种,属性可以用`readonly`修饰
  - `public`为默认，公开访问
  - `private` 不能被直接访问，有两种实现方式，ECMAScript用`#`在属性名前表示私有，TypeScript用`private`关键字
  - `protected` 可以被派生类访问
- `static`属性和方法属于类属性或者类方法，不用实例调用
- 构造器参数使用属性装饰器，可以声明并初始化对应的属性，`public`,`protected`,`private`,`readonly`
- 访问器属性需要把编译器设置为ECMAScript 5 或者更高才支持，访问器属性有`get`没有`set`会自动设置为`readonly`
- `abstract`类和方法，不能被直接实例化，必须被派生类是实现了才可以
- 接口可以继承类，类声明做两件事一个是声明实例的类型，另一个是一个构造器函数

### interface 接口类型

- 基本定义
- 使用`?`定义可选属性
- `readonly`定义只读属性

  ```
  interface LabeledValue {
  label: string;
  value?: string
  readonly flag: boolean
  }
  ```

  ```
  function printLabel(labeledObj: LabeledValue) {
  console.log(labeledObj.label);
  }
  ```
  ```
  let myObj = { size: 10, label: "Size 10 Object" };
  printLabel(myObj);
  ```

- 避免过度检测的方式

  - 使用类型断言

```
    let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);
```

  - 使用任意类型的额外属性定义

    ```
    interface SquareConfig {
      color?: string;
      width?: number;
      [propName: string]: any;
    }
    ```

    

#### Function 函数类型

```
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;

mySearch = function (source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
};
```

#### Indexable 索引类型

- 可以用`readonly`设置只读

```
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```



#### Class 类 类型

- 接口描述了类公共的部分，不能描述私有的部分

```
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date): void;
}

class Clock implements ClockInterface {
  currentTime: Date = new Date();
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) {}
}
```

- 类实现接口，只有类的实例部分会被检测，静态部分不会被检测，可以用类表达式来整合两者

  ```
  interface ClockConstructor {
    new (hour: number, minute: number);
  }
  
  interface ClockInterface {
    tick();
  }
  
  const Clock: ClockConstructor = class Clock implements ClockInterface {
    constructor(h: number, m: number) {}
    tick() {
      console.log("beep beep");
    }
  };
  ```



#### 接口继承

- 接口可以继承，可以多继承

```
interface Shape {
  color: string;
}

interface PenStroke {
  penWidth: number;
}

interface Square extends Shape, PenStroke {
  sideLength: number;
}

let square = {} as Square;
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```



#### 混合类型

- 得益于JavaScript动态类型的特性

```
interface Counter {
  (start: number): string;
  interval: number;
  reset(): void;
}

function getCounter(): Counter {
  let counter = function (start: number) {} as Counter;
  counter.interval = 123;
  counter.reset = function () {};
  return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```



#### 接口继承类

- 继承类的接口具有类的所有属性（不管私有的还是公有的）
- 只有类的后代类能实现这个接口

```
class Control {
  private state: any;
}

interface SelectableControl extends Control {
  select(): void;
}

class Button extends Control implements SelectableControl {
  select() {}
}

class TextBox extends Control {
  select() {}
}

// Error: Property 'state' is missing in type 'Image'.
class ImageControl implements SelectableControl {
Class 'ImageControl' incorrectly implements interface 'SelectableControl'.
  Types have separate declarations of a private property 'state'.
  private state: any;
  select() {}
}

class Location {}
Duplicate identifier 'Location'.

```



### Literal 字面类型

#### 字符串字面量

- 字面量实现枚举

```
type Easing = "ease-in" | "ease-out" | "ease-in-out";

class UIElement {
  animate(dx: number, dy: number, easing: Easing) {
    if (easing === "ease-in") {
      // ...
    } else if (easing === "ease-out") {
    } else if (easing === "ease-in-out") {
    } else {
      // It's possible that someone could reach this
      // by ignoring your types though.
    }
  }
}

let button = new UIElement();
button.animate(0, 0, "ease-in");
button.animate(0, 0, "uneasy");
```

- 字面类型实现重载

  ```
  function createElement(tagName: "img"): HTMLImageElement;
  function createElement(tagName: "input"): HTMLInputElement;
  // ... more overloads ...
  function createElement(tagName: string): Element {
    // ... code goes here ...
  }
  ```

#### 数字字面量

```
interface MapConfig {
  lng: number;
  lat: number;
  tileSize: 8 | 16 | 32;
}

setupMap({ lng: -73.935242, lat: 40.73061, tileSize: 16 });
```



### Enum 枚举类型

#### 数值枚举

- 默认为0 ，可以指定第一个开始的值，也可以为每一个名称指定值

```
enum Direction {
  Up = 1,
  Down,
  Left,
  Right
}
```

#### 字符串枚举

```
enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT"
}
```

#### 计算和常数值

```
enum FileAccess {
  // constant members
  None,
  Read = 1 << 1,
  Write = 1 << 2,
  ReadWrite = Read | Write,
  // computed member
  G = "123".length
}
```



### Union 和 Intersection 类型

#### union 联合类型

```
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: string | number) {
  // ...
}

let indentedString = padLeft("Hello world", true);
Argument of type 'true' is not assignable to parameter of type 'string | number'.
```

- 返回共用类型的，只能用公共类型

  ```
  interface Bird {
    fly(): void;
    layEggs(): void;
  }
  
  interface Fish {
    swim(): void;
    layEggs(): void;
  }
  
  declare function getSmallPet(): Fish | Bird;
  
  let pet = getSmallPet();
  pet.layEggs();
  
  // Only available in one of the two possible types
  pet.swim();
  Property 'swim' does not exist on type 'Bird | Fish'.
    Property 'swim' does not exist on type 'Bird'.
  ```

- 区别对待联合类型

  ```
  type NetworkLoadingState = {
    state: "loading";
  };
  
  type NetworkFailedState = {
    state: "failed";
    code: number;
  };
  
  type NetworkSuccessState = {
    state: "success";
    response: {
      title: string;
      duration: number;
      summary: string;
    };
  };
  
  // Create a type which represents only one of the above types
  // but you aren't sure which it is yet.
  type NetworkState =
    | NetworkLoadingState
    | NetworkFailedState
    | NetworkSuccessState;
  ```

#### intersection 交叉类型

```
interface ErrorHandling {
  success: boolean;
  error?: { message: string };
}

interface ArtworksData {
  artworks: { title: string }[];
}

interface ArtistsData {
  artists: { name: string }[];
}

// These interfaces are composed to have
// consistent error handling, and their own data.

type ArtworksResponse = ArtworksData & ErrorHandling;
type ArtistsResponse = ArtistsData & ErrorHandling;

const handleArtistsResponse = (response: ArtistsResponse) => {
  if (response.error) {
    console.error(response.error.message);
    return;
  }

  console.log(response.artists);
};
```

- 交叉类型的应用

  ```
  class Person {
    constructor(public name: string) {}
  }
  
  interface Loggable {
    log(name: string): void;
  }
  
  class ConsoleLogger implements Loggable {
    log(name: string) {
      console.log(`Hello, I'm ${name}.`);
    }
  }
  
  // Takes two objects and merges them together
  function extend<First extends {}, Second extends {}>(
    first: First,
    second: Second
  ): First & Second {
    const result: Partial<First & Second> = {};
    for (const prop in first) {
      if (first.hasOwnProperty(prop)) {
        (result as First)[prop] = first[prop];
      }
    }
    for (const prop in second) {
      if (second.hasOwnProperty(prop)) {
        (result as Second)[prop] = second[prop];
      }
    }
    return result as First & Second;
  }
  
  const jim = extend(new Person("Jim"), ConsoleLogger.prototype);
  jim.log(jim.name);
  ```

  

### generic 通用类型

#### any 作为通用，不推荐

#### 通用类型变量

```
function identity<T>(arg: T): T {
  return arg;
}
```

```
function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: <T>(arg: T) => T = identity;
```

```
function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: { <T>(arg: T): T } = identity;
```

```
interface GenericIdentityFn {
  <T>(arg: T): T;
}

function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: GenericIdentityFn = identity;
```



#### 通用类

```
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) {
  return x + y;
};
```

#### 通用约束

```
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length); // Now we know it has a .length property, so no more error
  return arg;
}
```



```
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a"); // okay
getProperty(x, "m"); // error: Argument of type 'm' isn't assignable to 'a' | 'b' | 'c' | 'd'.
```

```
class BeeKeeper {
  hasMask: boolean;
}

class ZooKeeper {
  nametag: string;
}

class Animal {
  numLegs: number;
}

class Bee extends Animal {
  keeper: BeeKeeper;
}

class Lion extends Animal {
  keeper: ZooKeeper;
}

function createInstance<A extends Animal>(c: new () => A): A {
  return new c();
}

createInstance(Lion).keeper.nametag; // typechecks!
createInstance(Bee).keeper.hasMask; // typechecks!
```



## 类型操作

### 类型断言

- 在明确值类型的时候可以让解析器有更明确的动作

```
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;

let strLength2: number = (someValue as string).length;
```



### 类型别名

```
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
  if (typeof n === "string") {
    return n;
  } else {
    return n();
  }
}

type Container<T> = { value: T };

type Tree<T> = {
  value: T;
  left: Tree<T>;
  right: Tree<T>;
};

type LinkedList<T> = T & { next: LinkedList<T> };

interface Person {
  name: string;
}

var people: LinkedList<Person>;
var s = people.name;
var s = people.next.name;
var s = people.next.next.name;
var s = people.next.next.next.name;
```



#### 声明合并

- 合并接口内容
- 合并名称空间
- 合并名称空间和类
- 合并名称空间和函数
- 合并名称空间和枚举





# 代码组织

## Module 模块

- 任何包含顶级的`import`或`export`的文件被当做模块，反之则当做普通脚本

### Export

- 可以导出变量、函数、类、类型别名、接口

  ```
  export TYPE OBJECT
  export {OBJECT}
  export {OBJECT as ANOTHERNAME}
  export {OBJECT} from 'MODULE'
  export {OBJECT as ANOTHERNAME} from 'MODULE'
  export * from 'MODULE'
  export * as OBJECT from 'MODULE'
  export default DEFAULT
  ```

- `export=` 只能用`import MODULE = require('MODULE')`来导入

  ```
  export = OBJECT
  ```

  

### Import

```
import {OBJECT} from 'MODULE'
import {OBJECT as ANOTHERNAME} from 'MODULE'
import * as OBJECT from 'MODULE'
import "MODULE"
improt {OBJECTTYPE} from 'MODULE'
import type {OBJECTTYPE} from 'MODULE'
import DEFAULT from 'MODULE'
```



### 动态模块加载

```
declare function require(moduleName: string): any;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
  let ZipCodeValidator: typeof Zip = require("./ZipCodeValidator");
  let validator = new ZipCodeValidator();
  if (validator.isAcceptable("...")) {
    /* ... */
  }
}
Sample: Dynam
```



### 模块接口定义

- 不是`TypeScript`编写的库，需要声明库的接口才能使用

- 库的声明一般定义在`.d.ts`文件里面，类似于`C/C++`里面的头文件`.h`

  

#### 外围模块

##### 精准声明和引用

- 声明

  ```
  declare module "url" {
    export interface Url {
      protocol?: string;
      hostname?: string;
      pathname?: string;
    }

    export function parse(
      urlStr: string,
      parseQueryString?,
      slashesDenoteHost?
    ): Url;
  }

  declare module "path" {
    export function normalize(p: string): string;
    export function join(...paths: any[]): string;
    export var sep: string;
  }
  ```

- 引用

  ```
  /// <reference path="node.d.ts"/>
  import * as URL from "url";
  let myUrl = URL.parse("http://www.typescriptlang.org");
  ```




##### 简化声明和引用

- 声明

  ```
  declare module "hot-new-module";
  ```

- 引用

  ```
  import x, { y } from "hot-new-module";
  x(y);
  ```



##### 模块通配声明 SystemJS 和  AMD

- 声明

  ```
  declare module "*!text" {
    const content: string;
    export default content;
  }
  // Some do it the other way around.
  declare module "json!*" {
    const value: any;
    export default value;
  }
  ```

- 引用

  ```
  import fileContent from "./xyz.txt!text";
  import data from "json!http://example.com/data.json";
  console.log(data, fileContent);
  ```



#### UMD模块声明

- 声明

  ```
  export function isPrime(x: number): boolean;
  export as namespace mathLib;
  ```

- 引用

  ```
  import { isPrime } from "math-lib";
  isPrime(2);
  mathLib.isPrime(2); // ERROR: can't use the global definition from inside a module
  ```

  ```
  mathLib.isPrime(2);
  ```

  

### 模块构建指南

- 导出到接近模块顶级的部分，这样引入就更加轻便
  - 导出`namespace`会多了名称空间这一层
  - 导出类的静态方法会多了类这一层，可以考虑导出为工具函数
  - 导出单个的类或者函数，用`export default`
  - 导出多个对象，都放在顶层`export xx`
- 导入
  - 明确导入名称 `import {xx,yy} from 'MODULE'`
  - 使用名称空间方式导入多接口模块`import * as xxx from 'MODULE'`

- 通过导入再重新导出的方式来扩展功能，保持原来的接口

- 在模块里面不要用名称空间

  - 名称空间是在全局用来对逻辑相关的对象和类型进行分组归类的
  - 在全局用来避免命名冲突很重要

  - 常见的乱用场景
    - 单个文件的唯一顶层声明是`export namespace xx{}`, 这种直接删掉并上移一层
    - 多个文件有相同的顶层`export namespace xx`，这个不会合并的成一个的



## Namespace 名称空间

- 用来逻辑上包装一系列的功能
- 可以在多个文件中使用同一个名称空间，也可以用引用标签来给编译器提供引用关系
- 可以通过`import xx = x.y.z`这样的方式来给名称空间设置别名
- 原来使用`script`标签加载的库而不是模块加载器定义的，用`namespace`来定义接口



## 模块和名称空间的使用

- 新项目新代码都用模块来组织代码
- 跨多个文件时使用名称空间
- 最外层没必要使用名称空间，这样会多一层，只有逻辑归类的时候更需要这个

### 模块

- 模块可以包含代码和声明
- 模块能够明确依赖（比如依赖模块加载器，运行时），能更好的重用代码，强隔离以及更好的开发工具支持
- 官方推荐优先使用模块，在新项目和新代码中

### 名称空间

- 名称空间是`TypeScript`特有的，`ECMAScript`没有
- 名称空间就是代码的逻辑组织，简单好用
- 名称空间可以跨多个文件（使用同一个名就行），也能用`--outFile`拼起来
- 名称空间在Web应用中组织代码的好方式，所有的依赖都包含在`<script>`标签中
- 名称空间有全局污染的问题，难以确认组件依赖，尤其是在大型项目中

## 模块声明

### 声明引用

```
// myModule.d.ts
// In a .d.ts file or .ts file that is not a module:
declare module "SomeModule" {
export function fn(): string;
}
```

```
// myOtherModule.ts
/// <reference path="myModules.d.ts" />
import * as m from "SomeModule";
```



## 模块解析

### 相对引用

- 相对导入是以`/`,`./`,`../`开始的
- 除此之外的为非相对导入`MODULE`,`@xx/yy`



### 模块解析策略

#### 经典 AMD或者System或者ES2015

- 相对引用相对于引用文件来解析
- 非相对引用从目录树来解析 `MODULE.ts`和`MODULE.d.ts`
  - 从当前文件夹找
  - 从上一级文件找
  - 一直找到根目录

####  Node

- 相对引用
  - 从相对引用路径的文件来找
  - 从相对引用路径的目录来找，目录要是一个包（有`package.json`文件，并有`main`字段配置）
  - 从相对引用路径的目录来找，目录下有文件`index.js`

- 非相对引用，从`node_modules`来解析
  - 找当前模块同一级的`node_modules`下对应的
    - 对应的js文件,`MODULE.js`
    - 对应的模块目录`MODULE`,目录要是一个包（有`package.json`文件，并有`main`字段配置）
    - 对应的模块目录,目录下有文件`index.js`
  - 找上一级的`node_modules`
  - 一直找到根目录



#### TypeScript模块解析

- 相对引用 找`.ts`,`.tsx`,`.d.ts`
  - 找`MODULE`
  - 类似于Node的相对查找方法，`package.json`中找`types`属性
  - 类似于Node的相对查找方法，找`MODULE/index`
- 非相对引用 找 `.ts`,`.tsx`,`.d.ts`
  - 找当前目录下`node_modules`下的`MODULE`
  - 类似于Node的相对查找方法，`package.json`中找`types`属性
  - 找当前目录下`node_modules/@types/MODULE.d.ts`
  - 类似于Node的相对查找方法，找`MODULE/index`
  - 找上一级的`node_modules`
  - 一直找到根目录