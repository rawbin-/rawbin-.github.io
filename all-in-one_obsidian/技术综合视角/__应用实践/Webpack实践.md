# 概念
## 入口（entry）
- 寻找依赖，构建依赖图的起点
## 输出（output）
- 产物相关的配置，比如名称、路径等
## 加载器（loader）
- 除了JavaScript和JSON之外的资源，都需要用加载器来处理，将其转换为模块
## 插件（plugin）
- loader之外的能力，几乎都可以用插件来实现，比如打包优化，打包信息获取，资源管理，信息注入等等
## 模式
- webpack简化配置的两种预制模式，一种开发模式，一种线上模式
## 浏览器兼容（browser compatibility）
Webpack 支持所有符合 [ES5 标准](https://kangax.github.io/compat-table/es5/) 的浏览器（不支持 IE8 及以下版本）。webpack 的 `import()` 和 `require.ensure()` 需要 `Promise`。如果你想要支持旧版本浏览器，在使用这些表达式之前，还需要 [提前加载 polyfill](https://webpack.docschina.org/guides/shimming/)。
## 环境（environment）
Webpack 5 运行于 Node.js v10.13.0+ 的版本。


# 配置
## 入口（entry）
- 支持的配置方式
	-  字符串
	- 字符串数组
	- 配置对象
- 用于描述入口的对象。你可以使用如下属性：
	- dependOn: 当前入口所依赖的入口。它们必须在该入口被加载前被加载。
	- filename: 指定要输出的文件名称。
	- import: 启动时需加载的模块。
	- library: 指定 library 选项，为当前 entry 构建一个 library。
	- runtime: 运行时 chunk 的名字。如果设置了，就会创建一个新的运行时 chunk。在 webpack 5.43.0 之后可将其设为 false 以避免一个新的运行时 chunk。
	- publicPath: 当该入口的输出文件在浏览器中被引用时，为它们指定一个公共 URL 地址。请查看 output.publicPath。

## 输出（output）
- 只有一个输出配置
	- filename
	- path
	- publicPath
- https://webpack.docschina.org/configuration/output/

## 加载器（loader）
- 使用 module.rules 指定多个loader，每个loader对应 `test` 和`use`俩属性
	- loader 从右到左（或从下到上）地取值(evaluate)/执行(execute)。
- 使用内联方式定义，使用`!`分隔
	- 通过为内联 `import` 语句添加前缀，可以覆盖 [配置](https://webpack.docschina.org/configuration) 中的所有 loader, preLoader 和 postLoader：
	-   使用 `!` 前缀，将禁用所有已配置的 normal loader(普通 loader)
    ```js
    import Styles from '!style-loader!css-loader?modules!./styles.css';
    ```
	-   使用 `!!` 前缀，将禁用所有已配置的 loader（preLoader, loader, postLoader）
    ```js
    import Styles from '!!style-loader!css-loader?modules!./styles.css';
    ```
	-   使用 `-!` 前缀，将禁用所有已配置的 preLoader 和 loader，但是不禁用 postLoaders
    
    ```js
    import Styles from '-!style-loader!css-loader?modules!./styles.css';
    ```
	- 选项可以传递查询参数，例如 `?key=value&foo=bar`，或者一个 JSON 对象，例如 `?{"key":"value","foo":"bar"}`。
- loader的特性
	- loader 支持链式调用。链中的每个 loader 会将转换应用在已处理过的资源上。一组链式的 loader 将按照相反的顺序执行。链中的第一个 loader 将其结果（也就是应用过转换后的资源）传递给下一个 loader，依此类推。最后，链中的最后一个 loader，返回 webpack 所期望的 JavaScript。
	-   loader 可以是同步的，也可以是异步的。
	-   loader 运行在 Node.js 中，并且能够执行任何操作。
	-   loader 可以通过 `options` 对象配置（仍然支持使用 `query` 参数来设置选项，但是这种方式已被废弃）。
	-   除了常见的通过 `package.json` 的 `main` 来将一个 npm 模块导出为 loader，还可以在 module.rules 中使用 `loader` 字段直接引用一个模块。
	-   插件(plugin)可以为 loader 带来更多特性。
	-   loader 能够产生额外的任意文件。

- 可以通过 loader 的预处理函数，为 JavaScript 生态系统提供更多能力。用户现在可以更加灵活地引入细粒度逻辑，例如：压缩、打包、语言转译（或编译）和 [更多其他特性](https://webpack.docschina.org/loaders)。
- loader 遵循标准 [模块解析](https://webpack.docschina.org/concepts/module-resolution/) 规则。多数情况下，loader 将从 [模块路径](https://webpack.docschina.org/concepts/module-resolution/#module-paths) 加载（通常是从 `npm install`, `node_modules` 进行加载）。
- 我们预期 loader 模块导出为一个函数，并且编写为 Node.js 兼容的 JavaScript。通常使用 npm 进行管理 loader，但是也可以将应用程序中的文件作为自定义 loader。按照约定，loader 通常被命名为 `xxx-loader`（例如 `json-loader`）。更多详细信息，请查看 [编写一个 loader](https://webpack.docschina.org/contribute/writing-a-loader/)。

##  插件（plugin）
- webpack **插件**是一个具有 [`apply`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 方法的 JavaScript 对象。`apply` 方法会被 webpack compiler 调用，并且在 **整个** 编译生命周期都可以访问 compiler 对象。

**ConsoleLogOnBuildWebpackPlugin.js**

```
const pluginName = 'ConsoleLogOnBuildWebpackPlugin';

class ConsoleLogOnBuildWebpackPlugin {
  apply(compiler) {
    compiler.hooks.run.tap(pluginName, (compilation) => {
      console.log('webpack 构建正在启动！');
    });
  }
}

module.exports = ConsoleLogOnBuildWebpackPlugin;
```

compiler hook 的 tap 方法的第一个参数，应该是驼峰式命名的插件名称。建议为此使用一个常量，以便它可以在所有 hook 中重复使用。
- 由于**插件**可以携带参数/选项，你必须在 webpack 配置中，向 `plugins` 属性传入一个 `new` 实例。取决于你的 webpack 用法，对应有多种使用插件的方式。
- 要用new Plugin的方式去做，避免数据冲突

##  配置（configuration）
- 配置可以导出单个Object，也可以导出多个target配置
- 也支持用其他语言编写Webpack配置

## 模块
在[模块化编程](https://en.wikipedia.org/wiki/Modular_programming)中，开发者将程序分解为功能离散的 chunk，并称之为 **模块**。
每个模块都拥有小于完整程序的体积，使得验证、调试及测试变得轻而易举。 精心编写的 **模块** 提供了可靠的抽象和封装界限，使得应用程序中每个模块都具备了条理清晰的设计和明确的目的。

Node.js 从一开始就支持模块化编程。 然而，web 的 _模块化_ 正在缓慢支持中。 在 web 界存在多种支持 JavaScript 模块化的工具，这些工具各有优势和限制。 Webpack 从这些系统中汲取了经验和教训，并将 _模块_ 的概念应用到项目的任何文件中。

### Webpack中的模块
与 [Node.js 模块](https://nodejs.org/api/modules.html)相比，webpack _模块_ 能以各种方式表达它们的依赖关系。下面是一些示例：

-   [ES2015 `import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 语句
-   [CommonJS](http://www.commonjs.org/specs/modules/1.0/) `require()` 语句
-   [AMD](https://github.com/amdjs/amdjs-api/blob/master/AMD.md) `define` 和 `require` 语句
-   css/sass/less 文件中的 [`@import` 语句](https://developer.mozilla.org/en-US/docs/Web/CSS/@import)。
-   stylesheet `url(...)` 或者 HTML `<img src=...>` 文件中的图片链接。

###  支持的模块类型
Webpack 天生支持如下模块类型：

-   [ECMAScript 模块](https://webpack.docschina.org/guides/ecma-script-modules)
-   CommonJS 模块
-   AMD 模块
-   [Assets](https://webpack.docschina.org/guides/asset-modules)
-   WebAssembly 模块

通过 **loader** 可以使 webpack 支持多种语言和预处理器语法编写的模块。**loader** 向 webpack 描述了如何处理非原生_模块_，并将相关**依赖**引入到你的 **bundles**中。 webpack 社区已经为各种流行的语言和预处理器创建了 _loader_，其中包括：

-   [CoffeeScript](http://coffeescript.org/)
-   [TypeScript](https://www.typescriptlang.org/)
-   [ESNext (Babel)](https://babeljs.io/)
-   [Sass](http://sass-lang.com/)
-   [Less](http://lesscss.org/)
-   [Stylus](http://stylus-lang.com/)
-   [Elm](https://elm-lang.org/)

当然还有更多！总得来说，webpack 提供了可定制，强大且丰富的 API，允许在 **任何技术栈** 中使用，同时支持在开发、测试和生产环境的工作流中做到 **无侵入性**。

关于 loader 的相关信息，请参考 [**loader 列表**](https://webpack.docschina.org/loaders) 或 [**自定义 loader**](https://webpack.docschina.org/api/loaders)。


## 模块解析（Module Resolution）
resolver 是一个帮助寻找模块绝对路径的库。 一个模块可以作为另一个模块的依赖模块，然后被后者引用，如下：

```
import foo from 'path/to/module';
// 或者
require('path/to/module');
```

所依赖的模块可以是来自应用程序的代码或第三方库。 resolver 帮助 webpack 从每个 `require`/`import` 语句中，找到需要引入到 bundle 中的模块代码。 当打包模块时，webpack 使用 [enhanced-resolve](https://github.com/webpack/enhanced-resolve) 来解析文件路径。

使用 `enhanced-resolve`，webpack 能解析三种文件路径：

### 绝对路径

```
import '/home/me/file';

import 'C:\\Users\\me\\file';
```

由于已经获得文件的绝对路径，因此不需要再做进一步解析。

### 相对路径

```
import '../src/file1';
import './file2';
```

在这种情况下，使用 `import` 或 `require` 的资源文件所处的目录，被认为是上下文目录。在 `import/require` 中给定的相对路径，会拼接此上下文路径，来生成模块的绝对路径。

### 模块路径

```
import 'module';
import 'module/lib/file';
```

在 [`resolve.modules`](https://webpack.docschina.org/configuration/resolve/#resolvemodules) 中指定的所有目录中检索模块。 你可以通过配置别名的方式来替换初始模块路径，具体请参照 [`resolve.alias`](https://webpack.docschina.org/configuration/resolve/#resolvealias) 配置选项。

-   如果 package 中包含 `package.json` 文件，那么在 [`resolve.exportsFields`](https://webpack.docschina.org/configuration/resolve/#resolveexportsfields) 配置选项中指定的字段会被依次查找，`package.json` 中的第一个字段会根据 [package 导出指南](https://webpack.docschina.org/guides/package-exports/)确定 package 中可用的 export。

一旦根据上述规则解析路径后，resolver 将会检查路径是指向文件还是文件夹。如果路径指向文件：

-   如果文件具有扩展名，则直接将文件打包。
-   否则，将使用 [`resolve.extensions`](https://webpack.docschina.org/configuration/resolve/#resolveextensions) 选项作为文件扩展名来解析，此选项会告诉解析器在解析中能够接受那些扩展名（例如 `.js`，`.jsx`）。

如果路径指向一个文件夹，则进行如下步骤寻找具有正确扩展名的文件：

-   如果文件夹中包含 `package.json` 文件，则会根据 [`resolve.mainFields`](https://webpack.docschina.org/configuration/resolve/#resolve-mainfields) 配置中的字段顺序查找，并根据 `package.json` 中的符合配置要求的第一个字段来确定文件路径。
-   如果不存在 `package.json` 文件或 [`resolve.mainFields`](https://webpack.docschina.org/configuration/resolve/#resolvemainfields) 没有返回有效路径，则会根据 [`resolve.mainFiles`](https://webpack.docschina.org/configuration/resolve/#resolvemainfiles) 配置选项中指定的文件名顺序查找，看是否能在 import/require 的目录下匹配到一个存在的文件名。
-   然后使用 [`resolve.extensions`](https://webpack.docschina.org/configuration/resolve/#resolveextensions) 选项，以类似的方式解析文件扩展名。

Webpack 会根据构建目标，为这些选项提供合理的[默认](https://webpack.docschina.org/configuration/resolve)配置。

## 模块整合（Module Federation）Webpack5利器
### 动机

多个独立的构建可以组成一个应用程序，这些独立的构建之间不应该存在依赖关系，因此可以单独开发和部署它们。

这通常被称作微前端，但并不仅限于此。

###### Live Preview

Check out this live module federation example on StackBlitz.

[![Open in StackBlitz](https://webpack.docschina.org/open-in-stackblitz-button.5857815e117b5952651d.svg)](https://stackblitz.com/github/webpack/webpack.js.org/tree/master/examples/module-federation?terminal=start&terminal=)

### 底层概念

我们区分本地模块和远程模块。本地模块即为普通模块，是当前构建的一部分。远程模块不属于当前构建，并在运行时从所谓的容器加载。

加载远程模块被认为是异步操作。当使用远程模块时，这些异步操作将被放置在远程模块和入口之间的下一个 chunk 的加载操作中。如果没有 chunk 加载操作，就不能使用远程模块。

chunk 的加载操作通常是通过调用 `import()` 实现的，但也支持像 `require.ensure` 或 `require([...])` 之类的旧语法。

容器是由容器入口创建的，该入口暴露了对特定模块的异步访问。暴露的访问分为两个步骤：

1.  加载模块（异步的）
2.  执行模块（同步的）

步骤 1 将在 chunk 加载期间完成。步骤 2 将在与其他（本地和远程）的模块交错执行期间完成。这样一来，执行顺序不受模块从本地转换为远程或从远程转为本地的影响。

容器可以嵌套使用，容器可以使用来自其他容器的模块。容器之间也可以循环依赖。

### 高级概念

每个构建都充当一个容器，也可将其他构建作为容器。通过这种方式，每个构建都能够通过从对应容器中加载模块来访问其他容器暴露出来的模块。

共享模块是指既可重写的又可作为向嵌套容器提供重写的模块。它们通常指向每个构建中的相同模块，例如相同的库。

packageName 选项允许通过设置包名来查找所需的版本。默认情况下，它会自动推断模块请求，当想禁用自动推断时，请将 requiredVersion 设置为 false 。

### 构建块(Building blocks)

#### ContainerPlugin (low level)

该插件使用指定的公开模块来创建一个额外的容器入口。

#### ContainerReferencePlugin (low level)

该插件将特定的引用添加到作为外部资源（externals）的容器中，并允许从这些容器中导入远程模块。它还会调用这些容器的 `override` API 来为它们提供重载。本地的重载（当构建也是一个容器时，通过 `__webpack_override__` 或 `override` API）和指定的重载被提供给所有引用的容器。

#### ModuleFederationPlugin (high level)

[`ModuleFederationPlugin`](https://webpack.docschina.org/plugins/module-federation-plugin) 组合了 `ContainerPlugin` 和 `ContainerReferencePlugin`。

### 概念目标

-   它既可以暴露，又可以使用 webpack 支持的任何模块类型
-   代码块加载应该并行加载所需的所有内容(web:到服务器的单次往返)
-   从使用者到容器的控制
    -   重写模块是一种单向操作
    -   同级容器不能重写彼此的模块。
-   概念适用于独立于环境
    -   可用于 web、Node.js 等
-   共享中的相对和绝对请求
    -   会一直提供，即使不使用
    -   会将相对路径解析到 `config.context`
    -   默认不会使用 `requiredVersion`
-   共享中的模块请求
    -   只在使用时提供
    -   会匹配构建中所有使用的相等模块请求
    -   将提供所有匹配模块
    -   将从图中这个位置的 package.json 提取 `requiredVersion`
    -   当你有嵌套的 node_modules 时，可以提供和使用多个不同的版本
-   共享中尾部带有 `/`  的模块请求将匹配所有具有这个前缀的模块请求

### 用例

#### 每个页面单独构建

单页应用的每个页面都是在单独的构建中从容器暴露出来的。主体应用程序(application shell)也是独立构建，会将所有页面作为远程模块来引用。通过这种方式，可以单独部署每个页面。在更新路由或添加新路由时部署主体应用程序。主体应用程序将常用库定义为共享模块，以避免在页面构建中出现重复。

#### 将组件库作为容器

许多应用程序共享一个通用的组件库，可以将其构建成暴露所有组件的容器。每个应用程序使用来自组件库容器的组件。可以单独部署对组件库的更改，而不需要重新部署所有应用程序。应用程序自动使用组件库的最新版本。

### 动态远程容器

该容器接口支持 `get`  和 `init` 方法。 `init` 是一个兼容 `async` 的方法，调用时，只含有一个参数：共享作用域对象(shared scope object)。此对象在远程容器中用作共享作用域，并由 host 提供的模块填充。 可以利用它在运行时动态地将远程容器连接到 host 容器。

**init.js**

```
(async () => {
  // 初始化共享作用域（shared scope）用提供的已知此构建和所有远程的模块填充它
  await __webpack_init_sharing__('default');
  const container = window.someContainer; // 或从其他地方获取容器
  // 初始化容器 它可能提供共享模块
  await container.init(__webpack_share_scopes__.default);
  const module = await container.get('./module');
})();
```

容器尝试提供共享模块，但是如果共享模块已经被使用，则会发出警告，并忽略所提供的共享模块。容器仍能将其作为降级模块。

你可以通过动态加载的方式，提供一个共享模块的不同版本，从而实现 A/B 测试。

###### Tip

在尝试动态连接远程容器之前，确保已加载容器。

例子：

**init.js**

```
function loadComponent(scope, module) {
  return async () => {
    // 初始化共享作用域（shared scope）用提供的已知此构建和所有远程的模块填充它
    await __webpack_init_sharing__('default');
    const container = window[scope]; // 或从其他地方获取容器
    // 初始化容器 它可能提供共享模块
    await container.init(__webpack_share_scopes__.default);
    const factory = await window[scope].get(module);
    const Module = factory();
    return Module;
  };
}

loadComponent('abtests', 'test123');
```

[查看完整实现](https://github.com/module-federation/module-federation-examples/tree/master/advanced-api/dynamic-remotes)

### 基于 Promise 的动态 Remote

一般来说，remote 是使用 URL 配置的，示例如下：

```
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'host',
      remotes: {
        app1: 'app1@http://localhost:3001/remoteEntry.js',
      },
    }),
  ],
};
```

但是你也可以向 remote 传递一个 promise，其会在运行时被调用。你应该用任何符合上面描述的 `get/init` 接口的模块来调用这个 promise。例如，如果你想传递你应该使用哪个版本的联邦模块，你可以通过一个查询参数做以下事情：

```
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'host',
      remotes: {
        app1: `promise new Promise(resolve => {
      const urlParams = new URLSearchParams(window.location.search)
      const version = urlParams.get('app1VersionParam')
      // This part depends on how you plan on hosting and versioning your federated modules
      const remoteUrlWithVersion = 'http://localhost:3001/' + version + '/remoteEntry.js'
      const script = document.createElement('script')
      script.src = remoteUrlWithVersion
      script.onload = () => {
        // the injected script has loaded and is available on window
        // we can now resolve this Promise
        const proxy = {
          get: (request) => window.app1.get(request),
          init: (arg) => {
            try {
              return window.app1.init(arg)
            } catch(e) {
              console.log('remote container already initialized')
            }
          }
        }
        resolve(proxy)
      }
      // inject this script with the src set to the versioned remoteEntry.js
      document.head.appendChild(script);
    })
    `,
      },
      // ...
    }),
  ],
};
```

请注意当使用该 API 时，你 _必须_ resolve 一个包含 get/init API 的对象。

### 动态 Public Path

#### 提供一个 host api 以设置 publicPath

可以允许 host 在运行时通过公开远程模块的方法来设置远程模块的 publicPath。

当你在 host 域的子路径上挂载独立部署的子应用程序时，这种方法特别有用。

场景：

你在 `https://my-host.com/app/*` 上有一个 host 应用，并且在 `https://foo-app.com` 上有一个子应用。子应用程序也挂载在 host 域上, 因此， `https://foo-app.com` 可以通过 `https://my-host.com/app/foo-app` 访问，并且 `https://my-host.com/app/foo-app/*` 可以通过代理重定向到 `https://foo-app.com/*`。

示例：

**webpack.config.js (remote)**

```
module.exports = {
  entry: {
    remote: './public-path',
  },
  plugins: [
    new ModuleFederationPlugin({
      name: 'remote', // 该名称必须与入口名称相匹配
      exposes: ['./public-path'],
      // ...
    }),
  ],
};
```

**public-path.js (remote)**

```
export function set(value) {
  __webpack_public_path__ = value;
}
```

**src/index.js (host)**

```
const publicPath = await import('remote/public-path');
publicPath.set('/your-public-path');

//boostrap app  e.g. import('./boostrap.js')
```

#### Infer publicPath from script

One could infer the publicPath from the script tag from `document.currentScript.src` and set it with the [`__webpack_public_path__`](https://webpack.docschina.org/api/module-variables/#__webpack_public_path__-webpack-specific) module variable at runtime.

示例：

**webpack.config.js (remote)**

```
module.exports = {
  entry: {
    remote: './setup-public-path',
  },
  plugins: [
    new ModuleFederationPlugin({
      name: 'remote', // 该名称必须与入口名称相匹配
      // ...
    }),
  ],
};
```

**setup-public-path.js (remote)**

```
// 使用你自己的逻辑派生 publicPath，并使用 __webpack_public_path__ API 设置它
__webpack_public_path__ = document.currentScript.src + '/../';
```

###### Tip

[`output.publicPath`](https://webpack.docschina.org/configuration/output/#outputpublicpath) 配置项也可设置为 `'auto'`，它将为你自动决定一个 publicPath。

### 故障排除

#### `Uncaught Error: Shared module is not available for eager consumption`

应用程序正急切地执行一个作为全局主机运行的应用程序。有如下选项可供选择:

你可以在模块联邦的高级 API 中将依赖设置为即时依赖，此 API 不会将模块放在异步 chunk 中，而是同步地提供它们。这使得我们在初始块中可以直接使用这些共享模块。但是要注意，由于所有提供的和降级模块是要异步下载的，因此，建议只在应用程序的某个地方提供它，例如 shell。

我们强烈建议使用异步边界(asynchronous boundary)。它将把初始化代码分割成更大的块，以避免任何额外的开销，以提高总体性能。

例如，你的入口看起来是这样的：

**index.js**

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
ReactDOM.render(<App />, document.getElementById('root'));
```

让我们创建 bootstrap.js 文件，并将入口文件的内容放到里面，然后将 bootstrap 引入到入口文件中:

**index.js**

```
+ import('./bootstrap');
- import React from 'react';
- import ReactDOM from 'react-dom';
- import App from './App';
- ReactDOM.render(<App />, document.getElementById('root'));
```

**bootstrap.js**

```
+ import React from 'react';
+ import ReactDOM from 'react-dom';
+ import App from './App';
+ ReactDOM.render(<App />, document.getElementById('root'));
```

这种方法有效，但存在局限性或缺点。

通过 `ModuleFederationPlugin`  将依赖的 `eager` 属性设置为 `true`

**webpack.config.js**

```
// ...
new ModuleFederationPlugin({
  shared: {
    ...deps,
    react: {
      eager: true,
    },
  },
});
```

#### `Uncaught Error: Module "./Button" does not exist in container.`

错误提示中可能不会显示 `"./Button"`，但是信息看起来差不多。这个问题通常会出现在将 webpack beta.16 升级到 webpack beta.17 中。

在 ModuleFederationPlugin 里，更改 exposes:

```
new ModuleFederationPlugin({
  exposes: {
-   'Button': './src/Button'
+   './Button':'./src/Button'
  }
});
```

#### `Uncaught TypeError: fn is not a function`

此处错误可能是丢失了远程容器，请确保在使用前添加它。 如果已为试图使用远程服务器的容器加载了容器，但仍然看到此错误，则需将主机容器的远程容器文件也添加到 HTML 中。

#### 来自多个 remote 的模块之间的冲突

如果你想从不同的 remote 中加载多个模块，建议为你的远程构建设置 [`output.uniqueName`](https://webpack.docschina.org/configuration/output/#outputuniquename) 以避免多个 webpack 运行时之间的冲突。 If you're going to load multiple modules from different remotes, it's advised to set the [`output.uniqueName`](https://webpack.docschina.org/configuration/output/#outputuniquename) option for your remote builds to avoid collisions between multiple webpack runtimes.

### Further Reading

-   [Webpack 5 Module Federation: A game-changer in JavaScript architecture](https://medium.com/swlh/webpack-5-module-federation-a-game-changer-to-javascript-architecture-bcdd30e02669)
-   [Explanations and Examples](https://github.com/module-federation/module-federation-examples)
-   [Module Federation YouTube Playlist](https://www.youtube.com/playlist?list=PLWSiF9YHHK-DqsFHGYbeAMwbd9xcZbEWJ)

## 模块热替换（HMR）
模块热替换(HMR - hot module replacement)功能会在应用程序运行过程中，替换、添加或删除 [模块](https://webpack.docschina.org/concepts/modules/)，而无需重新加载整个页面。主要是通过以下几种方式，来显著加快开发速度：

-   保留在完全重新加载页面期间丢失的应用程序状态。
-   只更新变更内容，以节省宝贵的开发时间。
-   在源代码中 CSS/JS 产生修改时，会立刻在浏览器中进行更新，这几乎相当于在浏览器 devtools 直接更改样式。

### 实现方式
#### 在应用程序中

通过以下步骤，可以做到在应用程序中置换(swap in and out)模块：

1.  应用程序要求 HMR runtime 检查更新。
2.  HMR runtime 异步地下载更新，然后通知应用程序。
3.  应用程序要求 HMR runtime 应用更新。
4.  HMR runtime 同步地应用更新。

你可以设置 HMR，以使此进程自动触发更新，或者你可以选择要求在用户交互时进行更新。

### 在 compiler 中

除了普通资源，compiler 需要发出 "update"，将之前的版本更新到新的版本。"update" 由两部分组成：

1.  更新后的 [manifest](https://webpack.docschina.org/concepts/manifest) (JSON)
2.  一个或多个 updated chunk (JavaScript)

manifest 包括新的 compilation hash 和所有的 updated chunk 列表。每个 chunk 都包含着全部更新模块的最新代码（或一个 flag 用于表明此模块需要被移除）。

compiler 会确保在这些构建之间的模块 ID 和 chunk ID 保持一致。通常将这些 ID 存储在内存中（例如，使用 [webpack-dev-server](https://webpack.docschina.org/configuration/dev-server/) 时），但是也可能会将它们存储在一个 JSON 文件中。

#### 在模块中

HMR 是可选功能，只会影响包含 HMR 代码的模块。举个例子，通过 [`style-loader`](https://github.com/webpack-contrib/style-loader) 为 style 追加补丁。为了运行追加补丁，`style-loader` 实现了 HMR 接口；当它通过 HMR 接收到更新，它会使用新的样式替换旧的样式。

类似的，当在一个模块中实现了 HMR 接口，你可以描述出当模块被更新后发生了什么。然而在多数情况下，不需要在每个模块中强行写入 HMR 代码。如果一个模块没有 HMR 处理函数，更新就会冒泡(bubble up)。这意味着某个单独处理函数能够更新整个模块树。如果在模块树的一个单独模块被更新，那么整组依赖模块都会被重新加载。

有关 `module.hot` 接口的详细信息，请查看 [HMR API 页面](https://webpack.docschina.org/api/hot-module-replacemen
#### 在 runtime 中

这件事情比较有技术性……如果你对其内部不感兴趣，可以随时跳到 [HMR API 页面](https://webpack.docschina.org/api/hot-module-replacement) 或 [HMR 指南](https://webpack.docschina.org/guides/hot-module-replacement)。

对于模块系统运行时(module system runtime)，会发出额外代码，来跟踪模块 `parents` 和 `children` 关系。在管理方面，runtime 支持两个方法 `check` 和 `apply`。

`check` 方法，发送一个 HTTP 请求来更新 manifest。如果请求失败，说明没有可用更新。如果请求成功，会将 updated chunk 列表与当前的 loaded chunk 列表进行比较。每个 loaded chunk 都会下载相应的 updated chunk。当所有更新 chunk 完成下载，runtime 就会切换到 `ready` 状态。

`apply` 方法，将所有 updated module 标记为无效。对于每个无效 module，都需要在模块中有一个 update handler，或者在此模块的父级模块中有 update handler。否则，会进行无效标记冒泡，并且父级也会被标记为无效。继续每个冒泡，直到到达应用程序入口起点，或者到达带有 update handler 的 module（以最先到达为准，冒泡停止）。如果它从入口起点开始冒泡，则此过程失败。

之后，所有无效 module 都会被（通过 dispose handler）处理和解除加载。然后更新当前 hash，并且调用所有 `accept` handler。runtime 切换回 `idle` 状态，一切照常继续。

## 构建目标
### 可选值
- async-node
- elecron-main
- electron-renderer
- electron-preload
- node
- node-webkit
- nwjs[[X].Y]
- web
- webworker
- esX
- browserlist
- `FunctionModulePlugin` 来为浏览器环境提供目标，使用 `NodeTargetPlugin` 和 `ExternalsPlugin`
- 为 CommonJS 和 Electron 内置模块提供目标。
- 使用 `NodeTemplatePlugin` 且 `asyncChunkLoading` 设置为 `true` ，`FunctionModulePlugin` 来为浏览器提供目标，使用 `NodeTargetPlugin` 和 `ExternalsPlugin` 为 CommonJS 和 Electron 内置模块提供目标。

## 元信息
在使用 webpack 构建的典型应用程序或站点中，有三种主要的代码类型：

1.  你或你的团队编写的源码。
2.  你的源码会依赖的任何第三方的 library 或 "vendor" 代码。
3.  webpack 的 runtime 和 **manifest**，管理所有模块的交互。
本文将重点介绍这三个部分中的最后部分：runtime 和 manifest，特别是 manifest。
### runtime
runtime，以及伴随的 manifest 数据，主要是指：在浏览器运行过程中，webpack 用来连接模块化应用程序所需的所有代码。它包含：在模块交互时，连接模块所需的加载和解析逻辑。包括：已经加载到浏览器中的连接模块逻辑，以及尚未加载模块的延迟加载逻辑。

### manifest
一旦你的应用在浏览器中以 `index.html` 文件的形式被打开，一些 bundle 和应用需要的各种资源都需要用某种方式被加载与链接起来。在经过打包、压缩、为延迟加载而拆分为细小的 chunk 这些 webpack [`优化`](https://webpack.docschina.org/configuration/optimization/) 之后，你精心安排的 `/src` 目录的文件结构都已经不再存在。所以 webpack 如何管理所有所需模块之间的交互呢？这就是 manifest 数据用途的由来……

当 compiler 开始执行、解析和映射应用程序时，它会保留所有模块的详细要点。这个数据集合称为 "manifest"，当完成打包并发送到浏览器时，runtime 会通过 manifest 来解析和加载模块。无论你选择哪种 [模块语法](https://webpack.docschina.org/api/module-methods)，那些 `import` 或 `require` 语句现在都已经转换为 `__webpack_require__` 方法，此方法指向模块标识符(module identifier)。通过使用 manifest 中的数据，runtime 将能够检索这些标识符，找出每个标识符背后对应的模块。

### 长期缓存
所以，现在你应该对 webpack 在幕后工作有一点了解。“但是，这对我有什么影响呢？”，你可能会问。答案是大多数情况下没有。runtime 做完成这些工作：一旦你的应用程序加载到浏览器中，使用 manifest，然后所有内容将展现出魔幻般运行结果。然而，如果你决定通过使用浏览器缓存来改善项目的性能，理解这一过程将突然变得极为重要。

通过使用内容散列(content hash)作为 bundle 文件的名称，这样在文件内容修改时，会计算出新的 hash，浏览器会使用新的名称加载文件，从而使缓存无效。一旦你开始这样做，你会立即注意到一些有趣的行为。即使某些内容明显没有修改，某些 hash 还是会改变。这是因为，注入的 runtime 和 manifest 在每次构建后都会发生变化。

查看 _管理输出_ 指南的 [manifest 部分](https://webpack.docschina.org/guides/output-management/#the-manifest)，了解如何提取 manifest，并阅读下面的指南，以了解更多长效缓存错综复杂之处。

## 构建监听
- watch:true
- watchOptions
	- aggregateTimeout
	- ignored
	- poll
	- followSymlinks
	- stdin

## 外部扩展
`externals` 配置选项提供了「从输出的 bundle 中排除依赖」的方法。相反，所创建的 bundle 依赖于那些存在于用户环境(consumer's environment)中的依赖。此功能通常对 **library 开发人员**来说是最有用的，然而也会有各种各样的应用程序用到它。

### externals

`string` `[string]` `object` `function` `RegExp`

**防止**将某些 `import` 的包(package)**打包**到 bundle 中，而是在运行时(runtime)再去从外部获取这些_扩展依赖(external dependencies)_。

例如，从 CDN 引入 [jQuery](https://jquery.com/)，而不是把它打包：

**index.html**

```
<script
  src="https://code.jquery.com/jquery-3.1.0.js"
  integrity="sha256-slogkvB1K3VOkzAI8QITxV3VzpOnkeNVsKvtkYLMjfk="
  crossorigin="anonymous"
></script>
```

**webpack.config.js**

```
module.exports = {
  //...
  externals: {
    jquery: 'jQuery',
  },
};
```

这样就剥离了那些不需要改动的依赖模块，换句话，下面展示的代码还可以正常运行：

```
import $ from 'jquery';

$('.my-element').animate(/* ... */);
```

具有外部依赖(external dependency)的 bundle 可以在各种模块上下文(module context)中使用，例如 [CommonJS, AMD, 全局变量和 ES2015 模块](https://webpack.docschina.org/concepts/modules)。外部 library 可能是以下任何一种形式：

-   **root**：可以通过一个全局变量访问 library（例如，通过 script 标签）。
-   **commonjs**：可以将 library 作为一个 CommonJS 模块访问。
-   **commonjs2**：和上面的类似，但导出的是 `module.exports.default`.
-   **amd**：类似于 `commonjs`，但使用 AMD 模块系统。

可以接受以下语法……

### 字符串

请查看上面的例子。属性名称是 `jquery`，表示应该排除 `import $ from 'jquery'` 中的 `jquery` 模块。为了替换这个模块，`jQuery` 的值将被用来检索一个全局的 `jQuery` 变量。换句话说，当设置为一个字符串时，它将被视为`全局的`（定义在上面和下面）。

另一方面，如果你想将一个符合 CommonJS 模块化规则的类库外部化，你可以提供外联类库的类型以及类库的名称。

如果你想将 `fs-extra` 从输出的 bundle 中剔除并在运行时中引入它，你可以如下定义：

```
module.exports = {
  // ...
  externals: {
    'fs-extra': 'commonjs2 fs-extra',
  },
};
```

这样的做法会让任何依赖的模块都不变，正如以下所示的代码：

```
import fs from 'fs-extra';
```

会将代码编译成：

```
const fs = require('fs-extra');
```

### [string]

```
module.exports = {
  //...
  externals: {
    subtract: ['./math', 'subtract'],
  },
};
```

`subtract: ['./math', 'subtract']` 转换为父子结构，其中 `./math` 是父模块，而 bundle 只引用 `subtract` 变量下的子集。该例子会编译成 `require('./math').subtract;`

### 对象

###### Warning

一个形如 `{ root, amd, commonjs, ... }` 的对象仅允许用于 [`libraryTarget: 'umd'`](https://webpack.docschina.org/configuration/output/#outputlibrarytarget) 这样的配置.它不被允许 用于其它的 library targets 配置值.

```
module.exports = {
  //...
  externals: {
    react: 'react',
  },

  // 或者

  externals: {
    lodash: {
      commonjs: 'lodash',
      amd: 'lodash',
      root: '_', // 指向全局变量
    },
  },

  // 或者

  externals: {
    subtract: {
      root: ['math', 'subtract'],
    },
  },
};
```

此语法用于描述外部 library 所有可用的访问方式。这里 `lodash` 这个外部 library 可以在 AMD 和 CommonJS 模块系统中通过 `lodash` 访问，但在全局变量形式下用 `_` 访问。`subtract` 可以通过全局 `math` 对象下的属性 `subtract` 访问（例如 `window['math']['subtract']`）。

### 函数

-   `function ({ context, request, contextInfo, getResolve }, callback)`
-   `function ({ context, request, contextInfo, getResolve }) => promise` 5.15.0+

对于 webpack 外部化，通过定义函数来控制行为，可能会很有帮助。例如，[webpack-node-externals](https://www.npmjs.com/package/webpack-node-externals) 能够排除 `node_modules` 目录中所有模块，还提供一些选项，比如白名单 package(whitelist package)。

函数接收两个入参：

-   `ctx` (`object`)：包含文件详情的对象。
    -   `ctx.context` (`string`): 包含引用的文件目录。
    -   `ctc.request` (`string`): 被请求引入的路径。
    -   `ctx.contextInfo` (`string`): 包含 issuer 的信息（如，layer）
    -   `ctx.getResolve` 5.15.0+: 获取当前解析器选项的解析函数。
-   `callback` (`function (err, result, type)`): 用于指明模块如何被外部化的回调函数

回调函数接收三个入参：

-   `err` (`Error`): 被用于表明在外部外引用的时候是否会产生错误。如果有错误，这将会是唯一被用到的参数。
-   `result` (`string` `[string]` `object`): 描述外部化的模块。可以接受形如 `${type} ${path}` 格式的字符串，或者其它标准化外部化模块格式，([`string`](https://webpack.docschina.org/configuration/externals/#string), [`[string]`](https://webpack.docschina.org/configuration/externals/#string-1)，或 [`object`](https://webpack.docschina.org/configuration/externals/#object))。
-   `type` (`string`): 可选的参数，用于指明模块的类型（如果它没在 `result` 参数中被指明）。

作为例子，要外部化所有匹配一个正则表达式的引入，你可以像下面那样处理：

**webpack.config.js**

```
module.exports = {
  //...
  externals: [
    function ({ context, request }, callback) {
      if (/^yourregex$/.test(request)) {
        // 使用 request 路径，将一个 commonjs 模块外部化
        return callback(null, 'commonjs ' + request);
      }

      // 继续下一步且不外部化引用
      callback();
    },
  ],
};
```

其它例子使用不同的模块格式：

**webpack.config.js**

```
module.exports = {
  externals: [
    function (ctx, callback) {
      // 该外部化的模块，是一个 `commonjs2` 的模块，且放在 `@scope/library` 目录中
      callback(null, '@scope/library', 'commonjs2');
    },
  ],
};
```

**webpack.config.js**

```
module.exports = {
  externals: [
    function (ctx, callback) {
      // 该外部化模块是一个全局变量叫作 `nameOfGlobal`.
      callback(null, 'nameOfGlobal');
    },
  ],
};
```

**webpack.config.js**

```
module.exports = {
  externals: [
    function (ctx, callback) {
      // 该外部化模块是一个在`@scope/library`模块里的命名导出（named export）。
      callback(null, ['@scope/library', 'namedexport'], 'commonjs');
    },
  ],
};
```

**webpack.config.js**

```
module.exports = {
  externals: [
    function (ctx, callback) {
      // 外部化模块是一个 UMD 模块
      callback(null, {
        root: 'componentsGlobal',
        commonjs: '@scope/components',
        commonjs2: '@scope/components',
        amd: 'components',
      });
    },
  ],
};
```

### RegExp

匹配给定正则表达式的每个依赖，都将从输出 bundle 中排除。

**webpack.config.js**

```
module.exports = {
  //...
  externals: /^(jquery|\$)$/i,
};
```

这个示例中，所有名为 `jQuery` 的依赖（忽略大小写），或者 `$`，都会被外部化。

### 混用语法

有时你需要混用上面介绍的语法。这可以像以下这样操作：

**webpack.config.js**

```
module.exports = {
  //...
  externals: [
    {
      // 字符串
      react: 'react',
      // 对象
      lodash: {
        commonjs: 'lodash',
        amd: 'lodash',
        root: '_', // indicates global variable
      },
      // 字符串数组
      subtract: ['./math', 'subtract'],
    },
    // 函数
    function ({ context, request }, callback) {
      if (/^yourregex$/.test(request)) {
        return callback(null, 'commonjs ' + request);
      }
      callback();
    },
    // 正则表达式
    /^(jquery|\$)$/i,
  ],
};
```

###### Warning

如果你指定的 `externals` 未使用类型，则会使用[默认类型](https://webpack.docschina.org/configuration/externals/#externalstype)。例如 `externals: { react: 'react' }` 会被替换成 `externals: { react: 'commonjs-module react' }`。

关于如何使用此 externals 配置的更多信息，请参考 [如何编写 library](https://webpack.docschina.org/guides/author-libraries)。

### byLayer

`function` `object`

按层指定 externals：

**webpack.config.js**

```
module.exports = {
  externals: {
    byLayer: {
      layer: {
        external1: 'var 43',
      },
    },
  },
};
```

### externalsType

`string = 'var'`

指定 externals 的默认类型。当 external 被设置为 `amd`，`umd`，`system` 以及 `jsonp` 时，[`output.libraryTarget`](https://webpack.docschina.org/configuration/output/#outputlibrarytarget) 的值也应相同。例如，你只能在 `amd` 库中使用 `amd` 的 externals。

支持的类型如下：

-   `'amd'`
-   `'amd-require'`
-   `'assign'`
-   `'commonjs'`
-   `'commonjs-module'`
-   `'global'`
-   `'import'` - uses `import()` to load a native EcmaScript module (async module)
-   `'jsonp'`
-   [`'module'`](https://webpack.docschina.org/configuration/externals/#externalstypemodule)
-   [`'node-commonjs'`](https://webpack.docschina.org/configuration/externals/#externalstypenode-commonjs)
-   `'promise'` - 与 `'var'` 相同，但是会 await 结果（适用于 async 模块）
-   `'self'`
-   `'system'`
-   [`'script'`](https://webpack.docschina.org/configuration/externals/#externalstypescript)
-   `'this'`
-   `'umd'`
-   `'umd2'`
-   `'var'`
-   `'window'`

**webpack.config.js**

```
module.exports = {
  //...
  externalsType: 'promise',
};
```

#### externalsType.module

Specify the default type of externals as `'module'`. Webpack will generate code like `import * as X from '...'` for externals used in a module.

Make sure to enable [`experiments.outputModule`](https://webpack.docschina.org/configuration/experiments/#experimentsoutputmodule) first, otherwise webpack will throw errors.

**webpack.config.js**

```
module.exports = {
  experiments: {
    outputModule: true,
  },
  externalsType: 'module',
};
```

#### externalsType.node-commonjs

Specify the default type of externals as `'node-commonjs'`. Webpack will import [`createRequire`](https://nodejs.org/api/module.html#module_module_createrequire_filename) from `'module'` to construct a require function for loading externals used in a module.

```
module.export = {
  experiments: {
    outputModule: true,
  },
  externalsType: 'node-commonjs',
};
```

#### externalsType.script

Specify the default type of externals as `'script'`. Webpack will Load the external as a script exposing predefined global variables with HTML `<script>` element. The `<script>` tag would be removed after the script has been loaded.

##### Syntax

```
module.exports = {
  externalsType: 'script',
  externals: {
    packageName: [
      'http://example.com/script.js',
      'global',
      'property',
      'property',
    ], // properties are optional
  },
};
```

如果你不打算定义任何熟悉，你可以使用简写形式：

```
module.exports = {
  externalsType: 'script',
  externals: {
    packageName: 'global@http://example.com/script.js', // no properties here
  },
};
```

请注意，[`output.publicPath`](https://webpack.docschina.org/configuration/output/#outputpublicpath) 不会被添加到提供的 URL 中。

##### 示例

从 CDN 加载 `lodash`：

**webpack.config.js**

```
module.exports = {
  // ...
  externalsType: 'script',
  externals: {
    lodash: ['https://cdn.jsdelivr.net/npm/lodash@4.17.19/lodash.min.js', '_'],
  },
};
```

然后，代码中使用方式如下：

```
import _ from 'lodash';
console.log(_.head([1, 2, 3]));
```

下面示例是针对上面示例新增了属性配置：

```
module.exports = {
  // ...
  externalsType: 'script',
  externals: {
    lodash: [
      'https://cdn.jsdelivr.net/npm/lodash@4.17.19/lodash.min.js',
      '_',
      'head',
    ],
  },
};
```

当你 `import 'loadsh'` 时，局部变量 `head` 和全局变量 `window._` 都会被暴露：

```
import head from 'lodash';
console.log(head([1, 2, 3])); // logs 1 here
console.log(window._.head(['a', 'b'])); // logs a here
```

###### Tip

当加载带有 HTML `<script>` 标签的代码时，webpack 的 runtime 将试图寻找一个已经存在的 `<script>` 标签，此标签需与 `src` 的属性相匹配，或者具有特定的 `data-webpack` 属性。对于 chunk 加载来说，`data-webpack` 属性的值为 `'[output.uniqueName]:chunk-[chunkId]'`，而 external 脚本的值为 `'[output.uniqueName]:[global]'`。

###### Tip

像 `output.chunkLoadTimeout`，`output.crossOriginLoading` 以及 `output.scriptType` 等选项也会对这种方式加载的 external 脚本产生影响。

### externalsPresets

`object`

为特定的 target 启用 externals 的 preset。

选项

描述

输入类型

`electron`

将 main 和预加载上下文中常见的 electron 内置模块视为 external 模块（如 `electron`，`ipc` 或 `shell`），使用时通过 `require()` 加载。

boolean

`electronMain`

将 main 上下文中的 electron 内置模块视为 external 模块（如 `app`，`ipc-main` 或 `shell`），使用时通过 `require()` 加载。

boolean

`electronPreload`

将预加载上下文的 electron 内置模块视为 external 模块（如 `web-frame`，`ipc-renderer` 或 `shell`），使用时通过 `require()` 加载。

boolean

`electronRenderer`

将 renderer 上下文的 electron 内置模块视为 external 模块（如 `web-frame`、`ipc-renderer` 或 `shell`），使用时通过 `require()` 加载。

boolean

`node`

将 node.js 的内置模块视为 external 模块（如 `fs`，`path` 或 `vm`），使用时通过 `require()` 加载。

boolean

`nwjs`

将 `NW.js` 遗留的 `nw.gui` 模块视为 external 模块，使用时通过 `require()` 加载。

boolean

`web`

将 `http(s)://...` 以及 `std:...` 视为 external 模块，使用时通过 `import` 加载。**（注意，这将改变执行顺序，因为 external 代码会在该块中的其他代码执行前被执行）**。

boolean

`webAsync`

将 'http(s)://...' 以及 'std:...' 的引用视为 external 模块，使用时通过 `async import()` 加载。**（注意，此 external 类型为 `async` 模块，它对执行会产生各种副作用）**。

boolean

Note that if you're going to output ES Modules with those node.js-related presets, webpack will set the default `externalsType` to [`node-commonjs`](https://webpack.docschina.org/configuration/externals/#externalstypenode-commonjs) which would use `createRequire` to construct a require function instead of using `require()`.

**Example**

使用 `node` 的 preset 不会构建内置模块，而会将其视为 external 模块，使用时通过 `require()` 加载。

**webpack.config.js**

```
module.exports = {
  // ...
  externalsPresets: {
    node: true,
  },
};
```

## 性能
- performance.assetFilter
- performance.hints
- maxAssetSize
- maxEntrypointSize

## Node环境
这些选项可以配置是否 polyfill 或 mock 某些 [Node.js 全局变量](https://nodejs.org/docs/latest/api/globals.html)。
此功能由 webpack 内部的 [`NodeStuffPlugin`](https://github.com/webpack/webpack/blob/master/lib/NodeStuffPlugin.js) 插件提供。
- node.global
- `node.__filename`
- `node.__dirname`

## Stats对象
`stats` 选项让你更精确地控制 bundle 信息该怎么显示。 如果你不希望使用 `quiet` 或 `noInfo` 这样的不显示信息，而是又不想得到全部的信息，只是想要获取某部分 bundle 的信息，使用 stats 选项是比较好的折衷方式。

https://webpack.docschina.org/configuration/stats/

## 