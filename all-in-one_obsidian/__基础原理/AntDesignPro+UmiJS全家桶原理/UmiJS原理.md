# 架构设计
- 整体是插件化的架构设计，不同的功能用不同的插件来完成
- 底层打包基于Webpack封装
- 路由基于ReactRouter封装
- 网络请求基于fetch封装

# 项目创建
## 基于 `npm`
使用 `npm create umi my-app`，这个命令`npm create` 是 `npm init`的别名，利用一个【创建工具】来初始化项目，【创建工具】指的是npm包`create-XXX`  这个`XXX`就是创建工具的名字，我们就可以用`npm create xxx target` 来创建初始化的项目了。
这个命令，在npm 的文档上没写，是用来对标`yarn create` 的命令，是`npm init`的别名，可以参考[npm init](https://docs.npmjs.com/cli/v8/commands/npm-init)

## 基于 `yarn`
同理，使用`yarn create umi my-app` 来实现项目的初始化，`yarn create`是[官方文档](https://classic.yarnpkg.com/en/docs/cli/create)上有的，可以直接去看 。 当前1.x 的文档里面有，2.x的文档还是没有的

## 案例
如上，`yarn create umi my-app` 使用的是npm 源上的[create-umi](https://www.npmjs.com/package/create-umi) 这个包来初始化项目的，如果一个包包含了命名空间，比如`@umijs/create-umi-app` 这个时候我们都需要带上命名空间。
比如，创建标准umi项目 `yarn create @umijs/umi-app my-app`，这个是创建更轻量的umi项目模板的方式。

### `create-umi`实现
- [代码地址](https://github.com/umijs/create-umi)
- 定义了一个生成器基类，其他具体的生成器扩展基类来实现，选择不同的类型就对应到了不同的生成器上面。

#### ant-design-pro 模板
- 通过[[NodeJS]] 执行 `git clone` 命令来实现的，简单版本的在`master`分支，全量版本的在`all-blocks`，可以通过`--branch`来实现不同的分支切换，从而实现简单版本和全量版本的切换。
- 如果选择的不是`TypeScript`版本，而是`JavaScript`版本，则用`sylvanas`这个npm包把TS的转换成JS，然后删除TS相关文件。
- 然后拷贝一份ReadMe.md
- 最后把`ant-design-pro`模板中的`package.json`中配置的`create-umi`字段中的内容删掉，得到的就是一个干净的副本了
```json
// ant-design-pro/package.json
"create-umi": {  
  "ignoreScript": [  
    "docker*",  
    "functions*",  
    "site",  
    "generateMock"  
 ],  
  "ignoreDependencies": [  
    "netlify*",  
    "serverless"  
 ],  
  "ignore": [  
    ".dockerignore",  
    ".git",  
    ".github",  
    ".gitpod.yml",  
    "CODE_OF_CONDUCT.md",  
    "Dockerfile",  
    "Dockerfile.*",  
    "lambda",  
    "LICENSE",  
    "netlify.toml",  
    "README.*.md",  
    "azure-pipelines.yml",  
    "docker",  
    "CNAME",  
    "create-umi"  
 ]  
}

```

#### app 和 plugin 模板
- 在生成器的目录里面有一个叫`templates`的目录，这里面有了创建当前目标的所有资源，但资源里面不是直接可以用的，而是在命令行交互界面收集了不同参数，这些参数可以说是开关，这些开关组成了条件生成的开关，开关使用`mustache`模板来表达的
- 将`templates`中的 `mustache`模板按照开关参数渲染出来，得到的就是我们配置的目标产物
- 将目标产物写到命令行的路径中即可

### `@umijs/create-umi-app` 实现
- [代码地址](https://github.com/umijs/umi/tree/master/packages/create-umi-app)
- 跟上面的 `create-umi` 的`app`和 `plugin`一样，目录中带`templates`目录，目录中的部分内容是用模板来表达的，根据指定参数把模板填充完成，然后写入对应的路径即可

# 项目配置
## 约定的文件
- `src/app.tsx`  约定配置文件
- `src/access.ts` 权限定义文件
- 数据模型定义
	- `src/models`下的文件
	- `src/pages`下的`models`目录下的文件
	- `src/pages`下所有`model.ts`文件（不区分大小写
- `src/locales` 国际化文件
- `config/routes`  路由配置

## 运行时配置 `src/app.ts`
### modifyClientRenderOpts(fn)
- 修改 clientRender 参数
```ts
let isSubApp = false;
export function modifyClientRenderOpts(memo) {
  return {
    ...memo,
    rootElement: isSubApp ? 'sub-root' : memo.rootElement,    
  };
}
```

### patchRoutes({ routes })
- 直接修改路由，不用返回。
```ts
export function patchRoutes({ routes }) {
  routes.unshift({
    path: '/foo',
    exact: true,
    component: require('@/extraRoutes/foo').default,
  });
}
```

### render(oldRender: Function)
- 比如用于渲染之前做权限校验，
```ts
import { history } from 'umi';

export function render(oldRender) {
  fetch('/api/auth').then(auth => {
    if (auth.isLogin) { oldRender() }
    else { 
      history.push('/login'); 
      oldRender()
    }
  });
}
```

### onRouteChange({ routes, matchedRoutes, location, action })
- 在初始加载和路由切换时做一些事情。
```ts
export function onRouteChange({ location, routes, action }) {
  bacon(location.pathname);
  if (matchedRoutes.length) {
    document.title = matchedRoutes[matchedRoutes.length - 1].route.title || '';
  }
}
```

### rootContainer(LastRootContainer, args)
- 修改交给 react-dom 渲染时的根组件
```ts
export function rootContainer(container) {
  return React.createElement(ThemeProvider, null, container);
}
```

### `@umijs/plugin-request`  配置
- 构建时配置`request`对象
```ts
import { RequestConfig } from 'umi';

export const request: RequestConfig = {
  timeout: 1000,
  errorConfig: {
	adapter:{}， // 接口数据适配
	errorPage:{}
  },
  middlewares: [],
  requestInterceptors: [],
  responseInterceptors: [],
};
```

### `@umijs/plugin-layout`
- 构建时配置`layout`对象
```ts
import React from 'react';
import {
  BasicLayoutProps,
  Settings as LayoutSettings,
} from '@ant-design/pro-layout';

export const layout = ({
  initialState,
}: {
  initialState: { settings?: LayoutSettings; currentUser?: API.CurrentUser };
}): BasicLayoutProps => {
  return {
    rightContentRender: () => <RightContent />,
    footerRender: () => <Footer />,
    onPageChange: () => {
      const { currentUser } = initialState;
      const { location } = history;
      // 如果没有登录，重定向到 login
      if (!currentUser && location.pathname !== '/user/login') {
        history.push('/user/login');
      }
    },
    menuHeaderRender: undefined,
    ...initialState?.settings,
  };
};
```

### `@umijs/plugin-initial-state`
- 没有构建时配置
```ts
// src/app.ts
export async function getInitialState() {
  const data = await fetchXXX();
  return data;
}
```

### `@umijs/plugin-locale`
```js
// src/app.js
import qs from 'qs';

export const locale = {
  getLocale() {
    const { search } = window.location;
    const { locale = 'zh-CN' } = qs.parse(search, { ignoreQueryPrefix: true });
    return locale;
  },
  setLocale({ lang, realReload, updater }) {
	history.push(`/?locale=${lang}`);
	updater();
  },
};

```

### `@umijs/plugin-dva`
```js
import { createLogger } from 'redux-logger';
import { message } from 'antd';

export const dva = {
  config: {
    onAction: createLogger(),
    onError(e: Error) {
      message.error(e.message, 3);
    },
  },
};
```

### SSR 预取数据
```js
// app.(ts|js)
export const ssr = {
 modifyGetInitialPropsCtx: async (ctx) => {
 ctx.title = 'params';
 return ctx;
 }
}
```

### SSR polyfill
```js
// app.ts
export const ssr = {
 beforeRenderServer: async ({
 env,
 location,
 history,
 mode,
 context,
 }) => {
 // global 为 Node.js 下的全局变量
 // 避免直接 mock location，这样会造成一些环境判断失效
 global.mockLocation = location;
 // 国际化
 if (location.pathname.indexOf('zh-CN') > -1) {
 global.locale = 'zh-CN'
 }
 }
}
```

### `@umijs/plugin-qiankun`
#### 主应用配置
- 构建时配置`qiankun`对象 `master`属性
```ts
// 从接口中获取子应用配置，export 出的 qiankun 变量是一个 promise
export const qiankun = fetch('/config').then(({ apps }) => ({
  // 注册子应用信息
  apps,
  // 完整生命周期钩子请看 https://qiankun.umijs.org/zh/api/#registermicroapps-apps-lifecycles
  lifeCycles: {
    afterMount: (props) => {
      console.log(props);
    },
  },
   routes: [
      {
        path: '/app1',
        microApp: 'app1',
      },
    ],  // 支持更多的其他配置，详细看这里 https://qiankun.umijs.org/zh/api/#start-opts
}));
```

#### 子应用配置
- 构建时配置`qiankun`对象`slave`属性
```ts
export const qiankun = {
  // 应用加载之前
  async bootstrap(props) {
    console.log('app1 bootstrap', props);
  },
  // 应用 render 之前触发
  async mount(props) {
    console.log('app1 mount', props);
  },
  // 应用卸载之后触发
  async unmount(props) {
    console.log('app1 unmount', props);
  },
};
```

#### 微应用配置
```js

// src/app.ts
export function useQiankunStateForSlave() {
  const [masterState, setMasterState] = useState({});

  return {
    masterState,
    setMasterState,
  };
}
```


## 默认配置文件
- 配置文件是从前往后查找
- 取第一个存在的路径
### 配置文件路径（优先级从前到后递减）
- `.umirc.ts`
- `.umirc.js`
- `config/config.ts`
- `config/config.js`

如果有`mfsu`（webpack5）的特性，或者配置了用`webpack5`,就用webpack5，否则就用webpack4，这些包都在 `umi/packages/deps`中

## 默认插件文件
### `@umijs/plugin-access`
- 配置权限定义
- `src/access.ts`  权限定义文件，支持`useAccess` 

### `@umijs/plugin-analytics`
- 在`config.ts`中配置 `analytics` 对象

### `@umijs/plugin-antd-mobile`

### `@umijs/plugin-crossorigin`
- 为索引第三方脚本加上`crossorigin="anomymous"`
- 在`config.ts`中配置`crossorigin:true`

### `@umijs/plugin-dva`
-  整合 dva数据流
- 在`config.ts`中配置`dva`对象
- 约定的内容
	- `src/models`下的文件
	- `src/pages`下的`models`目录下的文件
	- `src/pages`下所有`model.ts`文件（不区分大小写）

### `@umijs/plugin-esbuild`
- 使用esbuild作为压缩器
- 在`config.ts`中配置`esbuild`对象开启

###  `@umijs/plugin-helmet`
- 整合 [react-helmet](https://github.com/nfl/react-helmet)，管理 HTML 文档标签（如标题、描述等）
- 默认开启，支持服务端渲染（SSR）

### `@umijs/plugin-initial-state`
- 约定一个地方生产和消费初始化数据。
- 在`src/app.tsx`中，导出`getInitialState`时开启，需要搭配`@umijs/plugin-model`使用

### `@umijs/plugin-layout`
- 在`config.ts`中配置`layout`对象开启
- 为了进一步降低研发成本，我们尝试将布局通过 umi 插件的方式内置，只需通过简单的配置即可拥有 Ant Design 的 Layout，包括导航以及侧边栏。从而做到用户无需关心布局。
	-   默认为 Ant Design 的 Layout [@ant-design/pro-layout](https://www.npmjs.com/package/@ant-design/pro-layout)，支持它全部配置项。
	-   侧边栏菜单数据根据路由中的配置自动生成。
	-   默认支持对路由的 403/404 处理和 Error Boundary。
	-   搭配 @umijs/plugin-access 插件一起使用，可以完成对路由权限的控制。
	-   搭配 @umijs/plugin-initial-state 插件和 @umijs/plugin-model 插件一起使用，可以拥有默认用户登陆信息的展示。

> 想要动态菜单？查看这里 [菜单的高级用法](https://beta-pro.ant.design/docs/advanced-menu-cn)

### `@umijs/plugin-locale`
- 国际化插件，用于解决 i18n 问题。
- 在`config.ts`中配置`locale`开启

###   `@umijs/plugin-model`
- 一种基于 `hooks` 范式的简易数据管理方案（部分场景可以取代 `dva`），通常用于中台项目的全局共享数据
- 在`src/models`目录下有hooks model时启用

### `@umijs/plugin-preact`
- 切换 react 为 preact。
- 默认开启，antd可能不兼容preact

### `@umijs/plugin-qiankun`
- 微前端插件
- 在`config.ts`中配置`qiankun`时开启


### `@umijs/plugin-request`
- `@umijs/plugin-request` 基于 [umi-request](https://github.com/umijs/umi-request) 和 [ahooks](http://ahooks.js.org/hooks) 的 `useRequest` 提供了一套统一的网络请求和错误处理方案。
- 默认开启，在`config.ts` 中配置`request`对象

### `@umijs/plugin-sass`
- 启用 sass 编译支持。
- 默认启用，在`config.ts`中配置`sass`对象
	- `implementation` 默认用`Dart Sass` 可以换为`node-sass`
	- `sassOptions`



# 项目运行打包
- umi命令在`umi/packages/preset-built-in/plugins/commands`中
- webpack的配置特性在`umi/packages/preset-built-in/plugins/features`中，对应webpack的部分配置项
## umi命令
- 命令列表
	- `build`
	- `config`
	- `dev`
	- `generate`
	- `help`
	- `plugin`
	- `version`
	- `webpack`

## umi dev
-  用`express`实现了一个server在`umi/packages/server`中
- watch了所有项目目录的文件的变更

## umi build
- 默认使用了webpack作为打包工具

## umi generate
- 目录下内置了几个生成器，同样是用模板渲染出来的

## umi webpack
- 读取webpack的配置


# umi扩展
## 数据Mock
- 生效条件`process.env.MOCK !== 'none'`
### mock文件路径
- `mock/**/*.[jt]s`
- `**/_mock.[jt]s`
- `umirc.mock.js`
- `umirc.mock.ts`


## openapi集成
- 在`@umijs/plugins/plugin-openapi`中实现
- 外部引用了 `@umijs/openapi` [chenshuai2144](https://github.com/chenshuai2144)/**[openapi2typescript](https://github.com/chenshuai2144/openapi2typescript)**
- 自动根据openapi的配置，生成相应的ts 请求文件，如果在`config/config.ts`中配置了对应的`mock:true`的话，还会生成对应的mock文件

## 网络请求
- 通过`@umijs/plugin-request`来实现，通过[umijs](https://github.com/umijs)/**[umi-request](https://github.com/umijs/umi-request)**  最终表达
- 最终是通过`isomorphic-fetch`这个包来实现的，也就是封装了fetch API

## 代理服务器
- 现在市面上所有的脚手架都提供了 [proxy](https://webpack.js.org/configuration/dev-server/#devserverproxy) 的能力，底层基于 [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware), 这个包可以把所有符合正则匹配的请求转发到某个地址
- umi也是通过`@umijs/deps/combiles/http-proxy-middleware`来实现的


# API调用
- 在`umi/packages/runtime`中实现

## 来自React库的内容`react-router-dom`
- Link
- MatchPath
- MemoryRouter
- NavLink
- Prompt
- Redirect
- Route
- Router
- StaticRouter
- Switch
- useHistory
- useLocation
- useParams
- useRouteMatch
- withRouter 