# React 顶层 API

`React` 是 React 库的入口。如果你通过使用 `<script>` 标签的方式来加载 React，则可以通过 `React` 全局变量对象来获得 React 的顶层 API。当你使用 ES6 与 npm 时，可以通过编写 `import React from 'react'` 来引入它们。当你使用 ES5 与 npm 时，则可以通过编写 `var React = require('react')` 来引入它们。

## 概览

### 组件

使用 React 组件可以将 UI 拆分为独立且复用的代码片段，每部分都可独立维护。你可以通过子类 `React.Component` 或 `React.PureComponent` 来定义 React 组件。

-   [`React.Component`](https://zh-hans.reactjs.org/docs/react-api.html#reactcomponent)
-   [`React.PureComponent`](https://zh-hans.reactjs.org/docs/react-api.html#reactpurecomponent)

如果你不使用 ES6 的 class，则可以使用 `create-react-class` 模块来替代。请参阅[不使用 ES6](https://zh-hans.reactjs.org/docs/react-without-es6.html) 以获取更多详细信息。

React 组件也可以被定义为可被包装的函数：

-   [`React.memo`](https://zh-hans.reactjs.org/docs/react-api.html#reactmemo)

### 创建 React 元素

我们建议[使用 JSX](https://zh-hans.reactjs.org/docs/introducing-jsx.html) 来编写你的 UI 组件。每个 JSX 元素都是调用 [`React.createElement()`](https://zh-hans.reactjs.org/docs/react-api.html#createelement) 的语法糖。一般来说，如果你使用了 JSX，就不再需要调用以下方法。

-   [`createElement()`](https://zh-hans.reactjs.org/docs/react-api.html#createelement)
-   [`createFactory()`](https://zh-hans.reactjs.org/docs/react-api.html#createfactory)

请参阅[不使用 JSX](https://zh-hans.reactjs.org/docs/react-without-jsx.html) 以获取更多详细信息。

### 转换元素

`React` 提供了几个用于操作元素的 API：

-   [`cloneElement()`](https://zh-hans.reactjs.org/docs/react-api.html#cloneelement)
-   [`isValidElement()`](https://zh-hans.reactjs.org/docs/react-api.html#isvalidelement)
-   [`React.Children`](https://zh-hans.reactjs.org/docs/react-api.html#reactchildren)

### Fragments

`React` 还提供了用于减少不必要嵌套的组件。

-   [`React.Fragment`](https://zh-hans.reactjs.org/docs/react-api.html#reactfragment)

### Refs

-   [`React.createRef`](https://zh-hans.reactjs.org/docs/react-api.html#reactcreateref)
-   [`React.forwardRef`](https://zh-hans.reactjs.org/docs/react-api.html#reactforwardref)

### Suspense

Suspense 使得组件可以“等待”某些操作结束后，再进行渲染。目前，Suspense 仅支持的使用场景是：[通过 `React.lazy` 动态加载组件](https://zh-hans.reactjs.org/docs/code-splitting.html#reactlazy)。它将在未来支持其它使用场景，如数据获取等。

-   [`React.lazy`](https://zh-hans.reactjs.org/docs/react-api.html#reactlazy)
-   [`React.Suspense`](https://zh-hans.reactjs.org/docs/react-api.html#reactsuspense)

### Hook

_Hook_ 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。Hook 拥有[专属文档章节](https://zh-hans.reactjs.org/docs/hooks-intro.html)和单独的 API 参考文档：

-   [基础 Hook](https://zh-hans.reactjs.org/docs/hooks-reference.html#basic-hooks)
    
    -   [`useState`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usestate)
    -   [`useEffect`](https://zh-hans.reactjs.org/docs/hooks-reference.html#useeffect)
    -   [`useContext`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usecontext)
-   [额外的 Hook](https://zh-hans.reactjs.org/docs/hooks-reference.html#additional-hooks)
    
    -   [`useReducer`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usereducer)
    -   [`useCallback`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usecallback)
    -   [`useMemo`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usememo)
    -   [`useRef`](https://zh-hans.reactjs.org/docs/hooks-reference.html#useref)
    -   [`useImperativeHandle`](https://zh-hans.reactjs.org/docs/hooks-reference.html#useimperativehandle)
    -   [`useLayoutEffect`](https://zh-hans.reactjs.org/docs/hooks-reference.html#uselayouteffect)
    -   [`useDebugValue`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usedebugvalue)

---

## 参考

### `React.Component`

`React.Component` 是使用 [ES6 classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 方式定义 React 组件的基类：

```
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

请参阅 [React.Component API 参考](https://zh-hans.reactjs.org/docs/react-component.html)，获取与基类 `React.Component` 相关方法和属性的详细列表。

---

### `React.PureComponent`

`React.PureComponent` 与 [`React.Component`](https://zh-hans.reactjs.org/docs/react-api.html#reactcomponent) 很相似。两者的区别在于 [`React.Component`](https://zh-hans.reactjs.org/docs/react-api.html#reactcomponent) 并未实现 [`shouldComponentUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#shouldcomponentupdate)，而 `React.PureComponent` 中以浅层对比 prop 和 state 的方式来实现了该函数。

如果赋予 React 组件相同的 props 和 state，`render()` 函数会渲染相同的内容，那么在某些情况下使用 `React.PureComponent` 可提高性能。

> 注意
> 
> `React.PureComponent` 中的 `shouldComponentUpdate()` 仅作对象的浅层比较。如果对象中包含复杂的数据结构，则有可能因为无法检查深层的差别，产生错误的比对结果。仅在你的 props 和 state 较为简单时，才使用 `React.PureComponent`，或者在深层数据结构发生变化时调用 [`forceUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#forceupdate) 来确保组件被正确地更新。你也可以考虑使用 [immutable 对象](https://facebook.github.io/immutable-js/)加速嵌套数据的比较。
> 
> 此外，`React.PureComponent` 中的 `shouldComponentUpdate()` 将跳过所有子组件树的 prop 更新。因此，请确保所有子组件也都是“纯”的组件。

---

### `React.memo`

```
const MyComponent = React.memo(function MyComponent(props) {
  /* 使用 props 渲染 */
});
```

`React.memo` 为[高阶组件](https://zh-hans.reactjs.org/docs/higher-order-components.html)。

如果你的组件在相同 props 的情况下渲染相同的结果，那么你可以通过将其包装在 `React.memo` 中调用，以此通过记忆组件渲染结果的方式来提高组件的性能表现。这意味着在这种情况下，React 将跳过渲染组件的操作并直接复用最近一次渲染的结果。

`React.memo` 仅检查 props 变更。如果函数组件被 `React.memo` 包裹，且其实现中拥有 [`useState`](https://zh-hans.reactjs.org/docs/hooks-state.html)，[`useReducer`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usereducer) 或 [`useContext`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usecontext) 的 Hook，当 state 或 context 发生变化时，它仍会重新渲染。

默认情况下其只会对复杂对象做浅层对比，如果你想要控制对比过程，那么请将自定义的比较函数通过第二个参数传入来实现。

```
function MyComponent(props) {
  /* 使用 props 渲染 */
}
function areEqual(prevProps, nextProps) {
  /*
  如果把 nextProps 传入 render 方法的返回结果与
  将 prevProps 传入 render 方法的返回结果一致则返回 true，
  否则返回 false
  */
}
export default React.memo(MyComponent, areEqual);
```

此方法仅作为**[性能优化](https://zh-hans.reactjs.org/docs/optimizing-performance.html)**的方式而存在。但请不要依赖它来“阻止”渲染，因为这会产生 bug。

> 注意
> 
> 与 class 组件中 [`shouldComponentUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#shouldcomponentupdate) 方法不同的是，如果 props 相等，`areEqual` 会返回 `true`；如果 props 不相等，则返回 `false`。这与 `shouldComponentUpdate` 方法的返回值相反。

---

### `createElement()`

```
React.createElement(
  type,
  [props],
  [...children]
)
```

创建并返回指定类型的新 [React 元素](https://zh-hans.reactjs.org/docs/rendering-elements.html)。其中的类型参数既可以是标签名字符串（如 `'div'` 或 `'span'`），也可以是 [React 组件](https://zh-hans.reactjs.org/docs/components-and-props.html) 类型 （class 组件或函数组件），或是 [React fragment](https://zh-hans.reactjs.org/docs/react-api.html#reactfragment) 类型。

使用 [JSX](https://zh-hans.reactjs.org/docs/introducing-jsx.html) 编写的代码将会被转换成使用 `React.createElement()` 的形式。如果使用了 JSX 方式，那么一般来说就不需要直接调用 `React.createElement()`。请查阅[不使用 JSX](https://zh-hans.reactjs.org/docs/react-without-jsx.html) 章节获得更多信息。

---

### `cloneElement()`

```
React.cloneElement(
  element,
  [config],
  [...children]
)
```

以 `element` 元素为样板克隆并返回新的 React 元素。`config` 中应包含新的 props，`key` 或 `ref`。返回元素的 props 是将新的 props 与原始元素的 props 浅层合并后的结果。新的子元素将取代现有的子元素，如果在 `config` 中未出现 `key` 或 `ref`，那么原始元素的 `key` 和 `ref` 将被保留。

`React.cloneElement()` 几乎等同于：

```
<element.type {...element.props} {...props}>{children}</element.type>
```

但是，这也保留了组件的 `ref`。这意味着当通过 `ref` 获取子节点时，你将不会意外地从你祖先节点上窃取它。相同的 `ref` 将添加到克隆后的新元素中。如果存在新的 `ref` 或 `key` 将覆盖之前的。

引入此 API 是为了替换已弃用的 `React.addons.cloneWithProps()`。

---

### `createFactory()`

```
React.createFactory(type)
```

返回用于生成指定类型 React 元素的函数。与 [`React.createElement()`](https://zh-hans.reactjs.org/docs/react-api.html#createelement) 相似的是，类型参数既可以是标签名字符串（像是 `'div'` 或 `'span'`），也可以是 [React 组件](https://zh-hans.reactjs.org/docs/components-and-props.html) 类型 （class 组件或函数组件），或是 [React fragment](https://zh-hans.reactjs.org/docs/react-api.html#reactfragment) 类型。

此辅助函数已废弃，建议使用 JSX 或直接调用 `React.createElement()` 来替代它。

如果你使用 JSX，通常不会直接调用 `React.createFactory()`。请参阅[不使用 JSX](https://zh-hans.reactjs.org/docs/react-without-jsx.html) 以获得更多信息。

---

### `isValidElement()`

```
React.isValidElement(object)
```

验证对象是否为 React 元素，返回值为 `true` 或 `false`。

---

### `React.Children`

`React.Children` 提供了用于处理 `this.props.children` 不透明数据结构的实用方法。

####  `React.Children.map`

```
React.Children.map(children, function[(thisArg)])
```

在 `children` 里的每个直接子节点上调用一个函数，并将 `this` 设置为 `thisArg`。如果 `children` 是一个数组，它将被遍历并为数组中的每个子节点调用该函数。如果子节点为 `null` 或是 `undefined`，则此方法将返回 `null` 或是 `undefined`，而不会返回数组。

> 注意
> 
> 如果 `children` 是一个 `Fragment` 对象，它将被视为单一子节点的情况处理，而不会被遍历。

#### `React.Children.forEach`

```
React.Children.forEach(children, function[(thisArg)])
```

与 [`React.Children.map()`](https://zh-hans.reactjs.org/docs/react-api.html#reactchildrenmap) 类似，但它不会返回一个数组。

#### `React.Children.count`

```
React.Children.count(children)
```

返回 `children` 中的组件总数量，等同于通过 `map` 或 `forEach` 调用回调函数的次数。

#### `React.Children.only`

```
React.Children.only(children)
```

验证 `children` 是否只有一个子节点（一个 React 元素），如果有则返回它，否则此方法会抛出错误。

> 注意：
> 
> `React.Children.only()` 不接受 [`React.Children.map()`](https://zh-hans.reactjs.org/docs/react-api.html#reactchildrenmap) 的返回值，因为它是一个数组而并不是 React 元素。

#### `React.Children.toArray`

```
React.Children.toArray(children)
```

将 `children` 这个复杂的数据结构以数组的方式扁平展开并返回，并为每个子节点分配一个 key。当你想要在渲染函数中操作子节点的集合时，它会非常实用，特别是当你想要在向下传递 `this.props.children` 之前对内容重新排序或获取子集时。

> 注意：
> 
> `React.Children.toArray()` 在拉平展开子节点列表时，更改 key 值以保留嵌套数组的语义。也就是说，`toArray` 会为返回数组中的每个 key 添加前缀，以使得每个元素 key 的范围都限定在此函数入参数组的对象内。

---

### `React.Fragment`

`React.Fragment` 组件能够在不额外创建 DOM 元素的情况下，让 `render()` 方法中返回多个元素。

```
render() {
  return (
    <React.Fragment>
      Some text.
      <h2>A heading</h2>
    </React.Fragment>
  );
}
```

你也可以使用其简写语法 `<></>`。欲了解更多相关信息，请参阅 [React v16.2.0: Fragments 支持改进](https://zh-hans.reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html)。

### `React.createRef`

`React.createRef` 创建一个能够通过 ref 属性附加到 React 元素的 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html)。

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);

    this.inputRef = React.createRef();  }

  render() {
    return <input type="text" ref={this.inputRef} />;  }

  componentDidMount() {
    this.inputRef.current.focus();  }
}
```

### `React.forwardRef`

`React.forwardRef` 会创建一个React组件，这个组件能够将其接受的 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 属性转发到其组件树下的另一个组件中。这种技术并不常见，但在以下两种场景中特别有用：

-   [转发 refs 到 DOM 组件](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)
-   [在高阶组件中转发 refs](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-in-higher-order-components)

`React.forwardRef` 接受渲染函数作为参数。React 将使用 `props` 和 `ref` 作为参数来调用此函数。此函数应返回 React 节点。

```
const FancyButton = React.forwardRef((props, ref) => (  <button ref={ref} className="FancyButton">    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

在上述的示例中，React 会将 `<FancyButton ref={ref}>` 元素的 `ref` 作为第二个参数传递给 `React.forwardRef` 函数中的渲染函数。该渲染函数会将 `ref` 传递给 `<button ref={ref}>` 元素。

因此，当 React 附加了 ref 属性之后，`ref.current` 将直接指向 `<button>` DOM 元素实例。

欲了解更多相关信息，请参阅 [refs 转发](https://zh-hans.reactjs.org/docs/forwarding-refs.html)。

### `React.lazy`

`React.lazy()` 允许你定义一个动态加载的组件。这有助于缩减 bundle 的体积，并延迟加载在初次渲染时未用到的组件。

你可以在[代码分割文档](https://zh-hans.reactjs.org/docs/code-splitting.html#reactlazy)中学习如何使用它。查阅[此文章](https://medium.com/@pomber/lazy-loading-and-preloading-components-in-react-16-6-804de091c82d)可以了解更多用法细节。

```
// 这个组件是动态加载的
const SomeComponent = React.lazy(() => import('./SomeComponent'));
```

请注意，渲染 `lazy` 组件依赖该组件渲染树上层的 `<React.Suspense>` 组件。这是指定加载指示器（loading indicator）的方式。

> **注意**
> 
> 使用 `React.lazy` 的动态引入特性需要 JS 环境支持 Promise。在 IE11 及以下版本的浏览器中需要通过引入 polyfill 来使用该特性。

### `React.Suspense`

`React.Suspense` 可以指定加载指示器（loading indicator），以防其组件树中的某些子组件尚未具备渲染条件。目前，懒加载组件是 `<React.Suspense>` 支持的**唯一**用例：

```
// 该组件是动态加载的
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // 显示 <Spinner> 组件直至 OtherComponent 加载完成
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```

它已被收录在了我们的[代码分割指南](https://zh-hans.reactjs.org/docs/code-splitting.html#reactlazy)中。请注意，`lazy` 组件可以位于 `Suspense` 组件树的深处——它不必包装树中的每一个延迟加载组件。最佳实践是将 `<Suspense>` 置于你想展示加载指示器（loading indicator）的位置，而 `lazy()` 则可被放置于任何你想要做代码分割的地方。

虽然目前尚未支持其它特性，但未来我们计划让 `Suspense` 支持包括数据获取在内的更多场景。你可以在 [roadmap](https://zh-hans.reactjs.org/blog/2018/11/27/react-16-roadmap.html) 中了解相关信息。

> 注意:
> 
> `React.lazy()` 和 `<React.Suspense>` 尚未在 `ReactDOMServer` 中支持。这是已知问题，将会在未来解决。


# React.Component

本章节提供了 React class 组件的详细 API 参考。本章节默认你已熟悉基本的 React 概念，例如 [组件 & Props](https://zh-hans.reactjs.org/docs/components-and-props.html)，以及 [State & 生命周期](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html)等。如果你还未熟悉，请先阅读之前章节进行学习。

## 概览

React 的组件可以定义为 class 或函数的形式。class 组件目前提供了更多的功能，这些功能将在此章节中详细介绍。如需定义 class 组件，需要继承 `React.Component`：

```
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

在 `React.Component` 的子类中有个必须定义的 [`render()`](https://zh-hans.reactjs.org/docs/react-component.html#render) 函数。本章节介绍其他方法均为可选。

**我们强烈建议你不要创建自己的组件基类。** 在 React 组件中，[代码重用的主要方式是组合而不是继承](https://zh-hans.reactjs.org/docs/composition-vs-inheritance.html)。

> 注意:
> 
> React 并不会强制你使用 ES6 的 class 语法。如果你倾向于不使用它，你可以使用 `create-react-class` 模块或类似的自定义抽象来代替。请查阅[不使用 ES6](https://zh-hans.reactjs.org/docs/react-without-es6.html) 了解更多。

### 组件的生命周期

每个组件都包含 “生命周期方法”，你可以重写这些方法，以便于在运行过程中特定的阶段执行这些方法。**你可以使用此[生命周期图谱](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)作为速查表**。在下述列表中，常用的生命周期方法会被加粗。其余生命周期函数的使用则相对罕见。

#### 挂载

当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：

-   [**`constructor()`**](https://zh-hans.reactjs.org/docs/react-component.html#constructor)
-   [`static getDerivedStateFromProps()`](https://zh-hans.reactjs.org/docs/react-component.html#static-getderivedstatefromprops)
-   [**`render()`**](https://zh-hans.reactjs.org/docs/react-component.html#render)
-   [**`componentDidMount()`**](https://zh-hans.reactjs.org/docs/react-component.html#componentdidmount)

> 注意:
> 
> 下述生命周期方法即将过时，在新代码中应该[避免使用它们](https://zh-hans.reactjs.org/blog/2018/03/27/update-on-async-rendering.html)：
> 
> -   [`UNSAFE_componentWillMount()`](https://zh-hans.reactjs.org/docs/react-component.html#unsafe_componentwillmount)

#### 更新

当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：

-   [`static getDerivedStateFromProps()`](https://zh-hans.reactjs.org/docs/react-component.html#static-getderivedstatefromprops)
-   [`shouldComponentUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#shouldcomponentupdate)
-   [**`render()`**](https://zh-hans.reactjs.org/docs/react-component.html#render)
-   [`getSnapshotBeforeUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#getsnapshotbeforeupdate)
-   [**`componentDidUpdate()`**](https://zh-hans.reactjs.org/docs/react-component.html#componentdidupdate)

> 注意:
> 
> 下述方法即将过时，在新代码中应该[避免使用它们](https://zh-hans.reactjs.org/blog/2018/03/27/update-on-async-rendering.html)：
> 
> -   [`UNSAFE_componentWillUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#unsafe_componentwillupdate)
> -   [`UNSAFE_componentWillReceiveProps()`](https://zh-hans.reactjs.org/docs/react-component.html#unsafe_componentwillreceiveprops)

#### 卸载

当组件从 DOM 中移除时会调用如下方法：

-   [**`componentWillUnmount()`**](https://zh-hans.reactjs.org/docs/react-component.html#componentwillunmount)

#### 错误处理

当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：

-   [`static getDerivedStateFromError()`](https://zh-hans.reactjs.org/docs/react-component.html#static-getderivedstatefromerror)
-   [`componentDidCatch()`](https://zh-hans.reactjs.org/docs/react-component.html#componentdidcatch)

### 其他 APIs

组件还提供了一些额外的 API：

-   [`setState()`](https://zh-hans.reactjs.org/docs/react-component.html#setstate)
-   [`forceUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#forceupdate)

### class 属性

-   [`defaultProps`](https://zh-hans.reactjs.org/docs/react-component.html#defaultprops)
-   [`displayName`](https://zh-hans.reactjs.org/docs/react-component.html#displayname)

### 实例属性

-   [`props`](https://zh-hans.reactjs.org/docs/react-component.html#props)
-   [`state`](https://zh-hans.reactjs.org/docs/react-component.html#state)

---

## 参考

### 常用的生命周期方法

本节中的方法涵盖了创建 React 组件时能遇到的绝大多数用例。**想要更好了解这些方法，可以参考[生命周期图谱](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)。**

### `render()`

```
render()
```

`render()` 方法是 class 组件中唯一必须实现的方法。

当 `render` 被调用时，它会检查 `this.props` 和 `this.state` 的变化并返回以下类型之一：

-   **React 元素**。通常通过 JSX 创建。例如，`<div />` 会被 React 渲染为 DOM 节点，`<MyComponent />` 会被 React 渲染为自定义组件，无论是 `<div />` 还是 `<MyComponent />` 均为 React 元素。
-   **数组或 fragments**。 使得 render 方法可以返回多个元素。欲了解更多详细信息，请参阅 [fragments](https://zh-hans.reactjs.org/docs/fragments.html) 文档。
-   **Portals**。可以渲染子节点到不同的 DOM 子树中。欲了解更多详细信息，请参阅有关 [portals](https://zh-hans.reactjs.org/docs/portals.html) 的文档
-   **字符串或数值类型**。它们在 DOM 中会被渲染为文本节点
-   **布尔类型或 `null`**。什么都不渲染。（主要用于支持返回 `test && <Child />` 的模式，其中 test 为布尔类型。)

`render()` 函数应该为纯函数，这意味着在不修改组件 state 的情况下，每次调用时都返回相同的结果，并且它不会直接与浏览器交互。

如需与浏览器进行交互，请在 `componentDidMount()` 或其他生命周期方法中执行你的操作。保持 `render()` 为纯函数，可以使组件更容易思考。

> 注意
> 
> 如果 `shouldComponentUpdate()` 返回 false，则不会调用 `render()`。

---

### `constructor()`

```
constructor(props)
```

**如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数。**

在 React 组件挂载之前，会调用它的构造函数。在为 React.Component 子类实现构造函数时，应在其他语句之前调用 `super(props)`。否则，`this.props` 在构造函数中可能会出现未定义的 bug。

通常，在 React 中，构造函数仅用于以下两种情况：

-   通过给 `this.state` 赋值对象来初始化[内部 state](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html)。
-   为[事件处理函数](https://zh-hans.reactjs.org/docs/handling-events.html)绑定实例

在 `constructor()` 函数中**不要调用 `setState()` 方法**。如果你的组件需要使用内部 state，请直接在构造函数中为 **`this.state` 赋值初始 state**：

```
constructor(props) {
  super(props);
  // 不要在这里调用 this.setState()
  this.state = { counter: 0 };
  this.handleClick = this.handleClick.bind(this);
}
```

只能在构造函数中直接为 `this.state` 赋值。如需在其他方法中赋值，你应使用 `this.setState()` 替代。

要避免在构造函数中引入任何副作用或订阅。如遇到此场景，请将对应的操作放置在 `componentDidMount` 中。

> 注意
> 
> **避免将 props 的值复制给 state！这是一个常见的错误：**
> 
> ```
> constructor(props) {
>  super(props);
>  // 不要这样做
>  this.state = { color: props.color };
> }
> ```
> 
> 如此做毫无必要（你可以直接使用 `this.props.color`），同时还产生了 bug（更新 prop 中的 `color` 时，并不会影响 state）。
> 
> **只有在你刻意忽略 prop 更新的情况下使用。**此时，应将 prop 重命名为 `initialColor` 或 `defaultColor`。必要时，你可以[修改它的 `key`](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key)，以强制“重置”其内部 state。
> 
> 请参阅关于[避免派生状态的博文](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)，以了解出现 state 依赖 props 的情况该如何处理。

---

### `componentDidMount()`

```
componentDidMount()
```

`componentDidMount()` 会在组件挂载后（插入 DOM 树中）立即调用。依赖于 DOM 节点的初始化应该放在这里。如需通过网络请求获取数据，此处是实例化请求的好地方。

这个方法是比较适合添加订阅的地方。如果添加了订阅，请不要忘记在 `componentWillUnmount()` 里取消订阅

你可以在 `componentDidMount()` 里**直接调用 `setState()`**。它将触发额外渲染，但此渲染会发生在浏览器更新屏幕之前。如此保证了即使在 `render()` 两次调用的情况下，用户也不会看到中间状态。请谨慎使用该模式，因为它会导致性能问题。通常，你应该在 `constructor()` 中初始化 state。如果你的渲染依赖于 DOM 节点的大小或位置，比如实现 modals 和 tooltips 等情况下，你可以使用此方式处理

---

### `componentDidUpdate()`

```
componentDidUpdate(prevProps, prevState, snapshot)
```

`componentDidUpdate()` 会在更新后会被立即调用。首次渲染不会执行此方法。

当组件更新后，可以在此处对 DOM 进行操作。如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求。（例如，当 props 未发生变化时，则不会执行网络请求）。

```
componentDidUpdate(prevProps) {
  // 典型用法（不要忘记比较 props）：
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }
}
```

你也可以在 `componentDidUpdate()` 中**直接调用 `setState()`**，但请注意**它必须被包裹在一个条件语句里**，正如上述的例子那样进行处理，否则会导致死循环。它还会导致额外的重新渲染，虽然用户不可见，但会影响组件性能。不要将 props “镜像”给 state，请考虑直接使用 props。 欲了解更多有关内容，请参阅[为什么 props 复制给 state 会产生 bug](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)。

如果组件实现了 `getSnapshotBeforeUpdate()` 生命周期（不常用），则它的返回值将作为 `componentDidUpdate()` 的第三个参数 “snapshot” 参数传递。否则此参数将为 undefined。

> 注意
> 
> 如果 [`shouldComponentUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#shouldcomponentupdate) 返回值为 false，则不会调用 `componentDidUpdate()`。

---

### `componentWillUnmount()`

```
componentWillUnmount()
```

`componentWillUnmount()` 会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作，例如，清除 timer，取消网络请求或清除在 `componentDidMount()` 中创建的订阅等。

`componentWillUnmount()` 中**不应调用 `setState()`**，因为该组件将永远不会重新渲染。组件实例卸载后，将永远不会再挂载它。

---

### 不常用的生命周期方法

本节中的生命周期方法并不太常用。它们偶尔会很方便，但是大部分情况下组件可能都不需要它们。你可以在[生命周期图谱](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)中，选择“显示不常用的生命周期”复选框，即可看到下述相关方法。

### `shouldComponentUpdate()`

```
shouldComponentUpdate(nextProps, nextState)
```

根据 `shouldComponentUpdate()` 的返回值，判断 React 组件的输出是否受当前 state 或 props 更改的影响。默认行为是 state 每次发生变化组件都会重新渲染。大部分情况下，你应该遵循默认行为。

当 props 或 state 发生变化时，`shouldComponentUpdate()` 会在渲染执行之前被调用。返回值默认为 true。首次渲染或使用 `forceUpdate()` 时不会调用该方法。

此方法仅作为**[性能优化的方式](https://zh-hans.reactjs.org/docs/optimizing-performance.html)**而存在。不要企图依靠此方法来“阻止”渲染，因为这可能会产生 bug。你应该**考虑使用内置的 [`PureComponent`](https://zh-hans.reactjs.org/docs/react-api.html#reactpurecomponent) 组件**，而不是手动编写 `shouldComponentUpdate()`。`PureComponent` 会对 props 和 state 进行浅层比较，并减少了跳过必要更新的可能性。

如果你一定要手动编写此函数，可以将 `this.props` 与 `nextProps` 以及 `this.state` 与`nextState` 进行比较，并返回 `false` 以告知 React 可以跳过更新。请注意，返回 `false` 并不会阻止子组件在 state 更改时重新渲染。

我们不建议在 `shouldComponentUpdate()` 中进行深层比较或使用 `JSON.stringify()`。这样非常影响效率，且会损害性能。

目前，如果 `shouldComponentUpdate()` 返回 `false`，则不会调用 [`UNSAFE_componentWillUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#unsafe_componentwillupdate)，[`render()`](https://zh-hans.reactjs.org/docs/react-component.html#render) 和 [`componentDidUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#componentdidupdate)。后续版本，React 可能会将 `shouldComponentUpdate` 视为提示而不是严格的指令，并且，当返回 `false` 时，仍可能导致组件重新渲染。

---

### `static getDerivedStateFromProps()`

```
static getDerivedStateFromProps(props, state)
```

`getDerivedStateFromProps` 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。它应返回一个对象来更新 state，如果返回 `null` 则不更新任何内容。

此方法适用于[罕见的用例](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#when-to-use-derived-state)，即 state 的值在任何时候都取决于 props。例如，实现 `<Transition>` 组件可能很方便，该组件会比较当前组件与下一组件，以决定针对哪些组件进行转场动画。

派生状态会导致代码冗余，并使组件难以维护。 [确保你已熟悉这些简单的替代方案：](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)

-   如果你需要**执行副作用**（例如，数据提取或动画）以响应 props 中的更改，请改用 [`componentDidUpdate`](https://zh-hans.reactjs.org/docs/react-component.html#componentdidupdate)。
-   如果只想在 **prop 更改时重新计算某些数据**，[请使用 memoization helper 代替](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization)。
-   如果你想**在 prop 更改时“重置”某些 state**，请考虑使组件[完全受控](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-controlled-component)或[使用 `key` 使组件完全不受控](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key) 代替。

此方法无权访问组件实例。如果你需要，可以通过提取组件 props 的纯函数及 class 之外的状态，在`getDerivedStateFromProps()`和其他 class 方法之间重用代码。

请注意，不管原因是什么，都会在_每次_渲染前触发此方法。这与 `UNSAFE_componentWillReceiveProps` 形成对比，后者仅在父组件重新渲染时触发，而不是在内部调用 `setState` 时。

---

### `getSnapshotBeforeUpdate()`

```
getSnapshotBeforeUpdate(prevProps, prevState)
```

`getSnapshotBeforeUpdate()` 在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。此生命周期方法的任何返回值将作为参数传递给 `componentDidUpdate()`。

此用法并不常见，但它可能出现在 UI 处理中，如需要以特殊方式处理滚动位置的聊天线程等。

应返回 snapshot 的值（或 `null`）。

例如：

```
class ScrollingList extends React.Component {
  constructor(props) {
    super(props);
    this.listRef = React.createRef();
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // 我们是否在 list 中添加新的 items ？
    // 捕获滚动​​位置以便我们稍后调整滚动位置。
    if (prevProps.list.length < this.props.list.length) {
      const list = this.listRef.current;
      return list.scrollHeight - list.scrollTop;
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    // 如果我们 snapshot 有值，说明我们刚刚添加了新的 items，
    // 调整滚动位置使得这些新 items 不会将旧的 items 推出视图。
    //（这里的 snapshot 是 getSnapshotBeforeUpdate 的返回值）
    if (snapshot !== null) {
      const list = this.listRef.current;
      list.scrollTop = list.scrollHeight - snapshot;
    }
  }

  render() {
    return (
      <div ref={this.listRef}>{/* ...contents... */}</div>
    );
  }
}
```

在上述示例中，重点是从 `getSnapshotBeforeUpdate` 读取 `scrollHeight` 属性，因为 “render” 阶段生命周期（如 `render`）和 “commit” 阶段生命周期（如 `getSnapshotBeforeUpdate` 和 `componentDidUpdate`）之间可能存在延迟。

---

### Error boundaries

[Error boundaries](https://zh-hans.reactjs.org/docs/error-boundaries.html) 是 React 组件，它会在其子组件树中的任何位置捕获 JavaScript 错误，并记录这些错误，展示降级 UI 而不是崩溃的组件树。Error boundaries 组件会捕获在渲染期间，在生命周期方法以及其整个树的构造函数中发生的错误。

如果 class 组件定义了生命周期方法 `static getDerivedStateFromError()` 或 `componentDidCatch()` 中的任何一个（或两者），它就成为了 Error boundaries。通过生命周期更新 state 可让组件捕获树中未处理的 JavaScript 错误并展示降级 UI。

仅使用 Error boundaries 组件来从意外异常中恢复的情况；**不要将它们用于流程控制。**

欲了解更多详细信息，请参阅 [_React 16 中的错误处理_](https://zh-hans.reactjs.org/blog/2017/07/26/error-handling-in-react-16.html)。

> 注意
> 
> Error boundaries 仅捕获组件树中**以下**组件中的错误。但它本身的错误无法捕获。

### `static getDerivedStateFromError()`

```
static getDerivedStateFromError(error)
```

此生命周期会在后代组件抛出错误后被调用。 它将抛出的错误作为参数，并返回一个值以更新 state

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {    // 更新 state 使下一次渲染可以显降级 UI    return { hasError: true };  }
  render() {
    if (this.state.hasError) {      // 你可以渲染任何自定义的降级  UI      return <h1>Something went wrong.</h1>;    }
    return this.props.children;
  }
}
```

> 注意
> 
> `getDerivedStateFromError()` 会在`渲染`阶段调用，因此不允许出现副作用。 如遇此类情况，请改用 `componentDidCatch()`。

---

### `componentDidCatch()`

```
componentDidCatch(error, info)
```

此生命周期在后代组件抛出错误后被调用。 它接收两个参数：

1.  `error` —— 抛出的错误。
2.  `info` —— 带有 `componentStack` key 的对象，其中包含[有关组件引发错误的栈信息](https://zh-hans.reactjs.org/docs/error-boundaries.html#component-stack-traces)。

`componentDidCatch()` 会在“提交”阶段被调用，因此允许执行副作用。 它应该用于记录错误之类的情况：

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染可以显示降级 UI
    return { hasError: true };
  }

  componentDidCatch(error, info) {    // "组件堆栈" 例子:    //   in ComponentThatThrows (created by App)    //   in ErrorBoundary (created by App)    //   in div (created by App)    //   in App    logComponentStackToMyService(info.componentStack);  }
  render() {
    if (this.state.hasError) {
      // 你可以渲染任何自定义的降级 UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}
```

React 的开发和生产构建版本在 `componentDidCatch()` 的方式上有轻微差别。

在开发模式下，错误会冒泡至 `window`，这意味着任何 `window.onerror` 或 `window.addEventListener('error', callback)` 会中断这些已经被 `componentDidCatch()` 捕获的错误。

相反，在生产模式下，错误不会冒泡，这意味着任何根错误处理器只会接受那些没有显式地被 `componentDidCatch()` 捕获的错误。

> 注意
> 
> 如果发生错误，你可以通过调用 `setState` 使用 `componentDidCatch()` 渲染降级 UI，但在未来的版本中将不推荐这样做。 可以使用静态 `getDerivedStateFromError()` 来处理降级渲染。

---

### 过时的生命周期方法

以下生命周期方法标记为“过时”。这些方法仍然有效，但不建议在新代码中使用它们。参阅此[博客文章](https://zh-hans.reactjs.org/blog/2018/03/27/update-on-async-rendering.html)以了解更多有关迁移旧版生命周期方法的信息。

### `UNSAFE_componentWillMount()`

```
UNSAFE_componentWillMount()
```

> 注意
> 
> 此生命周期之前名为 `componentWillMount`。该名称将继续使用至 React 17。可以使用 [`rename-unsafe-lifecycles` codemod](https://github.com/reactjs/react-codemod#rename-unsafe-lifecycles) 自动更新你的组件。

`UNSAFE_componentWillMount()` 在挂载之前被调用。它在 `render()` 之前调用，因此在此方法中同步调用 `setState()` 不会触发额外渲染。通常，我们建议使用 `constructor()` 来初始化 state。

避免在此方法中引入任何副作用或订阅。如遇此种情况，请改用 `componentDidMount()`。

此方法是服务端渲染唯一会调用的生命周期函数。

---

### `UNSAFE_componentWillReceiveProps()`

```
UNSAFE_componentWillReceiveProps(nextProps)
```

> 注意
> 
> 此生命周期之前名为 `componentWillReceiveProps`。该名称将继续使用至 React 17。可以使用 [`rename-unsafe-lifecycles` codemod](https://github.com/reactjs/react-codemod#rename-unsafe-lifecycles) 自动更新你的组件。

> 注意:
> 
> 使用此生命周期方法通常会出现 bug 和不一致性：
> 
> -   如果你需要**执行副作用**（例如，数据提取或动画）以响应 props 中的更改，请改用 [`componentDidUpdate`](https://zh-hans.reactjs.org/docs/react-component.html#componentdidupdate) 生命周期。
> -   如果你使用 `componentWillReceiveProps` **仅在 prop 更改时重新计算某些数据**，请[使用 memoization helper 代替](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization)。
> -   如果你使用 `componentWillReceiveProps` 是为了**在 prop 更改时“重置”某些 state**，请考虑使组件[完全受控](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-controlled-component)或[使用 `key` 使组件完全不受控](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key) 代替。
> 
> 对于其他使用场景，[请遵循此博客文章中有关派生状态的建议](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)。

`UNSAFE_componentWillReceiveProps()` 会在已挂载的组件接收新的 props 之前被调用。如果你需要更新状态以响应 prop 更改（例如，重置它），你可以比较 `this.props` 和 `nextProps` 并在此方法中使用 `this.setState()` 执行 state 转换。

请注意，如果父组件导致组件重新渲染，即使 props 没有更改，也会调用此方法。如果只想处理更改，请确保进行当前值与变更值的比较。

在[挂载](https://zh-hans.reactjs.org/docs/react-component.html#mounting)过程中，React 不会针对初始 props 调用 `UNSAFE_componentWillReceiveProps()`。组件只会在组件的 props 更新时调用此方法。调用 `this.setState()` 通常不会触发 `UNSAFE_componentWillReceiveProps()`。

---

### `UNSAFE_componentWillUpdate()`

```
UNSAFE_componentWillUpdate(nextProps, nextState)
```

> 注意
> 
> 此生命周期之前名为 `componentWillUpdate`。该名称将继续使用至 React 17。可以使用 [`rename-unsafe-lifecycles` codemod](https://github.com/reactjs/react-codemod#rename-unsafe-lifecycles) 自动更新你的组件。

当组件收到新的 props 或 state 时，会在渲染之前调用 `UNSAFE_componentWillUpdate()`。使用此作为在更新发生之前执行准备更新的机会。初始渲染不会调用此方法。

注意，你不能此方法中调用 `this.setState()`；在 `UNSAFE_componentWillUpdate()` 返回之前，你也不应该执行任何其他操作（例如，dispatch Redux 的 action）触发对 React 组件的更新

通常，此方法可以替换为 `componentDidUpdate()`。如果你在此方法中读取 DOM 信息（例如，为了保存滚动位置），则可以将此逻辑移至 `getSnapshotBeforeUpdate()` 中。

> 注意
> 
> 如果 `shouldComponentUpdate()` 返回 false，则不会调用 `UNSAFE_componentWillUpdate()`。

---

## 其他 API

不同于上述生命周期方法（React 主动调用），以下方法是你可以在组件中调用的方法。

只有两个方法：`setState()` 和 `forceUpdate()`。

### setState()`

```
setState(updater, [callback])
```

`setState()` 将对组件 state 的更改排入队列，并通知 React 需要使用更新后的 state 重新渲染此组件及其子组件。这是用于更新用户界面以响应事件处理器和处理服务器数据的主要方式

将 `setState()` 视为_请求_而不是立即更新组件的命令。为了更好的感知性能，React 会延迟调用它，然后通过一次传递更新多个组件。React 并不会保证 state 的变更会立即生效。

`setState()` 并不总是立即更新组件。它会批量推迟更新。这使得在调用 `setState()` 后立即读取 `this.state` 成为了隐患。为了消除隐患，请使用 `componentDidUpdate` 或者 `setState` 的回调函数（`setState(updater, callback)`），这两种方式都可以保证在应用更新后触发。如需基于之前的 state 来设置当前的 state，请阅读下述关于参数 `updater` 的内容。

除非 `shouldComponentUpdate()` 返回 `false`，否则 `setState()` 将始终执行重新渲染操作。如果可变对象被使用，且无法在 `shouldComponentUpdate()` 中实现条件渲染，那么仅在新旧状态不一时调用 `setState()`可以避免不必要的重新渲染

参数一为带有形式参数的 `updater` 函数：

```
(state, props) => stateChange
```

`state` 是对应用变化时组件状态的引用。当然，它不应直接被修改。你应该使用基于 `state` 和 `props` 构建的新对象来表示变化。例如，假设我们想根据 `props.step` 来增加 state：

```
this.setState((state, props) => {
  return {counter: state.counter + props.step};
});
```

updater 函数中接收的 `state` 和 `props` 都保证为最新。updater 的返回值会与 `state` 进行浅合并。

`setState()` 的第二个参数为可选的回调函数，它将在 `setState` 完成合并并重新渲染组件后执行。通常，我们建议使用 `componentDidUpdate()` 来代替此方式。

`setState()` 的第一个参数除了接受函数外，还可以接受对象类型：

```
setState(stateChange[, callback])
```

`stateChange` 会将传入的对象浅层合并到新的 state 中，例如，调整购物车商品数：

```
this.setState({quantity: 2})
```

这种形式的 `setState()` 也是异步的，并且在同一周期内会对多个 `setState` 进行批处理。例如，如果在同一周期内多次设置商品数量增加，则相当于：

```
Object.assign(
  previousState,
  {quantity: state.quantity + 1},
  {quantity: state.quantity + 1},
  ...
)
```

后调用的 `setState()` 将覆盖同一周期内先调用 `setState` 的值，因此商品数仅增加一次。如果后续状态取决于当前状态，我们建议使用 updater 函数的形式代替：

```
this.setState((state) => {
  return {quantity: state.quantity + 1};
});
```

有关更多详细信息，请参阅：

-   [State 和生命周期指南](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html)
-   [深入学习：何时以及为什么 `setState()` 会批量执行？](https://stackoverflow.com/a/48610973/458193)
-   [深入：为什么不直接更新 `this.state`？](https://github.com/facebook/react/issues/11527#issuecomment-360199710)

---

### `forceUpdate()`

```
component.forceUpdate(callback)
```

默认情况下，当组件的 state 或 props 发生变化时，组件将重新渲染。如果 `render()` 方法依赖于其他数据，则可以调用 `forceUpdate()` 强制让组件重新渲染。

调用 `forceUpdate()` 将致使组件调用 `render()` 方法，此操作会跳过该组件的 `shouldComponentUpdate()`。但其子组件会触发正常的生命周期方法，包括 `shouldComponentUpdate()` 方法。如果标记发生变化，React 仍将只更新 DOM。

通常你应该避免使用 `forceUpdate()`，尽量在 `render()` 中使用 `this.props` 和 `this.state`。

---

## Class 属性

### `defaultProps`

`defaultProps` 可以为 Class 组件添加默认 props。这一般用于 props 未赋值，但又不能为 `null` 的情况。例如：

```
class CustomButton extends React.Component {
  // ...
}

CustomButton.defaultProps = {
  color: 'blue'
};
```

如果未提供 `props.color`，则默认设置为 `'blue'`

```
  render() {
    return <CustomButton /> ; // props.color 将设置为 'blue'
  }
```

如果 `props.color` 被设置为 `null`，则它将保持为 `null`

```
  render() {
    return <CustomButton color={null} /> ; // props.color 将保持是 null
  }
```

---

### `displayName`

`displayName` 字符串多用于调试消息。通常，你不需要设置它，因为它可以根据函数组件或 class 组件的名称推断出来。如果调试时需要显示不同的名称或创建高阶组件，请参阅[使用 displayname 轻松进行调试](https://zh-hans.reactjs.org/docs/higher-order-components.html#convention-wrap-the-display-name-for-easy-debugging)了解更多。

---

## 实例属性

### `props`

`this.props` 包括被该组件调用者定义的 props。欲了解 props 的详细介绍，请参阅[组件 & Props](https://zh-hans.reactjs.org/docs/components-and-props.html)。

需特别注意，`this.props.children` 是一个特殊的 prop，通常由 JSX 表达式中的子组件组成，而非组件本身定义。

### `state`

组件中的 state 包含了随时可能发生变化的数据。state 由用户自定义，它是一个普通 JavaScript 对象。

如果某些值未用于渲染或数据流（例如，计时器 ID），则不必将其设置为 state。此类值可以在组件实例上定义。

欲了解关于 state 的更多信息，请参阅 [State & 生命周期](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html)。

永远不要直接改变 `this.state`，因为后续调用的 `setState()` 可能会替换掉你的改变。请把 `this.state` 看作是不可变的。


# ReactDOM

如果你使用一个 `<script>` 标签引入 React，所有的顶层 API 都能在全局 `ReactDOM` 上调用。如果你使用 npm 和 ES6，你可以用 `import ReactDOM from 'react-dom'`。如果你使用 npm 和 ES5，你可以用 `var ReactDOM = require('react-dom')`。

## 概览

`react-dom` 的 package 提供了可在应用顶层使用的 DOM（DOM-specific）方法，如果有需要，你可以把这些方法用于 React 模型以外的地方。不过一般情况下，大部分组件都不需要使用这个模块。

-   [`render()`](https://zh-hans.reactjs.org/docs/react-dom.html#render)
-   [`hydrate()`](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate)
-   [`unmountComponentAtNode()`](https://zh-hans.reactjs.org/docs/react-dom.html#unmountcomponentatnode)
-   [`findDOMNode()`](https://zh-hans.reactjs.org/docs/react-dom.html#finddomnode)
-   [`createPortal()`](https://zh-hans.reactjs.org/docs/react-dom.html#createportal)

### 浏览器支持

React 支持所有的现代浏览器，包括 IE9 及以上版本，但是需要为旧版浏览器比如 IE9 和 IE10 引入[相关的 polyfills 依赖](https://zh-hans.reactjs.org/docs/javascript-environment-requirements.html)。

> 注意：
> 
> 我们不支持那些不兼容 ES5 方法的旧版浏览器，但如果你的应用包含了 polyfill，例如 [es5-shim 和 es5-sham](https://github.com/es-shims/es5-shim) 你可能会发现你的应用仍然可以在这些浏览器中正常运行。但是如果你选择这种方法，你便需要孤军奋战了。

---

## 参考

### `render()`

```
ReactDOM.render(element, container[, callback])
```

在提供的 `container` 里渲染一个 React 元素，并返回对该组件的[引用](https://zh-hans.reactjs.org/docs/more-about-refs.html)（或者针对[无状态组件](https://zh-hans.reactjs.org/docs/components-and-props.html#function-and-class-components)返回 `null`）。

如果 React 元素之前已经在 `container` 里渲染过，这将会对其执行更新操作，并仅会在必要时改变 DOM 以映射最新的 React 元素。

如果提供了可选的回调函数，该回调将在组件被渲染或更新之后被执行。

> 注意：
> 
> `ReactDOM.render()` 会控制你传入容器节点里的内容。当首次调用时，容器节点里的所有 DOM 元素都会被替换，后续的调用则会使用 React 的 DOM 差分算法（DOM diffing algorithm）进行高效的更新。
> 
> `ReactDOM.render()` 不会修改容器节点（只会修改容器的子节点）。可以在不覆盖现有子节点的情况下，将组件插入已有的 DOM 节点中。
> 
> `ReactDOM.render()` 目前会返回对根组件 `ReactComponent` 实例的引用。 但是，目前应该避免使用返回的引用，因为它是历史遗留下来的内容，而且在未来版本的 React 中，组件渲染在某些情况下可能会是异步的。 如果你真的需要获得对根组件 `ReactComponent` 实例的引用，那么推荐为根元素添加 [callback ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html#callback-refs)。
> 
> 使用 `ReactDOM.render()` 对服务端渲染容器进行 hydrate 操作的方式已经被废弃，并且会在 React 17 被移除。作为替代，请使用 [`hydrate()`](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate)。

---

### `hydrate()`

```
ReactDOM.hydrate(element, container[, callback])
```

与 [`render()`](https://zh-hans.reactjs.org/docs/react-dom.html#render) 相同，但它用于在 [`ReactDOMServer`](https://zh-hans.reactjs.org/docs/react-dom-server.html) 渲染的容器中对 HTML 的内容进行 hydrate 操作。React 会尝试在已有标记上绑定事件监听器。

React 希望服务端与客户端渲染的内容完全一致。React 可以弥补文本内容的差异，但是你需要将不匹配的地方作为 bug 进行修复。在开发者模式下，React 会对 hydration 操作过程中的不匹配进行警告。但并不能保证在不匹配的情况下，修补属性的差异。由于性能的关系，这一点非常重要，因为大多是应用中不匹配的情况很少见，并且验证所有标记的成本非常昂贵。

如果单个元素的属性或者文本内容，在服务端和客户端之间有无法避免差异（比如：时间戳），则可以为元素添加 `suppressHydrationWarning={true}` 来消除警告。这种方式只在一级深度上有效，应只作为一种应急方案（escape hatch）。请不要过度使用！除非它是文本内容，否则 React 仍不会尝试修补差异，因此在未来的更新之前，仍会保持不一致。

如果你执意要在服务端与客户端渲染不同内容，你可以采用双重（two-pass）渲染。在客户端渲染不同内容的组件可以读取类似于 `this.state.isClient` 的 state 变量，你可以在 `componentDidMount()` 里将它设置为 `true`。这种方式在初始渲染过程中会与服务端渲染相同的内容，从而避免不匹配的情况出现，但在 hydration 操作之后，会同步进行额外的渲染操作。注意，因为进行了两次渲染，这种方式会使得组件渲染变慢，请小心使用。

记得保证弱网环境下的用户体验。JavaScript 代码的加载要比最初的 HTML 渲染晚的多。因此如果你只在客户端渲染不同的内容，其转换可能会不稳定。但是，如果执行顺利，那么在服务端负责渲染的 shell 会对渲染提供帮助，并且只显示客户端上额外的小组件。欲了解如何在不出现标记不匹配的情况下执行此操作，请参考上一段的解释。

---

### `unmountComponentAtNode()`

```
ReactDOM.unmountComponentAtNode(container)
```

从 DOM 中卸载组件，会将其事件处理器（event handlers）和 state 一并清除。如果指定容器上没有对应已挂载的组件，这个函数什么也不会做。如果组件被移除将会返回 `true`，如果没有组件可被移除将会返回 `false`。

---

### `findDOMNode()`

> 注意:
> 
> `findDOMNode` 是一个访问底层 DOM 节点的应急方案（escape hatch）。在大多数情况下，不推荐使用该方法，因为它会破坏组件的抽象结构。[严格模式下该方法已弃用。](https://zh-hans.reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)

```
ReactDOM.findDOMNode(component)
```

如果组件已经被挂载到 DOM 上，此方法会返回浏览器中相应的原生 DOM 元素。此方法对于从 DOM 中读取值很有用，例如获取表单字段的值或者执行 DOM 检测（performing DOM measurements）。**大多数情况下，你可以绑定一个 ref 到 DOM 节点上，可以完全避免使用 findDOMNode。**

当组件渲染的内容为 `null` 或 `false` 时，`findDOMNode` 也会返回 `null`。当组件渲染的是字符串时，`findDOMNode` 返回的是字符串对应的 DOM 节点。从 React 16 开始，组件可能会返回有多个子节点的 fragment，在这种情况下，`findDOMNode` 会返回第一个非空子节点对应的 DOM 节点。

> 注意:
> 
> `findDOMNode` 只在已挂载的组件上可用（即，已经放置在 DOM 中的组件）。如果你尝试调用未挂载的组件（例如在一个还未创建的组件上调用 `render()` 中的 `findDOMNode()`）将会引发异常。
> 
> `findDOMNode` 不能用于函数组件。

---

### `createPortal()`

```
ReactDOM.createPortal(child, container)
```

创建 portal。[Portal](https://zh-hans.reactjs.org/docs/portals.html) 将提供一种将子节点渲染到 DOM 节点中的方式，该节点存在于 DOM 组件的层次结构之外。


# ReactDOMServer

`ReactDOMServer` 对象允许你将组件渲染成静态标记。通常，它被使用在 Node 服务端上：

```
// ES modules
import ReactDOMServer from 'react-dom/server';
// CommonJS
var ReactDOMServer = require('react-dom/server');
```

## 概览

下述方法可以被使用在服务端和浏览器环境。

-   [`renderToString()`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertostring)
-   [`renderToStaticMarkup()`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertostaticmarkup)

下述附加方法依赖一个**只能在服务端使用**的 package（`stream`）。它们在浏览器中不起作用。

-   [`renderToNodeStream()`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertonodestream)
-   [`renderToStaticNodeStream()`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertostaticnodestream)

---

## 参考

### `renderToString()`

```
ReactDOMServer.renderToString(element)
```

将 React 元素渲染为初始 HTML。React 将返回一个 HTML 字符串。你可以使用此方法在服务端生成 HTML，并在首次请求时将标记下发，以加快页面加载速度，并允许搜索引擎爬取你的页面以达到 SEO 优化的目的。

如果你在已有服务端渲染标记的节点上调用 [`ReactDOM.hydrate()`](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate) 方法，React 将会保留该节点且只进行事件处理绑定，从而让你有一个非常高性能的首次加载体验。

---

### `renderToStaticMarkup()`

```
ReactDOMServer.renderToStaticMarkup(element)
```

此方法与 [`renderToString`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertostring) 相似，但此方法不会在 React 内部创建的额外 DOM 属性，例如 `data-reactroot`。如果你希望把 React 当作静态页面生成器来使用，此方法会非常有用，因为去除额外的属性可以节省一些字节。

如果你计划在前端使用 React 以使得标记可交互，请不要使用此方法。你可以在服务端上使用 [`renderToString`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertostring) 或在前端上使用 [`ReactDOM.hydrate()`](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate) 来代替此方法。

---

### `renderToNodeStream()`

```
ReactDOMServer.renderToNodeStream(element)
```

将一个 React 元素渲染成其初始 HTML。返回一个可输出 HTML 字符串的[可读流](https://nodejs.org/api/stream.html#stream_readable_streams)。通过可读流输出的 HTML 完全等同于 [`ReactDOMServer.renderToString`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertostring) 返回的 HTML。你可以使用本方法在服务器上生成 HTML，并在初始请求时将标记下发，以加快页面加载速度，并允许搜索引擎抓取你的页面以达到 SEO 优化的目的。

如果你在已有服务端渲染标记的节点上调用 [`ReactDOM.hydrate()`](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate) 方法，React 将会保留该节点且只进行事件处理绑定，从而让你有一个非常高性能的首次加载体验。

> 注意:
> 
> 这个 API 仅允许在服务端使用。不允许在浏览器使用。
> 
> 通过本方法返回的流会返回一个由 utf-8 编码的字节流。如果你需要另一种编码的流，请查看像 [iconv-lite](https://www.npmjs.com/package/iconv-lite) 这样的项目，它为转换文本提供了转换流。

---

### `renderToStaticNodeStream()`

```
ReactDOMServer.renderToStaticNodeStream(element)
```

此方法与 [`renderToNodeStream`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertonodestream) 相似，但此方法不会在 React 内部创建的额外 DOM 属性，例如 `data-reactroot`。如果你希望把 React 当作静态页面生成器来使用，此方法会非常有用，因为去除额外的属性可以节省一些字节。

通过可读流输出的 HTML，完全等同于 [`ReactDOMServer.renderToStaticMarkup`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertostaticmarkup) 返回的 HTML。

如果你计划在前端使用 React 以使得标记可交互，请不要使用此方法。你可以在服务端上使用 [`renderToNodeStream`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertonodestream) 或在前端上使用 [`ReactDOM.hydrate()`](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate) 来代替此方法。

> 注意:
> 
> 此 API 仅限于服务端使用，在浏览器中是不可用的。
> 
> 通过本方法返回的流会返回一个由 utf-8 编码的字节流。如果你需要另一种编码的流，请查看像 [iconv-lite](https://www.npmjs.com/package/iconv-lite) 这样的项目，它为转换文本提供了转换流。


# DOM 元素

React 实现了一套独立于浏览器的 DOM 系统，兼顾了性能和跨浏览器的兼容性。我们借此机会完善了浏览器 DOM 实现的一些特殊情况。

在 React 中，所有的 DOM 特性和属性（包括事件处理）都应该是小驼峰命名的方式。例如，与 HTML 中的 `tabindex` 属性对应的 React 的属性是 `tabIndex`。例外的情况是 `aria-*` 以及 `data-*` 属性，一律使用小写字母命名。比如, 你依然可以用 `aria-label` 作为 `aria-label`。

## 属性差异

React 与 HTML 之间有很多属性存在差异：

### checked

当 `<input>` 组件的 type 类型为 `checkbox` 或 `radio` 时，组件支持 `checked` 属性。你可以使用它来设置组件是否被选中。这对于构建受控组件（controlled components）很有帮助。而 `defaultChecked` 则是非受控组件的属性，用于设置组件首次挂载时是否被选中。

### className

`className` 属性用于指定 CSS 的 class，此特性适用于所有常规 DOM 节点和 SVG 元素，如 `<div>`，`<a>` 及其它标签。

如果你在 React 中使用 Web Components（这是一种不常见的使用方式），请使用 class 属性代替。

### dangerouslySetInnerHTML

`dangerouslySetInnerHTML` 是 React 为浏览器 DOM 提供 `innerHTML` 的替换方案。通常来讲，使用代码直接设置 HTML 存在风险，因为很容易无意中使用户暴露于[跨站脚本（XSS）](https://en.wikipedia.org/wiki/Cross-site_scripting)的攻击。因此，你可以直接在 React 中设置 HTML，但当你想设置 `dangerouslySetInnerHTML` 时，需要向其传递包含 key 为 `__html` 的对象，以此来警示你。例如：

```
function createMarkup() {
  return {__html: 'First &middot; Second'};
}

function MyComponent() {
  return <div dangerouslySetInnerHTML={createMarkup()} />;
}
```

### htmlFor

由于 `for` 在 JavaScript 中是保留字，所以 React 元素中使用了 `htmlFor` 来代替。

### onChange

`onChange` 事件与预期行为一致：每当表单字段变化时，该事件都会被触发。我们故意没有使用浏览器已有的默认行为，是因为 `onChange` 在浏览器中的行为和名称不对应，并且 React 依靠了该事件实时处理用户输入。

### selected

如果要将 `<option>` 标记为已选中状态，请在 `select` 的 `value` 中引用该选项的值。 请查阅 [“select 标签”](https://zh-hans.reactjs.org/docs/forms.html#the-select-tag) 了解更多详情。

### style

> 注意
> 
> 在文档中，部分例子为了方便，直接使用了 `style`，但是**通常不推荐将 `style` 属性作为设置元素样式的主要方式**。在多数情况下，应使用 [`className`](https://zh-hans.reactjs.org/docs/dom-elements.html#classname) 属性来引用外部 CSS 样式表中定义的 class。`style` 在 React 应用中多用于在渲染过程中添加动态计算的样式。另请参阅：[FAQ：Styling 和 CSS](https://zh-hans.reactjs.org/docs/faq-styling.html)。

`style` 接受一个采用小驼峰命名属性的 JavaScript 对象，而不是 CSS 字符串。这与 DOM 中 `style` 的 JavaScript 属性是一致的，同时会更高效的，且能预防跨站脚本（XSS）的安全漏洞。例如：

```
const divStyle = {
  color: 'blue',
  backgroundImage: 'url(' + imgUrl + ')',
};

function HelloWorldComponent() {
  return <div style={divStyle}>Hello World!</div>;
}
```

注意：样式不会自动补齐前缀。如需支持旧版浏览器，请手动补充对应的样式属性：

```
const divStyle = {
  WebkitTransition: 'all', // note the capital 'W' here
  msTransition: 'all' // 'ms' is the only lowercase vendor prefix
};

function ComponentWithTransition() {
  return <div style={divStyle}>This should work cross-browser</div>;
}
```

Style 中的 key 采用小驼峰命名是为了与 JS 访问 DOM 节点的属性保持一致（例如：`node.style.backgroundImage` ）。浏览器引擎前缀都应以大写字母开头，[除了 `ms`](https://www.andismith.com/blog/2012/02/modernizr-prefixed/)。因此，`WebkitTransition` 首字母为 ”W”。

React 会自动添加 ”px” 后缀到内联样式为数字的属性后。如需使用 ”px” 以外的单位，请将此值设为数字与所需单位组成的字符串。例如：

```
// Result style: '10px'
<div style={{ height: 10 }}>
  Hello World!
</div>

// Result style: '10%'
<div style={{ height: '10%' }}>
  Hello World!
</div>
```

但并非所有样式属性都转换为像素字符串。有些样式属性是没有单位的(例如 `zoom`，`order`，`flex`)。无单位属性的完整列表在[此处](https://github.com/facebook/react/blob/4131af3e4bf52f3a003537ec95a1655147c81270/src/renderers/dom/shared/CSSProperty.js#L15-L59)。

### suppressContentEditableWarning

通常，当拥有子节点的元素被标记为 `contentEditable` 时，React 会发出一个警告，因为这不会生效。该属性将禁止此警告。尽量不要使用该属性，除非你要构建一个类似 [Draft.js](https://facebook.github.io/draft-js/) 的手动管理 `contentEditable` 属性的库。

### suppressHydrationWarning

如果你使用 React 服务端渲染，通常会在当服务端与客户端渲染不同的内容时发出警告。但是，在一些极少数的情况下，很难甚至于不可能保证内容的一致性。例如，在服务端和客户端上，时间戳通常是不同的。

如果设置 `suppressHydrationWarning` 为 `true`，React 将不会警告你属性与元素内容不一致。它只会对元素一级深度有效，并且打算作为应急方案。因此不要过度使用它。你可以在 [`ReactDOM.hydrate()` 文档](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate) 中了解更多关于 hydration 的信息。

### value

`<input>`、`<select>` 和 `<textarea>` 组件支持 `value` 属性。你可以使用它为组件设置 value。这对于构建受控组件是非常有帮助。`defaultValue` 属性对应的是非受控组件的属性，用于设置组件第一次挂载时的 value。

## All Supported HTML Attributes

在 React 16 中，任何标准的[或自定义的](https://zh-hans.reactjs.org/blog/2017/09/08/dom-attributes-in-react-16.html) DOM 属性都是完全支持的。

React 为 DOM 提供了一套以 JavaScript 为中心的 API。由于 React 组件经常采用自定义或和 DOM 相关的 props 的关系，React 采用了`小驼峰命名`的方式，正如 DOM APIs 那样：

```
<div tabIndex={-1} />      // Just like node.tabIndex DOM API
<div className="Button" /> // Just like node.className DOM API
<input readOnly={true} />  // Just like node.readOnly DOM API
```

除了上述文档提到的特殊拼写方式以外，这些 props 的用法与 HTML 的属性也极为类似。

React 支持的 DOM 属性有：

```
accept acceptCharset accessKey action allowFullScreen alt async autoComplete
autoFocus autoPlay capture cellPadding cellSpacing challenge charSet checked
cite classID className colSpan cols content contentEditable contextMenu controls
controlsList coords crossOrigin data dateTime default defer dir disabled
download draggable encType form formAction formEncType formMethod formNoValidate
formTarget frameBorder headers height hidden high href hrefLang htmlFor
httpEquiv icon id inputMode integrity is keyParams keyType kind label lang list
loop low manifest marginHeight marginWidth max maxLength media mediaGroup method
min minLength multiple muted name noValidate nonce open optimum pattern
placeholder poster preload profile radioGroup readOnly rel required reversed
role rowSpan rows sandbox scope scoped scrolling seamless selected shape size
sizes span spellCheck src srcDoc srcLang srcSet start step style summary
tabIndex target title type useMap value width wmode wrap
```

同样，所有的 SVG 属性也完全得到了支持：

```
accentHeight accumulate additive alignmentBaseline allowReorder alphabetic
amplitude arabicForm ascent attributeName attributeType autoReverse azimuth
baseFrequency baseProfile baselineShift bbox begin bias by calcMode capHeight
clip clipPath clipPathUnits clipRule colorInterpolation
colorInterpolationFilters colorProfile colorRendering contentScriptType
contentStyleType cursor cx cy d decelerate descent diffuseConstant direction
display divisor dominantBaseline dur dx dy edgeMode elevation enableBackground
end exponent externalResourcesRequired fill fillOpacity fillRule filter
filterRes filterUnits floodColor floodOpacity focusable fontFamily fontSize
fontSizeAdjust fontStretch fontStyle fontVariant fontWeight format from fx fy
g1 g2 glyphName glyphOrientationHorizontal glyphOrientationVertical glyphRef
gradientTransform gradientUnits hanging horizAdvX horizOriginX ideographic
imageRendering in in2 intercept k k1 k2 k3 k4 kernelMatrix kernelUnitLength
kerning keyPoints keySplines keyTimes lengthAdjust letterSpacing lightingColor
limitingConeAngle local markerEnd markerHeight markerMid markerStart
markerUnits markerWidth mask maskContentUnits maskUnits mathematical mode
numOctaves offset opacity operator order orient orientation origin overflow
overlinePosition overlineThickness paintOrder panose1 pathLength
patternContentUnits patternTransform patternUnits pointerEvents points
pointsAtX pointsAtY pointsAtZ preserveAlpha preserveAspectRatio primitiveUnits
r radius refX refY renderingIntent repeatCount repeatDur requiredExtensions
requiredFeatures restart result rotate rx ry scale seed shapeRendering slope
spacing specularConstant specularExponent speed spreadMethod startOffset
stdDeviation stemh stemv stitchTiles stopColor stopOpacity
strikethroughPosition strikethroughThickness string stroke strokeDasharray
strokeDashoffset strokeLinecap strokeLinejoin strokeMiterlimit strokeOpacity
strokeWidth surfaceScale systemLanguage tableValues targetX targetY textAnchor
textDecoration textLength textRendering to transform u1 u2 underlinePosition
underlineThickness unicode unicodeBidi unicodeRange unitsPerEm vAlphabetic
vHanging vIdeographic vMathematical values vectorEffect version vertAdvY
vertOriginX vertOriginY viewBox viewTarget visibility widths wordSpacing
writingMode x x1 x2 xChannelSelector xHeight xlinkActuate xlinkArcrole
xlinkHref xlinkRole xlinkShow xlinkTitle xlinkType xmlns xmlnsXlink xmlBase
xmlLang xmlSpace y y1 y2 yChannelSelector z zoomAndPan
```

你也可以使用自定义属性，但要注意属性名全都为小写。


# 合成事件

此参考指南记录了构成 React 事件系统一部分的 `SyntheticEvent` 包装器。请参考有关[事件处理](https://zh-hans.reactjs.org/docs/handling-events.html)的指南来了解更多。

## 概览

`SyntheticEvent` 实例将被传递给你的事件处理函数，它是浏览器的原生事件的跨浏览器包装器。除兼容所有浏览器外，它还拥有和浏览器原生事件相同的接口，包括 `stopPropagation()` 和 `preventDefault()`。

如果因为某些原因，当你需要使用浏览器的底层事件时，只需要使用 `nativeEvent` 属性来获取即可。合成事件与浏览器的原生事件不同，也不会直接映射到原生事件。例如，在 `onMouseLeave` 事件中 `event.nativeEvent` 将指向 `mouseout` 事件。每个 `SyntheticEvent` 对象都包含以下属性：

```
boolean bubbles
boolean cancelable
DOMEventTarget currentTarget
boolean defaultPrevented
number eventPhase
boolean isTrusted
DOMEvent nativeEvent
void preventDefault()
boolean isDefaultPrevented()
void stopPropagation()
boolean isPropagationStopped()
void persist()
DOMEventTarget target
number timeStamp
string type
```

> 注意：
> 
> 从 v17 开始，`e.persist()` 将不再生效，因为 `SyntheticEvent` 不再放入[事件池](https://zh-hans.reactjs.org/docs/legacy-event-pooling.html)中。

> 注意：
> 
> 从 v0.14 开始，事件处理器返回 `false` 时，不再阻止事件传递。你可以酌情手动调用 `e.stopPropagation()` 或 `e.preventDefault()` 作为替代方案。

## 支持的事件

React 通过将事件 normalize 以让他们在不同浏览器中拥有一致的属性。

以下的事件处理函数在冒泡阶段被触发。如需注册捕获阶段的事件处理函数，则应为事件名添加 `Capture`。例如，处理捕获阶段的点击事件请使用 `onClickCapture`，而不是 `onClick`。

-   [Clipboard Events](https://zh-hans.reactjs.org/docs/events.html#clipboard-events)
-   [Composition Events](https://zh-hans.reactjs.org/docs/events.html#composition-events)
-   [Keyboard Events](https://zh-hans.reactjs.org/docs/events.html#keyboard-events)
-   [Focus Events](https://zh-hans.reactjs.org/docs/events.html#focus-events)
-   [Form Events](https://zh-hans.reactjs.org/docs/events.html#form-events)
-   [Generic Events](https://zh-hans.reactjs.org/docs/events.html#generic-events)
-   [Mouse Events](https://zh-hans.reactjs.org/docs/events.html#mouse-events)
-   [Pointer Events](https://zh-hans.reactjs.org/docs/events.html#pointer-events)
-   [Selection Events](https://zh-hans.reactjs.org/docs/events.html#selection-events)
-   [Touch Events](https://zh-hans.reactjs.org/docs/events.html#touch-events)
-   [UI Events](https://zh-hans.reactjs.org/docs/events.html#ui-events)
-   [Wheel Events](https://zh-hans.reactjs.org/docs/events.html#wheel-events)
-   [Media Events](https://zh-hans.reactjs.org/docs/events.html#media-events)
-   [Image Events](https://zh-hans.reactjs.org/docs/events.html#image-events)
-   [Animation Events](https://zh-hans.reactjs.org/docs/events.html#animation-events)
-   [Transition Events](https://zh-hans.reactjs.org/docs/events.html#transition-events)
-   [Other Events](https://zh-hans.reactjs.org/docs/events.html#other-events)

---

## 参考

### 剪贴板事件

事件名：

```
onCopy onCut onPaste
```

属性：

```
DOMDataTransfer clipboardData
```

---

### 复合事件

事件名:

```
onCompositionEnd onCompositionStart onCompositionUpdate
```

属性:

```
string data
```

---

### 键盘事件

事件名:

```
onKeyDown onKeyPress onKeyUp
```

属性:

```
boolean altKey
number charCode
boolean ctrlKey
boolean getModifierState(key)
string key
number keyCode
string locale
number location
boolean metaKey
boolean repeat
boolean shiftKey
number which
```

`key` 属性可以是 [DOM Level 3 Events spec](https://www.w3.org/TR/uievents-key/#named-key-attribute-values) 里记录的任意值。

---

### 焦点事件

事件名：

```
onFocus onBlur
```

这些焦点事件在 React DOM 上的所有元素都有效，不只是表单元素。

属性：

```
DOMEventTarget relatedTarget
```

#### onFocus

`onFocus` 事件在元素（或其内部某些元素）聚焦时被调用。例如，当用户点击文本输入框时，就会调用该事件。

```
function Example() {
  return (
    <input
      onFocus={(e) => {
        console.log('Focused on input');
      }}
      placeholder="onFocus is triggered when you click this input."
    />
  )
}
```

#### onBlur

`onBlur` 事件处理程序在元素（或元素内某些元素）失去焦点时被调用。例如，当用户在已聚焦的文本输入框外点击时，就会被调用。

```
function Example() {
  return (
    <input
      onBlur={(e) => {
        console.log('Triggered because this input lost focus');
      }}
      placeholder="onBlur is triggered when you click this input and then you click outside of it."
    />
  )
}
```

#### 监听焦点的进入与离开

你可以使用 `currentTarget` 和 `relatedTarget` 来区分聚焦和失去焦点是否来自父元素_外部_。这里有个 DEMO，你可以复制并在本地运行，它展示了如何监听一个子元素的聚焦，元素本身的聚焦，以及整个子树进入焦点或离开焦点。

```
function Example() {
  return (
    <div
      tabIndex={1}
      onFocus={(e) => {
        if (e.currentTarget === e.target) {
          console.log('focused self');
        } else {
          console.log('focused child', e.target);
        }
        if (!e.currentTarget.contains(e.relatedTarget)) {
          // Not triggered when swapping focus between children
          console.log('focus entered self');
        }
      }}
      onBlur={(e) => {
        if (e.currentTarget === e.target) {
          console.log('unfocused self');
        } else {
          console.log('unfocused child', e.target);
        }
        if (!e.currentTarget.contains(e.relatedTarget)) {
          // Not triggered when swapping focus between children
          console.log('focus left self');
        }
      }}
    >
      <input id="1" />
      <input id="2" />
    </div>
  );
}
```

---

### 表单事件

事件名：

```
onChange onInput onInvalid onReset onSubmit 
```

想了解 onChange 事件的更多信息，查看 [Forms](https://zh-hans.reactjs.org/docs/forms.html) 。

---

### [](https://zh-hans.reactjs.org/docs/events.html#generic-events)通用事件

事件名：

```
onError onLoad
```

---

### 鼠标事件

事件名：

```
onClick onContextMenu onDoubleClick onDrag onDragEnd onDragEnter onDragExit
onDragLeave onDragOver onDragStart onDrop onMouseDown onMouseEnter onMouseLeave
onMouseMove onMouseOut onMouseOver onMouseUp
```

`onMouseEnter` 和 `onMouseLeave` 事件从离开的元素向进入的元素传播，不是正常的冒泡，也没有捕获阶段。

属性：

```
boolean altKey
number button
number buttons
number clientX
number clientY
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
number pageX
number pageY
DOMEventTarget relatedTarget
number screenX
number screenY
boolean shiftKey
```

---

### 指针事件

事件名:

```
onPointerDown onPointerMove onPointerUp onPointerCancel onGotPointerCapture
onLostPointerCapture onPointerEnter onPointerLeave onPointerOver onPointerOut
```

`onPointerEnter` 和 `onPointerLeave` 事件从离开的元素向进入的元素传播，不是正常的冒泡，也没有捕获阶段。

属性：

如 [W3 spec](https://www.w3.org/TR/pointerevents/) 中定义的，指针事件通过以下属性扩展了[鼠标事件](https://zh-hans.reactjs.org/docs/events.html#mouse-events)：

```
number pointerId
number width
number height
number pressure
number tangentialPressure
number tiltX
number tiltY
number twist
string pointerType
boolean isPrimary
```

关于跨浏览器支持的说明：

并非每个浏览器都支持指针事件（在写这篇文章时，已支持的浏览器有：Chrome，Firefox，Edge 和 Internet Explorer）。React 故意不通过 polyfill 的方式适配其他浏览器，主要是符合标准的 polyfill 会显著增加 react-dom 的 bundle 大小。

如果你的应用要求指针事件，我们推荐添加第三方的指针事件 polyfill。

---

### 选择事件

事件名：

```
onSelect
```

---

### 触摸事件

事件名：

```
onTouchCancel onTouchEnd onTouchMove onTouchStart
```

属性：

```
boolean altKey
DOMTouchList changedTouches
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
boolean shiftKey
DOMTouchList targetTouches
DOMTouchList touches
```

---

### UI 事件

事件名：

```
onScroll
```

> 注意
> 
> 从 React 17 开始，`onScroll` 事件在 React 中**不再冒泡**。这与浏览器的行为一致，并且避免了当一个嵌套且可滚动的元素在其父元素触发事件时造成混乱。

属性：

```
number detail
DOMAbstractView view
```

---

### 滚轮事件

事件名：

```
onWheel
```

属性：

```
number deltaMode
number deltaX
number deltaY
number deltaZ
```

---

### 媒体事件

事件名：

```
onAbort onCanPlay onCanPlayThrough onDurationChange onEmptied onEncrypted
onEnded onError onLoadedData onLoadedMetadata onLoadStart onPause onPlay
onPlaying onProgress onRateChange onSeeked onSeeking onStalled onSuspend
onTimeUpdate onVolumeChange onWaiting
```

---

### 图像事件

事件名：

```
onLoad onError
```

---

### 动画事件

事件名：

```
onAnimationStart onAnimationEnd onAnimationIteration
```

属性：

```
string animationName
string pseudoElement
float elapsedTime
```

---

### 过渡事件

事件名：

```
onTransitionEnd
```

属性：

```
string propertyName
string pseudoElement
float elapsedTime
```

---

### 其他事件

事件名：

```
onToggle
```


# Test Utilities

**如何引入**

```
import ReactTestUtils from 'react-dom/test-utils'; // ES6
var ReactTestUtils = require('react-dom/test-utils'); // ES5 使用 npm 的方式
```

## 概览

`ReactTestUtils` 可搭配你所选的测试框架，轻松实现 React 组件测试。在 Facebook 内部，我们使用 [Jest](https://facebook.github.io/jest/) 来轻松实现 JavaScript 测试。你可以从 Jest 官网的 [React 教程](https://jestjs.io/docs/tutorial-react)中了解如何开始使用它。

> 注意：
> 
> 我们推荐使用 [React Testing Library](https://testing-library.com/react)，它使得针对组件编写测试用例就像终端用户在使用它一样方便。
> 
> 当使用的 React 版本 <= 16 时，可以使用 [Enzyme](https://airbnb.io/enzyme/) 的测试工具，通过它能够轻松对 React 组件的输出进行断言、操控和遍历。

-   [`act()`](https://zh-hans.reactjs.org/docs/test-utils.html#act)
-   [`mockComponent()`](https://zh-hans.reactjs.org/docs/test-utils.html#mockcomponent)
-   [`isElement()`](https://zh-hans.reactjs.org/docs/test-utils.html#iselement)
-   [`isElementOfType()`](https://zh-hans.reactjs.org/docs/test-utils.html#iselementoftype)
-   [`isDOMComponent()`](https://zh-hans.reactjs.org/docs/test-utils.html#isdomcomponent)
-   [`isCompositeComponent()`](https://zh-hans.reactjs.org/docs/test-utils.html#iscompositecomponent)
-   [`isCompositeComponentWithType()`](https://zh-hans.reactjs.org/docs/test-utils.html#iscompositecomponentwithtype)
-   [`findAllInRenderedTree()`](https://zh-hans.reactjs.org/docs/test-utils.html#findallinrenderedtree)
-   [`scryRenderedDOMComponentsWithClass()`](https://zh-hans.reactjs.org/docs/test-utils.html#scryrendereddomcomponentswithclass)
-   [`findRenderedDOMComponentWithClass()`](https://zh-hans.reactjs.org/docs/test-utils.html#findrendereddomcomponentwithclass)
-   [`scryRenderedDOMComponentsWithTag()`](https://zh-hans.reactjs.org/docs/test-utils.html#scryrendereddomcomponentswithtag)
-   [`findRenderedDOMComponentWithTag()`](https://zh-hans.reactjs.org/docs/test-utils.html#findrendereddomcomponentwithtag)
-   [`scryRenderedComponentsWithType()`](https://zh-hans.reactjs.org/docs/test-utils.html#scryrenderedcomponentswithtype)
-   [`findRenderedComponentWithType()`](https://zh-hans.reactjs.org/docs/test-utils.html#findrenderedcomponentwithtype)
-   [`renderIntoDocument()`](https://zh-hans.reactjs.org/docs/test-utils.html#renderintodocument)
-   [`Simulate`](https://zh-hans.reactjs.org/docs/test-utils.html#simulate)

## 参考

### `act()`

为断言准备一个组件，包裹要渲染的代码并在调用 `act()` 时执行更新。这会使得测试更接近 React 在浏览器中的工作方式。

> 注意
> 
> 如果你使用了 `react-test-renderer`，它也提供了与 `act` 行为相同的函数。

例如，假设我们有个 `Counter` 组件:

```
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: 0};
    this.handleClick = this.handleClick.bind(this);
  }
  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }
  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }
  handleClick() {
    this.setState(state => ({
      count: state.count + 1,
    }));
  }
  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={this.handleClick}>
          Click me
        </button>
      </div>
    );
  }
}
```

以下是其测试代码：

```
import React from 'react';
import ReactDOM from 'react-dom';
import { act } from 'react-dom/test-utils';import Counter from './Counter';

let container;

beforeEach(() => {
  container = document.createElement('div');
  document.body.appendChild(container);
});

afterEach(() => {
  document.body.removeChild(container);
  container = null;
});

it('can render and update a counter', () => {
  // 首先测试 render 和 componentDidMount
  act(() => {    ReactDOM.render(<Counter />, container);  });  const button = container.querySelector('button');
  const label = container.querySelector('p');
  expect(label.textContent).toBe('You clicked 0 times');
  expect(document.title).toBe('You clicked 0 times');

  // 再测试 render 和 componentDidUpdate
  act(() => {    button.dispatchEvent(new MouseEvent('click', {bubbles: true}));  });  expect(label.textContent).toBe('You clicked 1 times');
  expect(document.title).toBe('You clicked 1 times');
});
```

千万不要忘记，只有将 DOM 容器添加到 `document` 时，触发 DOM 事件才生效。你可以使用类似于 [React Testing Library](https://testing-library.com/react) 等库来减少样板代码（boilerplate code）。

-   [`recipes`](https://zh-hans.reactjs.org/docs/testing-recipes.html) 文档包含了关于 `act()` 函数的示例、用法及更多详细信息。

---

### `mockComponent()`

```
mockComponent(
  componentClass,
  [mockTagName]
)
```

将模拟组件模块传入这个方法后，React 内部会使用有效的方法填充该模块，使其成为虚拟的 React 组件。与通常的渲染不同，组件将变成一个简单的 `<div>` (如果提供了 `mockTagName` 则是其他标签)，包含任何提供的子级。

> 注意：
> 
> `mockComponent()` 是一个过时的 API，我们推荐使用 [`jest.mock()`](https://jestjs.io/docs/tutorial-react-native#mock-native-modules-using-jestmock) 来代替。

---

### `isElement()`

```
isElement(element)
```

当 `element` 是任何一种 React 元素时，返回 `true`。

---

### `isElementOfType()`

```
isElementOfType(
  element,
  componentClass
)
```

当 `element` 是一种 React 元素，并且它的类型是参数 `componentClass` 的类型时，返回 `true`。

---

### `isDOMComponent()`

```
isDOMComponent(instance)
```

当 `instance` 是一个 DOM 组件（比如 `<div>` 或 `<span>`）时，返回 `true`。

---

### `isCompositeComponent()`

```
isCompositeComponent(instance)
```

当 `instance` 是一个用户自定义的组件，比如一个类或者一个函数时，返回 `true`。

---

### `isCompositeComponentWithType()`

```
isCompositeComponentWithType(
  instance,
  componentClass
)
```

当 `instance` 是一个组件，并且它的类型是参数 `componentClass` 的类型时，返回 `true`。

---

### `findAllInRenderedTree()`

```
findAllInRenderedTree(
  tree,
  test
)
```

遍历所有在参数 `tree` 中的组件，记录所有 `test(component)` 为 `true` 的组件。单独调用此方法不是很有用，但是它常常被作为底层 API 被其他测试方法使用。

---

### `scryRenderedDOMComponentsWithClass()`

```
scryRenderedDOMComponentsWithClass(
  tree,
  className
)
```

查找渲染树中组件的所有 DOM 元素，这些组件是 css 类名与参数 `className` 匹配的 DOM 组件。

---

### `findRenderedDOMComponentWithClass()`

```
findRenderedDOMComponentWithClass(
  tree,
  className
)
```

用法与 [`scryRenderedDOMComponentsWithClass()`](https://zh-hans.reactjs.org/docs/test-utils.html#scryrendereddomcomponentswithclass) 保持一致，但期望仅返回一个结果。不符合预期的情况下会抛出异常。

---

### `scryRenderedDOMComponentsWithTag()`

```
scryRenderedDOMComponentsWithTag(
  tree,
  tagName
)
```

查找渲染树中组件的所有的 DOM 元素，这些组件是标记名与参数 `tagName` 匹配的 DOM 组件。

---

### `findRenderedDOMComponentWithTag()`

```
findRenderedDOMComponentWithTag(
  tree,
  tagName
)
```

用法与 [`scryRenderedDOMComponentsWithTag()`](https://zh-hans.reactjs.org/docs/test-utils.html#scryrendereddomcomponentswithtag) 保持一致，但期望仅返回一个结果。不符合预期的情况下会抛出异常。

---

### `scryRenderedComponentsWithType()`

```
scryRenderedComponentsWithType(
  tree,
  componentClass
)
```

查找组件类型等于 `componentClass` 组件的所有实例。

---

### `findRenderedComponentWithType()`

```
findRenderedComponentWithType(
  tree,
  componentClass
)
```

用法与 [`scryRenderedComponentsWithType()`](https://zh-hans.reactjs.org/docs/test-utils.html#scryrenderedcomponentswithtype) 保持一致，但期望仅返回一个结果。不符合预期的情况下会抛出异常。

---

### `renderIntoDocument()`

```
renderIntoDocument(element)
```

渲染 React 元素到 document 中的某个单独的 DOM 节点上。**这个函数需要一个 DOM 对象。** 它实际相当于：

```
const domContainer = document.createElement('div');
ReactDOM.render(element, domContainer);
```

> 注意：
> 
> 你需要在引入 `React` **之前**确保 `window` 存在，`window.document` 和 `window.document.createElement` 能在全局环境中获取到。不然 React 会认为它没有权限去操作 DOM，以及像 `setState` 这样的方法将不可用。

---

## 其他工具方法

### `Simulate`

```
Simulate.{eventName}(
  element,
  [eventData]
)
```

使用可选的 `eventData` 事件数据来模拟在 DOM 节点上触发事件。

[React 所支持的所有事件](https://zh-hans.reactjs.org/docs/events.html#supported-events) 在 `Simulate` 中都有对应的方法。

**点击元素**

```
// <button ref={(node) => this.button = node}>...</button>
const node = this.button;
ReactTestUtils.Simulate.click(node);
```

**修改一个 input 输入框的值，然后按回车键。**

```
// <input ref={(node) => this.textInput = node} />
const node = this.textInput;
node.value = 'giraffe';
ReactTestUtils.Simulate.change(node);
ReactTestUtils.Simulate.keyDown(node, {key: "Enter", keyCode: 13, which: 13});
```

> 注意：
> 
> 你必须提供一切需要在组件中用到的事件属性（比如：keyCode、which 等等），因为 React 没有为你创建这些属性。


# Test Renderer

**如何引入**

```
import TestRenderer from 'react-test-renderer'; // ES6
const TestRenderer = require('react-test-renderer'); // ES5 with npm
```

## 概览

这个 package 提供了一个 React 渲染器，用于将 React 组件渲染成纯 JavaScript 对象，无需依赖 DOM 或原生移动环境。

这个 package 提供的主要功能是在不依赖浏览器或 [jsdom](https://github.com/tmpvar/jsdom) 的情况下，返回某个时间点由 React DOM 或者 React Native 平台渲染出的视图结构（类似与 DOM 树）快照。

示例:

```
import TestRenderer from 'react-test-renderer';

function Link(props) {
  return <a href={props.page}>{props.children}</a>;
}

const testRenderer = TestRenderer.create(
  <Link page="https://www.facebook.com/">Facebook</Link>
);

console.log(testRenderer.toJSON());
// { type: 'a',
//   props: { href: 'https://www.facebook.com/' },
//   children: [ 'Facebook' ] }
```

你可以使用 Jest 的快照测试功能来自动保存当前 `JSON` 树结构到一个文件中，并在测试中检查它是否被修改：[了解更多](https://jestjs.io/docs/en/snapshot-testing)。

你也可以通过遍历输出来查找特定节点，并对它们进行断言。

```
import TestRenderer from 'react-test-renderer';

function MyComponent() {
  return (
    <div>
      <SubComponent foo="bar" />
      <p className="my">Hello</p>
    </div>
  )
}

function SubComponent() {
  return (
    <p className="sub">Sub</p>
  );
}

const testRenderer = TestRenderer.create(<MyComponent />);
const testInstance = testRenderer.root;

expect(testInstance.findByType(SubComponent).props.foo).toBe('bar');
expect(testInstance.findByProps({className: "sub"}).children).toEqual(['Sub']);
```

### TestRenderer

-   [`TestRenderer.create()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testrenderercreate)
-   [`TestRenderer.act()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testrendereract)

### TestRenderer instance

-   [`testRenderer.toJSON()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testrenderertojson)
-   [`testRenderer.toTree()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testrenderertotree)
-   [`testRenderer.update()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testrendererupdate)
-   [`testRenderer.unmount()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testrendererunmount)
-   [`testRenderer.getInstance()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testrenderergetinstance)
-   [`testRenderer.root`](https://zh-hans.reactjs.org/docs/test-renderer.html#testrendererroot)

### TestInstance

-   [`testInstance.find()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testinstancefind)
-   [`testInstance.findByType()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testinstancefindbytype)
-   [`testInstance.findByProps()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testinstancefindbyprops)
-   [`testInstance.findAll()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testinstancefindall)
-   [`testInstance.findAllByType()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testinstancefindallbytype)
-   [`testInstance.findAllByProps()`](https://zh-hans.reactjs.org/docs/test-renderer.html#testinstancefindallbyprops)
-   [`testInstance.instance`](https://zh-hans.reactjs.org/docs/test-renderer.html#testinstanceinstance)
-   [`testInstance.type`](https://zh-hans.reactjs.org/docs/test-renderer.html#testinstancetype)
-   [`testInstance.props`](https://zh-hans.reactjs.org/docs/test-renderer.html#testinstanceprops)
-   [`testInstance.parent`](https://zh-hans.reactjs.org/docs/test-renderer.html#testinstanceparent)
-   [`testInstance.children`](https://zh-hans.reactjs.org/docs/test-renderer.html#testinstancechildren)

## 参考

### `TestRenderer.create()`

```
TestRenderer.create(element, options);
```

通过传来的 React 元素创建一个 `TestRenderer` 实例。它并不使用真实的 DOM，但是它依然将组件树完整地渲染到内存，以便于你对它进行断言。此时将返回一个 [TestRenderer 实例](https://zh-hans.reactjs.org/docs/test-renderer.html#testrenderer-instance)。

### `TestRenderer.act()`

```
TestRenderer.act(callback);
```

与 [`react-dom/test-utils` 中的 `act()`](https://zh-hans.reactjs.org/docs/test-utils.html#act) 相似，`TestRender.act` 为断言准备一个组件。可以使用 `act()` 来包装 `TestRenderer.create` 和 `testRenderer.update`。

```
import {create, act} from 'react-test-renderer';
import App from './app.js'; // The component being tested

// 渲染组件
let root; 
act(() => {
  root = create(<App value={1}/>)
});

// 对根元素进行断言
expect(root.toJSON()).toMatchSnapshot();

// 更新 props
act(() => {
  root.update(<App value={2}/>);
})

// 对根元素进行断言
expect(root.toJSON()).toMatchSnapshot();
```

### `testRenderer.toJSON()`

```
testRenderer.toJSON()
```

返回一个已渲染的的树对象。该树仅包含特定平台的节点，例如 `<div>` 或 `<View>` 和它们的 props，但并不包含任何用户编写的组件。这对于[快照测试](http://facebook.github.io/jest/docs/en/snapshot-testing.html#snapshot-testing-with-jest)非常方便。

### `testRenderer.toTree()`

```
testRenderer.toTree()
```

返回一个已渲染的的树对象。它所表示的内容比 `toJSON()` 提供的内容要更加详细，并且包含用户编写的组件。除非你要在测试渲染器（test renderer）之上编写自己的断言库，否则你可能并不需要这个方法。

### `testRenderer.update()`

```
testRenderer.update(element)
```

使用新的根元素重新渲染内存中的树。它模拟根元素的一次 React 更新。如果新的元素和之前的元素有相同的 type 和 key，该树将会被更新；否则，它将重挂载一个新树。

### `testRenderer.unmount()`

```
testRenderer.unmount()
```

卸载内存中的树，会触发相应的生命周期事件。

### `testRenderer.getInstance()`

```
testRenderer.getInstance()
```

如果可用的话，返回与根元素相对应的实例。如果根元素是函数定义组件，该方法无效，因为函数定义组件没有实例。

### `testRenderer.root`

```
testRenderer.root
```

返回根元素“测试实例”对象，它对于断言树中的特定节点十分有用。你可以利用它来查找其他更深层的“测试实例”。

### `testInstance.find()`

```
testInstance.find(test)
```

找到一个 `test(testInstance)` 返回 `true` 的后代测试实例。如果不只有一个测试实例匹配，将会报错。

### `testInstance.findByType()`

```
testInstance.findByType(type)
```

找到匹配指定 `type` 的后代测试实例，如果不是只有一个测试实例匹配指定的 `type`，将会报错。

### `testInstance.findByProps()`

```
testInstance.findByProps(props)
```

找到匹配指定 `props`的后代测试实例，如果不是正好只有一个测试实例匹配指定的 `props`，将会报错。

### `testInstance.findAll()`

```
testInstance.findAll(test)
```

找到所有 `test(testInstance)` 返回 `true` 的后代测试实例。

### `testInstance.findAllByType()`

```
testInstance.findAllByType(type)
```

找到所有匹配指定 `type` 的后代测试实例。

### `testInstance.findAllByProps()`

```
testInstance.findAllByProps(props)
```

找到所有匹配指定 `props` 的后代测试实例。

### `testInstance.instance`

```
testInstance.instance
```

该测试实例相对应的组件实例。它只能用于类定义组件，因为函数定义组件没有实例。它匹配给定的组件内部的 `this` 的值。

### `testInstance.type`

```
testInstance.type
```

该测试实例相对应的组件的类型。例如，一个 `<Button />` 组件有一个 `Button` 类型。

### `testInstance.props`

```
testInstance.props
```

该测试实例相对应的组件的 props。例如，一个 `<Button size="small" />` 组件的 props 为 `{size: 'small'}`。

### `testInstance.parent`

```
testInstance.parent
```

该测试实例的父测试实例。

### `testInstance.children`

```
testInstance.children
```

该测试实例的子测试实例。

## 想法

你可以把 `createNodeMock` 函数作为选项（option）传递给 `TestRenderer.create`，进行自定义 refs 模拟。`createNodeMock` 接受当前元素作为参数，并且返回一个模拟 ref 对象的。这十分有利于依赖 refs 组件的测试。

```
import TestRenderer from 'react-test-renderer';

class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.input = null;
  }
  componentDidMount() {
    this.input.focus();
  }
  render() {
    return <input type="text" ref={el => this.input = el} />
  }
}

let focused = false;
TestRenderer.create(
  <MyComponent />,
  {
    createNodeMock: (element) => {
      if (element.type === 'input') {
        // 模拟 focus 函数
        return {
          focus: () => {
            focused = true;
          }
        };
      }
      return null;
    }
  }
);
expect(focused).toBe(true);
```

# JavaScript 环境要求

React 16 依赖集合类型 [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 和 [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) 。如果你要支持无法原生提供这些能力（例如 IE < 11）或实现不规范（例如 IE 11）的旧浏览器与设备，考虑在你的应用库中包含一个全局的 polyfill ，例如 [core-js](https://github.com/zloirock/core-js)。

React 16 使用 core-js 支持老版本浏览器，其 polyfill 环境可能如下：

```
import 'core-js/es/map';
import 'core-js/es/set';

import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```

React 同时还依赖于 `requestAnimationFrame`（甚至包括测试环境）。 你可以使用 [raf](https://www.npmjs.com/package/raf) 的 package 增添 `requestAnimationFrame` 的 shim：

```
import 'raf/polyfill';
```