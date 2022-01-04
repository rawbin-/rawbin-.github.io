---
layout: post
title: "JavaScript模块标准化那些事"
sticky: 95
cagegories: [Web开发,前端开发,移动端]
tags: [JavaScript,CommonJS,ECMAScript,模块化]
---

# CommonJS

参考[wiki](http://wiki.commonjs.org/wiki/CommonJS)这里说标准也不那么恰当，但在`Node.js`环境中，比起前面的各种MD来说，又更近了一步，更加统一，以至于体验上会更加规范，但还不是规范，至少可以做到只要是Node，都遵守一致的模块定义，所有使用者也能达成共识。

## 规范说明（从当前最新Node.js [ 17.x官方文档](https://nodejs.org/docs/latest-v17.x/api/modules.html)而来）

- 每个单文件被当做一个模块
- 函数和对象都添加到模块对象的特殊属性 `exports`的属性上，也就是 `exports.xx = yy` ，`module.exports` 也可以赋一个新的值，重新模块的所有内容，也就是`module.exports = xx`。
- 模块本地的变量是私有的，因为模块在Node.js 中会被一个叫做模块包装器的函数包裹，（这个函数也就是形参带module,exports...等等的那个函数）
- 模块系统是在`require` 模块中实现的
- 如果直接运行一个模块，`require.main` 字段被设置为当前运行的模块对象，也就是可以用`require.main` 来判断当前运行的模块 `require.main === module`
- 无法使用`require`去加载`.mjs`的模块(`.mjs`是Node.js 支持 ECMAScript Module的方式，详情见下文)
- 想要获取确切的`require`加载的模块的文件名，需要用`require.resolve()` 它获取的是被加载的模块的绝对路径
- 模块首次被加载后会被缓存下来，只要路径相同，每次`require`拿到的都是同一个对象的引用，虽然对象一样，但对象属性可能不一样，这也就是循环引用出现的地方。要让一个模块每次获取的内容不一样，导出一个函数并每次执行那个函数。
  - 不同地方的同一个表达式`require('foo')`可能加载的不是同一个模块，还可能是从`node_module`中加载的，这个时候要区分的话可以用`require('node:xx')`明确指定从核心模块加载
  - 在大小写敏感的文件系统中，大小写不一样的模块返回的是不同的对象
- 模块分类
  - 文件模块
    - 如果能加载精确路径就直接加载
    - 没有精确路径，引用路径中带有`/`,`./`,`../`开头的，会和`.js`，`.json`,`.node`组合尝试加载
    - 其他的不是核心模块就是`node_modules`的模块
  - 目录模块
    - 目录包含`package.json`上有非`null`非`undefined`的`main`字段值
    - 目录包含`index.js`或者`index.node`文件
- `require`函数的几个属性
  - `main`当前执行的模块的Module对象
  - `extensions` 默认加载的扩展及对应的加载函数`.js`,`.json`,`.node`【已废弃】
  - `cache` 当前已加载的模块映射,key为模块绝对路径，value为Module对象
  - `resolve` 需要用`require.resolve()` 它获取的是被加载的模块的绝对路径（比如`require('./xx')` 到底是`.js`,`.json`,`.node`?）



## 模块加载原理

- 使用类似如下的包装器，将模块代码在函数范围内执行

  这样就可以将模块的作用域隔离开来，不会出现相互影响（如果是全局的话就会）

  ```
  (function(exports, require, module, __filename, __dirname) {
  // Module code actually lives in here
  });
  ```

  - `exports` 当前模块导出对象，是`module.exports`的引用
  - `require`函数
    - `main`当前执行的模块的Module对象
    - `extensions` 默认加载的扩展及对应的加载函数`.js`,`.json`,`.node` 【已废弃】
    - `cache` 当前已加载的模块映射,key为模块绝对路径，value为Module对象
    - `resolve` 需要用`require.resolve()` 它获取的是被加载的模块的绝对路径（比如`require('./xx')` 到底是`.js`,`.json`,`.node`?）
  - `module`当前模块对象
    - `children` 当前模块的依赖模块（准确的说当前模块加载前需要加载的模块，有些依赖可能不是第一时间要用）
    - `exports` 默认是模块系统生成的对象，需要的话可以重新赋值，赋值需要立即执行，放到异步回调里面不好使
    - `filename` 模块绝对路径
    - `id` 通常是解析的文件名，但不一定，比如你试试看
    - `parent` 【已废弃】用`module.echildren`替代
    - `isPreloading` 是否是在预加载阶段
    - `isloaded` 是否加载完，或者说是不是在加载阶段
    - `path`  一般为模块所在目录绝对路径
    - `paths` 模块的查找路径
    - `require` 可以简单理解为绑了`this`的模块

## 模块加载过程

详情可参考[模块加载伪代码](https://nodejs.org/docs/latest-v17.x/api/modules.html#all-together),  [模块解析算法伪代码]（https://nodejs.org/docs/latest-v17.x/api/esm.html#resolver-algorithm-specification

- `require`加载模块的规则 举例在`Y`路径（当前路径），调用`require(X)`
  - 如果X是核心模块(node源码`./lib`目录下的)，直接返回，核心模块总是优先加载，可以直接使用`require('node:xxx')`可以跳过`require`的缓存，直接取系统模块，哪怕缓存中有这个模块也会跳过
  - 如果是X以`/`开头，从文件系统根路径加载找
  - 如果X以`./` 或者`/`或者`../` 开头，先【按文件加载】，找不到就【按目录加载】，还是找不到就抛找不到的异常 
  - 如果X以`#`开头，【加载包导入】
  - 【加载包自身】
  - 【加载node_modules】
  - 抛出找不到的异常
- `require(X)` 加载模块的场景
  - [按文件加载](`require.extension`后缀)
    - 如果X是个文件，以带后缀的文件全路径加载
    - 如果`X.js` 是个文件，以JavaScript代码的方式加载
    - 如果`X.json`是个文件，解析文件内容为JavaScript对象
    - 如果`X.node`是个文件，以二进制扩展的形式加载（`process.dlopen()`）
  - [加载索引](`require.extension`后缀)
    - 如果`X/index.js`是个文件，以JavaScript代码的方式加载
    - 如果`X/index.json`是个文件，解析文件内容为JavaScript对象
    - 如果`X/index.node`是个文件，以二进制扩展的形式加载（`process.dlopen()`）
  - 【按目录加载】
    - 如果`X/package.json`是个文件，解析这个文件，并查找`main`字段
      - 如果不是真值，就【加载索引】；
      - 如果`main`字段为真值，就以相对路径的方式按【文件加载】，不行再按【索引加载】，还不行就抛出找不到的异常
  - 【加载node_modules】
    - 确定【模块加载路径】
      - 如果路径中已经以`node_modules`结尾node不动，没有的话Node会拼上`node_modules`去查找
      - 当前目录找不到就找上一级目录，直到找到模块或者找到文件系统根目录为止
      - `NODE_PATH`环境变量可以增加更多的查找路径
      - Node还会查找其他几个路径
        - `$HOME/.node_modules`
        - `$HOME/.node_libraries`
        - `$NODE_PREFIX/lib/node`
    - 循环尝试如下加载
      - 【加载包导出】
      - 【按文件加载】
      - 【按目录加载】
  - 【模块加载路径】
    - 把传入的路径，除了`node_module`外都滤出来，然后加上`node_modules`,一起作为包查找的路径
  - 【加载包导入】
    - 找到最近的作用域（@xx/），找不到就返回
    - 如果找到`SCOPE/package.json` 并且`imports`是`null`或者`undefined` 直接返回
    - 找到【包导入解析】的匹配，并【解析ESM匹配】
  - 【加载包导出】
    - 先试着以`@scope/xx`的组合解析路径
    - 如不不匹配或者路径的`package.json`文件不存在，直接返回
    - 找到`package.json` 就解析`exports` 字段，如果字段是`null`或者`undefined`直接返回
    - 找到【包导出解析】，并【解析ESM匹配】
  - 【加载包自身】
    - 找到最近的作用域（@xx/），找不到就返回
    - 如果`SCOPE/package.json`的`exports`字段为`null`或者`undefined`,直接返回
    - 如果`SCOPE/package.json`的`name`字段不是路径的第一段，直接返回
    - 找到【包导出解析】，并【解析ESM匹配】
  - 【解析ESM匹配】
    - 如果是精确加载，就加载全路径
    - 如果不是精确加载，先【按文件加载】，不行再【按目录加载】，还是不行就抛找不到异常



## 常见的坑

### 循环引用

- 各种模块相互引用的过程中，如果你看到`xxx is not defined`但明明就躺那，那么就需要看看是不是循环引用了



### 模块导出无效

```
exports.a = 1
exports.b = 2
exports.c = 3
exports = {a:1,b:2}  // 这个会导出无效，但在当前模块范围内又是有效的

module.exports = {a:1,b:2} // c = 3 也无效了

// 最佳实践，这样写，明确表示你懂了且确认了
module.exports = exports = xxx
```

如下代码可帮助理解

```
function require(/* ... */) {
  const module = { exports: {} };
  ((module, exports) => {
    // Module code here. In this example, define a function.
    function someFunc() {}
    exports = someFunc;
    // At this point, exports is no longer a shortcut to module.exports, and
    // this module will still export an empty default object.
    module.exports = someFunc;
    // At this point, the module will now export someFunc, instead of the
    // default object.
  })(module, module.exports);
  return module.exports;
}
```



## Module API 区别于前面的module对象，用得少

- `require('module').builtinModules` Nodejs内置模块列表
- `require('module').createRequire`  
- `require('module').syncBuiltinESMExports()`
- `require('module').findSourceMap()`
- `require('module').syncBuiltinESMExports()`



# ECMAScript Module

- [规范参考](https://tc39.es/ecma262/#sec-modules)



## ES Module规范

ES的模块规范是静态化的，这使得模块在编译的过程中就能确定依赖关系和输入输出。这个跟CommonJS和AMD有所区别，他们是运行时才能确定。`import {xxx,yyy,zzz} from 'pkg' ` 这样的方式在编译时就能确定依赖关系，可以直接做静态分析，这也是`tree shaking`的基础。同时因为是静态化的，所以`import`和`export`要求在代码的顶层，不能出现在任何块和分支逻辑中。同时代码放在哪里都跟放在文件前面一个效果，可类比JS的变量提升，词法作用域，也都是静态玩法。

ES Module默认是严格模式，严格模式更加可控和安全，也避免了不少问题。

ES Module主要依据`export` 和 `import` 两个关键字来实现，一个模块就是一个单独的文件跟CommonJS一致，只有用`export`导出的内容可以明确被访问到。

模块导入会有缓存，同一个模块多次导入也只会加载一次。

### `export` 用于导出

#### 直接导出变量声明

- 可以单个导出需要的变量

```
// test-export.mjs
export const a = 1
export var b = 2
export function test(){}
export class Person{}
export let obj = {}
```

#### 通过导出对象的形式一次性导出（推荐，集中而且可以重命名）

- 可以一次性导出所有需要导出的内容
- 这个时候可以在导出的时候重新指定名称

```
// test-export-all.mjs
const a = 1
var b = 2
function test(){}
class Person{}
let obj = {}
export {
	a,b,
	Person,
	test as testFunc,
	obj as exportedObj
}
```

#### 默认导出

- 上面的导出方式，都要求模块使用方（导入方）知道导出的内容，有时候会带来不便
- 可以将模块提供的内容直接导出为一个默认对象，作为模块的整体入口
- 默认导出的可以是对象，函数，类等等变量值
- 导出的对象名称在模块外不再有效，都是`default`一个引用入口

```
// test-export-default.mjs
const a = 1
var b = 2
function test(){}
class Person{}
let obj = {}
export default {   // 注意带不带default的区别，区别很大
	a,b,
	Person,
	test as testFunc,
	obj as exportedObj
}
```

#### 动态的数据

- 不像CommonJS导出的数据是一份拷贝，ES Module导出的数据是动态绑定，可以理解为引用，可以感知数据的变化。

```
export let changing = 123

setInterval(() => {
    changing += 111
},2000)

// 导入changeing 的模块，可以在不同的时候取到不同的值
```

#### 区别

- `export` 导出的是声明，各种声明
- `export default`导出的是值

### `import` 用于导入

#### 导入命名导出的内容

- 可以选择性的导入部分内容
- 导入的时候可以重新命名，尤其是在有重名的情况下很有用
- 导入的名称是只读的，不能对齐进行重新绑定，但可以更改引用值，有点类似于C++的常指针，不可改变指针指向，但可以改变指针值（如果值是可变的话）

```
import {a,b,test} from './test-export.mjs'

import {a,b,Person,test as testFunc,obj as exportedObj}

// a = 2 // 报错

obj.a = a // ok 但尽量不这样做，否则就跟全局变量污染没区别，到处都在改
```

#### 直接导入自执行模块

 ```
 import 'pkg'
 ```

#### 直接导入所有模块内容

```
import * as all from './test-export.mjs' // 导人非defaut的所有命名导出
console.log(all)
```

#### 导入模块默认

```
import moduleDefault as defaultTest from './test-export-default.mjs'
```

#### 同时导入默认和声明的值

```
import moduleDefault as defaultTest, {a,b test as testFunc} from './test-export-default.mjs'
```



### `import` + `export` 组合

#### 代理命名导出

```
export {a,b as bb} from './test-export.mjs'
// 可以简单理解为，但当前模块是无法引用到a,b的
import {a,b} from './test-export.mjs'
export {a,b as bb}
```

#### 代理全导出

```
export * from './test-export.mjs'  // 导出非defaut的所有命名导出

export * as xxx from './test-export.mjs'
```

#### 代理默认导出

```
export {default} from './test-export-default.mjs' //导出默认
export {default as mine } from './test-export-default.mjs' //默认导出转命名导出
```

###  动态加载`import()`

- 前面说到的`import` 和`export`都是静态化的，无法处理类似于运行时的逻辑，比如在特定情况下才加载某些模块，这个类似于NodeJS的`require`是动态的，同时也区别于`require`因为动态`import()`是同步的。
- 适合场景是特定条件下才加载特定的模块，也适合模块路径是动态计算出来的场景。
- 返回的是一个Promise，对象是整个模块的导出内容，可以用对象结构的方式方便的获得，因为是Promise，自然也可以跟`async` 函数配合使用或者使用Promise的所有特性。



## 浏览器环境ES Module的支持

### 指定`type`字段为`module`加载ES Module

- 浏览器会执行异步加载，页面渲染完再执行，类似于`defer`
- 如果有多个，按顺序执行
- 如果加了`async`属性，会加载完成立即执行，保持原有的行为一致性
- 模块是在模块自己的作用域执行

```
<script type="module" src="./es-module.js"></script>
```

- 也可以进行页面内嵌

```
<script type="module">
import mymodule from './es-module.js'
// do something
</script>
```

参考文档[ESM加载图解](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/) 以及 [浏览器 ES Module实现规范](https://html.spec.whatwg.org/#module-script)



## Demo环境ES Module支持

参考文档[ESM加载图解](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/) 以及 [浏览器 ES Module实现规范](https://html.spec.whatwg.org/#module-script)



## NodeJS环境ES Module支持

从官方文档中可以看出，NodeJS从9.x(官方文档入口最早只找到了9.x，可能更早) 开始实验性部分支持ECMASCript Module，到12.x移除了实验特性，正式支持。

### NodeJS中明确的ECMAScript Module

- 以`.mjs`结尾的文件，这时不管`package.json`中的配置，优先级最高可以无视其他配置

- 以`.js`结尾，且最近的`package.json`文件中有个根级别的值为`module`的`type`属性

- 带有`--input-type=module` 给`--eval`传递的字符串，或者通过管道从`STDIN`传递给`node`的字符串

  ```
  node --input-type=module --eval "import { sep } from 'path'; console.log(sep);"
  
  echo "import { sep } from 'path'; console.log(sep);" | node --input-type=module
  ```

  

- 除了以上三条之外，都是默认的 `CommonJS`规范的模块

### NodeJS中明确的CommonJS

- 以`.cjs`结尾的文件，这时不管`package.json`中的配置，优先级最高可以无视其他配置

- 以`.js`结尾，且最近的`package.json`文件中有个根级别的值为`commonjs`的`type`属性

- 带有`--input-type=commonjs` 给`--eval`传递的字符串，或者通过管道从`STDIN`传递给`node`的字符串

  ```
  node --input-type=module --eval "import { sep } from 'path'; console.log(sep);"
  
  echo "import { sep } from 'path'; console.log(sep);" | node --input-type=module
  ```

  

### NodeJS中的包`package.json`

- 包是一个带有`package.json`文件的目录及其子目录，除去那些包含了`package.json`或者`node_modules`的子目录
- `package.json`中的`type`字段值决定了Nodejs如何解析`.js`文件，如果没有属性或者值为`commonjs`则解析为CommonJS，如果值为`module`则解析为 ECMAScript Module
- 这些区分会在如下场景
  - 直接运行 `node xxx`
  - 通过`import` 声明引入
  - 通过`import()`表达式引入



#### `package.json`字段定义

下面这些是NodeJS运行时在用的字段，其他字段可能再其他工具像`npm`, `eslint` 等工具中使用，但NodeJS会忽略的，比如常用的`scripts`,`dependencies`,`devDependencies`,`husky`,`lint-staged`,`eslintConfig` ,`babel`等等

- `name` 包名，包管理器用来作为包的名字的字段，也是包内命名导入的时候用的字段
- `main` 在不支持`exports`的Node版本中或者`exports`没指定的情况下默认的包加载的模块
- `packageManager` 给[corepack](https://nodejs.org/docs/latest-v17.x/api/corepack.html) 这个包管理器的管理器用的字段，用来作为当前包开发时推荐的包管理器
- `type` 用来指定同时支持CommonJS和ES  Module的Node版本加载`.js`文件的时候的价值方式，为`commonjs`时是用CommonJS加载，为`module`时作为ES Module加载。当然在不支持ES Module的版本中无效。
- `exports` 包导出或者条件导出的配置，指定了的话，就会限制哪些内容可以在当前包中能被引用，虽然有些限制，但还是可以通过绝对路径引入打破这种限制
- `imports` 包导入，为包内的的导入指定快捷方式，字段Key要以`#`开头



#### NodeJS中包的导入导出

- 包的入口定义 在`package.json`中

  - `main`字段 一直支持的字段，但没法区分不同的版本

  - `exports`字段，对`main`字段的扩展或增强，而且优先级更高，但为了兼容低版本的Node，`main`字段还是要写。

    - 但这个字段的引入，原来用`package.json`引入包的方式没有定义的其他引入就不支持了，所以这个可能是破坏性变更，因为原来`main`就一个入口，而`exports`给的是可选的多个入口且只能是这里面给的这些入口，所以如果有其他的入口要暴露，都只能一个一个的都写上。比如像这样:

    ```
    {
      "name": "my-mod",
      "exports": {
        ".": "./lib/index.js",
        "./lib": "./lib/index.js",
        "./lib/*": "./lib/*.js",
        "./feature": "./feature/index.js",
        "./feature/*": "./feature/*.js",
        "./package.json": "./package.json"
      }
    }
    ```

    - 也可以将`exports`字段设置为`./*`这样的，这倒是能够很方便暴露更多的入口，但也失去了这个字段的特性。不但没起到概括包入口的作用，还需要用包的地方明确写清楚绝对全路径

      ```
      import feature from 'my-mod/feature' 就变成了
      import feature from 'my-mod/feature/index.js' 这个要求就有点高了
      ```

      出现上面的情况的原因是这个包检查不是强制的检查，或者没有完全堵死，使用绝对路径还是可以引用任何模块的，但用模糊路径或者包路径就被限制了。所以后续使用`exports`导出包内容就会类似如下:

      ```
      // 单入口
      {
        "main": "./main.js",
        "exports": "./main.js"
      }
      
      // 多入口
      {
        "main": "./main.js",
        "exports": {
          ".": "./main.js",
          "./submodule": "./src/submodule.js" // 只有这样定义了的，才能用 import submodule from 'xxx/submodule'
        }
      }
      ```

  - `imports`包内部包引入的定义

    - 允许映射外部包

    - 必须以`#`开头来消除歧义，区分于正常的包引入

      ```
      // package.json
      {
        "imports": {
          "#dep": {
            "node": "dep-node-native",
            "default": "./dep-polyfill.js"
          }
        },
        "dependencies": {
          "dep-node-native": "^1.0.0"
        }
      }
      
      import '#dep'
      ```

  - `imports` 和 `exports` 的规则

    - `*` 是简单的字符串替换

    - `exports`配置快捷方式

      ```
      {
        "exports": {
          ".": "./main.js"
        }
      }
      
      {
        "exports": "./main.js"
      }
      ```

#### NodeJS包条件导出

条件导出 `exports` 顾名思义，不同条件不同的导出内容，CommonJS和ECMAScript都支持，举个简单的例子，如果希望通过`require`和 `import`获取不同内容的话，可以做如下配置：

```
// package.json
{
  "main": "./main-require.cjs",
  "exports": {
    "import": "./main-module.js",
    "require": "./main-require.cjs"
  },
  "type": "module"
}
```

##### 条件导出的规则

- `import` 当用`import`或则`import()`导入包的时候适用，或者其他的ECMAScript 模块加载器的顶级导入或者解析操作的时候适用，这个时候会忽略模块的格式（直接用ECMAScript的方式加载）跟`require`互斥，也就是一码归一码各是各

- `require`当用`require`加载包的时候的时候适用，虽然也会忽略模块的格式，但模块还是要能用`require()`加载才行，支持的格式为CommonJS,JSON和原生的扩展，但不支持ECMAScript Module，因为`require()`支持不了ECMAScript Module，跟`import`互斥，也就是一码归一码各是各

- `node` NodeJS环境使用的时候适用，可以是CommonJS或者ECMAScript Module，这个条件要写在`import` 和`require`之后

- `node-addons` 和`node`类似，也是在NodeJS环境下适用，这个是用来给原生的C++扩展用的，可以用`--no-addons`禁用

- `default` 通用的兜底方案，可以是CommonJS和ECMAScript Module，这个条件永远要写在最后，而且一定要带上，为了使其他不支持前面条件的环境也能用，也可以避免其他JS环境为了支持这个包而伪装成支持的环境（比如像改浏览器UA这样的玩法，或者浏览器为了支持某些特性检查直接将检查标识设置为true，但其实没实现，这种对开发小哥来说就坑大了。）

- 这里面配置Key的顺序很重要，前面的比后面的优先级高。基本是越具体的越靠前面。那么问题来了，作为一个JSON文件，到JavaScript里面就是一个对象，对象的key顺序是咋搞的？先把问题撂这儿，后面我们统一搞搞

- 条件导出也可以扩展到导出子模块，只要是用上面这些关键字就会被识别，举例如下：

  ```
  {
    "main": "./main.js",
    "exports": {
      ".": "./main.js",
      "./feature": {
        "node": "./feature-node.js",
        "default": "./feature.js"
      }
    }
  }
  ```

- 这些规则在NodeJS中适用，但在其他的环境中不一定适用

- `import` 和`require`也还是有些问题，具体看一看下面



##### 自定义导出条件

- 在运行NodeJS的时候，可以给传包导入导出的自定义解析条件，会在标准条件之后解析，可以传多个

  ```
  node --conditions=development --conditions=xxx main.js
  ```

- NodeJS本身只支持`import` ，`require`，`node`，`node-addons`，`default`这几个条件，其他的都会被忽略，自定义导出条件要运行的环境自己解析。



##### 导出条件的定义

NodeJS支持的条件：

- `browser` 实现包括DOM API的浏览器API标准子集的环境
- `development` 只用在开发环境的入口，与`production`互斥
- `production` 用在生产环境的入口，与`development`互斥

平台指定的像`deno`，`electron`，`react-native`这样的条件也可以用，但现在还没实现，也不是明确支持的。其他条件也可以通过`pull request`的方式往里面加，新加的条件有些要求：

- 条件清晰不含糊，所有实现方能看懂
- 条件的使用场景以及必要性要清晰
- 要有充足的既有的实现使用场景
- 条件之间广义上不能有冲突
- 条件要通用，不能是公司级别的或者应用级别的



####  用名称自引用包（感觉没什么卵用）

当一个包的`package.json`中有`exports`字段，在包内可以用包名引用包导出的内容，没导出的没法引用，带`@SCOPE`的包也支持。实例如下

```
// package.json
{
  "name": "a-package",
  "exports": {
    ".": "./main.mjs",
    "./foo": "./foo.js"
  }
}

// ./a-module.mjs
import { something } from 'a-package'; // Imports "something" from ./main.mjs.

// ./a-module.js
const { something } = require('a-package/foo'); // Loads from ./foo.js.

// ./another-module.mjs

// Imports "another" from ./m.mjs. Fails because
// the "package.json" "exports" field
// does not provide an export named "./m.mjs".
import { another } from 'a-package/m.mjs';
```



### 同时支持CommonJS/ECMAScript Module的包

在NodeJS还没支持ECMAScript Module之前，一个常见的玩法让一个包同时支持CommonJS和ES Module是在`package.json` 中`main`字段配置CommonJS的支持，在`module`字段配置ECMAScript Module的支持。这就可以让NodeJS用CommonJS，其他的像打包工具这样的就用ECMAScript Module，因为NodeJS忽略`module`字段（现在还是忽略的）。

NodeJS现在可以运行ECMAScript Module了，一个包也可以同时支持CommonJS和ECMAScript Module了，可以用不同的标识符比如`pkg`和`pkg/es-module` 或者在同一个标识符下用条件导出。现在ECMAScript 不需要转换就可以在NodeJS里面玩了。

#### 同时支持CommonJS和ES Module的风险

因为`const pkgInstance = require('pkg')` 和 `import pkgInstance from 'pkg' `是两个不同的东西（因为NodeJS用两个不同的东西来实现他们了），如果一个包同时支持两种方式的话，可能会有一些怪异的行为，倒不是说同一个应用里面会有两种导入方式（也不是没可能，可能性大大的有，一会儿想用`import`一会儿想用`require`，一个人想用`import`一个人想用`require`），而是说应用中可能是一种导入方式而在依赖中可能是另外一种导入方式。表现不一样对使用者甚至是模块转义器来说都是个问题。



#### 如何规避或者减少风险

可以有如下几种方式，每种方式都有平衡的折中，区分包的运行场景（这个基本是确定的），也就是没有完美的解决办法，但可以满足如下条件的场景，基本绝大部分场景都够用了。

- 这个包可以通过`import`和`require`引用
- 这个包可以在当前版本的NodeJS里面使用，也可以在老的不支持ES Module的NodeJS版本中使用
- 这个包`main`主入口`pkg`可以被`require`解析成CommonJS也可以被`import`解析成ES Module，也可以用`pkg/feature`引用导出的其他路径
- 这个包提供了命名的导出，比如`import {name} from 'pkg'`而不是`import pkg from 'pkg';pkg.name` (不建议`export default`，因为不利于 `tree shaking`)
-  这个包可能被其他的像浏览器这样的 ES Module环境使用
- 前面这些场景的使用风险都可以被避免或者减少

##### 方法一：使用一个 ES Module 包裹器

用CommonJS的方法来写这个包或者转译ES Module到CommonJS，同时搞一个ES Module的包装器来定义命名导出。使用条件导出，这个ES Module包装器用来支持`import`，CommonJS的入口用来支持`require`。这样两种导入方式得到的结果是一样的，也就避免了不一致的问题。

缺点明显就是不能完全用ES Module，也就是没法用完全的`tree shaking`，但可以把它变成优势，比如代码本身不是ES Module的。

```
// ./node_modules/pkg/package.json
{
  "type": "module",
  "main": "./index.cjs",
  "exports": {
    "import": "./wrapper.mjs",
    "require": "./index.cjs"
  }
}

// ./node_modules/pkg/index.cjs
exports.name = 'value';

// ./node_modules/pkg/wrapper.mjs
import cjsModule from './index.cjs';
export const name = cjsModule.name;
```

如果模块不单单是命名的导出，还有其他的函数或者对象的导出像 `module.exports = function(){}` 或者需要支持全导入`import pkg from 'pkg'` 那么这个ES Module包装器需要同时导出默认的内容。

```
import cjsModule from './index.cjs';
export const name = cjsModule.name;
export default cjsModule;
```

###### 适用场景

- 这个包现在就是用CommonJS写的，也不想重构到ES Module了，但想同时支持两种引入方式，那么简单搞搞包装就行了
- 这个包有被其他包依赖，而且最终的使用者可能同时装这两个包，比如一个`utilities`包是被直接引用的，而`utilities-plus`包在原来的基础上增加了一些新功能。因为这个包装器在CommonJS下导出的， 不管`utilities-plus`是CommonJS还是ES Module都可以支持。
- 这个包存了内部的状态，也不想重构将状态管理独立出来。

这种方式也可以换个玩法，那就是在明确CommonJS包和ES Module包之间不会相互影响的情况下（状态隔离了或者本身无状态），各自提供一套。

```
// ./node_modules/pkg/package.json
{
  "type": "module",
  "main": "./index.cjs",
  "exports": {
    ".": "./index.cjs",
    "./module": "./wrapper.mjs"
  }
}
```



##### 方法二 状态隔离

如果CommonJS版本的和ES Module版本的包是等价的（比如一个就是另一个转义过来的），同时包的状态管理是隔离的或者本身是无状态的。就可以直接用条件导出对应不同版本就行。

```
// ./node_modules/pkg/package.json
{
  "type": "module",
  "main": "./index.cjs",
  "exports": {
    "import": "./index.mjs",
    "require": "./index.cjs"
  }
}
```

为啥老说这个状态是个问题呢？因为这个包的CommonJS版本和ES Module版本都可能都被用到，前面说到了，不同人写法不一样可能存在同时用的问题，也可能是项目引入和项目依赖引入的方式不一样也会导致同时用到。那么这个时候，两份代码都会被加载到内存里面，也就存在了两个完全独立的内部状态，这就可能会引入难以发现和难搞的Bug。（其实也是各种多线程多进程分布式多机部署前后端同构等等场景里面经常出现的问题）

先不说无状态的包（比如像JavaScript Math模块如果是个包的话，就没有状态的事，因为都是静态的内容），可以通过如下方法来隔离可能再CommonJS和ES Module中都存在的包里面的状态的问题：

###### 状态写到外部（也就是去除状态）

```
import Date from 'date';
const someDate = new Date();
// someDate contains state; Date does not
```

这样的话包可以返回新对象，可以改对象，但不存对象，将状态放到外部



###### 状态隔离到公共内容上

把状态相关的内容都放到一个或者多个CommonJS模块里面，并且让其他CommonJS和ES Module共享这些状态。这样就不会出现两种加载方式出现两种隔离的状态的问题，这样也就没有状态不一致的问题了。

```
// ./node_modules/pkg/index.cjs
const state = require('./state.cjs');
module.exports.state = state;

// ./node_modules/pkg/index.mjs
import state from './state.cjs';
export {
  state
};
```



所有加在这个包单例上面的插件，都需要分别加载CommonJS和ES Module单例上面。

###### 适用场景

- 这个包当前是使用ES Module来写的，同时希望只要支持ES Module的地方都能用
- 这个包要么是无状态的，要么是可以简单的进行状态隔离的
- 这个包不大可能被其他的公开的包所依赖，或者即使被依赖了，这包也是无状态的或者只有不必在依赖中或者整个项目中共享的状态

这种方式也可以换个玩法，不一定需要条件导出，可以导出不同的路径来区分也行，实例如下：

```
// ./node_modules/pkg/package.json
{
  "type": "module",
  "main": "./index.cjs",
  "exports": {
    ".": "./index.cjs",
    "./module": "./index.mjs"
  }
}
```



### NodeJS 加载ES Module的支持

#### 路径要求

路径都是按照URL的规范来加载的，所以路径中带有`#` 和`?`这样的关键字需要被干掉。目前支持`file:`,`node:`,`data:` 这几种路径规则，像`https://example.com/app.js` 这样的原生不支持，除非自己定义模块加载器

##### `file:` URLs

- 引用根路径可以用`/`,`//`,`file:///`,URL解析和路径解析还有些不一样，推荐用`url.pathToFileURL`先转一下再导入

- 可以给路径带参数或者页内片段，但不同参数和不同页内片段的加载都会被当做新的加载而多次加载模块

  ```
  import './foo.mjs?query=1'; // loads ./foo.mjs with query of "?query=1"
  import './foo.mjs?query=2'; // loads ./foo.mjs with query of "?query=2"
  ```



##### `data:` Imports

- 在如下几种MIME类型中支持 dataURL

  - `text/javascript` 作为 ES Module
  - `application/json` 作为 JSON
  - `application/wasm` 作为 WASM

- dataURL的解析中不支持相对路径，举例如下

  ```
  import 'data:text/javascript,console.log("hello!");';
  import _ from 'data:application/json,"world!"';
  ```

  

##### `node:` Imports

- 前面也提到了，明确指定加载Node核心库的方法，即使安装了相同依赖也会优先使用核心库

  ```
  import fs from 'node:http';
  ```



#### 导入断言

- 支持导入断言，但还只支持`json`这一种类型

  ```
  import fooData from './foo.json' assert { type: 'json' };
  
  const { default: barData } =
    await import('./bar.json', { assert: { type: 'json' } });
  ```

  

#### 动态导入 `import()`

- 动态导入同时支持CommonJS 和 ES  Module，也可以支持交叉导入比如在CommonJS中导入ES Module，经测试简单的ES Module中导入CommonJS也可以

###### import.meta

- `url` 导入模块文件的绝对路径 `file:` URL，和浏览器访问文件的URL一样，`import.meta.url` 可以作为一个方便的URL的相对路径base
- `resolve` 【实验性的】，解析一个当前模块的相对路径



### NodeJS环境下模块加载互操作

- `import`  `import` 只能在ES Module中使用，但动态导入`import()`可以在CommonJS和ES Module中使用，如果导入的是CommonJS 模块，则`module.exports` 会作为默认的导出。也给静态分析提供了命名导出的支持。

  - 加载自定义模块的时候 `import`只能整体导入 CommonJS模块
  - 加载NodeJS内置模块的时候，`import`可以导入命名导出

- `require` `require` 只能加载 CommonJS的模块，要加载ES Module，需要用`import()`

- 为了更好的兼容现有的JS生态的用法，NodeJS中用了一个静态解析的过程让CommonJS的命名导出去对标ES module的导出，因为是做静态分析得来的，所以动态绑定的和新加到`module.exports`上面的导出都没法被感知到。基于通用语法的命名导出检测不一定能覆盖所以场景的命名导出检测，所以用default导出会更好。

  ```
  // cjs.cjs
  exports.name = 'exported';
  
  import { name } from './cjs.cjs';
  console.log(name);
  // Prints: 'exported'
  
  import cjs from './cjs.cjs';
  console.log(cjs);
  // Prints: { name: 'exported' }
  
  import * as m from './cjs.cjs';
  console.log(m);
  // Prints: [Module] { default: { name: 'exported' }, name: 'exported' }
  ```

  

- CommonJS 模块的`module.exports`导入可以和ES Module 的`default`导入对等

  ```
  import { default as cjs } from 'cjs';
  
  // The following import statement is "syntax sugar" (equivalent but sweeter)
  // for `{ default as cjsSugar }` in the above import statement:
  import cjsSugar from 'cjs';
  
  console.log(cjs);
  console.log(cjs === cjsSugar);
  // Prints:
  //   <module.exports>
  //   true
  ```

- ES Module静态导入和动态导入的对象也可以对等

  ```
  import * as m from 'cjs';
  console.log(m);
  console.log(m === await import('cjs'));
  // Prints:
  //   [Module] { default: <module.exports> }
  //   true
  ```

  

### NodeJS中 ES Module和 CommonJS 的差异

- 没有`require`,`exports`,`module.exports` ，大部分情况下，`import`可以用来加载 CommonJS 模块。如果需要的话，可以在ES Module中用`module.createRequire`来创建一个`require`函数

- 没有`__filename` 和`__dirname`，可以用`import.meta.url`来替代

- 没有 JSON  模块加载（当前还是实验特性），可以通过文件加载的方式加载，也可以用`module.createRequire`来创造CommonJS加载环境

  ```
  import { readFile } from 'fs/promises';
  const json = JSON.parse(await readFile(new URL('./dat.json', import.meta.url)));
  ```

- 没有原生模块加载（现在还不支持），可以用`module.createRequire`或者`process.dlopen`替代

- 没有`require.resolve` 这个自然，连`require`都没有，当然也可以用`module.createRequire`搞一个，也可以用`new URL('./local', import.meta.url)`来做相对路径解析，也可以用当前的实验特性`import.meta.resolve`

- 没有`NODE_PATH`环境变量，因为不是包导入的一部分，可以用symlinks来替代

- 没有`require.extensions`  这个也不需要被`import`用到

- 没有`require.cache` 因为 ES  Module是单独的，有自己的缓存方式



### 扩展的加载器

- [loaders](https://nodejs.org/docs/latest-v17.x/api/esm.html#loaders),【实验特性】，可以自定义加载和转译的方式



#  CommonJS 和 ES Module 区别

- CommonJS模块导出的是拷贝值后来变更无效，而ES Module导出的是值引用，可以感知变更
- CommonJS模块加载方式是运行时加载， ES Module是编译时静态输出
- CommonJS模块加载方式`require`是同步加载，而ES Module的`import`命令是异步处理，有静态处理的过程



## ES Module加载过程

参考文档[ESM加载图解](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)，官方规范里面就有[ECMAScript](https://tc39.es/ecma262/#sec-modules)

加载过程概要翻译：

- 构建过程：通过`import`获取依赖关系，创建依赖关系图，对每一个依赖的文件入口进行解析，并根据解析结果转换成【模块记录】,包含了当前模块的依赖模块，依赖的入口，以及相关的属性和方法
- 实例化过程：对每一个【模块记录】转换成【模块实例】(包含代码和状态的运行时结构)，（申请内层空间，这时内存中就有了这些内容）
- 执行过程：执行代码，并将实际的代码执行并填充相关实例

这三个过程是独立的异步的。



# 常见问题

## CommonJS 的循环引用

- CommonJS是动态导入，执行到特定位置，此时已经导出了哪些内容，就有哪些内容
  - `exports`为先定义的一个空对象，每次执行`exports.xx` 就添加相应的对象中
  - `module.exports` 是直接替换当前模块的导出对象
- 所以根据上面的内容，可以通过对使用`exports`导出的方式来避免因为`module.exports`集中导出导致的要使用的入口还不存在的情况，以此来解决循环引用
- 更好的方式是合理拆分模块，比如将相互引用的内容拆到第三个模块中

```
// moduleA.js

console.log('#moduleA start')

exports.varA = 1

const varB = 2
const varC = 3

const moduleB = require('./moduleB')

console.log(moduleB.varA,moduleB.varB,moduleB.varC)

module.exports = {
    varB,
    varC
}

console.log('#moduleA end')



```

```
// moduleB.js
console.log('#moduleB start')
exports.varA = 1

const varB = 2
const varC = 3

const moduleA = require('./moduleA')
console.log(moduleA.varA,moduleA.varB,moduleA.varC)

module.exports = {
    varB,
    varC
}

console.log('#moduleB end')
```

```
console.log('#module main start')
require('./moduleA')
require('./moduleB')

console.log('module main end')

```



## ES  Module 的循环引用

- ES Module的循环引用，可以直接被检查出来

- 模块导入导出的解析表现的有点怪异，可自行感受

  ```
  // moduleA.mjs
  
  console.log('#moduleA start')
  import moduleB from "./moduleB.mjs"
  console.log(moduleB.varA,moduleB.varB,moduleB.varC)
  export let varA = 1
  
  const varB = 2
  const varC = 3
  
  export default {
      varB,
      varC
  }
  
  console.log('#moduleA end')
  
  
  // moduleB.mjs
  
  console.log('#moduleB start')
  import moduleA from "./moduleA.mjs"
  console.log(moduleA.varA,moduleA.varB,moduleA.varC)
  export const varA = 1
  
  const varB = 2
  const varC = 3
  
  export default {
      varB,
      varC
  }
  
  console.log('#moduleB end')
  
  
  // main.mjs
  console.log('#module main start')
  import "./moduleA.mjs"
  import "./moduleB.mjs"
  
  console.log('module main end')
  
  ```

  

## `package.json`的`exports`字段先后顺序来判断优先级（前面的遗留问题）

疑惑点：一个对象的Key如何做到有序的？原理上对象也就是字典，key 可能是无序的或者顺序是不确定的（不同环境不一样）

虽然17.x文档上写了支持条件导出，但用了当前最新17.1.0 的版本的Node写了个[条件导出的demo](https://github.com/rawbin-/module-esmodule-demo)，发现还不支持，但不影响去找源码



### 查看NodeJS 条件导出的实现

- 下载NodeJS源码
- 翻开`lib/modules`，然后搜索`conditional` 可以找到`resolve.js`，同时能找到几个相关的函数
  - `resolvePackageTarget`
  - `isConditionalExportsMainSugar`
  - `packageExportsResolve`

- 再查看他们的调用情况，可以找出主线来

- 从上面几个函数中可以看出，也就一个`ObjectGetOwnPropertyNames` 获取配置的同时，用循环来处理的

- 那如果要有序的话，那么`ObjectGetOwnPropertyNames` 或者 `Object.getOwnPropertyNames`或者`Object.keys`是有序的

  分别用如下样例在NodeJS 和 Chrome浏览器中测试，可以发现获取的对象key是有顺序的。

  ```
  const testObjA = {
          "import": "./esm-export.mjs",
          "require": "./reuire-export.cjs",
          "node": {
              "require": "./require-export.cjs",
              "import": "./esm-export.mjs"
          },
          "default": "./main-export.mjs"
      }
      
  const testObjB = {
          "node": {
              "require": "./require-export.cjs",
              "import": "./esm-export.mjs"
          },
          "default": "./main-export.mjs",
          "import": "./esm-export.mjs",
          "require": "./reuire-export.cjs",
      }    
      
  console.log(Object.keys(testObjA))
  console.log(Object.getOwnPropertyNames(testObjA))
  console.log(Object.keys(testObjB))
  console.log(Object.getOwnPropertyNames(testObjB))
  ```

- 再看看[ECMAScript规范](https://tc39.es/ecma262/#sec-object.getownpropertynames)的实现要求，`Object.keys`和`Object.getOwnPropertyNames`都是拿到数据之后，循环放到列表里面，然后转成数组，所以需要看看拿数据的时候做了什么

- 再翻开`chromium v8`的源码，在源码里面找ECMAScript中，拿数据的关键字`OwnPropertyKeys` ，在结果里面可以看到`src/builtins/builtins-object.cc`里面有`GetOwnPropertyKeys`的定义

  ```
  Object GetOwnPropertyKeys(Isolate* isolate, BuiltinArguments args,
                            PropertyFilter filter) {
    HandleScope scope(isolate);
    Handle<Object> object = args.atOrUndefined(isolate, 1);
    Handle<JSReceiver> receiver;
    ASSIGN_RETURN_FAILURE_ON_EXCEPTION(isolate, receiver,
                                       Object::ToObject(isolate, object));
    Handle<FixedArray> keys;
    ASSIGN_RETURN_FAILURE_ON_EXCEPTION(
        isolate, keys,
        KeyAccumulator::GetKeys(receiver, KeyCollectionMode::kOwnOnly, filter,
                                GetKeysConversion::kConvertToString));
    return *isolate->factory()->NewJSArrayWithElements(keys);
  }
  ```

- 再找到`KeyAccumulator::GetKeys`的定义，可以看到这里用的数据结构是`OrderedHashSet`有序的Hash结构，也就是本身是有序的支撑

  ```
  Handle<FixedArray> KeyAccumulator::GetKeys(GetKeysConversion convert) {
    if (keys_.is_null()) {
      return isolate_->factory()->empty_fixed_array();
    }
    if (mode_ == KeyCollectionMode::kOwnOnly &&
        keys_->map() == ReadOnlyRoots(isolate_).fixed_array_map()) {
      return Handle<FixedArray>::cast(keys_);
    }
    USE(ContainsOnlyValidKeys);
    Handle<FixedArray> result =
        OrderedHashSet::ConvertToKeysArray(isolate(), keys(), convert);
    DCHECK(ContainsOnlyValidKeys(result));
    return result;
  }
  ```

- 再继续查找继承链`OrderedHashSet`=>`OrderedHashTable`=>`FixedArray` 发现本质上是一个数组或者列表，有序就是本性了。

