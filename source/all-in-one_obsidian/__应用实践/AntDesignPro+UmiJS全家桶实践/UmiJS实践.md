---
alias: umi
---

# 简介

Umi 是面向企业级开发的前端工具套件（官网上说是应用框架），经过了3000+项目的洗礼，使用插件化可扩展，包含了微前端、组件化，文档化，hooks，数据流，请求库封装等。
比起`create-react-app`来说，更加深度定制和封装了

umi 是一个 webpack 之上的整合工具。 umi 相比于 webpack 增加了运行时的能力，同时帮助我们配置了很多 webpack 的预设。也减少了 webpack 升级导致的问题。这也是我们能提供插件的原因。
如果不喜欢 umi 默认的配置，可以在这里看看有没有你喜欢的[配置](https://umijs.org/zh-CN/config)。如果还是不能满足就要自定义 webpack 了，[chainWebpack](https://umijs.org/zh-CN/config#chainwebpack) 可以自定义内置的 webpack 配置。
webpack 对于 node 也是有版本需求的，不同 webpack 对 node 版本的依赖也不同，所以最好的办法是升级到最新的[长期维护版本](https://nodejs.org/en/)。

## 限制条件（以下做不到）
-   需要支持 IE 8 或更低版本的浏览器
-   需要支持 React 16.8.0 以下的 React
-   需要跑在 Node 10 以下的环境中
-   有很强的 webpack 自定义需求和主观意愿
-   需要选择不同的路由方案


## 概念
- 配置，普通的配置信息，在Node环境中使用
- 运行时配置，页面相关的配置信息，在浏览器环境中使用

## 特有的处理
- `app.ts`  这个放在第一个，扩展运行时能力，修改路由，修改render方法等都在这里
- 配置文件 `.umirc.ts`(非复杂项目推荐使用) 或者  `config/config.ts`（复杂项目用个config目录来定义各个配置）
	- 本地临时配置`.umirc.local.ts` 会和`.umirc.ts`做逐层合并，并只在`umi dev`时有效，同理，config下面的`config.local.ts`, `config.test.ts`.`config.dev.ts` 等等类似
	- `.local.ts` 本地验证临时用，优先级比 `UMI_ENV`更高
- `.env`   配置环境变量
- `package.json` 中的以 `@umijs/preset-`、`@umijs/plugin-`、`umi-preset-` 和 `umi-plugin-` 开头的依赖会被自动注册为插件或插件集
- src 下有.umi 相关的目录，这个是umi 临时的文件目录
- `layouts/index.tsx`  约定式路由时的全局布局文件
- `pages` 所有的路由及其组件


# 实战应用
## 编译提速
- https://umijs.org/zh-CN/guide/boost-compile-speed
- 
## 运行时相关配置  在 `src/app.tsx`中导出方法 
### 修改ClientRender参数 modifyClientOpts(fn)
- `export function modifyClientRenderOpts(memo)`

### 修改路由 patchRoutes({routes})
- `export function patchRoutes({routes})` 
- 直接修改 不需要返回值
- 应用场景
	- 从后台接口获取路由配置

### 覆写render  render(oldRender: Function)
- `export function render(oldRender)`
- 应用场景
	- 渲染之前做权限校验

### 监听初始加载和路由切换 onRouteChange({routes, matchedRoutes,location,action})
- `export function onRouteChange(routes,matchedRoutes,location,action)`
- 应用场景
	- 做埋点统计
	- 设置页面标题

### 修改交给react-dom渲染时的根组件 rootContainer(LastRootContainer, args)
- `export function rootContainer(lastRootContainer,args)`
- args包含`routes`,`plugin`,`history`
- 应用场景
	- 外面包装一个Provider
-
### SSR修改预取数据 modifyGetInitialPropsCtx(ctx)
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

### dva运行时配置
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
### 初始化数据
```js
// src/app.ts

export async function getInitialState() {

 const data = await fetchXXX();

 return data;

}

```

### layout配置
```js
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

###  国际化配置
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

### qiankun子应用配置
```js
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
  // 支持更多的其他配置，详细看这里 https://qiankun.umijs.org/zh/api/#start-opts
}));
```

### 微应用配置
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

### request 相关配置
```js
// src/app.ts

import { RequestConfig } from 'umi';

export const request: RequestConfig = {
  timeout: 1000,
  errorConfig: {},
  middlewares: [],
  requestInterceptors: [],
  responseInterceptors: [],
};
```
- 该配置返回一个对象。除了 `errorConfig` 和 `middlewares` 以外其它配置都是直接透传 [umi-request](https://github.com/umijs/umi-request) 的全局配置。
- errorConfig.adaptor 当后端接口不满足该规范的时候你需要通过该配置把后端接口数据转换为该格式，该配置只是用于错误处理，不会影响最终传递给页面的数据格式。
```js
import { RequestConfig } from 'umi';

export const request: RequestConfig = {
  errorConfig: {
    adaptor: (resData) => {
      return {
        ...resData,
        success: resData.ok,
        errorMessage: resData.message,
      };
    },
  },
};
```
- errorConfig.errorPage 当 `showType` 为 `9` 时，默认会跳转到 `/exception` 页面，你可以通过该配置来修改该路径。
- middlewares umi-request 提供[中间件机制](https://github.com/umijs/umi-request#middleware)，之前是通过 `request.use(middleware)` 的方式引入，现在可以通过 `request.middlewares` 进行配置。
```js
export const request = {
  middlewares: [
    async function middlewareA(ctx, next) {
      console.log('A before');
      await next();
      console.log('A after');
    },
    async function middlewareB(ctx, next) {
      console.log('B before');
      await next();
      console.log('B after');
    }
  ]
}
```
- requestInterceptors 该配置接收一个数组，数组的每一项为一个 request 拦截器。等同于 umi-request 的 `request.interceptors.request.use()`。具体见 umi-request 的[拦截器文档](https://github.com/umijs/umi-request#interceptor)。
- responseInterceptors 该配置接收一个数组，数组的每一项为一个 response 拦截器。等同于 umi-request 的 `request.interceptors.response.use()`。具体见 umi-request 的[拦截器文档](https://github.com/umijs/umi-request#interceptor)。

## 路由配置
### history对象
-  配置的type 可以为`hash`,`browser`,`memory`
	
### 路由相关的属性
- path  字符串
- component  location和path匹配后渲染的组件，
	- 可以是相对路径，也可以是绝对路径，相对路径从`src/pages`开始找
	- 当然也可以用alias 的 `@`
- exact 是否严格匹配
- routes 配置子路由，通常是要给多个路径增加`layout`组件时候用
	- 配置`layout`后，要在`layout`组件中渲染 `props.children`
- redirect 重定向 比如配置首页
- wrappers 配置路由的高阶组件封装，可以理解为装饰器
	- 比如配置权限校验
- title 路由标题

### 路由操作
#### 页面跳转
- history对象
	- 配置的type 可以为`hash`,`browser`,`memory`
	- history.push 
		- 参数可以为字符串 `/list?a=1`
		- 参数可以为对象
			- pathname
			- query
	- history.goBack
- Link组件
	- to 属性
	- 只用于内部跳转，外部跳转要用`a`标签

### 路由组件参数 可从props中获取
-  match，当前路由和 url match 后的对象，包含 `params`、`path`、`url` 和 `isExact` 属性
-   location，表示应用当前处于哪个位置，包含 `pathname`、`search`、`query` 等属性
-   history，同 [api#history](https://umijs.org/zh-CN/api#history) 接口
-   route，当前路由配置，包含 `path`、`exact`、`component`、`routes` 等
-   routes，全部路由信息

### 传参给子路由
- React.cloneElement 


## 约定式路由-免配置自动生成
- 在没有配置 routes的情况下，umi会从`src/pages`中自从生成

### 路由生成规则（排除以下规则外的内容）
-  以 `.` 或 `_` 开头的文件或目录
-  以 `d.ts` 结尾的类型定义文件
-  以 `test.ts`、`spec.ts`、`e2e.ts` 结尾的测试文件（适用于 `.js`、`.jsx` 和 `.tsx` 文件）
-   `components` 和 `component` 目录
-   `utils` 和 `util` 目录
-   不是 `.js`、`.jsx`、`.ts` 或 `.tsx` 文件
-   文件内容不包含 JSX 元素


### 动态路由
- 以`[]`包裹的文件或文件夹为动态路由
- 比如`pages/users/[id].tsx` 为`/users/:id` 路由对应的文件

### 动态可选路由
- 约定 `[ $]` 包裹的文件或文件夹为动态可选路由，类似动态路由

### 嵌套路由
- 目录下有`_layout.tsx`时会生成嵌套路由，并以这个文件为layout
- layout需要返回一个React组件，并通过`props.children`渲染子组件

### 全局layout
- `src/layouts/index.tsx` 为全局的layout，同样要返回一个React组件，并通过`props.children`渲染子组件
- 也可以在这个全局layout中做判断，给不同路由不同的layout，无法通过配置实现

### 404路由
- `src/pages/404.tsx` 需要返回React组件

### 权限路由
- 通过指定高阶组件 `wrappers`实现，配置路由组件的`wrappers`属性

### 扩展路由属性
- 添加路由组件的属性

## 静态资源
### CSS 
- 使用`LESS` 作为解析语言，可以通过chainWebpack配置或者用umi插件的方式支持其他解析比如SASS
- 全局样式约定在`src/global.less`
- 自动识别CSS Modules
```css
// CSS Modules
import styles from './foo.css';
// 非 CSS Modules
import './foo.css';
```
- FAQ 里面说安装`node-sass sass-loader`然后配置`sass:{}`即可
### 图片
- 图片在CDN可以用绝对路径引用
- 图片在项目里用相对路径引用
	- 相对路径引用的时候会根据`publicPath`引入绝对路径
- 通过相对路径引入图片的时候（也就是图片在项目里的时候），如果图片小于10K，会被编译为Base64，否则为独立的图片文件，可以通过inlineLimit配置修改
#### JS中用图片
- 使用相对路径或者alias直接require
- 如果是svg 可以通过 import 的方式直接当做组件使用

#### CSS中使用图片
- 通过相对路径或则alias引用路径，alias用的时候要加`~`前缀，这些都是webpack的规则


## 按需加载
- 默认是关闭的
- 可以通过`dynamicImport`开启

### 按需加载组件
- 使用`dynamic`封装异步组件
```javascript
import { dynamic } from 'umi';
export default dynamic({
 loader: async function() {
 // 这里的注释 webpackChunkName 可以指导 webpack 将该组件 HugeA 以这个名字单独拆出去
 const { default: HugeA } = await import(/* webpackChunkName: "external_A" */ './HugeA');
 return HugeA;
 },
});
```
- 使用封装的异步组件
```javascript
import React from 'react';
import AsyncHugeA from './AsyncHugeA';
// 像使用普通组件一样即可
// dynamic 为你做:
// 1. 异步加载该模块的 bundle
// 2. 加载期间 显示 loading（可定制）
// 3. 异步组件加载完毕后，显示异步组件
export default () => {
 return <AsyncHugeA />;
}
```

## 页面模板 
### 默认模板
- `src/pages/document.ejs`

### 模板参数
- 通过`context`参数来获取
- context 属性
	- `route` 路由信息，需要打包出多个静态HTML时（配置了exportStatic时）有效
	- `config` 用户配置的信息

## 部署配置
### 路由在非根目录
- 比如开发环境共享服务器，部署到非根目录
- 通过修改`base`配置，这个base会加到路由上面，影响的是`history`模式的路由配置
- 可以直接直接设置hash模式来解决部署的问题，因为不存在服务端解析路由的问题

### 静态资源在非根目录或CDN
- 修改`publicPath`，调整产物中静态资源的引用路径，一般需要配合路由和部署环境一同修改

### 动态逻辑判断 runtimePublicPath
```html
<script>
window.publicPath = <%= YOUR PUBLIC_PATH %>
</script>
```

### 静态化
- 在一些场景中无法做服务端的html fallback，让每个路由都输出index.html内容，就是做静态化
- 配置`exportStatic`即可产生每个路由对应的html
- 静态化后输出到任意路径，配置`dynamicRoot:true`

### HTML后缀
- 在像支付宝的容器环境中，不会自动读取索引文件（目录下的index.html），需要直接生成html
- 配置`htmlSuffix:true`，直接根据目录名生成html(少一层index解析)

## 服务端渲染（SSR）
### 适用场景
- 有SEO诉求，用在搜索引擎检索及社交分享，用在前台类应用
- 首屏渲染时长有要求，常用在移动端、弱网情况下

### 预渲染
- 服务端渲染时机是在服务端，用户访问时渲染
- 预渲染的时机是在项目构建时，用户访问时数据不一定是最新的
- 预渲染适合静态站点生成

### umi SSR优势
- umi dev本就是SSR预览，开发调试方便
- umi不耦合服务端框架，可以简单集成框架或者Serverless
- 支持应用级和页面级数据预取
- 支持按需加载
- 支持渲染降级
- 支持流式渲染
- 兼容客户端动态加载
- SSR功能插件化
### 启用SSR
- 配置`ssr:true`
- 如果跟后端框架一起用时，可以通过配置关闭umi dev服务端渲染`ssr:{devServerRender:false}` 默认为`true`

### 数据预取
- 页面级数据预取方法，使用组件上`getInitialProps`静态方法，执行和将结果注入到`props`中
- `getInitialProps`中有几个固定参数
	-  `match`： 与客户端页面 props 中的 `match` 保持一致，有当前路由的相关数据。
	-   `isServer`：是否为服务端在执行该方法。
	-   `route`：当前路由对象
	-   `history`：history 对象
- 可以通过插件或应用来扩展ctx对象，使用`modifyGetInitialPropsCtx` 方法来做，在应用中就在`app.ts`

#### SSR 页面级数据预取`getInitialProps`
```js
app.use(async (req, res) => {
 // 或者从 CDN 上下载到 server 端
 // const serverPath = await downloadServerBundle('http://cdn.com/bar/umi.server.js');
 const render = require('./dist/umi.server');
 res.setHeader('Content-Type', 'text/html');
 const context = {};
 const { html, error, rootContainer } = await render({
 // 有需要可带上 query
 path: req.url,
 context,
 getInitialPropsCtx: {
 req,
 },
 });
})
```
- 关于 `getInitialProps` 执行逻辑和时机，这里需要注意：
	-   开启 ssr，且执行成功
	    -   未开启 `forceInitial`，首屏不触发 `getInitialProps`，切换页面时会执行请求，和客户端渲染逻辑保持一致。
	    -   开启 `forceInitial`，无论是首屏还是页面切换，都会触发 `getInitialProps`，目的是始终以客户端请求的数据为准。（有用在静态页面站点的实时数据请求上）
	-   未开启 ssr 时，只要页面组件中有 `getInitialProps` 静态方法，则会执行该方法。

### SSR部署
- 启用服务端渲染后，会生成`umi.server.js`
- 在服务端框架中引用这个生成的文件进行渲染
```js
// Express
app.use(async (req, res) => {
 // 或者从 CDN 上下载到 server 端
 // const serverPath = await downloadServerBundle('http://cdn.com/bar/umi.server.js');
 const render = require('./dist/umi.server');
 res.setHeader('Content-Type', 'text/html');
 const context = {};
 const { html, error, rootContainer } = await render({
 // 有需要可带上 query
 path: req.url,
 context,
 // 可自定义 html 模板
 // htmlTemplate: defaultHtml,
 // 启用流式渲染
 // mode: 'stream',
 // html 片段静态标记（适用于静态站点生成）
 // staticMarkup: false,
 // 扩展 getInitialProps 在服务端渲染中的参数
 // getInitialPropsCtx: {},
 // manifest，正常情况下不需要
 });
 // support stream content
 if (content instanceof Stream) {
 html.pipe(res);
 html.on('end', function() {
 res.end();
 });
 } else {
 res.send(res);
 }
})
```
- render方法参数
```js
{
 // 渲染页面路由，支持 `base` 和带 query 的路由，通过 umi 配置
 path: string;
 // 可选，初始化数据，传透传到 getInitialProps 方法的参数中
 initialData?: object;
 // 可选，html 模板，这里可自定义模板，默认是用 umi 内置的 html
 htmlTemplate?: string;
 // 可选，页面内容挂载节点，与 htmlTemplate 配合使用，默认为 root
 mountElementId?: string;
 // 上下文数据，可用来标记服务端渲染页面时的状态
 context?: object
 // ${protocol}://${host} 扩展 location 对象
 origin?: string;
}

```
- render方法返回值
```js
{
 // html 内容，服务端渲染错误后，会返回原始 html
 html?: string | Stream;
 // 挂载节点中的渲染内容（ssr 渲染实际上只是渲染挂载节点中的内容），同时你也可以用该值来拼接自定义模板
 rootContainer: string | Stream;
 // 错误对象，服务端渲染错误后，值不为 null
 error?: Error;
}
```

### 动态加载兼容
- 和客户端动态加载兼容
```js
// .umirc.ts

export default {
 ssr: {
    mode: 'stream', // 流式渲染
 },
 dynamicImport: {},
}
```
- 使用动态加载后，启动和构建会自动开启`manifest`配置，并在产物中生成`asset-manifest.json`做资源映射，并自动将页面对应的资源注入到HTML中，避免开启动态加载后，页面首屏闪烁的问题。

### SSR+预渲染
```js
export default {
 ssr: {},
 exportStatic: {},
}
```

### 预渲染动态路由
- 默认情况下，预渲染后会删除 `umi.server.js` 服务端入口文件，如果需要保留，可使用变量 `RM_SERVER_FILE=none` 来保留 `umi.server.js`
```js
export default {
  ssr: {},
  exportStatic: {
+ extraRoutePaths: async () => {
+ // const result = await request('https://your-api/news/list');
+ return Promise.resolve(['/news/1', 'news/2']);
+ }
  },
  routes: [
    {
      path: '/',
      component: '@/layout',
      routes: [
        { path: '/', component: '@/pages/index' },
        { path: '/news', component: '@/pages/news' },
        { path: '/news/:id', component: '@/pages/news/detail' }
      ]
    }
  ]
}
```

### 页面标题渲染
- [@umijs/preset-react](https://umijs.org/zh-CN/plugins/preset-react#umijspreset-react) 插件集中已内置对标题的渲染，通过以下步骤使用：

- 安装：`yarn add @umijs/preset-react`

- 在页面中，即直接可以渲染标题：

```js
// pages/bar.tsx
import React from 'react';
import { Helmet } from 'umi';
export default props => {
 return (
 <>
 {/* 可自定义需不需要编码 */}
 <Helmet encodeSpecialCharacters={false}>
 <html lang="en" data-direction="666" />
 <title>Hello Umi Bar Title</title>
 </Helmet>
 </>
 );
};
```

### dumi 文档化
```js
export default {
 ssr: {},
 exportStatic: {},
}
```

### 结合dva使用
- [@umijs/preset-react](https://umijs.org/zh-CN/plugins/preset-react#umijspreset-react) 插件集中已内置 dva，通过以下步骤使用：
- 安装`yarn add @umijs/preset-react`
- 配置
```js
export default {
 ssr: {},
 dva: {}
}
```
- 这时候 `getInitialProps(ctx)` 中的 `ctx` 就会有 `store` 属性，可执行 `dispatch`，并返回初始化数据。
```js
Page.getInitialProps = async (ctx) => {
  const { store } = ctx;
  store.dispatch({
    type: 'bar/getData',
  });
  return store.getState();
}
```

## 插件
- 插件id 和插件key
	- id 为路径的简写
	- key 为进一步简化后的配置
### 启用插件的方式
- 在`package.json` 中配置的umi插件，`dependencies` 和 `devDependencies` 会被自动检测
- 在配置中可以用 `presets`和`plugins` 进行设置，这里一般配置项目相对路径的插件，非npm包入口文件的插件，如果配置了 npm包插件会报重复注册的错误
- 环境变量`UMI_PRESETS` 和`UMI_PLUGINS`注册额外插件

### 检查插件注册情况
- `umi plugin list`
- `umi plugin list --key`
- 在插件中感知其他插件 `api.hasPlugins(pluginId[])` 和`api.hasPresets(pluginId[])`

### 禁用插件的方式
- 配置插件key 为false
- 在插件里禁用其他插件 `api.skitPlugins(pluginId[])`

### 配置插件
- 通过插件的key来配置


## Mock数据
- `/mock`文件夹下所有的文件为mock文件
- 可以集成 mock.js
### Mock格式
```javascript
	export default {
	 // 支持值为 Object 和 Array
	 'GET /api/users': { users: [1, 2] },
	 // GET 可忽略
	 '/api/users/1': { id: 1 },
	 // 支持自定义函数，API 参考 express@4
	 'POST /api/users/create': (req, res) => {
	 // 添加跨域请求头
	 res.setHeader('Access-Control-Allow-Origin', '*');
	 res.end('ok');
	 },
	}
```
### 关闭Mock
- 配置 `mock:false`
- 通过MOCK=none 环境变量


## 环境变量
### 设置环境变量方式
- 执行命令时添加 `PORT=3000 umi dev` 考虑多平台兼容，可以用`cross-env`
- 在.env 文件中定义

### 环境变量列表
- `APP_ROOT` 不能在.env 中配置，只能在命令行添加
- `ANALYZE` 默认关闭
- `ANALYZE_SSR` 默认关闭
- `BABEL_CACHE` 值为none时禁用缓存
- `BABEL_POLYFILL` 默认会根据模板浏览器的全量补丁设置为node时禁用
- `COMPRESS` 默认压缩CSS和JS 设置为none时不压缩
- `FORK_TS_CHECKER` 默认不开启TypeScript类型检查，值为1是启用
- `FRIENDLY_ERROR` 设置为none时禁用
- `HTTPS` 
- `HMR` 设置为none时禁用代码热更新
- `HTML` 设置为none时不输出html
- `HOST`默认`0.0.0.0`
- `PORT` 默认`8000`
- `PROGRESS` 设置为none时禁用进度条
- `SOCKET_SERVER` 指定用于HMR的socket服务器
- `SPEED_MEASURE` 分析webpack编译时间，`CONSOLE`和`JSON`两种形式 默认为`JSON`
- `TERSER_CACHE` 默认开启 Terser压缩缓存，值为none时禁用缓存
- `UMI_ENV` 指定不同环境各自的配置文件
- `WATCH` 设置为 none时不监听文件变更
- `WATCH_IGNORED` 默认不监听`node_modules`下的文件修改，如果需要可以通过此项设置
- `WEBPACK_FS_CACHE` 禁用webpack 5的物理缓存
- `WEBPACK_PROFILE` 生成umi构建性能分析文件，结合 [Webpack Xray](https://akx.github.io/webpack-xray) 或 [Webapck Analyse](http://webpack.github.io/analyse) 进行分析，`WEBPACK_PROFILE` 值有 `verbose`、`normal`、`minimal`
- `RM_SERVER_FILE` 预渲染下，默认会删除服务端渲染文件`umi.server.js` 配置为none不删除


## 编译加速MSFU（Module Federation Speed Up）
- 开启 `mfsu:{}`
- 开启生产环境的`mfsu:{production:{}}`

## umi 命令行
###  umi build
- 默认产物在`dist`文件夹，可以通过修改`outputPath`指定产物的目录
- 默认会将`public`目录文件夹内所有的文件原样拷贝到`dist`目录（outputPath）如果不需要可以用`chainWebpack`来删除
- 拷贝文件中有跟产物同名的会覆盖产物
```javascript
export default {
 chainWebpack(memo, { env, webpack }) {
 // 删除 umi 内置插件
 memo.plugins.delete('copy');
 }
}
```
### umi build
- 开发服务器，监听源文件变化，自动热加载
- 默认`8000`端口，如果占用会继续往后找

### umi generate/umi g
- `umi generate <type> <name> [options]`
- 内置可以生成页面 `umi g page xxx`
- 可以通过`api.registerGenerator` 注册自定义的生成器
```javascript
import { Generator, IApi } from 'umi';
const createPagesGenerator = function ({ api }: { api: IApi }) {
 return class PageGenerator extends Generator {
 constructor(opts: any) {
 super(opts);
 }
 async writing() {}
 };
}

api.registerGenerator({
 key: 'pages',
 Generator: createPageGenerator({ api }),
});
```
### umi plugin
- 查看当前项目用到的插件 `umi plugin list`
- `umi plugin list --key`

### umi help

### umi version

### umi webpack
- 查看umi使用的webpack配置
- 可选参数
	- rules 
	- rule=[name]
	- plugins
	- plugin=[name]


## 调试umi
- `node --inspect-brk ./node_modules/.bin/umi test`


##  自定义配置
- 使用chainWebpack 定义配置，比如合并css
```js
export default {
  dynamicImport: {
  },
  chainWebpack(config) {
    config.optimization.splitChunks({
      cacheGroups: {
        styles: {
          name: 'styles',
          test: /\.(css|less)$/,
          chunks: 'async',
          minChunks: 1,
          minSize: 0,
        }
      },
    });
  },
}
```
## 插件开发单元测试
-   [@umijs/test](https://www.npmjs.com/package/@umijs/test)，测试脚本，内置 `jest` 测试框架
-   [@testing-library/react](https://testing-library.com/docs/react-testing-library/example-intro)，React 组件测试工具
-   [puppeteer](https://github.com/puppeteer/puppeteer)，Headless 浏览器工具，用于 E2E 测试。


# 配置项
## 404 
- 默认true，用`src/pages/404.tsx`
## alias
-  `@`，项目 src 目录
-   `@@`，临时目录，通常是 `src/.umi` 目录
-   `umi`，当前所运行的 umi 仓库目录
-   `react-router` 和 `react-router-dom`，底层路由库，锁定版本，打包时所有依赖了他们的地方使用同一个版本
-   `react` 和 `react-dom`，默认使用 `16.x` 版本，但如果项目里有依赖，会优先使用项目中依赖的版本

## analyze
- 包模块结构分析工具 通过`ANALYZE=1 环境变量开启
```json
{
  // 配置具体含义见：https://github.com/umijs/umi-webpack-bundle-analyzer#options-for-plugin
  analyze: {
    analyzerMode: 'server',
    analyzerPort: 8888,
    openAnalyzer: true,
    // generate stats file while ANALYZE_DUMP exist
    generateStatsFile: false,
    statsFilename: 'stats.json',
    logLevel: 'info',
    defaultSizes: 'parsed', // stat  // gzip
  }
}
```

## autoprefixer
- 默认`{ flexbox: 'no-2009' }`
- https://github.com/postcss/autoprefixer#options
- 不要设置 `overrideBrowserslist`，此配置被内部接管，通过 `targets` 配置项选择你要兼容的浏览器

## base
- 默认`/`
- 设置路由前缀，通常用于部署到非根目录,比如，你有路由 `/` 和 `/users`，然后设置了 base 为 `/foo/`，那么就可以通过 `/foo/` 和 `/foo/users` 访问到之前的路由

## chainWebpack
- 通过 [webpack-chain](https://github.com/mozilla-neutrino/webpack-chain) 的 API 修改 webpack 配置
```js
export default {
  chainWebpack(memo, { env, webpack, createCSSRule }) {
    // 设置 alias
    memo.resolve.alias.set('foo', '/tmp/a/b/foo');

    // 删除 umi 内置插件
    memo.plugins.delete('progress');
    memo.plugins.delete('friendly-error');
    memo.plugins.delete('copy');
  },
};```
- 支持异步
```js
export default {
  async chainWebpack(memo) {
    await delay(100);
    memo.resolve.alias.set('foo', '/tmp/a/b/foo');
  },
};```
- SSR时同样适用
```js
import { BundlerConfigType } from 'umi';

export default {
  chainWebpack(memo, { type }) {
    // 对 ssr bundler config 的修改
    if (type === BundlerConfigType.ssr) {
      // 服务端渲染构建扩展
    }

    // 对 csr bundler config 的修改
    if (type === BundlerConfigType.csr) {
      // 客户端渲染构建扩展
    }

    // ssr 和 csr 都扩展
  },
};```
- 可选参数
	- -   memo，当前 webpack-chain 对象
	-   env，当前环境，`development`、`production` 或 `test` 等
	-   webpack，webpack 实例，用于获取其内部插件
	-   createCSSRule，用于扩展其他 CSS 实现，比如 sass, stylus
	-   type，当前 webpack 实例类型，默认走 csr，如果开启 ssr，会有 ssr 的 webpack 实例

## chunks
- 默认 ['umi'],可修改，比如做了 vendors 依赖提取之后，会需要在 `umi.js` 之前加载 `vendors.js`
```js
export default {
  chunks: ['vendors', 'umi'],
  chainWebpack: function (config, { webpack }) {
    config.merge({
      optimization: {
        splitChunks: {
          chunks: 'all',
          minSize: 30000,
          minChunks: 3,
          automaticNameDelimiter: '.',
          cacheGroups: {
            vendor: {
              name: 'vendors',
              test({ resource }) {
                return /[\\/]node_modules[\\/]/.test(resource);
              },
              priority: 10,
            },
          },
        },
      },
    });
  },
};```

## cssLoader
- 默认`{}`
- https://github.com/webpack-contrib/css-loader#options
- 自动转驼峰的配置
```js
{
  cssLoader: {
    localsConvention: 'camelCase',
  }
}

import React from 'react';

import styles from './index.less'; // .bar-foo { font-size: 16px; }

export default () => <div className={styles.barFoo}>Hello</div>;

// => <div class="bar-foo___{hash}">Hello</div>

```

## cssModulesTypescriptLoader
- 对照css modules方式引入的css或者less等样式文件，自动生成ts类型定义文件
- 默认 `undefined`
```js
export default {
  cssModulesTypescriptLoader: {},
};


export default {
  cssModulesTypescriptLoader: {
    mode: 'emit',
  },
};
```

## cssnano
- 默认 ``{ mergeRules: false, minifyFontValues: { removeQuotes: false } }`
- https://cssnano.co/docs/optimisations/

## copy
- 默认 []
- 支持二元组配置和`from` `to` 属性配置

## define
- 默认 `{}`
- 给代码中定义全局变量，会经过一次JSON.stringify

## devServer
- 默认 `{}`
- 配置项
	-   port，端口号，默认 `8000`
	-   host，默认 `0.0.0.0`
	-   https，是否启用 https server，同时也会开启 HTTP/2
	-   writeToDisk，生成 `assets` 到文件系统
- 启用 port 和 host 也可以通过环境变量 PORT 和 HOST 临时指定。


## devtool
- 默认 `cheap-module-source-map` in dev, `false` in build
- 配置选项
	-  eval，最快的类型，但不支持低版本浏览器，如果编译慢，可以试试
	-   source-map，最慢最全的类型
- https://webpack.js.org/configuration/devtool/#devtool

## dynamicImport
- 默认 false 只生成一个css一个js
- 启用之后，需要考虑 publicPath 的配置，可能还需要考虑 runtimePublicPath，因为需要知道从哪里异步加载 JS、CSS 和图片等资源
- 这里的 `p__users_index.js` 是路由组件所在路径 `src/pages/users/index`，其中 `src` 会被忽略，`pages` 被替换为 `p`。
- 配置子项
	- loading 字符串，指向loading组件的文件路径，可以用alias

## dynamicImportSyntax
- 默认 false
- 如果你不需要路由按需加载，只需要支持 `import()` 语法的 code splitting，可使用此配置

## exportStatic
- 配置 html 的输出形式，默认只输出 `index.html`
- 如果需要预渲染，请开启 [ssr](https://umijs.org/zh-CN/config#ssr-32) 配置，常用来解决没有服务端情况下，页面的 SEO 和首屏渲染提速
- 如果开启 `exportStatic`，则会针对每个路由输出 html 文件
- 可选配置项
	-  htmlSuffix，启用 `.html` 后缀，少掉目录index这一层`user/index.html` 变为`user.html`。
	-  dynamicRoot，部署到任意路径。
	-  extraRoutePaths，生成额外的路径页面，用法和场景见 [预渲染动态路由](https://umijs.org/zh-CN/docs/ssr#%E9%A2%84%E6%B8%B2%E6%9F%93%E5%8A%A8%E6%80%81%E8%B7%AF%E7%94%B1)
- 若有 [SEO](https://baike.baidu.com/item/%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%E4%BC%98%E5%8C%96/3132?fromtitle=SEO&fromid=102990) 需求，可开启 [ssr](https://umijs.org/zh-CN/config#ssr) 配置，在 `umi build` 后，会路由（除静态路由外）渲染成有具体内容的静态 html 页面，例如如下路由配置：
```js
// .umirc.ts | config/config.ts
{
  routes: [
    {
      path: '/',
      component: '@/layouts/Layout',
      routes: [
        { path: '/', component: '@/pages/Index' },
        { path: '/bar', component: '@/pages/Bar' },
        { path: '/news', component: '@/pages/News' },
        { path: '/news/:id', component: '@/pages/NewsDetail' },
      ],
    },
  ];
}```

- 考虑到预渲染后，大部分不会再用到 `umi.server.js` 服务端文件，构建完成后会删掉 `umi.server.js`  文件如果有调试、不删除 server 文件需求，可通过环境变量 `RM_SERVER_FILE=none` 来保留

## external
- 默认`{}`
- 设置哪些模块可以不被打包，通过 `<script>` 或其他方式引入，通常需要和 scripts 或 headScripts 配置同时使用。
```js
export default {
  externals: {
    react: 'window.React',
  },
  scripts: ['https://unpkg.com/react@17.0.1/umd/react.production.min.js'],
};
``` 
- 简单理解的话，可以理解为 `import react from 'react'` 会被替换为 `const react = window.React`

## extraBabelIncludes
- 默认  `[]`
- 配置额外需要做 babel 编译的 npm 包或目录
```js
export default {
  extraBabelIncludes: [
    // 支持绝对路径
    join(__dirname, '../../common'),

    // 支持 npm 包
    'react-monaco-editor',
  ],
};
```

## extraBabelPlugins
- 默认 `[]`
- 配置额外的 babel 插件。
```js
export default {
  extraBabelPlugins: ['babel-plugin-react-require'],
};
```

## extraBabelPresets
- 默认 `[]`
- 配置额外的 babel 插件集。

## extraPostCSSPlugins
- 默认 `[]`
- 配置额外的 [postcss 插件](https://github.com/postcss/postcss/blob/master/docs/plugins.md)。

## favicon
- 图片要相对于public路径
```html
<link rel="shortcut icon" type="image/x-icon" href="/assets/favicon.ico" />
```

## forkTSChecker
- 开启 TypeScript 编译时类型检查，默认关闭

## fastRefresh
- 快速刷新（Fast Refresh），开发时可以**保持组件状态**，同时编辑提供**即时反馈**。[文档](https://umijs.org/zh-CN/docs/fast-refresh)

## hash
- 默认 false 实际测试看默认是为true
- 配置是否让生成的文件包含 hash 后缀，通常用于增量发布和避免浏览器加载缓存。
- html文件始终没有hash

## headScript
- 默认 `[]`
- 配置 `<head>` 里的额外脚本，数组项为字符串或对象
```js
export default {
  headScripts: [`alert(1);`, `https://a.com/b.js`],
};

export default {
  headScripts: [
    { src: '/foo.js', defer: true },
    { content: `alert('你好');`, charset: 'utf-8' },
  ],
};
```

## history
- 默认 `{ type: 'browser' }`
- 配置 [history 类型和配置项](https://github.com/ReactTraining/history/blob/master/docs/getting-started.md)。包含以下子配置项：
	-   type，可选 `browser`、`hash` 和 `memory`
	-   options，传给 create{{{ type }}}History 的配置项，每个类型器的配置项不同
		-   options 中，`getUserConfirmation` 由于是函数的格式，暂不支持配置
		-   options 中，`basename` 无需配置，通过 umi 的 `base` 配置指定

## ignoreMomentLocale
- 默认 false
- 忽略 moment 的 locale 文件，用于减少尺寸

## inlineLimit
- 默认 `10000` (10k)
- 配置图片文件是否走 base64 编译的阈值。默认是 10000 字节，少于他会被编译为 base64 编码，否则会生成单独的文件

## lessLoader
- 默认 `{}`
- 设置 [less-loader 配置项](https://github.com/webpack-contrib/less-loader)。

## links
- 默认 `[]`
- 配置额外的 link 标签。

## manifest
- 配置是否需要生成额外用于描述产物的 manifest 文件，默认会生成 `asset-manifest.json`
-  只在 `umi build` 时会生成
- 包含以下子配置项：
	-   fileName，文件名，默认是 `asset-manifest.json`
	-   publicPath，默认会使用 webpack 的 `output.publicPath` 配置
	-   basePath，给所有文件路径加前缀
	-   writeToFileEmit，开发模式下，写 manifest 到文件系统中

## metas
- 默认 `[]`
- 配置额外的 meta 标签。数组中可以配置`key:value`形式的对象
```js
export default {
  metas: [
    {
      name: 'keywords',
      content: 'umi, umijs',
    },
    {
      name: 'description',
      content: '🍙 插件化的企业级前端应用框架。',
    },
    {
      bar: 'foo',
    },
  ],
};
```

```html
<meta name="keywords" content="umi, umijs" />

<meta name="description" content="🍙 插件化的企业级前端应用框架。" />

<meta bar="foo" />
```

## mfsu
- 默认 `{}`，实测默认没有开
- 开启 mfsu 功能并添加相关配置，开启该功能将会自动开启 `webpack5` 和 `dynamicImport`
- 包含以下子属性，
	-   development: `{ output: String }`。可以通过 output 自定义 dev 模式下的输出路径。用于将预编译文件同步到 git。
	-   production: `{ output: String }`。在生产模式中使用 mfsu。如果额外设置了 output，将会将生产模式预编译依赖编译到 output 下。
	-   mfName： `string`。指定预编译依赖的变量名，默认为 `mf`，比如可在 qiankun 主应用里配置
	-   exportAllMembers
	-   chunks: `string[]`。mfsu 阶段的 chunks 写死了 `['umi']`，可通过此配置项强行修改
	-   ignoreNodeBuiltInModules: `boolean`。配为 `true` 时不预编译 node 原生包，适用于 electron renderer

```json
{
	mfsu: {
	  development : {
	    output : "./.mfsu-dev",
	  },
	  production : {
	    output : "./.mfsu-prod",
	  }
	},
}	
```

## mock
- 默认 `{}` 
- 配置 mock 属性
- 包含以下子属性：
	-   exclude，格式为 `Array(string)`，用于忽略不需要走 mock 的文件


## mountElementId
- 默认 `root`
- 指定 react app 渲染到的 HTML 元素 id
- 如果需要把应用打包成 umd 包导出，需设置 mountElementId 为 `''`


## mpa
- 切换渲染模式为 mpa
- 包含以下特征：
	-   为每个页面输出 html
	-   输出不包含 react-router、react-router-dom、history 等库
	-   渲染和 url 解绑，html 文件放哪都能使用
- 注意：
	-   只支持一级路由配置
	-   不支持 layout 或嵌套路由的配置

## nodeModulesTransform
- 默认 `{ type: 'all' }`
- 设置 node_modules 目录下依赖文件的编译方式
- 子配置项包含：
	-   `type`，类型，可选 `all` 和 `none`
	-   `exclude`，忽略的依赖库，包名，暂不支持绝对路径

- 两种编译模式，
	-   默认是 `all`，全部编译，然后可以通过 `exclude` 忽略不需要编译的依赖库；
	-   可切换为 `none`，默认值编译 [es5-imcompatible-versions](https://github.com/umijs/es5-imcompatible-versions) 里声明的依赖，可通过 `exclude` 配置添加额外需要编译的；

前者速度较慢，但可规避常见的兼容性等问题，后者反之。


## outputPath
- 默认 dist
- 指定输出路径
- 不允许设定为 `src`、`public`、`pages`、`mock`、`config` 等约定目录


## plugins
- 默认 `[]`
- 配置额外的 umi 插件
- 数组项为指向插件的路径，可以是 npm 依赖、相对路径或绝对路径。如果是相对路径，则会从项目根目录开始找
```js
export default {
  plugins: [
    // npm 依赖
    'umi-plugin-hello',
    // 相对路径
    './plugin',
    // 绝对路径
    `${__dirname}/plugin.js`,
  ],
};
```
- 插件的参数平级的配置项声明，比如：
```js
export default {
  plugins: ['umi-plugin-hello'],
  hello: {
    name: 'foo',
  },
};
```
- 配置项的名字通常是插件名去掉 `umi-plugin-` 或 `@umijs/plugin` 前缀。

## polyfill
- 设置按需引入 polyfill，对应 core-js 的[引入范围](https://github.com/zloirock/core-js#commonjs-api)，默认全量引入
- 只引入稳定功能
```js
export default {
  polyfill: {
    imports: [
      'core-js/stable',
    ]
  }
}
```
- 自行按需引入
```js
export default {
  polyfill: {
    imports: [
      'core-js/features/promise/try',
      'core-js/proposals/math-extensions'
    ]
  },
}
```
- 设置 `BABEL_POLYFILL=none` 环境变量后，该配置失效，且无 polyfill 引入。

## postcssLoader
- 默认 `{}`
- 设置 [postcss-loader 配置项](https://github.com/postcss/postcss-loader#options)。

## presets
- 默认 `[]`
- 同 `plugins` 配置，用于配置额外的 umi 插件集

## proxy
- 默认 `{}`
- 配置代理能力,proxy 配置仅在 dev 时生效
```js
export default {
  proxy: {
    '/api': {
      'target': 'http://jsonplaceholder.typicode.com/',
      'changeOrigin': true,
      'pathRewrite': { '^/api' : '' },
    },
  },
}
```
- 然后访问 `/api/users` 就能访问到 [http://jsonplaceholder.typicode.com/users](http://jsonplaceholder.typicode.com/users) 的数据

## publicPath
- 默认 `/`
- 配置 webpack 的 publicPath。当打包的时候，webpack 会在静态文件路径前面添加 `publicPath` 的值，当你需要修改静态文件地址时，比如使用 CDN 部署，把 `publicPath` 的值设为 CDN 的值就可以。如果使用一些特殊的文件系统，比如混合开发或者 cordova 等技术，可以尝试将 `publicPath` 设置成 `./` 相对路径。
- 相对路径 `./` 有一些限制，例如不支持多层路由 `/foo/bar`，只支持单层路径 `/foo`
- 如果你的应用部署在域名的子路径上，例如 `https://www.your-app.com/foo/`，你需要设置 `publicPath` 为 `/foo/`，如果同时要兼顾开发环境正常调试，你可以这样配置：
```js
import { defineConfig } from 'umi';

export default defineConfig({
  publicPath: process.env.NODE_ENV === 'production' ? '/foo/' : '/',
});
```

## routes
- 配置路由。
- umi 的路由基于 [react-router@5](https://reacttraining.com/react-router/web/guides/quick-start) 实现，配置和 react-router 基本一致，详见[路由配置](https://umijs.org/zh-CN/docs/routing)章节。
```js
export default {
  routes: [
    {
      path: '/',
      component: '@/layouts/index',
      routes: [
        { path: '/user', redirect: '/user/login' },
        { path: '/user/login', component: './user/login' },
      ],
    },
  ],
};
```
-   `component` 的值如果是相对路径，会以 `src/pages` 为基础路径开始解析
-   如果配置了 `routes`，则优先使用配置式路由，且约定式路由会不生效

## runtimeHistory
- 配置是否需要动态变更 history 类型。
- 设置 runtimeHistory 后，可以在运行时动态修改 history 类型。
```js
import { setCreateHistoryOptions } from 'umi';

setCreateHistoryOptions({
  type: 'memory',
});
```

## runtimePublicPath
- 默认 false
- 配置是否启用运行时 publicPath
- 通常用于一套代码在不同环境有不同的 publicPath 需要，然后 publicPath 由服务器通过 HTML 的 `window.publicPath` 全局变量输出，启用后，打包时会额外加上这一段，`__webpack_public_path__ = window.resourceBaseUrl || window.publicPath;` 
- 然后 webpack 在异步加载 JS 等资源文件时会从 `window.resourceBaseUrl` 或 `window.publicPath` 里开始找。

## ssr
- 默认 `false`
- 配置是否开启服务端渲染，配置如下
```json
{
  // 一键开启
  ssr: {
    // 更多配置
    // forceInitial: false,
    // removeWindowInitialProps: false
    // devServerRender: true,
    // mode: 'string',
    // staticMarkup: false,
  }
}
```
- 配置说明：
	-   `forceInitial`：客户端渲染时强制执行 `getInitialProps` 方法，常见的场景：静态站点希望每次访问时保持数据最新，以客户端渲染为主。
	-   `removeWindowInitialProps`: HTML 中移除 `window.getInitialProps` 变量，避免 HTML 中有大量数据影响 SEO 效果，场景：静态站点
	-   `devServerRender`：在 `umi dev` 开发模式下，执行渲染，用于 umi SSR 项目的快速开发、调试，服务端渲染效果所见即所得，同时我们考虑到可能会与服务端框架（如 [Egg.js](https://eggjs.org/)、[Express](https://expressjs.com/)、[Koa](https://koajs.com/)）结合做本地开发、调试，关闭后，在 `umi dev` 下不执行服务端渲染，但会生成 `umi.server.js`（Umi SSR 服务端渲染入口文件），渲染开发流程交由开发者处理。
	-   `mode`：渲染模式，默认使用 `string` 字符串渲染，同时支持流式渲染 `mode: 'stream'`，减少 TTFB（浏览器开始收到服务器响应数据的时间） 时长。
	-   `staticMarkup`：html 上的渲染属性（例如 React 渲染的 `data-reactroot`），常用于静态站点生成的场景上。
- 注意：
	-   开启后，执行 `umi dev` 时，访问 [http://localhost:8000](http://localhost:8000/) ，默认将单页应用（SPA）渲染成 html 片段，片段可以通过开发者工具『显示网页源代码』进行查看。
	-   执行 `umi build`，产物会额外生成 `umi.server.js` 文件，此文件运行在 Node.js 服务端，用于做服务端渲染，渲染 html 片段。
	-   如果应用没有 Node.js 服务端，又希望生成 html 片段做 SEO（搜索引擎优化），可以开启 [exportStatic](https://umijs.org/zh-CN/config#exportstatic) 配置，会在执行 `umi build` 构建时进行**预渲染**。
	-   `removeWindowInitialProps` 与 `forceInitial` 不可同时使用	
- 了解更多，可点击 [服务端渲染文档](https://umijs.org/zh-CN/docs/ssr)。

## scripts
- 默认 `[]`
- 同 [headScripts](https://umijs.org/zh-CN/config#headscripts)，配置 `<body>` 里的额外脚本。

## singular
- 默认 `false`
- 配置是否启用单数模式的目录。
- 比如 `src/pages` 的约定在开启后为 `src/page` 目录，[@umijs/plugins](https://github.com/umijs/plugins) 里的插件也遵照此配置的约定。

## styleLoader
- 启用并设置 [style-loader 配置项](https://github.com/webpack-contrib/style-loader)，用于让 CSS 内联打包在 JS 中，不输出额外的 CSS 文件。

## styles
- 默认 `[]`
- 配置额外 CSS。
```js
export default {
  styles: [`body { color: red; }`, `https://a.com/b.css`],
};
```
```html
<head>
  <style>
    body {
      color: red;
    }
  </style>
  <link rel="stylesheet" href="https://a.com/b.css" />
</head>
```

## targets
- 默认 `{ chrome: 49, firefox: 64, safari: 10, edge: 13, ios: 10 }`
- 配置需要兼容的浏览器最低版本，会自动引入 polyfill 和做语法转换。
```js
export default {
  targets: {
    ie: 11,
  },
};
```

- 注意：
	-   配置的 targets 会和合并到默认值，不需要重复配置
	-   子项配置为 `false` 可删除默认配置的版本号

## terserOptions
-  默认 [terserOptions.ts](https://github.com/umijs/umi/blob/master/packages/bundler-webpack/src/getConfig/terserOptions.ts)
- 配置[压缩器 terser 的配置项](https://github.com/terser/terser#minify-options)。

## theme
- 默认 `{}`
- 配置主题，实际上是配 less 变量。
```js
export default {
  theme: {
    '@primary-color': '#1DA57A',
  },
};
```

## title
- 默认 ``
- 配置标题。可以对应用或者针对路由配置title
```js
export default {
  title: 'hi',
  routes: [
    { path: '/', title: 'Home' },
    { path: '/users', title: 'Users' },
    { path: '/foo' },
  ],
};
```
- 然后我们访问 `/` 标题是 `Home`，访问 `/users` 标题是 `Users`，访问 `/foo` 标题是默认的 `hi`。
- 注意：
	-   默认不会在 HTML 里输出 `<title>` 标签，通过动态渲染得到
	-   配 `exportStatic` 后会为每个 HTML 输出 `<title>` 标签
	-   如果需要自行通过 react-helmet 等方式渲染 title，配 `title: false` 可禁用内置的 title 渲染机制

## webpack5
- 默认 false 
- 使用 webpack 5 代替 webpack 4 进行构建。
- 物理缓存功能默认开启，可通过设置环境变量 `WEBPACK_FS_CACHE` 为 `none` 来禁用。
- 包含以下子配置项：
	- lazyCompilation，是否启用基于路由的按需编译

## workerLoader
- 默认 false
- 开启 worker-loader 功能。


# API
## 基本API
### dynamic
- 动态加载组件
- **常见使用场景**：组件体积太大，不适合直接计入 bundle 中，以免影响首屏加载速度。例如：某组件 HugeA 包含巨大的实现 / 依赖了巨大的三方库，且该组件 HugeA 的使用不在首屏显示范围内，可被单独拆出。这时候，`dynamic` 就该上场了
- **为什么使用 `dynamic`**：封装了使用一个异步组件需要做的状态维护工作，开发者可以更专注于自己的业务组件开发，而不必关心 code spliting、async module loading 等等技术细节。
- 通常搭配 [动态 import 语法](https://github.com/tc39/proposal-dynamic-import) 使用。
- 封装异步组件
```js
import { dynamic } from 'umi';

export default dynamic({
  loader: async function () {
    // 这里的注释 webpackChunkName 可以指导 webpack 将该组件 HugeA 以这个名字单独拆出去
    const { default: HugeA } = await import(
      /* webpackChunkName: "external_A" */ './HugeA'
    );
    return HugeA;
  },
});
```
- 使用异步组件
```js
import React from 'react';
import AsyncHugeA from './AsyncHugeA';

// 像使用普通组件一样即可
// dynamic 为你做:
// 1. 异步加载该模块的 bundle
// 2. 加载期间 显示 loading（可定制）
// 3. 异步组件加载完毕后，显示异步组件
export default () => {
  return <AsyncHugeA />;
};
```

### history
- 获取当前路由信息
- 路由跳转
- 路由监听
```js
import { history } from 'umi';

// history 栈里的实体个数
console.log(history.length);

// 当前 history 跳转的 action，有 PUSH、REPLACE 和 POP 三种类型
console.log(history.action);

// location 对象，包含 pathname、search 和 hash
console.log(history.location.pathname);
console.log(history.location.search);
console.log(history.location.hash);

import { history } from 'umi';

// 跳转到指定路由
history.push('/list');

// 带参数跳转到指定路由
history.push('/list?a=b');
history.push({
  pathname: '/list',
  query: {
    a: 'b',
  },
});

// 跳转到上一个路由
history.goBack();

import { history } from 'umi';
const unlisten = history.listen((location, action) => {
  console.log(location.pathname);
});
unlisten();
```

### plugin
- 运行时插件接口，是 Umi 内置的跑在浏览器里的一套插件体系
```js
import { Plugin, ApplyPluginsType } from 'umi';

// 注册插件
Plugin.register({
  apply: { dva: { foo: 1 } },
  path: 'foo',
});
Plugin.register({
  apply: { dva: { bar: 1 } },
  path: 'bar',
});

// 执行插件
// 得到 { foo: 1, bar: 1 }
Plugin.applyPlugins({
  key: 'dva',
  type: ApplyPluginsType.modify,
  initialValue: {},
  args: {},
  async: false,
});
```
- 参数属性包含：
	-   **key**，坑位的 key
	-   **type**，执行方式类型，详见 [ApplyPluginsType](https://umijs.org/zh-CN/api#ApplyPluginsType)
	-   **initialValue**，初始值
	-   **args**，参数
	-   **async**，是否异步执行且返回 Promise

### ApplyPluginsType
- 运行时插件执行类型，enum 类型，包含三个属性：
	-   **compose**，用于合并执行多个函数，函数可决定前序函数的执行时机
	-   **modify**，用于修改值
	-   **event**，用于执行事件，前面没有依赖关系

## 路由
### Link
```js
import { Link } from 'umi';

export default () => {
  return (
    <div>
      {/* 点击跳转到指定 /about 路由 */}
      <Link to="/about">About</Link>

      {/* 点击跳转到指定 /courses 路由，
          附带 query { sort: 'name' }
      */}
      <Link to="/courses?sort=name">Courses</Link>

      {/* 点击跳转到指定 /list 路由，
          附带 query: { sort: 'name' }
          附带 hash: 'the-hash'
          附带 state: { fromDashboard: true }
      */}
      <Link
        to={{
          pathname: '/list',
          search: '?sort=name',
          hash: '#the-hash',
          state: { fromDashboard: true },
        }}
      >
        List
      </Link>

      {/* 点击跳转到指定 /profile 路由，
          附带所有当前 location 上的参数
      */}
      <Link
        to={(location) => {
          return { ...location, pathname: '/profile' };
        }}
      />

      {/* 点击跳转到指定 /courses 路由，
          但会替换当前 history stack 中的记录
      */}
      <Link to="/courses" replace />

      {/* 
          innerRef 允许你获取基础组件（这里应该就是 a 标签或者 null）
      */}
      <Link
        to="/courses"
        innerRef={(node) => {
          // `node` refers to the mounted DOM element
          // or null when unmounted
        }}
      />
    </div>
  );
};
```

### NavLink
- 特殊版本的 `<Link />` 。当指定路由（`to=指定路由`）命中时，可以附着特定样式。
```js
import { NavLink } from 'umi';

export default () => {
  return (
    <div>
      {/* 和 Link 等价 */}
      <NavLink to="/about">About</NavLink>

      {/* 当前路由为 /faq 时，附着 class selected */}
      <NavLink to="/faq" activeClassName="selected">
        FAQs
      </NavLink>

      {/* 当前路由为 /faq 时，附着 style */}
      <NavLink
        to="/faq"
        activeStyle={{
          fontWeight: 'bold',
          color: 'red',
        }}
      >
        FAQs
      </NavLink>

      {/* 当前路由完全匹配为 /profile 时，附着 class */}
      <NavLink exact to="/profile" activeClassName="selected">
        Profile
      </NavLink>

      {/* 当前路由为 /profile/ 时，附着 class */}
      <NavLink strict to="/profile/" activeClassName="selected">
        Profile
      </NavLink>

      {/* 当前路由为 /profile，并且 query 包含 name 时，附着 class */}
      <NavLink
        to="/profile"
        exact
        activeClassName="selected"
        isActive={(match, location) => {
          if (!match) {
            return false;
          }
          return location.search.includes('name');
        }}
      >
        Profile
      </NavLink>
    </div>
  );
};
```

###  Prompt
- 提供一个用户离开页面时的提示选择。
```js
import { Prompt } from 'umi';

export default () => {
  return (
    <div>
      {/* 用户离开页面时提示一个选择 */}
      <Prompt message="你确定要离开么？" />

      {/* 用户要跳转到首页时，提示一个选择 */}
      <Prompt
        message={(location) => {
          return location.pathname !== '/' ? true : `您确定要跳转到首页么？`;
        }}
      />

      {/* 根据一个状态来确定用户离开页面时是否给一个提示选择 */}
      <Prompt when={formIsHalfFilledOut} message="您确定半途而废么？" />
    </div>
  );
};
```

### withRouter
- 高阶组件，可以通过 `withRouter` 获取到 `history`、`location`、`match` 对象
```js
import { withRouter } from 'umi';

export default withRouter(({ history, location, match }) => {

 return (

 <div>

 <ul>

 <li>history: {history.action}</li>

 <li>location: {location.pathname}</li>

 <li>match: {`${match.isExact}`}</li>

 </ul>

 </div>

 );

});
```

### useHistory
- hooks，获取 `history` 对象
```js
import { useHistory } from 'umi';

export default () => {
  const history = useHistory();
  return (
    <div>
      <ul>
        <li>history: {history.action}</li>
      </ul>
    </div>
  );
};
```

### useLocation
- hooks，获取 `location` 对象
```js
import { useLocation } from 'umi';

export default () => {
  const location = useLocation();
  return (
    <div>
      <ul>
        <li>location: {location.pathname}</li>
      </ul>
    </div>
  );
};
```

### useParams
- hooks，获取 `params` 对象。 `params` 对象为动态路由（例如：`/users/:id`）里的参数键值对。
```js
import { useParams } from 'umi';

export default () => {
  const params = useParams();
  return (
    <div>
      <ul>
        <li>params: {JSON.stringify(params)}</li>
      </ul>
    </div>
  );
};
```
### useRouteMatch
- 获取当前路由的匹配信息。
```js
import { useRouteMatch } from 'umi';

export default () => {
  const match = useRouteMatch();
  return (
    <div>
      <ul>
        <li>match: {JSON.stringify(match.params)}</li>
      </ul>
    </div>
  );
};
```

## NodeJS侧接口
- 通过 package.json 的 main 字段露出，且不存在于 modules 字段里。
### Service
- Umi 内核的 Service 方法，用于测试，或调用 Umi 底层命令。

### utils
- utils 方法，给插件使用，和插件里的 api.utils 是同一个底层库。

### defineConfig
- 用于校验和提示用户配置类型，详见[配置#TypeScript 提示](https://umijs.org/config#typescript-%E6%8F%90%E7%A4%BA)。


## 插件类型定义

### [](https://umijs.org/zh-CN/api#iapi)IApi

### [](https://umijs.org/zh-CN/api#iconfig)IConfig


# 插件
## 插件集 @umijs/preset-react
- 针对 react 应用的插件集。
- 包含：
	-   [plugin-access](https://umijs.org/zh-CN/plugins/plugin-access)，权限管理
	-   [plugin-analytics](https://umijs.org/zh-CN/plugins/plugin-analytics)，统计管理
	-   [plugin-antd](https://umijs.org/zh-CN/plugins/plugin-antd)，整合 antd UI 组件
	-   [plugin-crossorigin](https://umijs.org/zh-CN/plugins/plugin-crossorigin)，通常用于 JS 出错统计
	-   [plugin-dva](https://umijs.org/zh-CN/plugins/plugin-dva)，整合 dva
	-   [plugin-helmet](https://umijs.org/zh-CN/plugins/plugin-helmet)，整合 [react-helmet](https://github.com/nfl/react-helmet)，管理 HTML 文档标签（如标题、描述等）
	-   [plugin-initial-state](https://umijs.org/zh-CN/plugins/plugin-initial-state)，初始化数据管理
	-   [plugin-layout](https://umijs.org/zh-CN/plugins/plugin-layout)，配置启用 ant-design-pro 的布局
	-   [plugin-locale](https://umijs.org/zh-CN/plugins/plugin-locale)，国际化能力
	-   [plugin-model](https://umijs.org/zh-CN/plugins/plugin-model)，基于 hooks 的简易数据流
	-   [plugin-request](https://umijs.org/zh-CN/plugins/plugin-request)，基于 umi-request 和 umi-hooks 的请求方案
## 插件
### @umijs/plugin-access

- 有 `src/access.ts` 时启用。
- 配置样例 Foo为后面标识的内容
```js
// src/access.ts
export default function(initialState) {
  const { userId, role } = initialState;
 
  return {
    canReadFoo: true,
    canUpdateFoo: role === 'admin',
    canDeleteFoo: foo => {
      return foo.ownerId === userId;
    },
  };
}
```
- 使用权限
```js
// config/route.ts
export const routes =  [
  {
    path: '/pageA',
    component: 'PageA',
    access: 'canReadPageA', // 权限定义返回值的某个 key
  }
]
```
- 插件配置项
	- `strictMode` 默认逻辑是没有写明权限表示有访问权限，如果要修改为没有写明权限表示没有访问权限，请开启它
- useAccess
```js
import React from 'react';
import { useAccess } from 'umi';

const PageA = props => {
  const { foo } = props;
  const access = useAccess();
 
  if (access.canReadFoo) {
    // 如果可以读取 Foo，则...
  }
 
  return <>TODO</>;
};

export default PageA;
```
- Access组件
	- 组件属性
		- accessible 是否有权限
		- fallback 无权限时显示
		- children 有权限时显示
```js
import React from 'react';
import { useAccess, Access } from 'umi';

const PageA = props => {
  const { foo } = props;
  const access = useAccess(); // access 的成员: canReadFoo, canUpdateFoo, canDeleteFoo
 
  if (access.canReadFoo) {
    // 如果可以读取 Foo，则...
  }
 
  return (
    <div>
      <Access
        accessible={access.canReadFoo}
        fallback={<div>Can not read foo content.</div>}
      >
        Foo content.
      </Access>
      <Access
        accessible={access.canUpdateFoo}
        fallback={<div>Can not update foo.</div>}
      >
        Update foo.
      </Access>
      <Access
        accessible={access.canDeleteFoo(foo)}
        fallback={<div>Can not delete foo.</div>}
      >
        Delete foo.
      </Access>
    </div>
  );
};
```

### @umijs/plugin-analytics
- 配置了就启用
```js
export default {
  analytics: {
    ga: 'google analytics code',
    baidu: '5a66cxxxxxxxxxx9e13',
  },
}
```

### @umijs/plugin-antd-mobile
- 整合 antd-mobile 组件库
- 配置 `mobile:{}`
	- 配置项 hd， 开启 hd 模式，会使用 antd-mobile/2x 中的组件

###  @umijs/plugin-crossorigin
- 为所有非三方脚本加上 `crossorigin="anonymous"` 属性，通常用于统计脚本错误。
- 配置 `crossorigin:true`

### @umijs/plugin-dva
- 整合 dva 数据流。
- 功能介绍
	- **约定式的 model 组织方式**，不用手动注册 model
	-   **文件名即 namespace**，model 内如果没有声明 namespace，会以文件名作为 namespace
	-   **内置 dva-loading**，直接 connect `loading` 字段使用即可
	-   **支持 immer**，通过配置 `immer` 开启
- 约定式的 model 组织方式，符合以下规则的文件会被认为是 model 文件，
	-   `src/models` 下的文件
	-   `src/pages` 下，子目录中 models 目录下的文件
	-   `src/pages` 下，所有 model.ts 文件(不区分任何字母大小写)
- 可选配置项
	- skipModelValidate  是否跳过 model 验证，默认 `false`
	- extraModels 配置额外到 dva model，默认 `[]`
	- immer 表示是否启用 immer 以方便修改 reducer，默认 `false`
		- 如需兼容 IE11，需配置 `{ immer: { enableES5: true }}`。
	- hmr  表示是否启用 dva model 的热更新，默认 `false`
	- disableModelsReExport 表示禁用 dva models 类型导出，默认会将 model 导出的类型挂载到 `umi` 上，默认 `false`，设置为`true`后不再挂载到`umi`上
	- lazyLoad 懒加载 dva models，如果项目里 models 依赖了 `import from umi` 导出模块，建议开启，避免循环依赖导致模块 undefined 问题，默认`false`
- 暴露到`umi`中的方法
	- `connect` 绑定数据到组件
	- `getDvaApp` 获取dva实例，历史上的`window.g_app`
	- `useDispatch` hooks 的方式获取dispatch
	- `useSelector` hooks 的方式获取部分数据
	- `useStore` hooks 的方式获取 store
- 暴露到`umi` 的类型
	- 通过 umi 导出类型：`ConnectRC`，`ConnectProps`，`Dispatch`，`Action`，`Reducer`，`ImmerReducer`，`Effect`，`Subscription`，和所有 `model` 文件中导出的类型。
- Model用例
```js
import { Effect, ImmerReducer, Reducer, Subscription } from 'umi';

export interface IndexModelState {
  name: string;
}

export interface IndexModelType {
  namespace: 'index';
  state: IndexModelState;
  effects: {
    query: Effect;
  };
  reducers: {
    save: Reducer<IndexModelState>;
    // 启用 immer 之后
    // save: ImmerReducer<IndexModelState>;
  };
  subscriptions: { setup: Subscription };
}

const IndexModel: IndexModelType = {
  namespace: 'index',

  state: {
    name: '',
  },

  effects: {
    *query({ payload }, { call, put }) {},
  },
  reducers: {
    save(state, action) {
      return {
        ...state,
        ...action.payload,
      };
    },
    // 启用 immer 之后
    // save(state, action) {
    //   state.name = action.payload;
    // },
  },
  subscriptions: {
    setup({ dispatch, history }) {
      return history.listen(({ pathname }) => {
        if (pathname === '/') {
          dispatch({
            type: 'query',
          });
        }
      });
    },
  },
};

export default IndexModel;
```
- Page 用例
```js
import React, { FC } from 'react';
import { IndexModelState, ConnectProps, Loading, connect } from 'umi';

interface PageProps extends ConnectProps {
  index: IndexModelState;
  loading: boolean;
}

const IndexPage: FC<PageProps> = ({ index, dispatch }) => {
  const { name } = index;
  return <div>Hello {name}</div>;
};

export default connect(
  ({ index, loading }: { index: IndexModelState; loading: Loading }) => ({
    index,
    loading: loading.models.index,
  }),
)(IndexPage);
```

### @umijs/plugin-esbuild
- 使用 esbuild 作为压缩器。
- 试验性功能，可能有坑，但效果拔群。
- 配置`esbuild:{}`
	- target 默认 `es2015`
- https://esbuild.github.io/api/#target

### @umijs/plugin-helmet
- 整合 [react-helmet](https://github.com/nfl/react-helmet)，管理 HTML 文档标签（如标题、描述等）。
- 包含以下功能，
	- 导出 [react-helmet](https://github.com/nfl/react-helmet#example) API，直接从 umi 中导入
	-  支持[服务端渲染（SSR）](https://umijs.org/zh-CN/docs/ssr)
- react-helmet 与 umi 中的 title 配置不能同时使用，可以通过配置 `title: false` 关闭默认标题配置，会导致闪一下没了

### @umijs/plugin-initial-state
- 约定一个地方生产和消费初始化数据。
- 本插件不可直接使用，必须搭配 `@umijs/plugin-model` 一起使用。
- `useModel` 获取初始值
```js
import { useModel } from 'umi';

export default () => {
  const { initialState, loading, error, refresh, setInitialState } = useModel('@@initialState');
  return <>{initialState}</>
};
```
- initialState 默认 `undefined`  运行时配置中，getInitialState 的返回值
- loading 默认 `true`   getInitialState 是否处于 loading 状态，在首次获取到初始状态前，页面其他部分的渲染都会被阻止。loading 可用于判断 refresh 是否在进行中。
- error 默认 `undefined`  当运行时配置中，getInitialState throw Error 时，会将错误储存在 error 中
- refresh 重新执行 getInitialState 方法，并获取新数据
- setInitialState 手动设置 initialState 的值，手动设置完毕会将 loading 置为 false，`initialState` 从 `@umijs/plugin-initial-state` 插件中获取，需要搭配一起使用。

### @umijs/plugin-layout
为了进一步降低研发成本，我们尝试将布局通过 umi 插件的方式内置，只需通过简单的配置即可拥有 Ant Design 的 Layout，包括导航以及侧边栏。从而做到用户无需关心布局。

-   默认为 Ant Design 的 Layout [@ant-design/pro-layout](https://www.npmjs.com/package/@ant-design/pro-layout)，支持它全部配置项。
-   侧边栏菜单数据根据路由中的配置自动生成。
-   默认支持对路由的 403/404 处理和 Error Boundary。
-   搭配 @umijs/plugin-access 插件一起使用，可以完成对路由权限的控制。
-   搭配 @umijs/plugin-initial-state 插件和 @umijs/plugin-model 插件一起使用，可以拥有默认用户登陆信息的展示。

> 想要动态菜单？查看这里 [菜单的高级用法](https://beta-pro.ant.design/docs/advanced-menu-cn)

- 配置方式
```js
import { defineConfig } from 'umi';

export const config = defineConfig({
  layout: {
    // 支持任何不需要 dom 的
    // https://procomponents.ant.design/components/layout#prolayout
    name: 'Ant Design',
    locale: true,
    layout: 'side',
  },
});
```
- 可配置字段
	- name 默认为 `package.json`中的name
	- logo 默认为 Ant Design Logo
	- theme  默认 Pro，指定 Layout 主题，可选 `pro` 和 `tech`（`tech` 仅在蚂蚁内部框架 Bigfish 中生效）
	- locale 默认 false ，是否开始国际化配置。开启后路由里配置的菜单名会被当作菜单名国际化的 key，插件会去 locales 文件中查找 `menu.[key]`对应的文案，默认值为该 key，路由配置的 name 字段的值就是对应的 key 值。如果菜单是多级路由假设是二级路由菜单，那么插件就会去 locales 文件中查找 `menu.[key].[key]`对应的文案，该功能需要配置 `@umijs/plugin-locale` 使用。
		- config 支持所有的非 dom 配置并透传给 [`@ant-design/pro-layout`](https://procomponents.ant.design/components/layout#prolayout)。
	- logout 默认 null， 用于运行时配置默认 Layout 的 UI 中，点击退出登录的处理逻辑，默认不做处理，默认在顶部右侧并不会显示退出按钮，需要在运行配置中配合 `@umijs/plugin-intial-state` 的 `getInitialState` 返回一个对象，才可以显示
	- rightRender 默认展示用户名、头像、退出登录相关组件，`initialState` 从 `@umijs/plugin-initial-state` 插件中获取，需要搭配一起使用
	- onError  发生错误后的回调（可做一些错误日志上报，打点等）
	- ErrorComponent 默认Ant Design Pro 的错误页，发生错误后展示的组件。
- 扩展的路由配置，layout在路由上的配置扩展
	- 示例
	```js
//config/route.ts
export const routes: IBestAFSRoute[] = [
  {
    path: '/welcome',
    component: 'IndexPage',
    name: '欢迎', // 兼容此写法
    icon: 'testicon',
    // 更多功能查看
    // https://beta-pro.ant.design/docs/advanced-menu
    // ---
    // 新页面打开
    target: '_blank',
    // 不展示顶栏
    headerRender: false,
    // 不展示页脚
    footerRender: false,
    // 不展示菜单
    menuRender: false,
    // 不展示菜单顶栏
    menuHeaderRender: false,
    // 权限配置，需要与 plugin-access 插件配合使用
    access: 'canRead',
    // 隐藏子菜单
    hideChildrenInMenu: true,
    // 隐藏自己和子菜单
    hideInMenu: true,
    // 在面包屑中隐藏
    hideInBreadcrumb: true,
    // 子项往上提，仍旧展示,
    flatMenu: true,
  },
];	
	```
	- name 菜单上显示的名称，没有则不显示。
	- icon 菜单上显示的 Icon。
		- icon name 为 组件名小写后去掉 `outlined` 或者 `filled` 或者 `twotone`，所得值。举例：`<UserOutlined />` 的 icon name 即： `user`,antd 的 icon，为了按需加载 layout 插件会帮你自动转化为 Antd icon 的 dom。支持类型可以在 antd 中[找到](https://ant.design/components/icon-cn/)。
	- access 当 Layout 插件配合 `@umijs/plugin-access` 插件使用时生效。权限插件会将用户在这里配置的 access 字符串与当前用户所有权限做匹配，如果找到相同的项，并当该权限的值为 false，则当用户访问该路由时，默认展示 403 页面
	- locale 菜单的国际化配置，国际化的 key 是 `menu.${submenu-name}.${name}`
	- flatMenu 默认为 false，为true时在菜单中只隐藏此项，子项往上提，仍旧展示,打平菜单，如果只想要子级的 menu 不展示自己的，可以配置为 true
	- xxxRender
		- xxxRender 设置为 false，即可不展示部分 layout 模块
		- `headerRender=false` 不显示顶栏
		-   `footerRender=false` 不显示页脚
		-   `menuRender=false` 不显示菜单
		-   `menuHeaderRender=false` 不显示菜单的 title 和 logo
	- hideInXXX
		- hideInXXX 可以让管理 menu 的渲染。
		-   `hideChildrenInMenu=true` 隐藏子菜单
		-   `hideInMenu=true` 隐藏自己和子菜单
		-   `hideInBreadcrumb=true` 在面包屑中隐藏
### @umijs/plugin-locale
- 配置 `locale`开启
- 约定式语言支持 
	- `src/locales/zh-CN.ts` `src/locales/en-US.ts`
	- 多语言文件的命名规范：`<lang><分割符（通过 baseSeparator 配置）><COUNTRY>.js`
- 如果项目配置了 `singular: true` ，`locales` 要改成 `locale`
- @umijs/plugin-locale 基于 react-intl 封装，支持其所有的 api，详情可以看 [这里](https://github.com/formatjs/formatjs/blob/main/website/docs/react-intl/api.md)。为了方便使用我们也添加了一些其他的功能，这里将会列举所有的 api，并且展示它的功能
- 暴露给umi的API
	- addLocale
	- getALlLocales
	- getLocale
	- useIntl
	- setLocale
- 构建时配置
	- 配置项
		- baseSeparator 默认 - 
		- default 默认 zh-CN
		- antd 默认 false，开启后，支持 [antd 国际化](https://ant.design/docs/react/i18n-cn)。
		- title 默认 false，在项目中配置的 `title` 及路由中的 `title` 可直接使用国际化 key，自动被转成对应语言的文案，
		- baseNavigator 默认 true，开启浏览器语言检测。默认情况下，当前语言环境的识别按照：`localStorage` 中 `umi_locale` 值 > 浏览器检测 > [default](https://umijs.org/zh-CN/plugins/plugin-locale#default) 设置的默认语言 > 中文
	- 默认配置
```js
export default {
  locale: {
    default: 'zh-CN',
    antd: false,
    title: false,
    baseNavigator: true,
    baseSeparator: '-',
  },
};
```

### @umijs/plugin-model
- 一种基于 `hooks` 范式的简易数据管理方案（部分场景可以取代 `dva`），通常用于中台项目的全局共享数据。
- 我们都知道自定义 `hooks` 是逻辑复用的利器，但我们也知道它不能复用状态，就和 `react` 内置的 `hooks` 一样，每次调用产生的状态都是相互隔离、无关的。那么，在业务开发中，如果我们需要提取的逻辑和状态都希望能够在多个组件中『共享』，就像其他数据流管理工具（`dva`, `mobx`）一样，`@umijs/plugin-model` 就是一个不错的选择。
- `src/models` 目录下有 hooks model 时启用。
- 我们约定在 `src/models` 目录下的文件为项目定义的 model 文件。每个文件需要默认导出一个 function，该 function 定义了一个 Hook，不符合规范的文件我们会过滤掉。文件名则对应最终 model 的 name，你可以通过插件提供的 API 来消费 model 中的数据。
- 所谓 hooks model 文件，就是自定义 `hooks` 模块，没有任何需要使用者关注的黑魔法
```js
import { useState, useCallback } from 'react'

export default function useAuthModel() {
  const [user, setUser] = useState(null)

  const signin = useCallback((account, password) => {
    // signin implementation
    // setUser(user from signin API)
  }, [])

  const signout = useCallback(() => {
    // signout implementation
    // setUser(null)
  }, [])

  return {
    user,
    signin,
    signout
  }
}
```
- 使用者书写的就是一个普通的自定义 `hooks`，但 `@umijs/plugin-model` 把其中的状态变成了『全局状态』，多个组件中使用该 `model` 时，拿到的同一份状态。
- 在组件中使用
	- useModel 

### @umijs/plugin-preact
- 切换 react 为 preact。
- 默认开启
- 你使用的 ui 库可能不兼容 preact，比如 antd


### @umijs/plugin-qiankun
- Umi 应用一键开启 [qiankun](https://github.com/umijs/qiankun) 微前端模式。
	-  基于 qiankun
	-   ✔︎ 支持主应用和子应用都用 umi
	-   ✔︎ 支持通过 `<MicroApp />` 组件引入子应用
	-   ✔︎ 父子应用通讯
	-   ✔︎ 子应用运行时配置自定义 `bootstrap()`、`mount()` 和 `unmount()`
	-   ✔︎ 主应用、子应用联调
	-   ✔︎ 嵌套子应用
  
#### 主应用配置
##### 注册子应用
###### 注册子应用方式一 ——插件构建期配置子应用

```js
export default {
  qiankun: {
    master: {
      // 注册子应用信息
      apps: [
        {
          name: 'app1', // 唯一 id
          entry: '//localhost:7001', // html entry
        },
        {
          name: 'app2', // 唯一 id
          entry: '//localhost:7002', // html entry
        },
      ],
    },
  },
};

```
###### 注册子应用方式二 —— 运行时动态配置子应用（src/app.ts 里开启）
```js
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
  // 支持更多的其他配置，详细看这里 https://qiankun.umijs.org/zh/api/#start-opts
}));

```
- 完整的主应用配置项看这里 [masterOptions 配置列表](https://umijs.org/zh-CN/plugins/plugin-qiankun#masteroptions)

#####  装载子应用
###### 装载子应用方式一 —— 使用路由绑定的方式
	- 建议使用这种方式来引入自带路由的子应用。
```js
export default {
	routes: [
    {
      path: '/',
      component: '../layouts/index.js',
      routes: [
        {
          path: '/app1',
          component: './app1/index.js',
          routes: [
            {
              path: '/app1/user',
              component: './app1/user/index.js',
            },
+            // 配置微应用 app1 关联的路由
+            {
+              path: '/app1/project',
+              microApp: 'app1',
+            },
          ],
        },
+       // 配置 app2 关联的路由
+       {
+         path: '/app2',
+         microApp: 'app2'
+       },
        {
          path: '/',
          component: './index.js',
        },
      ],
    },
  ],
}
```
- 微应用路由也可以配置在运行时，通过 src/app.ts 添加
```js
export const qiankun = fetch('/config').then(({ apps }) => {
  return {
    apps,
    routes: [
      {
        path: '/app1',
        microApp: 'app1',
      },
    ],
  };
});
```

- 运行时注册的路由会自动关联到你配置的根路由下面
```js
export default {
  routes: [
    {
      path: '/',
      component: '../layouts/index.js',
      routes: [
+       {
+         path: '/app1',
+         microApp: 'app1',
+       },
        {
          path: '/test',
          component: './test.js',
        },
      ],
    },
  ]
}
```

###### 装载子应用方式二 —— 使用 `<MicroApp />` 组件的方式
- 建议使用这种方式来引入不带路由的子应用。 否则请自行关注微应用依赖的路由跟当前浏览器 url 是否能正确匹配上，否则很容易出现微应用加载了，但是页面没有渲染出来的情况。
- 我们可以直接使用 React 标签的方式加载我们已注册过的子应用：
```js
import { MicroApp } from 'umi';

export function MyPage() {

  return (
    <div>
      <div>
+        <MicroApp name="app1" />
      </div>
    </div>
  )
}
```

##### 子应用配置
###### 第一步 插件注册
```js
export default {
  qiankun: {
    slave: {},
  },
};
```

######  第二步：配置运行时生命周期钩子（可选）
- 插件会自动为你创建好 qiankun 子应用需要的生命周期钩子，但是如果你想在生命周期期间加一些自定义逻辑，可以在子应用的 `src/app.ts` 里导出 `qiankun` 对象，并实现每一个生命周期钩子，其中钩子函数的入参 `props` 由主应用自动注入。
```js
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

##### 父子应用通讯
###### 通讯方式一 配合useModel
- 需确保已安装 `@umijs/plugin-model` 或 `@umijs/preset-react`
- 主应用可以使用下面任一方式透传数据
- 如果你用的 [MicroApp](https://umijs.org/zh-CN/plugins/plugin-qiankun#MicroApp) 组件模式消费微应用，那么数据传递的方式就跟普通的 react 组件通信是一样的，直接通过 props 传递即可：
```js
function MyPage() {
  const [name, setName] = useState(null);
  return (
    <MicroApp name={name} onNameChange={(newName) => setName(newName)} />
  );
}
```
- 如果你用的 [路由绑定式](https://umijs.org/zh-CN/plugins/plugin-qiankun#RouteBased) 消费微应用，那么你需要在 `src/app.ts` 里导出一个 `useQiankunStateForSlave` 函数，函数的返回值将作为 props 传递给微应用，如：
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
- 微应用中会自动生成一个全局 model，可以在任意组件中获取主应用透传的 props 的值。
```js
import { useModel } from 'umi';

function MyPage() {

 const masterProps = useModel('@@qiankunStateFromMaster');

 return <div>{JSON.stringify(masterProps)}</div>;

}
```
- 或者可以通过高阶组件 connectMaster 来获取主应用透传的 props
```js
import { connectMaster } from 'umi';

function MyPage(props) {

 return <div>{JSON.stringify(props)}</div>;

}

export default connectMaster(MyPage);

```
- 和 `<MicroApp />` 的方式一同使用时，会额外向子应用传递一个 setLoading 的属性，在子应用中合适的时机执行 `masterProps.setLoading(false)`，可以标记微模块的整体 loading 为完成状态。

###### 通讯方式二 使用props
- 主应用中配置 apps 时以 props 将数据传递下去（参考主应用运行时配置一节）
```js
// src/app.js

export const qiankun = fetch('/config').then((config) => {
  return {
    apps: [
      {
        name: 'app1',
        entry: '//localhost:2222',
        props: {
          onClick: (event) => console.log(event),
          name: 'xx',
          age: 1,
        },
      },
    ],
  };
});
```
- 子应用在生命周期钩子中获取 props 消费数据（参考子应用运行时配置一节）

###### 嵌套子应用
- 除了导航应用之外，App1 与 App2 均依赖浏览器 url，为了让 App1 嵌套 App2，两个应用同时存在，我们需要在运行时将 App2 的路由改为 memory 类型
- 在 App1 中加入 master 配置
```js
export default {
  qiankun: {
    master: {
      // 注册子应用信息
      apps: [
        {
          name: 'app2', // 唯一 id
          entry: '//localhost:7002', // html entry
        },
      ],
    },
  },
};
```
- 通过 `<MicroAppWithMemoHistory />` 引入 App2
```js
import { MicroAppWithMemoHistory } from 'umi';

export function MyPage() {

  return (
    <div>
      <div>
+        <MicroAppWithMemoHistory name="app2" url="/user" />
      </div>
    </div>
  )
}
```

##### API 
###### MasterOptions
- apps 子应用配置
- routes 子应用运行时需要注册的微应用路由
- sandbox 是否启用沙箱，[详细说明](https://qiankun.umijs.org/zh/api/#start-opts)
- prefetch 是否启用 prefetch 特性，[详细说明](https://qiankun.umijs.org/zh/api/#start-opts)

###### App
- name 子应用唯一 id
- entry 子应用 html 地址
- credentials 拉取 entry 时是否需要携带cookie，[详见](https://qiankun.umijs.org/zh/faq#%E5%A6%82%E4%BD%95%E8%A7%A3%E5%86%B3%E6%8B%89%E5%8F%96%E5%BE%AE%E5%BA%94%E7%94%A8-entry-%E6%97%B6-cookie-%E6%9C%AA%E6%90%BA%E5%B8%A6%E7%9A%84%E9%97%AE%E9%A2%98)
- props 主应用传递给子应用的数据

###### Route
- path 路由 path
- microApp 关联的微应用名称
- microAppProps 微应用配置


### @umijs/plugin-request
- `@umijs/plugin-request` 基于 [umi-request](https://github.com/umijs/umi-request) 和 [ahooks](http://ahooks.js.org/hooks) 的 `useRequest` 提供了一套统一的网络请求和错误处理方案。
- 接口规范
```ts
interface ErrorInfoStructure {

 success: boolean; // if request is success

 data?: any; // response data

 errorCode?: string; // code for errorType

 errorMessage?: string; // message display to user 

 showType?: number; // error display type： 0 silent; 1 message.warn; 2 message.error; 4 notification; 9 page

 traceId?: string; // Convenient for back-end Troubleshooting: unique request ID

 host?: string; // Convenient for backend Troubleshooting: host of current access server

}
```
- 后端接口规范不满足的情况下你可以通过配置 `errorConfig.adaptor` 来做适配。当 `success` 返回是 `false` 的情况我们会按照 `showType` 和 `errorMessage` 来做统一的错误提示，同时抛出一个异常。
- 构建时配置
```js
export default {
  request: {
    dataField: 'data',
  },
};
```
- dataField  `dataField` 对应接口统一格式中的数据字段，比如接口如果统一的规范是 `{ success: boolean, data: any}` ，那么就不需要配置，这样你通过 `useRequest` 消费的时候会生成一个默认的 `formatResult`，直接返回 `data` 中的数据，方便使用。如果你的后端接口不符合这个规范，可以自行配置 `dataField` 。配置为 `''` （空字符串）的时候不做处理。

### @umijs/plugin-sass
- 启用 sass 编译支持。默认开启
- implementation 默认是 [Dart Sass](https://sass-lang.com/dart-sass)。
- 如果要切换到 [Node Sass](https://github.com/sass/node-sass)，可安装 `node-sass` 依赖，然后配置，
```js
export default {
  sass: {
    implementation: require('node-sass'),
  },
}

```
- sassOptions 传递给 [Dart Sass](https://github.com/sass/dart-sass#javascript-api) 或 [Node Sass](https://github.com/sass/node-sass/#options) 的配置项。

### 插件API
#### applyPlugins 
- 取得 register 注册的 hooks 执行后的数据
#### describe 
- 注册阶段执行，用于描述插件或插件集的 id、key、配置信息、启用方式等。
#### register
- 为 `api.applyPlugins` 注册可供其使用的 hook。
#### registerCommand
- 注册命令。
#### registerMethod
- 往 api 上注册方法。可以是 `api.register()` 的快捷使用方式，便于调用；也可以不是，如果有提供 `fn`，则执行 `fn` 定义的函数。
#### registerPresets
- 注册插件集，参数为路径数组。
#### registerPlugins
- 注册插件，参数为路径数组。
#### hasPlugins
- 判断是否有注册某个插件。
#### hasPresets
- 判断是否有注册某个插件集。
#### skipPlugins
- 声明哪些插件需要被禁用，参数为插件 id 的数组。

### 插件API扩展方法
- 通过 `api.registerMethod()` 扩展的方法。
#### addBeforeMiddlewares
- 添加在 webpack compiler 中间件之前的中间件，返回值格式为 express 中间件。
####  addDepInfo
- 添加依赖信息，包括 semver range 和别名信息。
####  addEntryCode
- 在入口文件最后添加代码。
####  addEntryCodeAhead
- 在入口文件最前面（import 之后）添加代码。
#### addEntryImports
- 在入口文件现有 import 的后面添加 import。
#### addEntryImportsAhead
- 在入口文件现有 import 的前面添加 import。
#### addHTMLMetas
- 在 HTML 中添加 meta 标签。
#### addHTMLLinks
- 在 HTML 中添加 Link 标签。
#### addHTMLStyles
- 在 HTML 中添加 Style 标签。
#### addHTMLScripts
- 在 HTML 尾部添加脚本。
#### addHTMLHeadScripts
- 在 HTML 头部添加脚本。
#### addMiddlewares
- 添加在 webpack compiler 中间件之后的中间件，返回值格式为 express 中间件。
#### addPolyfillImports
- 添加补充相关的 import，在整个应用的最前面执行。
#### addProjectFirstLibraries
- 添加以项目依赖为优先的依赖库列表，返回值为 `{ name: string; path: string }`。
#### addRuntimePlugin
- 添加运行时插件，返回值格式为表示文件路径的字符串。
#### addRuntimePluginKey
- 添加运行时插件的 key，返回值格式为字符串。
#### addUmiExports
- 添加需要 umi 额外导出的内容，返回值格式为 `{ source: string, specifiers?: (string | { local: string, exported: string })[], exportAll?: boolean }`。比如 `api.addUmiExports(() => { source: 'dva', specifiers: ['connect'] })`，然后就可以通过 `import { connect } from 'umi'` 使用 `dva` 的 `connect` 方法了
#### addTmpGenerateWatcherPaths
- 添加重新临时文件生成的监听路径。
####   chainWebpack(config, { webpack })
- 通过 [webpack-chain](https://github.com/neutrinojs/webpack-chain) 的方式修改 webpack 配置。
#### getPort()
- 获取端口号，dev 时有效。
#### getHostname()
- 获取 hostname，dev 时有效。
#### modifyBabelOpts
- 修改 babel 配置项。
#### modifyBabelPresetOpts
- 修改 @umijs/babel-preset-umi 的配置项。
#### modifyBundleConfig
- 修改 bundle 配置。
#### modifyBundleConfigs
- 修改 bundle 配置数组，比如可用于 dll、modern mode 的处理
#### modifyBundleConfigOpts
- 修改获取 bundleConfig 的函数参数。
#### modifyBundleImplementor
- 比如用于切换到 webpack@5 或其他。 
#### modifyBundler
- 比如用于切换到 parcel 或 rollup 做构建。
#### modifyConfig
- 修改最终配置。
#### modifyDefaultConfig
- 修改默认配置。
#### modifyHTML
- 修改 HTML，基于 [cheerio](https://github.com/cheeriojs/cheerio) 的 ast。
#### modifyHTMLChunks
- 修改 html 中的 js 文件引入，可以用于不同的页面使用，不同的 [chunks](https://umijs.org/zh-CN/config#chunks) 配置。
#### modifyExportRouteMap
- 修改导出路由对象 `routeMap`（路由与输出 HTML 的映射关系），触发时机在 HTML 文件生成之前，默认值为 `[{ route: { path: '/' }, file: 'index.html' }]`。
#### modifyDevHTMLContent
- `umi dev` 时修改输出的 HTML 内容。
#### modifyProdHTMLContent
- `umi build` 时修改输出的 HTML 内容。
#### modifyPaths
- 修改 paths 对象。
#### modifyRendererPath
- 修改 renderer 路径，用于使用自定义的 renderer。
#### modifyPublicPathStr
- 修改 publicPath 字符串。
#### modifyRoutes
- 修改路由。
####   onPatchRoute({ route, parentRoute })
- 修改路由项。
#### onPatchRouteBefore({ route, parentRoute })
- 修改路由项。
#### onPatchRoutes({ routes, parentRoute })
- 修改路由数组。
#### onPatchRoutesBefore({ routes, parentRoute })
-  修改路由数组。
#### onBuildComplete({ err?, stats? })
- 构建完成时可以做的事。
#### onDevCompileDone({ isFirstCompile: boolean, stats: webpack.Stats })
- 编译完成时可以做的事。
####  onGenerateFiles
- 生成临时文件，触发时机在 webpack 编译之前。
#### onPluginReady()
- 在插件初始化完成触发。在 `onStart` 之前，此时还没有 config 和 paths，他们尚未解析好。
#### onStart()
- 在命令注册函数执行前触发。可以使用 config 和 paths。
#### onExit()
- dev 退出时触发。
#### writeTmpFile({ path: string, content: string, skipTSCheck?: boolean })
- 写临时文件。
### 插件属性
- args 命令行参数
- config 用户配置
- cwd 当前路径
- env process.env.NODE_ENV
- id 插件id
- logger 插件日志类
- key 插件配置的key
- paths 相关路径
	-   `cwd`，当前路径
	-   `absSrcPath`，src 目录绝对路径，需注意 src 目录是可选的，如果没有 src 目录，`absSrcPath` 等同于 `cwd`
	-   `absPagesPath`，pages 目录绝对路径
	-   `absTmpPath`，临时目录绝对路径
	-   `absOutputPath`，输出路径，默认是 `./dist`
	-   `absNodeModulesPath`，node_modules 目录绝对路径
	-   `aliasedTmpPath`，以 `@` 开头的临时路径，通常用于
- pkg 当前项目的`package.json` Object
- service Service实例
- stage Service运行阶段
- userConfig 纯用户配置就是 `.umirc` 或 `config/config` 里的内容，没有经过 defaultConfig 以及插件的任何处理，和 config 的区别是，可以在注册阶段取到
- utils 详见 [@umijs/utils/src/index.ts](https://github.com/umijs/umi/blob/master/packages/utils/src/index.ts)。包含外部库：
	-   `lodash` : 导出自 `lodash`, 实用的 js 工具库。
	-   `got` : 导出自 `got`, 轻量级的请求库。
	-   `deepmerge` : 导出自 `deepmerge`, 将两个对象的可以枚举属性深度合并。
	-   `semver` : 导出自 `semver`, 用于实现版本号的解析和比较，规范版本号的格式。常见于版本过低提示用户升级等场景。
	-   `Mustache` : 导出自 `mustache`, 无逻辑的模版语法，是 JavaScript 中的 mustache 模板系统的零依赖实现。
	-   `address` : 导出自 `address`, 用于获取当前计算机的 IP ，MAC 和 DNS 服务器地址等。
	-   `cheerio` : 导出自 `cheerio`, 用于方便的处理爬取到的网页内容，在服务端对 DOM 结构进行方便的操作。
	-   `clipboardy` : 导出自 `clipboardy`, 用于对剪贴板内容写入与读取的处理。
	-   `chokidar` : 导出自 `chokidar`, 用于监听文件的变化。
	-   `createDebug`, `Debugger` : 导出自 `debug`, 用于控制调试日志的输出。
	-   `chalk` : 导出自 `chalk`, 常用于在终端中输出彩色文字，支持链式调用，能够设置文本样式、颜色、背景色等。
	-   `signale` : 导出自 `signale`, 用于日志记录、状态报告以及处理其他 Node 模块和应用的输出渲染方式。
	-   `portfinder` : 导出自 `portfinder`, 常用于在判断端口是否被占用或者获取没有被占用的端口等场景。
	-   `glob` : 导出自 `glob`, 用于获取匹配对应规则的文件。
	-   `pkgUp` : 导出自 `pkg-up`, 查找最近的 package.json 文件。
	-   `resolve` : 导出自 `resolve`, 实现了 node 的 require.resolve() 算法, 提供了方便处理获取模块完整路径相关需求的方法。
	-   `spawn` : 导出自 `cross-spawn` , 已经封装好了 Node.js 子进程（child_process）模块下 `spawn` 函数的跨平台写法的相关细节, 直接使用其调用系统上的命令如 `npm` 即可。
	-   `execa`: 导出自 `execa`, 更好的子进程管理工具。相当于衍生一个 shell，传入的 command 字符串在该 shell 中直接处理。
	-   `mkdirp` : 导出自 `mkdirp`, node 中 `mkdir -p` 功能的实现, 用于在 Node.js 中递归式创建目录及其子目录。
	-   `rimraf` : 导出自 `rimraf`, node 中 `rm -rf` 功能的实现,
	-   `yargs` : 导出自 `yargs`, 用于创建交互式命令行工具，能够方便的处理命令行参数。
	-   `yParser` : 导出自 `yargs-parser`, `yargs` 使用的强大 option 解析器, 用于解析命令行参数。
	-   `parser` : 导出自 `@babel/parser`, 解析代码生成 AST 抽象语法树。
	-   `traverse` : 导出自 `@babel/traverse`, 对 AST 节点进行递归遍历。
	-   `t` : 导出自 `@babel/types`, 用于 AST 节点的 Lodash 式工具库。它包含了构造、验证以及变换 AST 节点的方法。 该工具库包含考虑周到的工具方法，对编写处理 AST 逻辑非常有用。

- 内部工具方法
	-   `isBrowser`, 判断是否在浏览器环境。
	-   `isWindows`, 判断当前是否是 windows 系统。
	-   `isSSR`, whether SSR success in client。
	-   `isLernaPackage`, 判断是否存在 `lerna.json` 文件。
	-   `winPath`, 将文件路径转换为兼容 window 的路径，用于在代码中添加 `require('/xxx/xxx.js')` 之类的代码。
	-   `winEOL`, 在 windows 环境下，很多工具都会把换行符 lf 自动改成 crlf, 为了测试精准需要将换行符转化一下。
	-   `compatESModuleRequire`, 兼容 ESModule 以及 Require 为 Require。
	-   `mergeConfig`, 对象合并。
	-   `randomColor`, 随机生成颜色。
	-   `delay`, 延迟函数。
	-   `Generator`, `mustache` 模版代码生成。
	-   `BabelRegister`, `@babel/register` 的简易封装。
	-   `parseRequireDeps`, 获取特定文件的本地依赖。
	-   `cleanRequireCache`, 清理特定 Module 在 require cache 以及 parent.children 中的引用。
	-   `getWindowInitialProps`, 获取 window.g_initialProps。
	-   `getFile`, 获取特定目录中文件的完整扩展名，javascript 文件的匹配顺序 `['.ts', '.tsx', '.js', '.jsx']`，css 文件的匹配顺序 `['.less', '.sass', '.scss', '.stylus', '.css']`。
	-   `routeToChunkName`, transform route component into webpack chunkName。

- 类型
	-   `ArgsType<T extends (...args: any[]) => any>`, 获取函数参数数组类型。
	-   `PartialKeys<T>`, 找出 T 中类型是 undefined 的 key。
	-   `PartialProps<T>`, 取出 T 中类型是 undefined 的属性。
	-   `NodeEnv`: 联合类型 'development' | 'production' | 'test'。
	-   `Omit<T, U>`, 排除 T 中的 U key。
- ApplyPluginsType
- ConfigChangeType
- EnableBy
- Html