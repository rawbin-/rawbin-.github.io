# 简介
Ant Design Pro 是一套解决方案，专门针对中后台系统的前端快速开发的，承载了Ant Design 的组件库和前端/设计的能力，同时基于umi 这套工程化工具（基于webpack的整合工具）。

umi 是一个 webpack 之上的整合工具。 umi 相比于 webpack 增加了运行时的能力，同时帮助我们配置了很多 webpack 的预设。也减少了 webpack 升级导致的问题。这也是我们能提供插件的原因。
如果不喜欢 umi 默认的配置，可以在这里看看有没有你喜欢的[配置](https://umijs.org/zh-CN/config)。如果还是不能满足就要自定义 webpack 了，[chainWebpack](https://umijs.org/zh-CN/config#chainwebpack) 可以自定义内置的 webpack 配置。
webpack 对于 node 也是有版本需求的，不同 webpack 对 node 版本的依赖也不同，所以最好的办法是升级到最新的[长期维护版本](https://nodejs.org/en/)。

## 创建方式
- 创建 umi项目，`yarn create @umijs/umi-app myapp`  模板更简单
- 创建antdpro项目,`yarn create umi myapp` 模板更全面


## 目录结构
```
├── config                   # umi 配置，包含路由，构建等配置
├── mock                     # 本地模拟数据
├── public
│   └── favicon.png          # Favicon
├── src
│   ├── assets               # 本地静态资源
│   ├── components           # 业务通用组件
│   ├── e2e                  # 集成测试用例
│   ├── layouts              # 通用布局
│   ├── models               # 全局 dva model
│   ├── pages                # 业务页面入口和常用模板
│   ├── services             # 后台接口服务
│   ├── utils                # 工具库
│   ├── locales              # 国际化资源
│   ├── global.less          # 全局样式
│   └── global.ts            # 全局 JS
├── tests                    # 测试工具
├── README.md
└── package.json
```
- 推荐的代码组织结构
```js
src
├── components
└── pages
    ├── Welcome        // 路由组件下不应该再包含其他路由组件，基于这个约定就能清楚的区分路由组件和非路由组件了
    |   ├── components // 对于复杂的页面可以再自己做更深层次的组织，但建议不要超过三层
    |   ├── Form.tsx
    |   ├── index.tsx  // 页面组件的代码
    |   └── index.less // 页面样式
    ├── Order          // 路由组件下不应该再包含其他路由组件，基于这个约定就能清楚的区分路由组件和非路由组件了
    |   ├── index.tsx
    |   └── index.less
    ├── user           // 一系列页面推荐通过小写的单一字母做 group 目录
    |   ├── components // group 下公用的组件集合
    |   ├── Login      // group 下的页面 Login
    |   ├── Register   // group 下的页面 Register
    |   └── util.ts    // 这里可以有一些共用方法之类，不做推荐和约束，看业务场景自行做组织
    └── *              // 其它页面组件代码
```

## 常见搭配
## 组件库
- https://procomponents.ant.design/
- npm i @ant-design/pro-form --save`
-   `npm i @ant-design/pro-layout --save`
-   `npm i @ant-design/pro-table --save`
-   `npm i @ant-design/pro-list --save`
-   `npm i @ant-design/pro-descriptions --save`
-   `npm i @ant-design/pro-card --save`
-   `npm i @ant-design/pro-skeleton --save`
- 
### 样式 antd
样式文件默认使用[CSS Modules](http://www.ruanyifeng.com/blog/2016/06/css_modules.html)，如果需要，可以导入[antd less 变量](https://github.com/ant-design/ant-design/blob/master/components/style/themes/default.less) 在文件的头部：
```less
@import '~antd/lib/style/themes/default.less';
```
### 布局 
- https://umijs.org/zh-CN/plugins/plugin-layout
布局是一个中后台应用必备的，一个布局 + ProTable + Form 即可获得一个 CRUD 页面。

Pro 中内置了 [plugin-layout](https://umijs.org/zh-CN/plugins/plugin-layout) 来减少样板代码。简单的使用中我们只需要在 `config.ts` 中配置 layout 属性就可以实现通用的页面布局。
- 布局样式可定制
layout 插件 与[pro-layout](https://github.com/ant-design/ant-design-pro-layout) 配置的配置形同。

推荐先使用 [Pro 站点](https://preview.pro.ant.design/dashboard/analysis?primaryColor=daybreak) 的右侧抽屉来帮助你完成布局相关的整体风格、主题色、导航模式、内容区域宽度、固定 Header、固定侧边菜单、色弱模式等配置选择。然后将拷贝的配置粘贴在 layout 配置中。
```ts
// config.js
import { defineConfig } from 'umi';

export const config = defineConfig({
  layout: {
    name: 'Ant Design Pro',
    logo: 'https://preview.pro.ant.design/static/logo.f0355d39.svg',
    // copy from pro site
    navTheme: 'dark',
    primaryColor: '#1890ff',
    layout: 'sidemenu',
    contentWidth: 'Fluid',
    fixedHeader: false,
    fixSiderbar: false,
    title: 'Ant Design Pro',
    pwa: false,
    iconfontUrl: '',
  },
});
```
- 菜单样式可通过路由配置来显示
- 菜单国际化可通过layout的local来显示
- 导航右上角可以通过全局初始化信息来获取 `src/app.ts` `getInitialState`
- 退出登录，footer等等都可以通过layout的配置来显示，可以在`app.ts`中
```ts
// src/app.ts
export function getInitialState() {
  return {
    name: 'Serati Ma',
    avatar: 'https://gw.alipayobjects.com/zos/antfincdn/XAosXuNZyF/BiazfanxmamNRoxxVxka.png',
  };
}

// src/app.ts
export const layout = {  
  rightRender: (initialState, setInitialState) => {
    // xxx
    return 'xxx';
  },
footerRender: () => {
    // xxx
    return <xxx />;
  },
logout: () => {
    alert('退出登录成功');
  },
};
```


### 图表 @ant-design/charts
- https://charts.ant.design/zh
有些时候图表体积比较大就需要通过 CDN 的方式来加快加载。 CDN 模式下由于底层依赖不一样，为了降低包体积，从 1.0.5 版本开始，组织架构图、流程图、资金流向图、缩进树图被打包到 charts_g6.min.js 里，其它图表打包到 charts.min.js 里，使用时按需引入即可。
```ts
// config/config.ts
scripts: [
  'https://unpkg.com/react@17/umd/react.production.min.js',
  'https://unpkg.com/react-dom@17/umd/react-dom.production.min.js',
  'https://unpkg.com/@ant-design/charts@1.0.5/dist/charts.min.js',
  //使用 组织架构图、流程图、资金流向图、缩进树图 才需要使用
  'https://unpkg.com/@ant-design/charts@1.0.5/dist/charts_g6.min.js',
],
externals: {
 react: 'React',
 'react-dom': 'ReactDOM',
 "@ant-design/charts": "charts"
}
```

## Pro中的概念
### 区块
区块是研发资产的一种，它是一系列快速搭建页面的代码片段，它可以帮助你快速的在项目中初始化好一个页面，帮助你更快速的开发代码。当前的区块都是页面级别的区块，你可以理解为它是一些项目中经常会用到的典型页面的模板，使用区块其实相当于从已有的项目中复制一些页面的代码到你当前的项目中。

以前开发一个页面：创建 JS -> 创建 CSS -> 创建 Model -> 创建 service -> 写页面组件。现在开发一个页面：下载区块 -> 基于区块初始化好的页面组件修改代码。

### 模板
在 Pro 中资产被分为了两种，区块和模板。区块可以类比为一个组件，而模板代表一个页面。区块现在支持所有 antd 中的 demo，可以更加快速的将 demo 导入到项目中去。下图演示了快速开发一个 crud 页面

# 开发实战
## 环境变量
[config](https://github.com/ant-design/ant-design-pro/blob/33f562974d1c72e077652223bd816a57933fe242/config/config.ts) 是 node 环境，所以可以直接用 `process.env` 来直接拿到环境变量，但是非 node 环境的代码中，使用 `process.env` 可能只会获得 `NODE_ENV` 这个约定俗成的变量，别的变量 webpack 并不会自动注入。

> 关于 `process.env` 和 `NODE_ENV` 请看[这里](https://webpack.docschina.org/guides/production/#%E6%8C%87%E5%AE%9A-mode)。

这时候我们就需要使用 [`define`](https://umijs.org/zh/config/#define)，他是通过 [`define-plugin`](https://webpack.docschina.org/plugins/define-plugin/) 来实现的这个特性，在 [`config`](https://github.com/ant-design/ant-design-pro/blob/33f562974d1c72e077652223bd816a57933fe242/config/config.ts#L65) 中将 node 的环境变量注入 define 配置中。

使用时需要注意的是我们不需要通过 `window['key']` 的方式来使用它，而是直接使用。更具体的原理可以看一下[`define-plugin`](https://webpack.docschina.org/plugins/define-plugin/)插件的实现。
### REACT_APP_ENV
- 该变量代表当前应用所处环境的具体名称。如 dev、test、pre、prod 等。
- 如若需要在 `config` 外的非 node 环境文件中使用该环境变量，则需要在 `config` 导出默认 `defineConfig()` 时配置 `define{}`。

### UMI_ENV
- Pro 脚手架默认使用 Umi 作为底层框架，在 Umi 内可通过指定 `UMI_ENV` 环境变量来[区分不同环境的配置文件](https://umijs.org/zh-CN/docs/config#%E5%A4%9A%E7%8E%AF%E5%A2%83%E5%A4%9A%E4%BB%BD%E9%85%8D%E7%BD%AE)，`UMI_ENV` 需要在 `package.json` 内配置。
- 当 `UMI_ENV` 为 `test` 时，则必须在 config 目录下配置 `config.test.ts` 文件来管理 `test` 环境下的不同变量，Umi 框架会在 deep merge 后形成最终配置。
## 约定的配置和设置
### 主题配置
- 这里只能进行简单的设置 `config/defaultSetting.ts`
```ts
const settings: LayoutSettings & {
  pwa?: boolean;
  logo?: string;
} = {
  // 修改左上角的 logo
  logo: 'https://gw.alipayobjects.com/zos/rmsportal/KDpgvguMpGfqaHPjicRK.svg',
  // 设置标题的 title
  title: 'Ant Design Pro',
  navTheme: 'light',
  // 拂晓蓝
  primaryColor: '#1890ff',
  layout: 'mix',
  contentWidth: 'Fluid',
  fixSiderbar: true,
};

export default settings;
```
- 如果需要动态的设置，需要到`app.ts`中设置
```ts
// https://umijs.org/zh-CN/plugins/plugin-layout
export const layout: RunTimeLayoutConfig = ({ initialState }) => {
  return {
    title: initialState.serverName,
  };
};
```
- 同时 ProLayout 会根据菜单和路径来自动匹配浏览器的标题。欢迎页就会显示 `欢迎 - Ant Design Pro` ，如果你不喜欢这样可以设置 `pageTitleRender=false` 来关掉它。

- 如果需要根据内容动态更新页面标题，则可以使用浏览器 document.title API。 对于更复杂的场景，当您想从 React 组件更改标题时，可以使用第三方库 React Helmet。
### favicon
favicon 是展示在浏览器标签页上的内容，严格来说它是属于浏览器 meta 的一部分，浏览器认为 favicon 不会经常改动做了非常强的缓存。所以我们并没有做动态修改 favicon 的方案。

Pro 的默认 favicon 的配置存在于 `src\pages\document.ejs`, 我们可以在这里写 html 相关的代码, 我们默认配置了 public 文件夹中的 `favicon.ico` 文件，你可以直接覆盖来修改。当然也可以修改 href 为你想要的 cdn 地址。


### 加载页
由于场景的不同，Pro 中预设了不少的加载页。使用起来可能会有点混淆。
#### js 加载前
首先是在 js 还没加载成功，但是 html 已经加载成功的 landing 页面。这个页面的配置存在于 `src\pages\document.ejs` 中。它使用了 `home_bg.png` ,`pro_icon.svg` 和 `KDpgvguMpGfqaHPjicRK.svg` 三个带有品牌信息的图片，你可以按需修改他们。

#### js 加载后
如果我们在项目中打开了代码分割的话，在每次路由切换的时候都会进入一个加载页面。
```js
dynamicImport: {
 loading: '@ant-design/pro-layout/es/PageLoading',
}
```
这里的配置是一个路径的 string，也可以使用别名的配置。

#### 业务中的加载
在实际的项目中，我们需要等待用户信息或者鉴权系统的请求完成后才能展示页面。所以我们让 getInitialState支持了异步请求，同时在请求时会停止页面的渲染。这种情况下也是需要一个加载页的。我们可以在 src\app.tsx 中配置：
```
/** 获取用户信息比较慢的时候会展示一个 loading */
export const initialStateConfig = {
  loading: <PageLoading />,
};
```

## 菜单和路由配置
- 路由在`config/routes.ts`中
```ts
export default [
  {
    path: '/user',
    component: '../layouts/UserLayout',
    routes: [
      {
        // path 支持为一个 url，必须要以 http 开头
        path: 'https://pro.ant.design/docs/getting-started-cn',
        target: '_blank', // 点击新窗口打开
        name: '文档',
      },
      {
        // 访问路由，以 / 开头为绝对路径
        path: '/user/login',
        // ./Page ->src/pages/Login
        component: './NewPage',
      },
      {
        // 访问路由，如果不是以 / 开头会拼接父路由
        // reg -> /user/reg
        path: 'reg',
        // ./Page ->src/pages/Reg
        component: '../layouts/NewPage2',
      },
    ],
  },
];
```
- 路由配置完成后，访问页面即可看到效果，如果需要在菜单中显示，需要配置 `name`，`icon`，`hideChildrenInMenu`等来辅助生成菜单。
- 具体值如下：
	-   `name:string` 配置菜单的 name，如果配置了国际化，name 为国际化的 key。
	-   `icon:string` 配置菜单的图标，默认使用 antd 的 icon 名，默认不适用二级菜单的 icon。
	-   `access:string` 权限配置，需要预先配置权限
	-   `hideChildrenInMenu:true` 用于隐藏不需要在菜单中展示的子路由。
	-   `hideInMenu:true` 可以在菜单中不展示这个路由，包括子路由。
	-   `hideInBreadcrumb:true` 可以在面包屑中不展示这个路由，包括子路由。
	-   `headerRender:false` 当前路由不展示顶栏
	-   `footerRender:false` 当前路由不展示页脚
	-   `menuRender: false` 当前路由不展示菜单
	-   `menuHeaderRender: false` 当前路由不展示菜单顶栏
	-   `flatMenu` 子项往上提，只是不展示父菜单
-  在菜单中使用 iconFont
	- -   传入一个 iconFont 的 url 链接
	-   icon 命名必须以 icon-开头
```ts
// src/app.ts
export const layout: RunTimeLayoutConfig = ({ initialState }) => {
  return {
    iconfontUrl: '//at.alicdn.com/t/XXX.js',
  };
};
```
- 路由可以配置[layout](https://umijs.org/zh-CN/plugins/plugin-layout#layout)，

## 网络请求
- 使用request
```ts
request('/api/user', {
  params: {
    name: 1,
  },
  skipErrorHandler: true,
});
```
- 使用useRequest
```ts
import { useRequest } from 'umi';
export default () => {
  const { data, error, loading } = useRequest(() => {
    return services.getUserList('/api/test');
  });
  if (loading) {
    return <div>loading...</div>;
  }
  if (error) {
    return <div>{error.message}</div>;
  }
  return <div>{data.name}</div>;
};
```

### 中间件和拦截器
- 中间件
```ts
// src/app.ts
const demo1Middleware = async (ctx: Context, next: () => void) => {
  console.log('request1');
  await next();
  console.log('response1');
};

const demo2Middleware = async (ctx: Context, next: () => void) => {
  console.log('request2');
  await next();
  console.log('response2');
};

export const request: RequestConfig = {
  errorHandler,
  middlewares: [demo1Middleware, demo2Middleware],
};
```
- request1 -> request2 -> response -> response2 -> response1
- [参考文档](https://github.com/umijs/umi-request/blob/master/README_zh-CN.md#%E4%B8%AD%E9%97%B4%E4%BB%B6)
- 拦截器
	- 在网络请求的 `.then` 或 `catch` 处理前拦截，你可以在 `src/app.ts` 网络请求配置内增加如下配置：
```ts
// src/app.ts
const authHeaderInterceptor = (url: string, options: RequestOptionsInit) => {
  const authHeader = { Authorization: 'Bearer xxxxxx' };
  return {
    url: `${url}`,
    options: { ...options, interceptors: true, headers: authHeader },
  };
};
export const request: RequestConfig = {
  errorHandler,
  // 新增自动添加AccessToken的请求前拦截器
  requestInterceptors: [authHeaderInterceptor],
};


// src/app.ts
const demoResponseInterceptors = (response: Response, options: RequestOptionsInit) => {
  response.headers.append('interceptors', 'yes yo');
  return response;
};

export const request: RequestConfig = {
  errorHandler,
  responseInterceptors: [demoResponseInterceptors],
};
```

## 代理配置
```ts
const serveUrlMap = {
  dev: 'https://dev.pro.ant.design/',
  pre: 'https://pre.pro.ant.design/',
  test: 'https://test.pro.ant.design/',
  idc: 'https://idc.pro.ant.design/',
};

const { SERVE_ENV = 'idc' } = process.env;

export default {
  // ....
  proxy: {
    '/server/api/': {
      target: serveUrlMap[SERVE_ENV],
      changeOrigin: true,
      pathRewrite: { '^/server': '' },
    },
  },
};

```


## 数据Mock
- 使用`umi openapi` 生成相关的文档和配置
- 使用/umi/plugin/openapi 来访问
- MOCK=none时关闭



## 环境部署和访问
### 前后端集成
- Spring Boot集成
	- 将编译之后的文件复制到 spring boot 项目的 `/src/main/resources/static` 目录下。
	- 重新启动项目，访问 `http://localhost:8080/` 就可以看到效果。
```
@RequestMapping("/api/**")
public ApiResult api(HttpServletRequest request, HttpServletResponse response){
    return apiProxy.proxy(request, response);
}

@RequestMapping(value="/**", method=HTTPMethod.GET)
public String index(){
    return "index"
}
```
- express集成
```js
app.use(express.static(path.join(__dirname, 'build')));

app.get('/*', function (req, res) {
  res.sendFile(path.join(__dirname, 'build', 'index.html'));
});
```
- egg集成
```js
// controller
exports.index = function* () {
  yield this.render('App.jsx', {
    context: {
      user: this.session.user,
    },
  });
};

// router
app.get('home', '/*', 'home.index');
```

### nginx 完整配置
```nginx
server {
    listen 80;
    # gzip config
    gzip on;
    gzip_min_length 1k;
    gzip_comp_level 9;
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    gzip_vary on;
    gzip_disable "MSIE [1-6]\.";

    root /usr/share/nginx/html;

    location / {
        # 用于配合 browserHistory使用
        try_files $uri $uri/ /index.html;

        # 如果有资源，建议使用 https + http2，配合按需加载可以获得更好的体验
        # rewrite ^/(.*)$ https://preview.pro.ant.design/$1 permanent;

    }
    location /api {
        proxy_pass https://ant-design-pro.netlify.com;
        proxy_set_header   X-Forwarded-Proto $scheme;
        proxy_set_header   X-Real-IP         $remote_addr;
    }
}

server {
  # 如果有资源，建议使用 https + http2，配合按需加载可以获得更好的体验
  listen 443 ssl http2 default_server;

  # 证书的公私钥
  ssl_certificate /path/to/public.crt;
  ssl_certificate_key /path/to/private.key;

  location / {
        # 用于配合 browserHistory使用
        try_files $uri $uri/ /index.html;

  }
  location /api {
      proxy_pass https://ant-design-pro.netlify.com;
      proxy_set_header   X-Forwarded-Proto $scheme;
      proxy_set_header   Host              $http_host;
      proxy_set_header   X-Real-IP         $remote_addr;
  }
}
```
### 跨域访问
- 开发环境配置 proxy
```ts
proxy: {
  '/server/api/': {
    target: 'https://preview.pro.ant.design/',
    changeOrigin: true,
    pathRewrite: { '^/server': '' },
  },
},
```
- express 配置
```res.header('Access-Control-Allow-Origin', '你的项目地址，用*将会带来安全问题');
res.header('Access-Control-Allow-Headers', '*');
res.header('Access-Control-Allow-Methods', '*');
res.header('Content-Type', 'application/json;charset=utf-8');

```
- nginx 配置
```nginx
location ^~ /api {
  proxy_set_header Origin '';
  add_header Access-Control-Allow-Credentials true;
  add_header Access-Control-Allow-Headers $http_access_control_request_headers;
  add_header Access-Control-Allow-Methods POST,GET,OPTIONS,DELETE,PUT,HEAD,PATCH;
  add_header Access-Control-Allow-Origin $http_origin;
  add_header Access-Control-Expose-Headers $http_access_control_request_headers;

  if ($request_method = 'OPTIONS') {
    return 204;
  }
  if ($request_method != 'OPTIONS'){
    proxy_pass "你的项目地址";
  }
}

```

- java配置
```java
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class HttpErrorResponseUtil {
    public static void setResponeCorsHeader(HttpServletRequest request, HttpServletResponse response) {
      response.addHeader("Access-Control-Allow-Credentials", "true");
      response.addHeader("Access-Control-Allow-Methods", "POST,GET,OPTIONS,DELETE,PUT,HEAD,PATCH");
      response.addHeader("Access-Control-Allow-Origin", request.getHeader("Origin"));
      response.addHeader("Access-Control-Allow-Headers", request.getHeader("Access-Control-Request-Headers"));
    }
}
```

需要注意的是，目前 nginx 1.7.5 之前的版本，add_header 只对 2xx，3xx 的请求生效，5xx 的请求无法增加 header，仍会被浏览器跨域策略拦截，在 5xx 请求的 body 中包含的错误信息，前端无法获取到。

## 测试
### 单元测试
如果你要测试具体的组件的行为和预期的输出，我们推荐使用 [react-testing-library](https://github.com/testing-library/react-testing-library)。
umi 内置了 [jest](https://jestjs.io/) 作为测试方案。执行 `npm test` 会自动执行你的项目下的 *.(test|e2e).js 文件。

如果你要测试具体的组件的行为和预期的输出，我们推荐使用 [react-testing-library](https://github.com/testing-library/react-testing-library)。

> enzyme 也是个很好的选择，但是它跟更新有些缓慢，react-testing-library 是 react 官方推荐的。

`react-testing-library` 是一个用于测试 React 组件的库，其方式类似于最终用户使用组件的方式。它非常适合 React 组件和应用程序的单元测试、集成和 e2e 测试。它更直接地与 DOM 节点一起使用，因此建议与 jest-dom 一起使用,这样可以方便的做断言。

如果你要使用 `react-testing-library` 可以通过下列命令安装它。

```npm install --save @testing-library/react @testing-library/jest-dom```

接下来我们需要在 `tests/setupTests.js` 中引入的全局的配置，内容是这样的：

```
// react-testing-library 将您的组件显示为document.body，

// 这将为 jest-dom 添加一个自定义断言

import '@testing-library/jest-dom';
```
接下来我们就可以编写测试用例了。

```
import React from 'react';

import { render, screen } from '@testing-library/react';

import App from './App';

it('renders welcome message', () => {

 render(<App />);

 expect(screen.getByText('Learn React')).toBeInTheDocument();

});

it('renders', () => {

 const { asFragment } = render(<App text="Hello!" />);

 expect(asFragment()).toMatchSnapshot();

});
```
> [snapshot](https://jestjs.io/blog/2016/07/27/jest-14.html) 是一个相当低成本的测试方式，但是效果很好，可以很方便的监听一些 dom 变化。

我们可以从 [`react-testing-library 的文档和例子`](https://testing-library.com/docs/react-testing-library/intro/) 来查看更多的用法。

### e2e测试
E2E 是“End to End”的缩写，可以翻译成“端到端”测试。它模仿用户，从某个入口开始，逐步执行操作，直到完成某项工作。与单元测试不同，后者通常需要测试参数、参数类型、参数值、参数数量、返回值、抛出错误等，目的在于保证特定函数能够在任何情况下都稳定可靠完成工作。单元测试假定只要所有函数都正常工作，那么整个产品就能正常工作。

相对来说，E2E 测试并没有那么强调要覆盖全部使用场景，它关注的是 **一个完整的操作链是否能够完成**。对于 Web 前端来说，还关注**界面布局、内容信息是否符合预期**。

Pro 中自带了一个 e2e 的测试，我们可以在项目中快速的开始一个测试用例。
```js
it('page should have footer', async () => {
  const BASE_URL = 'http://localhost:8000';
  await page.goto(`${BASE_URL}`);
  await page.waitForSelector('footer', {
    timeout: 2000,
  });
  const haveFooter = await page.evaluate(() => document.getElementsByTagName('footer').length > 0);
  expect(haveFooter).toBeTruthy();
});
```

以上的用例是去访问 `http://localhost:8000` 的页面，并且判断是否含有 dom 元素 footer。在 Pro 中 `page` 会默认注入，这样可以每个测试都公用一份代码，更多的细节可以访问 [puppeteer](https://github.com/puppeteer/puppeteer)。


# 常用配置
## base
- 设置路由前缀，通常用于部署到非根目录。

## chainWebpack
- 通过 [webpack-chain](https://github.com/mozilla-neutrino/webpack-chain) 的 API 修改 webpack 配置。

## devServer
- 配置开发服务器。
- 包含以下子配置项：
	-   port，端口号，默认 `8000`
	-   host，默认 `0.0.0.0`
	-   https，是否启用 https server，同时也会开启 HTTP/2
	-   writeToDisk，生成 `assets` 到文件系统

## devtool
- 用户配置 sourcemap 类型。
- 常见的可选类型有：
	-   eval，最快的类型，但不支持低版本浏览器，如果编译慢，可以试试
	-   source-map，最慢最全的类型

更多类型详见 [webpack#devtool 配置](https://webpack.js.org/configuration/devtool/#devtool)。

## dynamicImport
- 是否启用按需加载，即是否把构建产物进行拆分，在需要的时候下载额外的 JS 再执行
- 默认关闭时，只生成一个 js 和一个 css，即 `umi.js` 和 `umi.css`。优点是省心，部署方便；缺点是对用户来说初次打开网站会比较慢

## dynamicImportSyntax
- 如果你不需要路由按需加载，只需要支持 `import()` 语法的 code splitting，可使用此配置。

## exportStatic
- 配置 html 的输出形式，默认只输出 `index.html`。
- 如果需要预渲染，请开启 [ssr](https://pro.ant.design/zh-CN/config/config#ssr-32) 配置，常用来解决没有服务端情况下，页面的 SEO 和首屏渲染提速。
- 如果开启 `exportStatic`，则会针对每个路由输出 html 文件。
- 包含以下几个属性：
	-   htmlSuffix，启用 `.html` 后缀。
	-   dynamicRoot，部署到任意路径。
	-   extraRoutePaths，生成额外的路径页面，用法和场景见 [预渲染动态路由](https://pro.ant.design/zh-CN/docs/ssr#%E9%A2%84%E6%B8%B2%E6%9F%93%E5%8A%A8%E6%80%81%E8%B7%AF%E7%94%B1)

## externals
- 设置哪些模块可以不被打包，通过 `<script>` 或其他方式引入，通常需要和 scripts 或 headScripts 配置同时使用。

## fastRefresh
- 快速刷新（Fast Refresh），开发时可以**保持组件状态**，同时编辑提供**即时反馈**。

## hash
- 配置是否让生成的文件包含 hash 后缀，通常用于增量发布和避免浏览器加载缓存。

## headScripts
- 配置 `<head>` 里的额外脚本，数组项为字符串或对象。

## history
- 配置 [history 类型和配置项](https://github.com/ReactTraining/history/blob/master/docs/GettingStarted.md)。
- 包含以下子配置项：
	-   type，可选 `browser`、`hash` 和 `memory`
	-   options，传给 create{{{ type }}}History 的配置项，每个类型器的配置项不同
	-   options 中，`getUserConfirmation` 由于是函数的格式，暂不支持配置
	-   options 中，`basename` 无需配置，通过 umi 的 `base` 配置指定

## links
配置额外的 link 标签。

## outputPath
- 指定输出路径。
- 不允许设定为 `src`、`public`、`pages`、`mock`、`config` 等约定目录

## proxy
- 配置代理能力。

## publicPath
- 配置 webpack 的 publicPath。当打包的时候，webpack 会在静态文件路径前面添加 `publicPath` 的值，当你需要修改静态文件地址时，比如使用 CDN 部署，把 `publicPath` 的值设为 CDN 的值就可以。如果使用一些特殊的文件系统，比如混合开发或者 cordova 等技术，可以尝试将 `publicPath` 设置成 `./` 相对路径。
- 相对路径 `./` 有一些限制，例如不支持多层路由 `/foo/bar`，只支持单层路径 `/foo`
- 如果你的应用部署在域名的子路径上，例如 `https://www.your-app.com/foo/`，你需要设置 `publicPath` 为 `/foo/`，如果同时要兼顾开发环境正常调试，你可以这样配置：
```js
import { defineConfig } from 'umi';

export default defineConfig({
  publicPath: process.env.NODE_ENV === 'production' ? '/foo/' : '/',
});```

## routes
- umi 的路由基于 [react-router@5](https://reacttraining.com/react-router/web/guides/quick-start) 实现，配置和 react-router 基本一致，详见[路由配置](https://pro.ant.design/zh-CN/config/docs/routing)章节。

## runtimePublicPath
- 配置是否启用运行时 publicPath。
- 通常用于一套代码在不同环境有不同的 publicPath 需要，然后 publicPath 由服务器通过 HTML 的 `window.publicPath` 全局变量输出。

## targets
- 配置需要兼容的浏览器最低版本，会自动引入 polyfill 和做语法转换。

## theme
- 配置主题，实际上是配 less 变量。


# 常用API
## history
## Link
## NavLink
- 特殊版本的 `<Link />` 。当指定路由（`to=指定路由`）命中时，可以附着特定样式。
## useHistory
## useLocation
## useParams