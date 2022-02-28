# 贡献流程

React 是 Facebook 首批开源项目中的一员，开发状态保持活跃，并在 [facebook.com](https://www.facebook.com/) 上为我们提供了源代码。现在，我们正不断解决若干个问题以使参与贡献 React 尽可能容易和公开透明，虽然目前还差得很远。我们希望本篇文章能够解释清楚贡献的流程，回答你可能会有的一些问题。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#code-of-conduct)[行为规范](https://github.com/facebook/react/blob/main/CODE_OF_CONDUCT.md)

Facebook 将[参与者公约](https://www.contributor-covenant.org/zh-cn/version/1/4/code-of-conduct)作为行为规范，我们希望参与项目的各位严格遵守。请阅读[全文](https://github.com/facebook/react/blob/main/CODE_OF_CONDUCT.md)去了解什么行为允许，什么行为不允许。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#open-development)人人皆可开发

React 的一切工作在 [GitHub](https://github.com/facebook/react) 上完成，核心团队及其以外的贡献者发送 pull requests，其代码评审流程皆为一致。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#semantic-versioning)语义化版本

React 遵循[语义化版本](https://semver.org/lang/zh-CN/)。我们对重要的漏洞修复发布修订号；对新特性或不重要的变更发布次版本号；对重大且不兼容的变更发布主版本号。我们在开发重大且不兼容的变更时，还会在次版本号用 deprecation warnings 让用户得知将来的变更并提前迁移代码。请查看[版本号规则](https://zh-hans.reactjs.org/docs/faq-versioning.html)来了解更多我们在稳定性和渐进迁移方面要做哪些事情。

每一个重要变更参见 [changelog file](https://github.com/facebook/react/blob/main/CHANGELOG.md)。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#branch-organization)分支管理

请直接提交你的变更至 [`main branch`](https://github.com/facebook/react/tree/main)。对于开发或即将推出的版本，我们不会另建分支。我们尽力保持 `main` 不出问题，并通过所有测试。

合并进入 `main` 的代码必须与最新稳定版本兼容，可以有额外特性，但不能有重大变更。我们应从 `main` 随时能发布新的次版本号。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#feature-flags)特性切换（Feature Flags）

我们为了使 `main` 能够发布，要求重大且不兼容的变更和实验性的特性必须用特性切换。

[`packages/shared/ReactFeatureFlags.js`](https://github.com/facebook/react/blob/main/packages/shared/ReactFeatureFlags.js) 中定义了特性切换。React 的一些版本可能启用了不同的特性切换；比如，React Native 可能与 React DOM 有不同的配置。这些特性切换见于 [`packages/shared/forks`](https://github.com/facebook/react/tree/main/packages/shared/forks)。特性切换使用了静态类型检查器 Flow，因此你可以运行 `yarn flow` 来确认所有必要文件已更新。

React 的构建系统（Build System）会先删去禁用的特性分支，之后再发布。每次 commit 都会运行持续集成（Continuous Integration）来检查包（Bundle）大小的变化。包大小的变化可以用来表明某特性正确合并。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#bugs)漏洞

#### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#where-to-find-known-issues)何处查找已知 issue

我们用 [GitHub Issues](https://github.com/facebook/react/issues) 来公开漏洞。我们密切关注该版块，内部解决 bug 时也会想办法说明清楚。在你提交 issue 前，请确定没有重复 issue 出现。

#### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#reporting-new-issues)报告新的 issue

修复 bug 的最佳方法是给出缩略版的测试用例。这个 [JSFiddle 模板](https://jsfiddle.net/Luktwrdm/)是个不错的起点。

#### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#security-bugs)安全漏洞

为了发现安全漏洞，Facebook 实行了漏洞[举报奖励制度](https://www.facebook.com/whitehat/)（Bounty Program）。为此，请不要将这类漏洞提交到 public issues，而要遵循举报奖励制度页面所描述的流程。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#how-to-get-in-touch)如何联系我们

-   因特网中继聊天（IRC）： [#reactjs on freenode](https://webchat.freenode.net/?channels=reactjs)
-   [论坛](https://zh-hans.reactjs.org/community/support.html#popular-discussion-forums)

你如果需要有关 React 的帮助，还可以前往 Discord 聊天平台，这里建有 React 用户们的[活跃社区](https://www.reactiflux.com/)。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#proposing-a-change)提出变更

如果你想改变公开的 API，或者对实现有不小的变更，我们建议你[发起 issue](https://github.com/facebook/react/issues/new)，这会让我们先对你的提议达成一致，然后你再着手工作。

如果只是修复漏洞，你当然可以立即提交 pull request，不过我们还是建议先去提出 issue 来说明你修复了什么，这就对一种情况来说就很方便：我们没有接受特定 bug 的修复但想跟进该 issue 的情况。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#your-first-pull-request)首个 Pull Request

在写第一个 Pull Request？你可以从这一系列视频中学习怎么做：

**[How to Contribute to an Open Source Project on GitHub](https://app.egghead.io/courses/how-to-contribute-to-an-open-source-project-on-github)**

为了使你能够快速上手和熟悉贡献流程，我们这里有个列表 **[good first issues](https://github.com/facebook/react/issues?q=is:open+is:issue+label:%22good+first+issue%22)**，里面有相对没那么笼统的漏洞，从这开始是个不错的选择。

如果你想解决一个 issue，请确定检查了该 issue 下的评论以防有人正在处理它。如果目前没人在处理该 issue，那么请留下评论去表明你想处理该 issue 以便其他人不会意外重复你的工作。

如果有人留言表明要处理该 issue 但是超过两周没有跟进，你可以接手工作，不过也应该留言说明。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#sending-a-pull-request)提交 Pull Request

核心团队时刻关注 pull requests，我们会先评审你的 pull request，之后可能会合并，可能会要求再次更改，也可能会关闭该 pull request 并对此作出解释。对于 API 上的变更，我们可能得确认在 Facebook.com 上的内部 API 使用方法，因此该变更可能会推迟。我们会尽力全程更新和反馈。

**提交 pull request 前**，请确保完成以下步骤：

1.  Fork [此仓库](https://github.com/facebook/react)，从 `main` 创建分支。
2.  在仓库根目录下执行 `yarn`。
3.  如果你修复了 bug 或者添加了代码，而这些内容需要测试，请添加测试！
4.  确保通过测试套件（`yarn test`）。提示：开发环境下，`yarn test --watch TestName` 很有用。
5.  生产环境下，执行 `yarn test --prod` 来进行测试。
6.  如果需要调试，请执行 `yarn debug-test --watch TestName`，打开 `chrome://inspect`， 之后再打开 “审查”。
7.  使用 [prettier](https://github.com/prettier/prettier)（`yarn prettier`）来格式化代码。
8.  确保 lint 校验代码（`yarn lint`）。提示：执行 `yarn linc` 去只检查更改过的文件。
9.  运行 [Flow](https://flowtype.org/) 来类型检查（`yarn flow`）。
10.  请签订贡献者许可证协议（Contributor License Agreement）。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#contributor-license-agreement-cla)贡献者许可证协议

为了让你的 pull request 得到接受，你得提交贡献者许可证协议。你只需提交该协议一次，所以如果你曾经对另一个 Facebook 开源项目提交过，那么你已经准备好了。如果你是第一次提交 pull request，请让我们得知你已提交协议，这样我们能多方核对你的 GitHub 用户名。

**[请在这里签订贡献者许可证协议](https://code.facebook.com/cla)**

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#contribution-prerequisites)必要条件

-   [Node](https://nodejs.org/) v8.0.0+、[Yarn](https://yarnpkg.com/en/) v1.2.0+。
-   已安装 [JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
-   你已安装 `gcc`（或者你在有必要安装编译器的情况下也不觉得麻烦），因为一些依赖可能得经过编译，而在 OS X，Xcode 命令行工具会帮你处理；在 Ubuntu，`apt-get install build-essential` 会安装所需的 package（其它 Linux 发行版的类似命令也有效）；在 Windows 上得做些额外步骤，请参考 [`node-gyp` 安装步骤](https://github.com/nodejs/node-gyp#installation)。
-   熟悉 Git。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#development-workflow)开发工作流程

克隆 React 项目后，执行 `yarn` 来获取依赖。 之后，你可以执行以下命令：

-   `yarn lint` 检查代码风格。
-   `yarn linc` 和 `yarn lint` 差不多，但是运行地更快，因为只检查了分支中的不同文件。
-   `yarn test` 运行完整的测试套装。
-   `yarn test --watch` 运行交互式的测试监听器。
-   `yarn test --prod` 在生产环境下运行测试。
-   `yarn test <pattern>` 匹配文件名，运行响应测试。
-   `yarn debug-test` 和 `yarn test` 差不多，不过多了个调试器，你可以打开 `chrome://inspect` 并审查。
-   `yarn flow` 运行 [Flow](https://flowtype.org/) 进行类型检查。
-   `yarn build` 新建涉及所有包的 `build` 文件夹。
-   `yarn build react/index,react-dom/index --type=UMD` 生成只有 React 和 ReactDOM 的 UMD 版本。

我们建议运行 `yarn test`（或上述命令）以确保你的代码没有引入回归，不管怎样，这有助于尝试你的 React 构建版本。

首先，运行 `yarn build`，这会于 `build` 文件夹中生成预先构建的 bundle，还会于 `build/packages` 中生成 npm 包。

想测试你做出的更改的话，最简单的方法就是运行 `yarn build react/index,react-dom/index --type=UMD`，之后再打开 `fixtures/packaging/babel-standalone/dev.html`，该文件已使用 `build` 文件夹内的 `react.development.js` 来搞定你的更改。

如果你想测试你对已有 React 项目做出的更改，你可以复制 `build/node_modules/react/umd/react.development.js` 和 `build/node_modules/react-dom/umd/react-dom.development.js` 或其它构建版本，放入你的应用中并使用这些构建版本而非稳定版。

如果你的项目用 npm，你可以从依赖中删去 `react` 和 `react-dom`，使用 `yarn link` 将其指向本地文件夹的 `build` 目录。请注意，**当请在构建时，传递 `--type=NODE`，而不是 `--type=UMD`。同时，你还需要构建 `scheduler` 的 package：

```
cd ~/path_to_your_react_clone/
yarn build react/index,react/jsx,react-dom/index,scheduler --type=NODE

cd build/node_modules/react
yarn link
cd build/node_modules/react-dom
yarn link

cd ~/path/to/your/project
yarn link react react-dom
```

每当你在项目文件夹下运行 `yarn build`，更新版本会出现在 `node_modules` 文件夹，之后可以重新构建项目来测试更改。

如果依然缺少某些 package（例如，可能在项目中使用到 `react-dom/server`），则应始终执行 `yarn build` 进行完整构建。请注意，不带选项运行 `yarn build` 会耗费很长时间。

我们仍要求：`pull request` 得包括新功能对应的单元测试。这样，我们能确保以后你的代码不出问题。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#style-guide)风格指南

我们使用自动化代码格式化软件 [Prettier](https://prettier.io/)。 对代码做出更改后，运行 `yarn prettier`。

之后，linter 会捕获代码中可能出现的多数问题。 你可以运行 `yarn linc` 来检查代码风格状态。

不过，linter 也有不能搞定的一些风格。如果有些东西不确定，请查看 [Airbnb’s Style Guide](https://github.com/airbnb/javascript) 来指导自己。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#request-for-comments-rfc)请求意见稿（RFC）

许多更改（包括修复 bug 和完善文档）会经过通常所用的 GitHub pull request 工作流程来评审。

不过有些更改较大，对此，我们要求这些更改得经过一番设计流程， 并在核心团队中达成共识。

请求意见稿让新特性的合并入库经过一致且受控的流程。你可以前往 [rfcs repository](https://github.com/reactjs/rfcs) 去贡献。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#license)License

你贡献 React 的同时也就同意了你的贡献部分使用了 MIT 协议。

### [](https://zh-hans.reactjs.org/docs/how-to-contribute.html#what-next)接下来做什么？

请阅读[下一部分](https://zh-hans.reactjs.org/docs/codebase-overview.html)来学习代码库是如何组织的.



# 源码概览

本节将对 React 的源码架构，约定及其实现进行概述。

如果您想[参与 React](https://zh-hans.reactjs.org/docs/how-to-contribute.html) 的开发，我们希望这份指南可以帮助你更加轻松地进行修改。

我们并不推荐在 React 应用中遵循这些约定。有许多约定是历史原因，并且之后也许会有所修改。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#top-level-folders)项目根目录

当克隆 [React 仓库](https://github.com/facebook/react)之后，你们将看到项目根目录的信息：

-   [`packages`](https://github.com/facebook/react/tree/main/packages) 包含元数据（比如 `package.json`）和 React 仓库中所有 package 的源码（子目录 `src`）。**如果你需要修改源代码, 那么每个包的 `src` 子目录是你最需要花费精力的地方。**
-   [`fixtures`](https://github.com/facebook/react/tree/main/fixtures) 包含一些给贡献者准备的小型 React 测试项目。
-   `build` 是 React 的输出目录。源码仓库中并没有这个目录，但是它会在你克隆 React 并且第一次[构建它](https://zh-hans.reactjs.org/docs/how-to-contribute.html#development-workflow)之后出现。

文档位于 [React 仓库之外的一个独立仓库中](https://github.com/reactjs/reactjs.org)。

还有一些其他的顶层目录，但是它们几乎都是工具类的，并且在贡献代码时基本不会涉及。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#colocated-tests)共置测试

我们没有单元测试的顶层目录。而是将它们放置在所需测试文件的相同目录下的 `__tests__` 的目录之中。

比如，一个用于 [`setInnerHTML.js`](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/client/utils/setInnerHTML.js) 的测试文件，会存放在 [`__tests__/setInnerHTML-test.js`](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/client/utils/__tests__/setInnerHTML-test.js)，就在它同级目录下。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#warnings-and-invariants)warning 和 invariant

React 代码库直接使用 `console.error` 来展示 warnings：

```
if (__DEV__) {
  console.error('Something is wrong.');
}
```

warning 仅在开发环境中启用。在生产环境中，他们会被完全剔除掉。如果你需要在生产环境禁止执行某些代码，请使用 `invariant` 模块代替 `warning`：

```
var invariant = require('invariant');

invariant(
  2 + 2 === 4,
  'You shall not pass!'
);
```

**当 `invariant` 判别条件为 `false` 时，会将 invariant 的信息作为错误抛出**

“Invariant” 用于声明 “这个条件应总为 true”。你可以把它当成一种断言。

保持开发和生产环境的行为相似是十分重要的，因此 `invariant` 在开发和生产环境下都会抛出错误。不同点在于在生产环境中这些错误信息会被自动替换成错误代码，这样可以让输出库文件变得更小。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#development-and-production)开发环境与生产环境

你可以在代码库中使用 `__DEV__` 这个伪全局变量，用于管理开发环境中需运行的代码块

这在编译阶段会被内联，在 CommonJS 构建中，转化成 `process.env.NODE_ENV !== 'production'` 这样的判断。

对于独立构建来说，在没有 minify 的构建中，它会变成 `true`，同时在 minify 的构建中，检测到的 `if` 代码块会被完全剔除。

```
if (__DEV__) {
  // 仅在开发环境下执行的代码
}
```

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#flow)Flow

我们最近将 [Flow](https://flow.org/) 引入源码，用于类型检查。在许可证头部的注释中，标记为 `@flow` 注释的文件是已经经过类型检查的。

我们接受[添加 Flow 注释到现有代码](https://github.com/facebook/react/pull/7600/files)。Flow 注释看上去像这样：

```
ReactRef.detachRefs = function(
  instance: ReactInstance,
  element: ReactElement | string | number | null | false,
): void {
  // ...
}
```

如果可以的话，新代码应尽量使用 Flow 注释。 你可以运行 `yarn flow`，用 Flow 本地检查你的代码。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#multiple-packages)Multiple Packages

React 采用 [monorepo](https://danluu.com/monorepo/) 的管理方式。仓库中包含多个独立的包，以便于更改可以一起联调，并且问题只会出现在同一地方。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#react-core)React Core

React “Core” 中包含所有[全局 `React` API](https://zh-hans.reactjs.org/docs/react-api.html#react)，比如：

-   `React.createElement()`
-   `React.Component`
-   `React.Children`

**React 核心只包含定义组件必要的 API**。它不包含[协调](https://zh-hans.reactjs.org/docs/reconciliation.html)算法或者其他平台特定的代码。它同时适用于 React DOM 和 React Native 组件。

React 核心代码在源码的 [`packages/react`](https://github.com/facebook/react/tree/main/packages/react) 目录中。在 npm 上发布为 [`react`](https://www.npmjs.com/package/react) 包。相应的独立浏览器构建版本称为 `react.js`，它会导出一个称为 `React` 的全局对象。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#renderers)渲染器

React 最初只是服务于 DOM，但是这之后被改编成也能同时支持原生平台的 [React Native](https://reactnative.dev/)。因此，在 React 内部机制中引入了“渲染器”这个概念。

**渲染器用于管理一棵 React 树，使其根据底层平台进行不同的调用。**

渲染器同样位于 [`packages/`](https://github.com/facebook/react/tree/main/packages/) 目录下：

-   [React DOM Renderer](https://github.com/facebook/react/tree/main/packages/react-dom) 将 React 组件渲染成 DOM。它实现了全局 [`ReactDOM`API](https://zh-hans.reactjs.org/docs/react-dom.html)，这在npm上作为 [`react-dom`](https://www.npmjs.com/package/react-dom) 包。这也可以作为单独浏览器版本使用，称为 `react-dom.js`，导出一个 `ReactDOM` 的全局对象.
-   [React Native Renderer](https://github.com/facebook/react/tree/main/packages/react-native-renderer) 将 React 组件渲染为 Native 视图。此渲染器在 React Native 内部使用。
-   [React Test Renderer](https://github.com/facebook/react/tree/main/packages/react-test-renderer) 将 React 组件渲染为 JSON 树。这用于 [Jest](https://facebook.github.io/jest) 的[快照测试](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html)特性。在 npm 上作为 [react-test-renderer](https://www.npmjs.com/package/react-test-renderer) 包发布。

另外一个官方支持的渲染器的是 [`react-art`](https://github.com/facebook/react/tree/main/packages/react-art)。它曾经是一个独立的 [GitHub 仓库](https://github.com/reactjs/react-art)，但是现在我们将此加入了主源代码树。

> **注意:**
> 
> 严格说来，[`react-native-renderer`](https://github.com/facebook/react/tree/main/packages/react-native-renderer) 实现了 React 和 React Native 的连接。真正渲染 Native 视图的平台特定代码及组件都存储在 [React Native 仓库](https://github.com/facebook/react-native)中。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#reconcilers)reconcilers

即便 React DOM 和 React Native 渲染器的区别很大，但也需要共享一些逻辑。特别是[协调](https://zh-hans.reactjs.org/docs/reconciliation.html)算法需要尽可能相似，这样可以让声明式渲染，自定义组件，state，生命周期方法和 refs 等特性，保持跨平台工作一致。

为了解决这个问题，不同的渲染器彼此共享一些代码。我们称 React 的这一部分为 “reconciler”。当处理类似于 `setState()` 这样的更新时，reconciler 会调用树中组件上的 `render()`，然后决定是否进行挂载，更新或是卸载操作。

Reconciler 没有单独的包，因为他们暂时没有公共 API。相反，它们被如 React DOM 和 React Native 的渲染器排除在外。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#stack-reconciler)Stack reconciler

“stack” reconciler 是 React 15 及更早的解决方案。虽然我们已经停止了对它的使用, 但是这在[下一章节](https://zh-hans.reactjs.org/docs/implementation-notes.html)有详细的文档。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#fiber-reconciler)Fiber reconciler

“fiber” reconciler 是一个新尝试，致力于解决 stack reconciler 中固有的问题，同时解决一些历史遗留问题。Fiber 从 React 16 开始变成了默认的 reconciler。

它的主要目标是：

-   能够把可中断的任务切片处理。
-   能够调整优先级，重置并复用任务。
-   能够在父元素与子元素之间交错处理，以支持 React 中的布局。
-   能够在 `render()` 中返回多个元素。
-   更好地支持错误边界。

你可以在[这里](https://github.com/acdlite/react-fiber-architecture)和[这里](https://medium.com/react-in-depth/inside-fiber-in-depth-overview-of-the-new-reconciliation-algorithm-in-react-e1c04700ef6e)，深入了解 React Fiber 架构。虽然这已经在 React 16 中启用了，但是 async 特性还没有默认开启。

源代码在 [`packages/react-reconciler`](https://github.com/facebook/react/tree/main/packages/react-reconciler) 目录下。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#event-system)事件系统

React 在原生事件基础上进行了封装，以抹平浏览器间差异。其源码在 [`packages/react-dom/src/events`](https://github.com/facebook/react/tree/main/packages/react-dom/src/events) 目录下。

### [](https://zh-hans.reactjs.org/docs/codebase-overview.html#what-next)下一章节学习什么？

查看下一章节去学习 reconciler 在 pre-React 16 中的实现。我们还没有为新的 reconciler 内部原理编写文档。



# 实现说明

这一部分是关于 [stack reconciler](https://zh-hans.reactjs.org/docs/codebase-overview.html#stack-reconciler) 的一些实现说明。

这部分比较具有技术性，需要对 React 公共 API，以及 React 是如何将其分为 core、renderer 和 reconciler 的具有较好的理解。如果你对源码库还不是很熟悉，请先阅读[源码总览](https://zh-hans.reactjs.org/docs/codebase-overview.html)。

这部分还要求了解 [React 组件及其实例和元素之间的不同](https://zh-hans.reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html)。

stack reconciler 是在 React 15 以及更早的版本中被采用。它的源码位于 [src/renderers/shared/stack/reconciler](https://github.com/facebook/react/tree/15-stable/src/renderers/shared/stack/reconciler)。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#video-building-react-from-scratch)视频：从零开始构建 React

[Paul O’Shannessy](https://twitter.com/zpao) 讲解的[从零开始构建 React](https://www.youtube.com/watch?v=_MAD4Oly9yg) 对本文档有较大的启发。

本文档和他的讲解都是对实际代码库的简化，所以你能通过熟悉它们来获得更好的理解。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#overview)概览

reconciler 本身没有公共的 API。像 React DOM 和 React Native 这样的 [renderer](https://zh-hans.reactjs.org/docs/codebase-overview.html#renderers) 使用它来根据用户写的 React 组件来高效地更新用户界面。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#mounting-as-a-recursive-process)挂载是递归过程

让我们考虑第一次挂载组件时：

```
ReactDOM.render(<App />, rootEl);
```

React DOM 把 `<App />` 传递给 reconciler。请记住，`<App />` 是一个 React 元素，也就是对要渲染的_内容_的描述。可以把它视为普通的对象：

```
console.log(<App />);
// { type: App, props: {} }
```

reconciler 检查 `App` 是一个类还是一个函数。

如果 `App` 是函数，那么 reconciler 会调用 `App(props)` 来获取渲染的元素。

如果 `App` 是类，那么 reconciler 会通过 `new App(props)` 来实例化 `App`，并调用生命周期方法 `componentWillMount()`，之后调用 `render()` 方法来获取渲染的元素。

无论哪种方式，reconciler 都会探悉 `App` 的内容并渲染。

这个过程是递归的。`App` 可能会渲染某个 `<Greeting />`，`Greeting` 可能会渲染某个 `<Button />`，以此类推。当它探悉各个组件渲染的元素时，reconciler 会通过用户定义的组件递归地 “向下探索”。

通过以下伪代码想象一下这个过程：

```
function isClass(type) {
  // 类组件会有这个标识位
  return (
    Boolean(type.prototype) &&
    Boolean(type.prototype.isReactComponent)
  );
}

// 这个函数接受一个 React 元素 (例如： <App />)
// 并返回表示已挂载树的 DOM 或者 原生节点
function mount(element) {
  var type = element.type;
  var props = element.props;

  // 将通过 type 作为函数运行
  // 或创建实例并调用 render() 
  // 返回渲染后的元素
  var renderedElement;
  if (isClass(type)) {
    // 类组件
    var publicInstance = new type(props);
    // 设置 props
    publicInstance.props = props;
    // 如果有生命周期方法就调用
    if (publicInstance.componentWillMount) {
      publicInstance.componentWillMount();
    }
    // 调用 render() 返回渲染后的元素
    renderedElement = publicInstance.render();
  } else {
    // 函数组件
    renderedElement = type(props);
  }

  // 这个过程是递归的
  // 因为组件可能会返回具体另一个组件类型的元素
  return mount(renderedElement);

  // 提示：这个实现是不完整的并且无限递归！
  // 只处理像 <App /> 或者 <Button /> 的元素
  // 还不能处理像 <div /> 或者 <p /> 的元素
}

var rootEl = document.getElementById('root');
var node = mount(<App />);
rootEl.appendChild(node);
```

> **注意：**
> 
> 这其实_是_一份伪代码。它与真实的实现并不相似。因为我们还没有讨论该递归过程何时停止，所以它也会造成堆栈溢出。

让我们回顾上面例子中的一些关键的想法：

-   React 元素是用来表示组件的类型（例如：`App`）和 props 的简单的对象。
-   用户定义的组件（例如：`App`）可以是类，也可以是函数，但是它们都“渲染产生”元素。
-   “挂载”是一个递归的过程，根据特定的顶层 React 元素（e.g. `<App />`）产生 DOM 或 Native 树。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#mounting-host-elements)挂载宿主元素

如果我们没有渲染某些东西输出到电脑屏幕，这个过程将会是无用的。

除了用户定义的（“组合”）组件，React 元素也可能表示为平台专属（“宿主”）组件。例如，`Button` 可能会从 render 方法返回一个 `<div />`。

如果元素的 `type` 属性是字符串，我们处理的就是宿主元素：

```
console.log(<div />);
// { type: 'div', props: {} }
```

宿主元素中没有用户定义代码。

当 reconciler 遇到宿主元素时，它会让 renderer 负责挂载它。例如，React DOM 会创建一个 DOM 节点。

如果宿主元素拥有子元素，reconciler 会根据上文提到的算法对其进行递归地挂载。无论子元素是宿主（像 `<div><hr /><div>`），还是组合（像 `<div><Button /></div>`），两者都无所谓。

子组件生成的 DOM 节点会附加在父 DOM 节点上，递归地完成整个 DOM 结构的组装。

> **注意：**
> 
> reconciler 本身不与 DOM 绑定。挂载的确切结果（在源代码中有时叫做 “挂载映像”）取决于 renderer，可以是一个 DOM 节点（React DOM），一个字符串（React DOM Server），或是一个表示原生视图的数字（React Native）。

如果我们扩展代码去处理宿主元素，会是如下样子：

```
function isClass(type) {
  // 类组件会有这个标识位
  return (
    Boolean(type.prototype) &&
    Boolean(type.prototype.isReactComponent)
  );
}

// 此函数仅处理组合类型的元素
// 例如，处理 <App /> 和 <Button />, 但不处理 <div />
function mountComposite(element) {
  var type = element.type;
  var props = element.props;

  var renderedElement;
  if (isClass(type)) {
    // 类组件
    var publicInstance = new type(props);
    // 设置 props
    publicInstance.props = props;
    // 如果有生命周期方法就调用
    if (publicInstance.componentWillMount) {
      publicInstance.componentWillMount();
    }
    renderedElement = publicInstance.render();
  } else if (typeof type === 'function') {
    // 函数组件
    renderedElement = type(props);
  }

  // 这是递归的，但是当元素是宿主(例如： <div />)而不是组合(例如 <App />)时，
  // 我们最终会到达递归的底部：
  return mount(renderedElement);
}

// 此函数只处理宿主类型的元素
// 例如： 处理 <div /> 和 <p />，但不处理 <App />.
function mountHost(element) {
  var type = element.type;
  var props = element.props;
  var children = props.children || [];
  if (!Array.isArray(children)) {
    children = [children];
  }
  children = children.filter(Boolean);

  // 这段代码不应该出现在 reconciler。
  // 不同的 renderer 可能会以不同方式初始化节点。
  // 例如，React Native 会创建 iOS 或 Android 的视图。
  var node = document.createElement(type);
  Object.keys(props).forEach(propName => {
    if (propName !== 'children') {
      node.setAttribute(propName, props[propName]);
    }
  });

  // 挂载子元素
  children.forEach(childElement => {
    // 子元素可能是宿主(例如：<div />)或者组合 (例如：<Button />).
    // 我们还是递归挂载他们
    var childNode = mount(childElement);

    // 这一行代码也是特殊的 renderer。
    // 根据 renderer 不同，方式也不同：
    node.appendChild(childNode);
  });

  // DOM 节点作为挂载的结果返回。
  // 这是递归结束的位置。
  return node;
}

function mount(element) {
  var type = element.type;
  if (typeof type === 'function') {
    // 用户定义组件
    return mountComposite(element);
  } else if (typeof type === 'string') {
    // 平台特定组件
    return mountHost(element);
  }
}

var rootEl = document.getElementById('root');
var node = mount(<App />);
rootEl.appendChild(node);
```

以上代码是可以运作的，但是与 reconciler 的实际实现依然相差很远。关键的缺失部分是对更新的支持。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#introducing-internal-instances)引入内部实例

React 的关键特点是你可以重新渲染所有内容，并且不会重新生成 DOM 或重置 state：

```
ReactDOM.render(<App />, rootEl);
// 应该重用已经存在的 DOM：
ReactDOM.render(<App />, rootEl);
```

然而，之前的实现只是知道如何挂载最初的树。由于它没有储存所有的必要信息，例如所有的 `publicInstance`，或 DOM 节点属于哪个组件，所以它不能完成更新操作。

stack reconciler 源码通过把 `mount()` 函数作为一个类的方法来解决这个问题。这种方法是存在缺点的，所以我们正朝着与之相对的方向[进行 reconciler 的重写工作](https://zh-hans.reactjs.org/docs/codebase-overview.html#fiber-reconciler)。不过这就是它现在的运作方式。

我们会创建两个类：`DOMComponent` 和 `CompositeComponent`，而不是分离的两个函数 `mountHost` 和 `mountComposite`。

两个类都有一个接受 `element` 的构造函数，同时也有一个返回挂载后节点的 `mount()` 方法。我们用一个可以实例化正确类的工厂函数替换了顶层的 `mount()` 函数：

```
function instantiateComponent(element) {
  var type = element.type;
  if (typeof type === 'function') {
    // 用户定义组件
    return new CompositeComponent(element);
  } else if (typeof type === 'string') {
    // 平台特定组件
    return new DOMComponent(element);
  }  
}
```

首先，让我们思考一下 `CompositeComponent` 的实现：

```
class CompositeComponent {
  constructor(element) {
    this.currentElement = element;
    this.renderedComponent = null;
    this.publicInstance = null;
  }

  getPublicInstance() {
    // 对于组合组件，公共类实例
    return this.publicInstance;
  }

  mount() {
    var element = this.currentElement;
    var type = element.type;
    var props = element.props;

    var publicInstance;
    var renderedElement;
    if (isClass(type)) {
      // 组件类
      publicInstance = new type(props);
      // 设置 props
      publicInstance.props = props;
      // 如果有生命周期方法就调用
      if (publicInstance.componentWillMount) {
        publicInstance.componentWillMount();
      }
      renderedElement = publicInstance.render();
    } else if (typeof type === 'function') {
      // 函数组件
      publicInstance = null;
      renderedElement = type(props);
    }

    // 保存公共实例
    this.publicInstance = publicInstance;

    // 根据元素实例化子内部实例。
    // <div /> 或者 <p /> 是 DOMComponent，
    // 而 <App /> 或者 <Button /> 是 CompositeComponent。
    var renderedComponent = instantiateComponent(renderedElement);
    this.renderedComponent = renderedComponent;

    // 挂载渲染后的输出
    return renderedComponent.mount();
  }
}
```

这与之前的 `mountComposite()` 的实现没有太多的不同，但是现在我们可以保存一些信息，如 `this.currentElement`，`this.renderedComponent` 和 `this.publicInstance`，用于更新期间使用。

需要注意的是 `CompositeComponent` 的实例与用户提供的 `element.type` 的实例是不同的东西。`CompositeComponent` 是我们的 reconciler 的实现细节，并且永远不会暴露给用户。用户定义的类是从 `element.type` 读取的，并且 `CompositeComponent` 会创建一个它的实例。

为了避免混淆，我们把 `CompositeComponent` 和 `DOMComponent` 的实例叫做“内部实例”。由于它们的存在，我们可以把一些长时间存在的数据存入其中。只有 renderer 和 reconciler 能意识到它们的存在。

相反，我们把用户定义的类的实例叫做“公共实例”。公共实例就是你在 `render()` 中所见到的 `this` 和你的自定义组件中的一些其他方法。

`mountHost()` 函数，重构为 `DOMComponent` 类的 `mount()` 方法，看起来也很熟悉：

```
class DOMComponent {
  constructor(element) {
    this.currentElement = element;
    this.renderedChildren = [];
    this.node = null;
  }

  getPublicInstance() {
    // 对于 DOM 组件，只公共 DOM 节点
    return this.node;
  }

  mount() {
    var element = this.currentElement;
    var type = element.type;
    var props = element.props;
    var children = props.children || [];
    if (!Array.isArray(children)) {
      children = [children];
    }

    // 创建并保存节点
    var node = document.createElement(type);
    this.node = node;

    // 设置属性
    Object.keys(props).forEach(propName => {
      if (propName !== 'children') {
        node.setAttribute(propName, props[propName]);
      }
    });

    // 创建并保存包含的子项
    // 他们每个都可以是 DOMComponent 或者是 CompositeComponent，
    // 取决于类型是字符串还是函数
    var renderedChildren = children.map(instantiateComponent);
    this.renderedChildren = renderedChildren;

    // 收集他们在 mount 上返回的节点
    var childNodes = renderedChildren.map(child => child.mount());
    childNodes.forEach(childNode => node.appendChild(childNode));

    // DOM 节点作为挂载结果返回
    return node;
  }
}
```

`mountHost()` 重构后主要的区别是我们保存了与内部 DOM 组件实例关联的 `this.node` 和 `this.renderedChildren`。在将来我们还使用他们来进行非破坏性更新。

因此，每个内部实例，组合或者宿主，现在都指向了它的子内部实例。为帮你更直观的了解，假设有函数组件 `<App>` 会渲染类组件 `<Button>`，并且 `Button` 渲染一个 `<div>`，其内部实例树将如下所示:

```
[object CompositeComponent] {
  currentElement: <App />,
  publicInstance: null,
  renderedComponent: [object CompositeComponent] {
    currentElement: <Button />,
    publicInstance: [object Button],
    renderedComponent: [object DOMComponent] {
      currentElement: <div />,
      node: [object HTMLDivElement],
      renderedChildren: []
    }
  }
}
```

在 DOM 中, 你只能看到 `<div>`。但是在内部实例树包含了组合和宿主的内部实例。

组合内部实例需要存储：

-   当前元素。
-   如果元素的类型是类的公共实例
-   单次渲染后的内部实例。它可以是 `DOMComponent` 或 `CompositeComponent`。

宿主内部实例需要存储：

-   当前元素。
-   DOM 节点.
-   所有子内部实例。它们中的每一个都可以是 `DOMComponent` 或`CompositeComponent`。

如果你难以想象内部实例树在较为复杂的应用程序中的结构，[React DevTools](https://github.com/facebook/react-devtools) 可以给你一个相似的结果，因为它突出呈现了灰色的宿主实例，以及紫色的组合实例。

[![React DevTools tree](https://zh-hans.reactjs.org/static/d96fec10d250eace9756f09543bf5d58/1e088/implementation-notes-tree.png)](https://zh-hans.reactjs.org/static/d96fec10d250eace9756f09543bf5d58/00d43/implementation-notes-tree.png)

为了完成重构，我们将引入一个函数，它将完整的树挂载到容器节点中，就像 `ReactDOM.render()` 一样。它返回公共实例，也像 `ReactDOM.render()`：

```
function mountTree(element, containerNode) {
  // 创建顶层内部实例
  var rootComponent = instantiateComponent(element);

  // 挂载顶层组件到容器中
  var node = rootComponent.mount();
  containerNode.appendChild(node);

  // 返回它提供的公共实例
  var publicInstance = rootComponent.getPublicInstance();
  return publicInstance;
}

var rootEl = document.getElementById('root');
mountTree(<App />, rootEl);
```

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#unmounting)卸载

现在，我们有内部实例，以保留其子节点和 DOM 节点，我们可以实现卸载。对于组合组件，卸载调用生命周期方法和递归。

```
class CompositeComponent {

  // ...

  unmount() {
    // 如果有生命周期方法就调用
    var publicInstance = this.publicInstance;
    if (publicInstance) {
      if (publicInstance.componentWillUnmount) {
        publicInstance.componentWillUnmount();
      }
    }

    // 卸载单个渲染的组件
    var renderedComponent = this.renderedComponent;
    renderedComponent.unmount();
  }
}
```

对于 `DOMComponent`，会告诉每一个子项去卸载

```
class DOMComponent {

  // ...

  unmount() {
    // 卸载所有的子项
    var renderedChildren = this.renderedChildren;
    renderedChildren.forEach(child => child.unmount());
  }
}
```

在实践中，卸载 DOM 组件也需要删除事件侦听器和清除一些缓存，但我们将跳过这些细节。

我们现在可以添加一个叫 `unmountTree(containerNode)` 顶层函数，该函数类似于 `ReactDOM.unmountComponentAtNode()` 。

```
function unmountTree(containerNode) {
  // 从 DOM 节点读取内部实例:
  // (这还不起作用,我们需要更改 mountTreeTree() 来存储它。)
  var node = containerNode.firstChild;
  var rootComponent = node._internalInstance;

  // 卸载树并清空容器
  rootComponent.unmount();
  containerNode.innerHTML = '';
}
```

为了使其工作，我们需要从 DOM 节点读取内部根实例。我们将修改 `mountTree()` 为其增加 `_internalInstance` 属性来添加 DOM 根节点，我们还将在 `mountTree()` 中实现销毁任何现有的树的功能, 以便它可以被多次调用:

```
function mountTree(element, containerNode) {
  // 销毁所有现有的树
  if (containerNode.firstChild) {
    unmountTree(containerNode);
  }

  // 创建顶层的内部实例
  var rootComponent = instantiateComponent(element);

  // 挂载顶层组件到容器中
  var node = rootComponent.mount();
  containerNode.appendChild(node);

  // 保存对内部实例的引用
  node._internalInstance = rootComponent;

  // 返回它提供的公共实例
  var publicInstance = rootComponent.getPublicInstance();
  return publicInstance;
}
```

现在，运行 `unmountTree()` 或重复运行 `mountTree()`，都会删除旧树并在组件上运行 `componentWillUnmount()` 生命周期方法。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#updating)更新

在上一个章节，我们实现了卸载。但是，如果每个 prop 更改都卸载整棵树，并重新挂载，那么 react 就不再高效了。reconciler 的目标是尽可能复用现有实例来保留 DOM 和状态：

```
var rootEl = document.getElementById('root');

mountTree(<App />, rootEl);
// 应该复用已经存在的 DOM：
mountTree(<App />, rootEl);
```

我们将用一种方法扩展内部实例。除了 `mount()` 和 `unmount()` 之外，`DOMComponent` 和 `CompositeComponent` 都将实现一个名为 `receive(nextElement)` 的新方法：

```
class CompositeComponent {
  // ...

  receive(nextElement) {
    // ...
  }
}

class DOMComponent {
  // ...

  receive(nextElement) {
    // ...
  }
}
```

它的工作是尽一切可能使组件（及其任何子组件）与 `nextElement` 提供的描述一起更新。

这通常被称为 “virtual DOM diffing” 的部分，但实际发生的情况是，我们递归遍历内部树，让每个内部实例接收更新。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#updating-composite-components)更新组合组件

当一个组合组件接收一个新的元素时，我们将运行生命周期方法 `componentWillUpdate()`。

然后我们使用新的 prop 重新渲染组件, 并获取下一次渲染的元素：

```
class CompositeComponent {

  // ...

  receive(nextElement) {
    var prevProps = this.currentElement.props;
    var publicInstance = this.publicInstance;
    var prevRenderedComponent = this.renderedComponent;
    var prevRenderedElement = prevRenderedComponent.currentElement;

    // 更新*自己的*元素
    this.currentElement = nextElement;
    var type = nextElement.type;
    var nextProps = nextElement.props;

    // 找下一次 render() 输出的是什么
    var nextRenderedElement;
    if (isClass(type)) {
      // 类组件
      // 如果有生命周期方法就调用
      if (publicInstance.componentWillUpdate) {
        publicInstance.componentWillUpdate(nextProps);
      }
      // 更新 props 
      publicInstance.props = nextProps;
      // 重新渲染
      nextRenderedElement = publicInstance.render();
    } else if (typeof type === 'function') {
      // 函数组件
      nextRenderedElement = type(nextProps);
    }

    // ...
```

接下来，我们可以看一下渲染元素的 `type`。如果 `type` 自上次渲染后没有改变，之后的组件也可以就地更新。

例如，如果第一次返回 `<Button color="red" />`，第二次返回 `<Button color="blue" />`，我们可以只告诉相应的内部实例 `receive()` 下一个元素：

```
    // ...

    // 如果渲染元素的 type 没有更改，
    // 重用已经存在组件实例并退出。
    if (prevRenderedElement.type === nextRenderedElement.type) {
      prevRenderedComponent.receive(nextRenderedElement);
      return;
    }

    // ...
```

但是，如果下一个渲染元素的 `type` 与先前渲染的元素不同，则无法更新内部实例。`<button>` 不能 “变成” `<input>`。

相反，我们必须卸载现有的内部实例，然后挂载并渲染元素 `type` 对应的新实例。例如，当先前渲染 `<button />` 的组件再渲染 `<input />` 时，会发生这种情况：

```
    // ...

    // 如果我们达到这里，我们需要卸载以前挂载的组件。
    // 挂载新的组件，并交换其节点。

    // 查找旧节点，因为需要替换它
    var prevNode = prevRenderedComponent.getHostNode();

    // 卸载旧的子组件并挂载新的子组件
    prevRenderedComponent.unmount();
    var nextRenderedComponent = instantiateComponent(nextRenderedElement);
    var nextNode = nextRenderedComponent.mount();

    // 替换子组件的引用
    this.renderedComponent = nextRenderedComponent;

    // 将旧节点替换为新节点
    // 注意：这是 renderer 特定的代码,
    // 理想情况下应位于 CompositeComponent 之外：
    prevNode.parentNode.replaceChild(nextNode, prevNode);
  }
}
```

综上所述，当组合组件收到新元素时，它可以将更新委派给其渲染的内部实例，或者卸载它并在其位置挂载新元素。

还有另一种情况，组件将重新挂载而非接收元素，即元素的 `key` 已更改。在当前文档中，我们不讨论 `key` 处理，因为它增加了复杂教程的复杂性。

请注意，我们需要向内部实例添加名为 `getHostNode()` 的方法，以便可以在更新期间找到平台特定的节点并替换它。对于两个类，其实现都非常简单:

```
class CompositeComponent {
  // ...

  getHostNode() {
    // 要求渲染组件提供它。
    // 递归深入任意组合组件。
    return this.renderedComponent.getHostNode();
  }
}

class DOMComponent {
  // ...

  getHostNode() {
    return this.node;
  }  
}
```

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#updating-host-components)更新宿主组件

宿主组件实现，如`DOMComponent`，更新方式不同。当他们收到一个元素时，他们需要更新平台特定的视图。在 React DOM 的情况下，这意味着更新 DOM 属性:

```
class DOMComponent {
  // ...

  receive(nextElement) {
    var node = this.node;
    var prevElement = this.currentElement;
    var prevProps = prevElement.props;
    var nextProps = nextElement.props;    
    this.currentElement = nextElement;

    // 删除旧的属性
    Object.keys(prevProps).forEach(propName => {
      if (propName !== 'children' && !nextProps.hasOwnProperty(propName)) {
        node.removeAttribute(propName);
      }
    });
    // 设置新的属性
    Object.keys(nextProps).forEach(propName => {
      if (propName !== 'children') {
        node.setAttribute(propName, nextProps[propName]);
      }
    });

    // ...
```

然后宿主组件需要更新其子组件。与组合组件不同，它们可能包含多个子组件。

在此简化的示例中，我们使用内部实例数组并遍历它，根据接收的 `type` 是否与以前的 `type` 匹配更新或替换内部实例。真正的 reconciler 还会在描述中获取元素的 `key`，并存储和跟踪除了插入和删除之外的移动，但我们这里将省略此逻辑。

我们在列表中收集子组件的 DOM 操作，以便可以批量执行它们:

```
    // ...

    // 这些是 React 元素的数组:
    var prevChildren = prevProps.children || [];
    if (!Array.isArray(prevChildren)) {
      prevChildren = [prevChildren];
    }
    var nextChildren = nextProps.children || [];
    if (!Array.isArray(nextChildren)) {
      nextChildren = [nextChildren];
    }
    // 这些是内部实例的数组:
    var prevRenderedChildren = this.renderedChildren;
    var nextRenderedChildren = [];

    // 当我们迭代子组件时，我们将向数组添加相应操作。
    var operationQueue = [];

    // 注意：以下部分非常简化!
    // 它不处理重新排序、带空洞或有 key 的子组件。
    // 它的存在只是为了说明整个流程，而不是细节。

    for (var i = 0; i < nextChildren.length; i++) {
      // 尝试去获取此子组件现有的内部实例
      var prevChild = prevRenderedChildren[i];

      // 如果此索引下没有内部实例，
      // 则子实例已追加到末尾。
      // 创建新的内部实例,挂载它,并使用其节点。
      if (!prevChild) {
        var nextChild = instantiateComponent(nextChildren[i]);
        var node = nextChild.mount();

        // 记录我们需要追加的节点
        operationQueue.push({type: 'ADD', node});
        nextRenderedChildren.push(nextChild);
        continue;
      }

      // 仅当实例的元素类型匹配时，我们才能更新该实例。
      // 例如，<Button size="small" /> 可以更新成 <Button size="large" />，
      // 但是不能更新成 <App />。
      var canUpdate = prevChildren[i].type === nextChildren[i].type;

      // 如果我们无法更新现有的实例，
      // 我们必须卸载它并安装一个新实例去替代
      if (!canUpdate) {
        var prevNode = prevChild.getHostNode();
        prevChild.unmount();

        var nextChild = instantiateComponent(nextChildren[i]);
        var nextNode = nextChild.mount();

        // 记录我们需要替换的节点
        operationQueue.push({type: 'REPLACE', prevNode, nextNode});
        nextRenderedChildren.push(nextChild);
        continue;
      }

      // 如果我们能更新现有的内部实例，
      // 只是让它接收下一个元素并处理自己的更新。
      prevChild.receive(nextChildren[i]);
      nextRenderedChildren.push(prevChild);
    }

    // 最后，卸载不存在的任何子组件:
    for (var j = nextChildren.length; j < prevChildren.length; j++) {
      var prevChild = prevRenderedChildren[j];
      var node = prevChild.getHostNode();
      prevChild.unmount();

      // 记录我们需要删除的节点
      operationQueue.push({type: 'REMOVE', node});
    }

    // 将渲染的子级列表指向更新的版本。
    this.renderedChildren = nextRenderedChildren;

    // ...
```

最后一步，我们执行 DOM 操作。同样，真正的 reconciler 代码更为复杂，因为它还处理移动操作:

```
    // ...

    // 处理操作队列。
    while (operationQueue.length > 0) {
      var operation = operationQueue.shift();
      switch (operation.type) {
      case 'ADD':
        this.node.appendChild(operation.node);
        break;
      case 'REPLACE':
        this.node.replaceChild(operation.nextNode, operation.prevNode);
        break;
      case 'REMOVE':
        this.node.removeChild(operation.node);
        break;
      }
    }
  }
}
```

这就是更新宿主组件。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#top-level-updates)顶层更新

现在，`CompositeComponent` 和 `DOMComponent` 都实现了 `receive(nextElement)` 方法，我们可以更改顶级的 `mountTree()` 函数，以便当元素的 `type` 与上次相同时使用它:

```
function mountTree(element, containerNode) {
  // 检查现有的树
  if (containerNode.firstChild) {
    var prevNode = containerNode.firstChild;
    var prevRootComponent = prevNode._internalInstance;
    var prevElement = prevRootComponent.currentElement;

    // 如果可以，重用现有的根组件
    if (prevElement.type === element.type) {
      prevRootComponent.receive(element);
      return;
    }

    // 否则，卸载现有树
    unmountTree(containerNode);
  }

  // ...

}
```

现在调用 `mountTree()` 两次相同的 `type` 是没有破坏性的

```
var rootEl = document.getElementById('root');

mountTree(<App />, rootEl);
// 应该重用已经存在的 DOM：
mountTree(<App />, rootEl);
```

这些是 React 内部工作原理的基础知识。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#what-we-left-out)我们遗漏了什么

与真实代码库相比，本文档得到了简化。我们没有解决几个重要方面：

-   组件可以呈现 `null`，reconciler 可以处理在数组中“空插槽”和渲染输出。
-   reconciler 还从元素中读取 `key`，并使用它来确定哪个内部实例对应于数组中的哪个元素。实际 React 实现中的大部分复杂性与此相关。
-   除了组合和宿主内部实例类外，还有用于“文本”和“空”组件的类。它们表示文本节点和通过渲染 `null` 获得 “空插槽”。
-   renderer 使用[注入](https://zh-hans.reactjs.org/docs/codebase-overview.html#dynamic-injection)的方式将宿主内部类传递给 reconciler. 例如，React DOM 告诉 reconciler 使用 `ReactDOMComponent` 作为宿主内部实例实现。
-   更新子列表的逻辑被提取到一个名为 `ReactMultiChild` 的 mixin 中，它由 React DOM 和 React Native 中的宿主内部实例类实现使用。
-   reconciler 还在组合组件中实现对 `setState()` 的支持。事件处理程序内的多个更新将被批处理为单一更新。
-   reconciler 还负责将 refs 附加和分离到组合组件和宿主节点。
-   在 DOM 准备好之后调用的生命周期方法，例如 `componentDidMount()` 和 `componentDidUpdate()`，被收集到“回调队列”中并在一个批处理中执行。
-   React 将有关当前更新的信息放入名为 “transaction” 的内部对象中。事务可用于跟踪挂起的生命周期方法的队列、警告的当前 DOM 嵌套以及特定更新的“全局”任何其他内容。事务还确保在更新后“清理所有内容”。例如，React DOM 提供的事务类在任何更新后还原 input selection。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#jumping-into-the-code)跳转到代码

-   [`ReactMount`](https://github.com/facebook/react/blob/83381c1673d14cd16cf747e34c945291e5518a86/src/renderers/dom/client/ReactMount.js) 就像本教程中 `mountTree()` 和 `unmountTree()` 这样的代码。它负责挂载和卸载顶层组件。[`ReactNativeMount`](https://github.com/facebook/react/blob/83381c1673d14cd16cf747e34c945291e5518a86/src/renderers/native/ReactNativeMount.js) 是 React Native 的模拟。
-   [`ReactDOMComponent`](https://github.com/facebook/react/blob/83381c1673d14cd16cf747e34c945291e5518a86/src/renderers/dom/shared/ReactDOMComponent.js) 相当于本教程中的 `DOMComponent`。它实现了React DOM renderer 的宿主组件类。[`ReactNativeBaseComponent`](https://github.com/facebook/react/blob/83381c1673d14cd16cf747e34c945291e5518a86/src/renderers/native/ReactNativeBaseComponent.js) 是 React Native 的模拟。
-   [`ReactCompositeComponent`](https://github.com/facebook/react/blob/83381c1673d14cd16cf747e34c945291e5518a86/src/renderers/shared/stack/reconciler/ReactCompositeComponent.js) 相当于本教程中的 `CompositeComponent`。它处理调用用户定义的组件并维护其状态。
-   [`instantiateReactComponent`](https://github.com/facebook/react/blob/83381c1673d14cd16cf747e34c945291e5518a86/src/renderers/shared/stack/reconciler/instantiateReactComponent.js) 包含选择要为元素构造的正确内部实例类的开关。它相当于本教程中的 `instantiateComponent()`.
-   [`ReactReconciler`](https://github.com/facebook/react/blob/83381c1673d14cd16cf747e34c945291e5518a86/src/renderers/shared/stack/reconciler/Reactreconciler.js) 是一个包含 `mountComponent()`、`receiveComponent()` 和 `unmountComponent()` 方法的包装器。它调用内部实例上的底层实现，但也包括一些由所有内部实例实现共享的代码。
-   [`ReactChildReconciler`](https://github.com/facebook/react/blob/83381c1673d14cd16cf747e34c945291e5518a86/src/renderers/shared/stack/reconciler/ReactChildreconciler.js) 实现根据子元素的 `key` 挂载、更新和卸载子级的逻辑。
-   [`ReactMultiChild`](https://github.com/facebook/react/blob/83381c1673d14cd16cf747e34c945291e5518a86/src/renderers/shared/stack/reconciler/ReactMultiChild.js) 实现对子组件插入、删除和移动操作队列的处理，独立于渲 renderer。
-   由于遗留原因，`mount()`、 `receive()` 和 `unmount()` 在 React 代码库中实际上名字为 `mountComponent()`、`receiveComponent()` 和 `unmountComponent()`，但是它们接收元素。
-   内部实例的属性以下划线开头，例如，`_currentElement`。它们被认为是整个代码库中的只读公共字段。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#future-directions)未来方向

stack reconciler 具有固有的局限性，例如同步并且无法中断工作或将其拆分为块。[新的 Fiber reconciler](https://zh-hans.reactjs.org/docs/codebase-overview.html#fiber-reconciler)正在进行中，具有[完全不同的架构](https://github.com/acdlite/react-fiber-architecture)。在未来，我们打算用它替换 stack reconciler，但目前它还远远没有达到功能对等。

### [](https://zh-hans.reactjs.org/docs/implementation-notes.html#next-steps)下一步

阅读[下一节](https://zh-hans.reactjs.org/docs/design-principles.html) 了解我们用于开发 React 的指导原则。

# 设计原则

编写该文档的目的是，使开发者更易于了解我们如何决策 React（应该做哪些，不应该做哪些），以及我们的开发理念。我们非常欢迎来自社区的贡献，但如若违背这些理念，实非我们所愿。

> **注意：**
> 
> 文章描述了 React 自身的设计原则，而非 React 组件或应用，阅读者需要对 React 有深入的理解。
> 
> 如需 React 的入门文档，查看 [React 哲学](https://zh-hans.reactjs.org/docs/thinking-in-react.html)。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#composition)组合

组件之间的组合是 React 的重要特征。不同开发者写的组件应该可以一起正常执行。给一个组件添加功能，而不会对整个代码库造成涟漪似的变化，这对我们很重要。

比如，应该可以在不影响任何使用它的组件的情况下，将一些内部 state 引入该组件。类似的，在必要的情况下可以在任何组件里添加一些初始化和销毁的代码。

在组件中使用 state 或者生命周期函数没什么“不好”。跟所有强大的特性一样，应该适度使用它们，我们并不打算移除他们。相反，我们认为他们是 React 之所以好用的一部分。我们未来也许会启用[更多函数模式](https://github.com/reactjs/react-future/tree/master/07%20-%20Returning%20State)，但内部 state 和生命周期函数都会在里面。

人们常常认为组件“只是函数”，但在我们看来，组件要好用的话，需要的不止这些。在 React 中，组件描述了任何可组合的行为，包含渲染、生命周期和 state。一些类似 [Relay](https://facebook.github.io/relay/) 的外部库给组件带来了其他增强功能，比如描述数据之间的依赖关系。有可能这些做法会以某种形式回到 React 中。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#common-abstraction)共用抽象

一般而言我们[拒绝添加](https://www.youtube.com/watch?v=4anAwXYqLG8)一些开发者可以实现的特性。我们不想因为无用的库代码使得大家的应用变的累赘，然而也有特例。

比如，如果 React 不支持内部 state 或者生命周期函数，大家会为此创建自己的抽象。当有多种抽象竞争的时候，React 不能强制使用或利用这些抽象中的任何一个。React 必须选择最基本的共同点。

这就是我们增加 React 特性的原因。如果我们发现很多组件以不兼容或者不高效的方式实现了某些特性，我们会倾向在 React 中实现它。我们轻易不这样做，只有我们非常确定提高抽象层级有助于整个生态系统时我们才会这样做。State、生命周期函数、跨浏览器事件的正规化都是很好的范例。

我们总是和社区一起商议这样的优化提议。你可以在 React 问题跟踪的 [“big picture”](https://github.com/facebook/react/issues?q=is:open+is:issue+label:%22Type:+Big+Picture%22) 标签上找到一些这样的讨论。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#escape-hatches)应急方案

React 是务实的，Facebook 的产品需求驱使它这样。尽管 React 受一些目前还非主流的编程思想比如函数式编程的影响，这个项目的一个明确目标是广泛地接触具有不同技能和经验的开发者。

如果要废弃某个我们不喜欢的模式，我们有责任在废弃之前，考虑所有已知的用例并且教育社区这些模式的[替代做法](https://zh-hans.reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)。如果某些有助于开发应用的模式很难以声明式的方式表达，我们会提供一个[命令式的 API](https://zh-hans.reactjs.org/docs/more-about-refs.html)。如果我们发现很多应用中必要的模式我们找不到一个完美的API，我们会[提供一个临时欠佳的 API](https://zh-hans.reactjs.org/docs/legacy-context.html)，只要以后可以移除它并且方便后续的优化。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#stability)稳定性

我们重视 API 的稳定性。在 Facebook，我们有超过 5 万多个组件在使用 React。很多其他公司，包括 [Twitter](https://twitter.com/) 和 [Airbnb](https://www.airbnb.com/) 也是 React 的重度用户。这就是我们一般不愿意变更公共 API 或行为的原因。

然而我们认为“没有变更”的这种稳定性被高估了，它很快会演变成停滞不前。我们偏向这样理解稳定性：“在生产环境大规模使用，当需要变更时有一个明确的（自动化更好）迁移路径。”。

当我们废弃一个模式时，我们会研究它在 Facebook 内部的使用情况并添加废弃警告。这样我们可以评估变更的影响范围。有时觉得时机太早了，我们就不作变更。我们需要更策略化的思考来让代码库准备好这样的变更。

如果我们自信地认为这次改动破坏性不是非常大，而且迁移策略对所有用户场景都可行，我们会在开源社区发布废弃警告。我们和很多 Facebook 以外的 React 用户联系紧密，而且我们会监控流行的开源项目并指导他们解决这些废弃警告。

只考虑 Facebook 里的 React 的代码库大小，成功的内部迁移往往是个好兆头：其他公司迁移也不会有困难。然而时不时会有人提出我们没有考虑到的场景，我们会给他们一个紧急出路，或者反思我们的做法。

没有充分的理由我们不会废弃任何特性。我们意识到有时废弃警告会让开发者沮丧，但我们添加废弃警告是因为它们为社区中的很多人认为有价值的优化和新特性扫除了障碍。

例如，我们在 React 15.2.0 中添加了一个[未知 DOM 属性的警告](https://zh-hans.reactjs.org/warnings/unknown-prop.html)，很多项目因此受到影响。然而修复这些警告非常重要，因为我们可以在 React 中新增支持[自定义属性](https://github.com/facebook/react/issues/140)。以往的每一次废弃警告都有像这样的考量在里面。

我们添加废弃警告时会保留到当前大版本，在[下一次大版本中改变行为](https://zh-hans.reactjs.org/blog/2016/02/19/new-versioning-scheme.html)。如果这其中涉及到大量重复性的人力工作，我们会发布一个[代码更改](https://www.youtube.com/watch?v=d0pOgY8__JM)脚本自动化大部分的改动。Codemods 使我们能够在庞大的代码库中继续前行，我们也推荐大家使用。

你可以在 [react-codemod](https://github.com/reactjs/react-codemod) 仓库中找到我们发布的 codemods。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#interoperability)互操作性

我们很看重与已有系统的互操作性和渐进式的采用。Facebook 有一个庞大的非 React 的代码库。Facebook 的网站混合使用了名为 XHP 的服务端组件系统、React 之前的内部 UI 库和 React。任何产品团队能够[开始在小功能上使用React](https://www.youtube.com/watch?v=BF58ZJ1ZQxY)而不是重写他们的代码，这对我们很重要。

这就是 React 提供应急方案的原因：让不同的模型协同工作，让不同的 UI 库协同工作。你可以把一个已有的命令式 UI 封装成声明式的组件，反之亦然。这对渐进采用而言至关重要。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#scheduling)调度

即便你的组件以 function 的方式声明，在 React 中你也并不会直接调用它们。每个组件返回一个该渲染什么的描述，该描述会包含开发者写的组件如 `<LikeButton>` 和 平台特定的组件如 `<div>`。由 React 决定在未来的某个时间点展开 `<LikeButton>`，并根据组件的渲染结果递归地把这些变更实际应用到 UI 树上。

虽然只是微小的区别，但这样做意义重大。因为你不需要调用组件方法而是让 React 调用它，这意味着如果必要 React 可以延迟调用。在 React 当前的实现中，React 在单个 tick 周期中递归地走完这棵树，然后调用整个更新后树的渲染方法。但是以后 React 可能会[延迟一些更新操作来防止掉帧](https://github.com/facebook/react/issues/6170)。

这在 React 的设计中很常见。有一些流行的库实现了 “push” 模式，即当新数据到达时再计算。然而 React 坚持 “pull” 模式，即计算可以延迟到必要时再执行。

React 不是一个常规的数据处理库，它是开发用户界面的库。我们认为 React 在一个应用中的位置很独特，它知道当前哪些计算当前是相关的，哪些不是。

如果不在当前屏幕，我们可以延迟执行相关逻辑。如果数据数据到达的速度快过帧速，我们可以合并、批量更新。我们优先执行用户交互（例如按钮点击形成的动画）的工作，延后执行相对不那么重要的后台工作（例如渲染刚从网络上下载的新内容），从而避免掉帧。

要清楚我们现在还没有利用调度。然而，我们之所以偏好自己控制调度以及异步`setState()`，是因为拥有了选择的自由度。

如果我们允许用户使用在一些变体的[函数反应式编程](https://en.wikipedia.org/wiki/Functional_reactive_programming)范式中常见的“推”模式直接拼接视图，我们将会很难获得调度的控制权。

React 的一个关键目标是在把控制权转交给 React 之前执行的用户代码量最少。这确保 React 保持调度的能力，并根据它所知道的 UI 的情况把工作切分成小块处理。

在团队内部有个笑话，React 本该叫做“调度”因为 React 不想变得完全“反应式（reactive）”。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#developer-experience)开发者体验

提供好的开发者体验对我们很重要。

例如，我们维护了 [React DevTools](https://github.com/facebook/react/tree/main/packages/react-devtools)，它让大家在 Chrome 和 Firefox 浏览器中可以检查 React 组件树。我们听说它大幅提高了 Facebook 的工程师和社区的生产效率。

我们还提供了对开发有所帮助的开发者警告。比如你以浏览器不理解的方式嵌套标签或者你在编写 API 时常见的错别字，React 会警告你。开发者警告和相关的检查导致了 React 的开发版本比生产版本速度慢。

Facebook 内部的使用模式帮助我们了解常见的错误有哪些，以及如何提前预防。当我们添加新特性时，我们尝试去预测可能会发生的错误并发出警告。

我们一直在寻找提高开发者体验的方法。我们很乐意听取大家的建议，接受大家的贡献，把开发者体验做的更好。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#debugging)调试

当发生错误时，你有一些线索可以追溯到代码库中的具体代码，这很重要。在 React 中，props 和 state 就是线索。

当你看到屏幕上出现错误时，你可以打开 React 开发者工具，找到负责渲染的组件，检查它的 props 和 state 是否正确。如果正确，就能大概知道问题可能出在组件的 `render()` 方法，或者某个被 `render()` 调用的方法。问题显而易见。

如果 state 发生错误，你就知道问题在于这个文件中的某个 `setState()` 调用，定位和修复也相对简单，因为在单个文件中 `setState()` 调用次数很少。

如果 props 出现错误，你可以在检查器中沿着树向上排查，查找到第一个因为向下传递了错误的 props 而“污染了这口井”的组件。

能够以当前的 props 和 state 这种形式追溯到其产生的任何 UI，这种能力对 React 来说非常重要。state 不会包裹在闭包和组合子中，并且在 React 中可以直接获取，这是 React 一个非常明确的设计目标。

尽管 UI 是动态变化的，但是我们认为 state 和 props 的同步执行的 `render()` 函数使得调试变成了无聊但是有限的步骤，而不是瞎猜。我们在 React 里保留了这个限制，即使它使调试在某些场景下变得更加困难，比如复杂的动画。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#configuration)配置

我们发现全局的运行时配置会造成很多问题。

比如我们时常收到这样的请求，实现类似于 `React.configure(options)` 或者 `React.register(component)` 的方法。然而这样做会出现很多问题，而我们对此并没有很好的解决方案。

如果有人在第三方组件库中调用了这样的方法怎么办？如果一个 React 应用内嵌了另一个 React 应用，而且他们需要的配置不兼容怎么办？一个第三方组件怎么申明它需要某个特定配置呢？我们认为全局的配置与组合搭配效果不好。既然组合是 React 的核心，那么我们不在代码里提供全局的配置。

然而我们在构建层面提供了一些全局的配置。比如我们提供了独立的开发和生产环境的构建。我们后面也许会[增加一个分析构建](https://github.com/facebook/react/issues/6627)。我们对考虑使用其他构建参数保持开放态度。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#beyond-the-dom)DOM 之外

我们认为 React 的价值在于它使我们写出更少 bug 的组件，而且组件很方便组合。React 当初的渲染目标是 DOM，但 [React Native](https://reactnative.dev/) 对 Facebook 和社区来说同样重要。

React 的一个重要设计约束是要渲染引擎无关。这在内部呈现增加了一些开销，另一方面，对内核的任何优化将对所有平台都有益。

单一的编程模型使我们能够围绕产品形成工程团队，而不是围绕平台。目前来看这样的取舍对我们来说很值得。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#implementation)实现

相比优雅的实现，我们更想提供尽可能优雅的 API。现实是不完美的，如果能让用户无需写这些代码，在合适的范围内，我们偏向把丑陋的代码写在库里。我们评估新代码时，我们看中的是正确地实现、高性能并且能够带来良好的开发体验，优雅是第二位的。

我们宁可写无聊的代码也不写耍聪明的代码。代码是一次性的而且经常变更，所以[除非极度必要，不引入新的内部抽象](https://youtu.be/4anAwXYqLG8?t=13m9s)很重要。很方便移动、改动和删除的啰嗦的代码比过早抽象且难以变更的优雅的代码更好。

### [](https://zh-hans.reactjs.org/docs/design-principles.html#optimized-for-tooling)针对工具优化

React 一些常用的 API 名字很冗长。比如，我们采用 `componentDidMount()` 而非 `didMount()` 或者 `onMount()`。这是[有意为之的](https://github.com/reactjs/react-future/issues/40#issuecomment-142442124)，目的是使得和 React 的交互点具有高可见性。

在像 Facebook 这样庞大的代码库中，能够搜索某些特定 API 的使用很重要。我们重视清晰冗长的名字，特别是在一些需要保守使用的特性上。比如，`dangerouslySetInnerHTML` 在代码评审中就很容易被发现。

针对搜索优化很重要，因为我们依赖 [codemods](https://www.youtube.com/watch?v=d0pOgY8__JM) 做不兼容的变更。我们希望非常容易、安全地在代码库中应用大量自动化变更，独特冗长的名字帮助了我们。类似地，独特的命名使得编写自定义 React 用法的[提示规则](https://github.com/yannickcr/eslint-plugin-react)变得很容易，无需担心潜在的错误匹配。

[JSX](https://zh-hans.reactjs.org/docs/introducing-jsx.html) 也类似这样。尽管 React 不强制使用 JSX，在 Facebook 我们大量使用 JSX，因为它既好看又实用。

在我们的代码库中，JSX 给和 React 元素树打交道的工具提供了明确的提示。这使得构建时优化成为可能，比如[提升常量元素](https://babeljs.io/docs/en/babel-plugin-transform-react-constant-elements/)、安全地进行代码提示、codemod 内部组件用法、在代码警告中[包含 JSX 源码定位](https://github.com/facebook/react/pull/6771).

### [](https://zh-hans.reactjs.org/docs/design-principles.html#dogfooding)内部测试

我们全力解决社区提出的问题。但我们可能会优先处理在 Facebook 内部遇到的_同样的_问题。也许这很反直觉，但是我们认为这是社区相信 React 的主要原因。

内部的重度使用使我们坚信 React 不会凭空消失。Facebook 创建了 React 是来解决它的问题的。React 给 Facebook 带来了现实的商业价值，并且在很多产品中使用。在内部测试意味着我们的目光保持敏锐，有着前进的重点方向。

这不代表我们会忽视社区提出的问题。比如，即便我们内部并不依赖他们，我们仍增加了 React 对 [web components](https://zh-hans.reactjs.org/docs/webcomponents.html) 和 [SVG](https://github.com/facebook/react/pull/6243) 的支持。我们倾听着大家的痛点，并全力解决他们。是社区使 React 变得与众不同，所以我们很荣幸可以回报社区。

在 Facebook 发布了很多开源的项目之后，我们学到了让大家都开心的做法会导致项目没有重点，成长不起来。相反，我们发现选择一小部分群体，重点关注如何使他们开心带来了积极的净效应。这正是 React 的做法，到目前为止解决 Facebook 产品团队的问题很好的传递到了开源社区。

这种做法的不好之处是有时对于 Facebook 团队无需关心的事，比如“起步”的体验，我们不能够给予足够的重视。我们已经觉察到了，我们正在着手思考如何做才能使社区里的每个人都受益，不重蹈以前开源项目的覆辙。