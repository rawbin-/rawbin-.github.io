# [[React术语表]]

# [[ReactAPI实践]]

# [[ReactHooks实践]]


# 特性点
## React哲学
- 从设计稿开始
- 第一步 将设计好的UI划分为组件层级
- 第二步 用React创建一个静态版本
- 第三步 确定UI State的最小（且完整）表示
- 第四步 确定state方式的位置
- 第五步 添加反向数据流
## 组件组合而不是继承
```js
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}      </div>
      <div className="SplitPane-right">
        {props.right}      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />      }
      right={
        <Chat />      } />
  );
}
```


## this 绑定
- 类组件默认不绑定`this`，如果需要传递函数对象，需要明确绑定
- 不显式明确绑定的方法：
	- 使用类的字段的写法，直接定义属性
	- 使用箭头函数
	- 使用`create-react-class`中的 `createReactClass`
```js
class LoggingButton extends React.Component {
// 此语法确保 `handleClick` 内的 `this` 已被绑定。  
// 注意: 这是 *实验性* 语法。 // ES2022
handleClick = () => {    console.log('this is:', this);  }
  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

```js
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // 此语法确保 `handleClick` 内的 `this` 已被绑定。    return (      <button onClick={() => this.handleClick()}>        Click me
      </button>
    );
  }
}
```

## 事件传参
```js
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

## 列表元素 key
- key 的值在兄弟节点中唯一，且不可变
- [使用索引作为key的负面影响](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)
- [为什么Key是必须的](https://zh-hans.reactjs.org/docs/reconciliation.html#recursing-on-children)

## 语义化+省节点层级
- 使用`Fragement`或者简写`<>` 来表达空层级，避免增加元素破坏语义
```js
import React, { Fragment } from 'react';
function ListItem({ item }) {
  return (
    <Fragment>      <dt>{item.term}</dt>
      <dd>{item.description}</dd>
    </Fragment>  );
}

function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        <ListItem item={item} key={item.id} />
      ))}
    </dl>
  );
}
```

## 无障碍+弹层外点击隐藏
- 可以使用弹层外点击隐藏，但对无障碍不友好
```js
class OuterClickExample extends React.Component {
  constructor(props) {
    super(props);

    this.state = { isOpen: false };
    this.toggleContainer = React.createRef();

    this.onClickHandler = this.onClickHandler.bind(this);
    this.onClickOutsideHandler = this.onClickOutsideHandler.bind(this);
  }

  componentDidMount() {    window.addEventListener('click', this.onClickOutsideHandler);  }
  componentWillUnmount() {
    window.removeEventListener('click', this.onClickOutsideHandler);
  }

  onClickHandler() {
    this.setState(currentState => ({
      isOpen: !currentState.isOpen
    }));
  }

  onClickOutsideHandler(event) {    if (this.state.isOpen && !this.toggleContainer.current.contains(event.target)) {      this.setState({ isOpen: false });    }  }
  render() {
    return (
      <div ref={this.toggleContainer}>
        <button onClick={this.onClickHandler}>Select an option</button>
        {this.state.isOpen && (
          <ul>
            <li>Option 1</li>
            <li>Option 2</li>
            <li>Option 3</li>
          </ul>
        )}
      </div>
    );
  }
}
```
- 无障碍的方式，需要更灵活完备
```js
class BlurExample extends React.Component {
  constructor(props) {
    super(props);

    this.state = { isOpen: false };
    this.timeOutId = null;

    this.onClickHandler = this.onClickHandler.bind(this);
    this.onBlurHandler = this.onBlurHandler.bind(this);
    this.onFocusHandler = this.onFocusHandler.bind(this);
  }

  onClickHandler() {
    this.setState(currentState => ({
      isOpen: !currentState.isOpen
    }));
  }

  // 我们在下一个时间点使用 setTimeout 关闭弹窗。  // 这是必要的，因为失去焦点事件会在新的焦点事件前被触发，  // 我们需要通过这个步骤确认这个元素的一个子节点  // 是否得到了焦点。  onBlurHandler() {    this.timeOutId = setTimeout(() => {      this.setState({        isOpen: false      });    });  }
  // 如果一个子节点获得了焦点，不要关闭弹窗。  onFocusHandler() {    clearTimeout(this.timeOutId);  }
  render() {
    // React 通过把失去焦点和获得焦点事件传输给父节点    // 来帮助我们。    return (
      <div onBlur={this.onBlurHandler}           onFocus={this.onFocusHandler}>        <button onClick={this.onClickHandler}
                aria-haspopup="true"
                aria-expanded={this.state.isOpen}>
          Select an option
        </button>
        {this.state.isOpen && (
          <ul>
            <li>Option 1</li>
            <li>Option 2</li>
            <li>Option 3</li>
          </ul>
        )}
      </div>
    );
  }
}
```
## 代码分割
- 使用动态`import()`，CRA和Next.js开箱即用，webpack支持
- `React.lazy` 和 Suspense 技术还不支持服务端渲染。如果你想要在使用服务端渲染的应用中使用，我们推荐 [Loadable Components](https://github.com/gregberge/loadable-components) 这个库。它有一个很棒的[服务端渲染打包指南](https://loadable-components.com/docs/server-side-rendering/)。
- `React.lazy` 目前只支持默认导出（default exports）。如果你想被引入的模块使用命名导出（named exports），你可以创建一个中间模块，来重新导出为默认模块。这能保证 tree shaking 不会出错，并且不必引入不需要的组件。
```js
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </div>
  );
}
```
```js
import React, { Suspense } from 'react';
import MyErrorBoundary from './MyErrorBoundary';

const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

const MyComponent = () => (
  <div>
    <MyErrorBoundary>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </MyErrorBoundary>
  </div>
);
```
## Context
- Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。
- Context 主要应用场景在于_很多_不同层级的组件需要访问同样一些的数据。请谨慎使用，因为这会使得组件的复用性变差。
- **如果你只是想避免层层传递一些属性，[组件组合（component composition）](https://zh-hans.reactjs.org/docs/composition-vs-inheritance.html)有时候是一个比 context 更好的解决方案。**
- Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据，例如当前认证的用户、主题或首选语言。举个例子，在下面的代码中，我们通过一个 “theme” 属性手动调整一个按钮组件的样式：
```js
// Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。// 为当前的 theme 创建一个 context（“light”为默认值）。const ThemeContext = React.createContext('light');class App extends React.Component {
  render() {
    // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。    // 无论多深，任何组件都能读取这个值。    // 在这个例子中，我们将 “dark” 作为当前的值传递下去。    return (
      <ThemeContext.Provider value="dark">        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 中间的组件再也不必指明往下传递 theme 了。function Toolbar() {  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 指定 contextType 读取当前的 theme context。  // React 会往上找到最近的 theme Provider，然后使用它的值。  // 在这个例子中，当前的 theme 值为 “dark”。  static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;  }
}
```
- 一种 **无需 context** 的解决方案是[将 `Avatar` 组件自身传递下去](https://zh-hans.reactjs.org/docs/composition-vs-inheritance.html#containment)，因为中间组件无需知道 `user` 或者 `avatarSize` 等 props：
- 这种对组件的_控制反转_减少了在你的应用中要传递的 props 数量，这在很多场景下会使得你的代码更加干净，使你对根组件有更多的把控。但是，这并不适用于每一个场景：这种将逻辑提升到组件树的更高层次来处理，会使得这些高层组件变得更复杂，并且会强行将低层组件适应这样的形式，这可能不会是你想要的。
- 而且你的组件并不限制于接收单个子组件。你可能会传递多个子组件，甚至会为这些子组件（children）封装多个单独的“接口（slots）”，[正如这里的文档所列举的](https://zh-hans.reactjs.org/docs/composition-vs-inheritance.html#containment)
```js
function Page(props) {
  const user = props.user;
  const userLink = (
    <Link href={user.permalink}>
      <Avatar user={user} size={props.avatarSize} />
    </Link>
  );
  return <PageLayout userLink={userLink} />;
}

// 现在，我们有这样的组件：
<Page user={user} avatarSize={avatarSize} />
// ... 渲染出 ...
<PageLayout userLink={...} />
// ... 渲染出 ...
<NavigationBar userLink={...} />
// ... 渲染出 ...
{props.userLink}
```

### API
- 值设置在Provider上面
- 组件树没有匹配到Provider会使用默认值，也就是createContext的值
- 从 Provider 到其内部 consumer 组件（包括 [.contextType](https://zh-hans.reactjs.org/docs/context.html#classcontexttype) 和 [useContext](https://zh-hans.reactjs.org/docs/hooks-reference.html#usecontext)）的传播不受制于 `shouldComponentUpdate` 函数，因此当 consumer 组件在其祖先组件跳过更新的情况下也能更新。
- 通过新旧值检测来确定变化，使用了与 [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description) 相同的算法。
#### React.createContext 创建默认Context对象
#### Context.Provider 提供包装入口
#### Class.contextType 挂载Context对象
#### Context.Consumer Context消费者/订阅
- 一个 React 组件可以订阅 context 的变更，此组件可以让你在[函数式组件](https://zh-hans.reactjs.org/docs/components-and-props.html#function-and-class-components)中可以订阅 context。

- 这种方法需要一个[函数作为子元素（function as a child）](https://zh-hans.reactjs.org/docs/render-props.html#using-props-other-than-render)。这个函数接收当前的 context 值，并返回一个 React 节点。传递给函数的 `value` 值等价于组件树上方离这个 context 最近的 Provider 提供的 `value` 值。如果没有对应的 Provider，`value` 参数等同于传递给 `createContext()` 的 `defaultValue`。
#### Context.displayName
- context 对象接受一个名为 `displayName` 的 property，类型为字符串。React DevTools 使用该字符串来确定 context 要显示的内容。

### 在嵌套组件中更新 Context
```js
// 确保传递给 createContext 的默认值数据结构是调用的组件（consumers）所能匹配的！
export const ThemeContext = React.createContext({
  theme: themes.dark,  toggleTheme: () => {},});
```

```js
import {ThemeContext} from './theme-context';

function ThemeTogglerButton() {
  // Theme Toggler 按钮不仅仅只获取 theme 值，  // 它也从 context 中获取到一个 toggleTheme 函数  return (
    <ThemeContext.Consumer>
      {({theme, toggleTheme}) => (        <button
          onClick={toggleTheme}
          style={{backgroundColor: theme.background}}>
          Toggle Theme
        </button>
      )}
    </ThemeContext.Consumer>
  );
}

export default ThemeTogglerButton;
```
### 消费多个 Context
- 为了确保 context 快速进行重渲染，React 需要使每一个 consumers 组件的 context 在组件树中成为一个单独的节点。

## 错误边界
部分 UI 的 JavaScript 错误不应该导致整个应用崩溃，为了解决这个问题，React 16 引入了一个新的概念 —— 错误边界。
错误边界**无法**捕获以下场景中产生的错误：
-   事件处理（[了解更多](https://zh-hans.reactjs.org/docs/error-boundaries.html#how-about-event-handlers)）
-   异步代码（例如 `setTimeout` 或 `requestAnimationFrame` 回调函数）
-   服务端渲染
-   它自身抛出来的错误（并非它的子组件）

如果一个 class 组件中定义了 [`static getDerivedStateFromError()`](https://zh-hans.reactjs.org/docs/react-component.html#static-getderivedstatefromerror) 或 [`componentDidCatch()`](https://zh-hans.reactjs.org/docs/react-component.html#componentdidcatch) 这两个生命周期方法中的任意一个（或两个）时，那么它就变成一个错误边界。当抛出错误后，请使用 `static getDerivedStateFromError()` 渲染备用 UI ，使用 `componentDidCatch()` 打印错误信息。

错误边界的工作方式类似于 JavaScript 的 `catch {}`，不同的地方在于错误边界只针对 React 组件。只有 class 组件才可以成为错误边界组件。大多数情况下, 你只需要声明一次错误边界组件, 并在整个应用中使用它。

- @babel/plugin-transform-react-jsx-source
- React 不需要错误边界来捕获事件处理器中的错误。与 render 方法和生命周期方法不同，事件处理器不会在渲染期间触发。因此，如果它们抛出异常，React 仍然能够知道需要在屏幕上显示什么。

## Refs和DOM
Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素。

在典型的 React 数据流中，[props](https://zh-hans.reactjs.org/docs/components-and-props.html) 是父组件与子组件交互的唯一方式。要修改一个子组件，你需要使用新的 props 来重新渲染它。但是，在某些情况下，你需要在典型数据流之外强制修改子组件。被修改的子组件可能是一个 React 组件的实例，也可能是一个 DOM 元素。对于这两种情况，React 都提供了解决办法。

### 何时使用 Refs

下面是几个适合使用 refs 的情况：

-   管理焦点，文本选择或媒体播放。
-   触发强制动画。
-   集成第三方 DOM 库。

避免使用 refs 来做任何可以通过声明式实现来完成的事情。

举个例子，避免在 `Dialog` 组件里暴露 `open()` 和 `close()` 方法，最好传递 `isOpen` 属性。

### 勿过度使用 Refs

你可能首先会想到使用 refs 在你的 app 中“让事情发生”。如果是这种情况，请花一点时间，认真再考虑一下 state 属性应该被安排在哪个组件层中。通常你会想明白，让更高的组件层级拥有这个 state，是更恰当的。查看 [状态提升](https://zh-hans.reactjs.org/docs/lifting-state-up.html) 以获取更多有关示例。

> 注意
> 
> 下面的例子已经更新为使用在 React 16.3 版本引入的 `React.createRef()` API。如果你正在使用一个较早版本的 React，我们推荐你使用[回调形式的 refs](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html#callback-refs)。

### 创建 Refs

Refs 是使用 `React.createRef()` 创建的，并通过 `ref` 属性附加到 React 元素。在构造组件时，通常将 Refs 分配给实例属性，以便可以在整个组件中引用它们。

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();  }
  render() {
    return <div ref={this.myRef} />;  }
}
```

### 访问 Refs

当 ref 被传递给 `render` 中的元素时，对该节点的引用可以在 ref 的 `current` 属性中被访问。

```
const node = this.myRef.current;
```

ref 的值根据节点的类型而有所不同：

-   当 `ref` 属性用于 HTML 元素时，构造函数中使用 `React.createRef()` 创建的 `ref` 接收底层 DOM 元素作为其 `current` 属性。
-   当 `ref` 属性用于自定义 class 组件时，`ref` 对象接收组件的挂载实例作为其 `current` 属性。
-   **你不能在函数组件上使用 `ref` 属性**，因为他们没有实例。

以下例子说明了这些差异。

#### 为 DOM 元素添加 ref

以下代码使用 `ref` 去存储 DOM 节点的引用：

```
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    // 创建一个 ref 来存储 textInput 的 DOM 元素
    this.textInput = React.createRef();    this.focusTextInput = this.focusTextInput.bind(this);
  }

  focusTextInput() {
    // 直接使用原生 API 使 text 输入框获得焦点
    // 注意：我们通过 "current" 来访问 DOM 节点
    this.textInput.current.focus();  }

  render() {
    // 告诉 React 我们想把 <input> ref 关联到
    // 构造器里创建的 `textInput` 上
    return (
      <div>
        <input
          type="text"
          ref={this.textInput} />        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

React 会在组件挂载时给 `current` 属性传入 DOM 元素，并在组件卸载时传入 `null` 值。`ref` 会在 `componentDidMount` 或 `componentDidUpdate` 生命周期钩子触发前更新。

#### 为 class 组件添加 Ref

如果我们想包装上面的 `CustomTextInput`，来模拟它挂载之后立即被点击的操作，我们可以使用 ref 来获取这个自定义的 input 组件并手动调用它的 `focusTextInput` 方法：

```
class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();  }

  componentDidMount() {
    this.textInput.current.focusTextInput();  }

  render() {
    return (
      <CustomTextInput ref={this.textInput} />    );
  }
}
```

请注意，这仅在 `CustomTextInput` 声明为 class 时才有效：

```
class CustomTextInput extends React.Component {  // ...
}
```

#### Refs 与函数组件

默认情况下，**你不能在函数组件上使用 `ref` 属性**，因为它们没有实例：

```
function MyFunctionComponent() {  return <input />;
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();  }
  render() {
    // This will *not* work!
    return (
      <MyFunctionComponent ref={this.textInput} />    );
  }
}
```

如果要在函数组件中使用 `ref`，你可以使用 [`forwardRef`](https://zh-hans.reactjs.org/docs/forwarding-refs.html)（可与 [`useImperativeHandle`](https://zh-hans.reactjs.org/docs/hooks-reference.html#useimperativehandle) 结合使用），或者可以将该组件转化为 class 组件。

不管怎样，你可以**在函数组件内部使用 `ref` 属性**，只要它指向一个 DOM 元素或 class 组件：

```
function CustomTextInput(props) {
  // 这里必须声明 textInput，这样 ref 才可以引用它  const textInput = useRef(null);
  function handleClick() {
    textInput.current.focus();  }

  return (
    <div>
      <input
        type="text"
        ref={textInput} />      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );
}
```


### 将 DOM Refs 暴露给父组件

在极少数情况下，你可能希望在父组件中引用子节点的 DOM 节点。通常不建议这样做，因为它会打破组件的封装，但它偶尔可用于触发焦点或测量子 DOM 节点的大小或位置。

虽然你可以[向子组件添加 ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html#adding-a-ref-to-a-class-component)，但这不是一个理想的解决方案，因为你只能获取组件实例而不是 DOM 节点。并且，它还在函数组件上无效。

如果你使用 16.3 或更高版本的 React, 这种情况下我们推荐使用 [ref 转发](https://zh-hans.reactjs.org/docs/forwarding-refs.html)。**Ref 转发使组件可以像暴露自己的 ref 一样暴露子组件的 ref**。关于怎样对父组件暴露子组件的 DOM 节点，在 [ref 转发文档](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)中有一个详细的例子。

如果你使用 16.2 或更低版本的 React，或者你需要比 ref 转发更高的灵活性，你可以使用[这个替代方案](https://gist.github.com/gaearon/1a018a023347fe1c2476073330cc5509)将 ref 作为特殊名字的 prop 直接传递。

可能的话，我们不建议暴露 DOM 节点，但有时候它会成为救命稻草。注意这个方案需要你在子组件中增加一些代码。如果你对子组件的实现没有控制权的话，你剩下的选择是使用 [`findDOMNode()`](https://zh-hans.reactjs.org/docs/react-dom.html#finddomnode)，但在[`严格模式`](https://zh-hans.reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage) 下已被废弃且不推荐使用。

### 回调 Refs

React 也支持另一种设置 refs 的方式，称为“回调 refs”。它能助你更精细地控制何时 refs 被设置和解除。

不同于传递 `createRef()` 创建的 `ref` 属性，你会传递一个函数。这个函数中接受 React 组件实例或 HTML DOM 元素作为参数，以使它们能在其他地方被存储和访问。

下面的例子描述了一个通用的范例：使用 `ref` 回调函数，在实例的属性中存储对 DOM 节点的引用。

```
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);

    this.textInput = null;
    this.setTextInputRef = element => {      this.textInput = element;    };
    this.focusTextInput = () => {      // 使用原生 DOM API 使 text 输入框获得焦点      if (this.textInput) this.textInput.focus();    };  }

  componentDidMount() {
    // 组件挂载后，让文本框自动获得焦点
    this.focusTextInput();  }

  render() {
    // 使用 `ref` 的回调函数将 text 输入框 DOM 节点的引用存储到 React
    // 实例上（比如 this.textInput）
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}        />
      </div>
    );
  }
}
```

React 将在组件挂载时，会调用 `ref` 回调函数并传入 DOM 元素，当卸载时调用它并传入 `null`。在 `componentDidMount` 或 `componentDidUpdate` 触发前，React 会保证 refs 一定是最新的。

你可以在组件间传递回调形式的 refs，就像你可以传递通过 `React.createRef()` 创建的对象 refs 一样。

```
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}      />
    );
  }
}
```

在上面的例子中，`Parent` 把它的 refs 回调函数当作 `inputRef` props 传递给了 `CustomTextInput`，而且 `CustomTextInput` 把相同的函数作为特殊的 `ref` 属性传递给了 `<input>`。结果是，在 `Parent` 中的 `this.inputElement` 会被设置为与 `CustomTextInput` 中的 `input` 元素相对应的 DOM 节点。

### 过时 API：String 类型的 Refs

如果你之前使用过 React，你可能了解过之前的 API 中的 string 类型的 ref 属性，例如 `"textInput"`。你可以通过 `this.refs.textInput` 来访问 DOM 节点。我们不建议使用它，因为 string 类型的 refs 存在 [一些问题](https://github.com/facebook/react/pull/8333#issuecomment-271648615)。它已过时并可能会在未来的版本被移除。

> 注意
> 
> 如果你目前还在使用 `this.refs.textInput` 这种方式访问 refs ，我们建议用[回调函数](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html#callback-refs)或 [`createRef` API](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html#creating-refs) 的方式代替。

### 关于回调 refs 的说明

如果 `ref` 回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数 `null`，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 ref 并且设置新的。通过将 ref 的回调函数定义成 class 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。


## Refs转发
- Ref 转发是一项将 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 自动地通过组件传递到其一子组件的技巧。对于大多数应用中的组件来说，这通常不是必需的。但其对某些组件，尤其是可重用的组件库是很有用的。
- **Ref 转发是一个可选特性，其允许某些组件接收 `ref`，并将其向下传递（换句话说，“转发”它）给子组件。**
```js
const FancyButton = React.forwardRef((props, ref) => (  <button ref={ref} className="FancyButton">    {props.children}
  </button>
));

// 你可以直接获取 DOM button 的 ref：
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```
- 第二个参数 `ref` 只在使用 `React.forwardRef` 定义组件时存在。常规函数和 class 组件不接收 `ref` 参数，且 props 中也不存在 `ref`。
- Ref 转发不仅限于 DOM 组件，你也可以转发 refs 到 class 组件实例中。

**当你开始在组件库中使用 `forwardRef` 时，你应当将其视为一个破坏性更改，并发布库的一个新的主版本。** 这是因为你的库可能会有明显不同的行为（例如 refs 被分配给了谁，以及导出了什么类型），并且这样可能会导致依赖旧行为的应用和其他库崩溃。

出于同样的原因，当 `React.forwardRef` 存在时有条件地使用它也是不推荐的：它改变了你的库的行为，并在升级 React 自身时破坏用户的应用。

### React.forwardRef
### React.createRef
### 高阶组件转发
```js
function logProps(Component) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props);
    }

    render() {
      const {forwardedRef, ...rest} = this.props;
      // 将自定义的 prop 属性 “forwardedRef” 定义为 ref
      return <Component ref={forwardedRef} {...rest} />;    }
  }

  // 注意 React.forwardRef 回调的第二个参数 “ref”。
  // 我们可以将其作为常规 prop 属性传递给 LogProps，例如 “forwardedRef”
  // 然后它就可以被挂载到被 LogProps 包裹的子组件上。
  return React.forwardRef((props, ref) => {    return <LogProps {...props} forwardedRef={ref} />;  });}
```

## Fragments
- React 中的一个常见模式是一个组件返回多个元素。Fragments 允许你将子列表分组，而无需向 DOM 添加额外节点。
- React.Fragement
```js
class Columns extends React.Component {
  render() {
    return (
      <>        <td>Hello</td>
        <td>World</td>
      </>    );
  }
}
```

```js
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // 没有`key`，React 会发出一个关键警告
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```

## Portals
- Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的优秀的方案。

```
ReactDOM.createPortal(child, container)
```

第一个参数（`child`）是任何[可渲染的 React 子元素](https://zh-hans.reactjs.org/docs/react-component.html#render)，例如一个元素，字符串或 fragment。第二个参数（`container`）是一个 DOM 元素。

### 用法

通常来讲，当你从组件的 render 方法返回一个元素时，该元素将被挂载到 DOM 节点中离其最近的父节点：

```
render() {
  // React 挂载了一个新的 div，并且把子元素渲染其中
  return (
    <div>      {this.props.children}
    </div>  );
}
```

然而，有时候将子元素插入到 DOM 节点中的不同位置也是有好处的：

```
render() {
  // React 并*没有*创建一个新的 div。它只是把子元素渲染到 `domNode` 中。
  // `domNode` 是一个可以在任何位置的有效 DOM 节点。
  return ReactDOM.createPortal(
    this.props.children,
    domNode  );
}
```

一个 portal 的典型用例是当父组件有 `overflow: hidden` 或 `z-index` 样式时，但你需要子组件能够在视觉上“跳出”其容器。例如，对话框、悬浮卡以及提示框：

> 注意:
> 
> 当在使用 portal 时, 记住[管理键盘焦点](https://zh-hans.reactjs.org/docs/accessibility.html#programmatically-managing-focus)就变得尤为重要。
> 
> 对于模态对话框，通过遵循 [WAI-ARIA 模态开发实践](https://www.w3.org/TR/wai-aria-practices-1.1/#dialog_modal)，来确保每个人都能够运用它。

### 通过 Portal 进行事件冒泡

尽管 portal 可以被放置在 DOM 树中的任何地方，但在任何其他方面，其行为和普通的 React 子节点行为一致。由于 portal 仍存在于 _React 树_， 且与 _DOM 树_ 中的位置无关，那么无论其子节点是否是 portal，像 context 这样的功能特性都是不变的。

这包含事件冒泡。一个从 portal 内部触发的事件会一直冒泡至包含 _React 树_的祖先，即便这些元素并不是 _DOM 树_ 中的祖先。假设存在如下 HTML 结构：

```
<html>
  <body>
    <div id="app-root"></div>
    <div id="modal-root"></div>
  </body>
</html>
```

在 `#app-root` 里的 `Parent` 组件能够捕获到未被捕获的从兄弟节点 `#modal-root` 冒泡上来的事件。

```
// 在 DOM 中有两个容器是兄弟级 （siblings）
const appRoot = document.getElementById('app-root');
const modalRoot = document.getElementById('modal-root');

class Modal extends React.Component {
  constructor(props) {
    super(props);
    this.el = document.createElement('div');
  }

  componentDidMount() {
    // 在 Modal 的所有子元素被挂载后，
    // 这个 portal 元素会被嵌入到 DOM 树中，
    // 这意味着子元素将被挂载到一个分离的 DOM 节点中。
    // 如果要求子组件在挂载时可以立刻接入 DOM 树，
    // 例如衡量一个 DOM 节点，
    // 或者在后代节点中使用 ‘autoFocus’，
    // 则需添加 state 到 Modal 中，
    // 仅当 Modal 被插入 DOM 树中才能渲染子元素。
    modalRoot.appendChild(this.el);
  }

  componentWillUnmount() {
    modalRoot.removeChild(this.el);
  }

  render() {
    return ReactDOM.createPortal(      this.props.children,      this.el    );  }
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {clicks: 0};
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {    // 当子元素里的按钮被点击时，    // 这个将会被触发更新父元素的 state，    // 即使这个按钮在 DOM 中不是直接关联的后代    this.setState(state => ({      clicks: state.clicks + 1    }));  }
  render() {
    return (
      <div onClick={this.handleClick}>        <p>Number of clicks: {this.state.clicks}</p>
        <p>
          Open up the browser DevTools
          to observe that the button
          is not a child of the div
          with the onClick handler.
        </p>
        <Modal>          <Child />        </Modal>      </div>
    );
  }
}

function Child() {
  // 这个按钮的点击事件会冒泡到父元素  // 因为这里没有定义 'onClick' 属性  return (
    <div className="modal">
      <button>Click</button>    </div>
  );
}

ReactDOM.render(<Parent />, appRoot);
```

## Render Props
- 术语 [“render prop”](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce) 是指一种在 React 组件之间使用一个值为函数的 prop 共享代码的简单技术
- 具有 render prop 的组件接受一个返回 React 元素的函数，并在组件内部通过调用此函数来实现自己的渲染逻辑。

```
<DataProvider render={data => (
  <h1>Hello {data.target}</h1>
)}/>
```

使用 render prop 的库有 [React Router](https://reacttraining.com/react-router/web/api/Route/render-func)、[Downshift](https://github.com/paypal/downshift) 以及 [Formik](https://github.com/jaredpalmer/formik)。

在这个文档中，我们将讨论为什么 render prop 是有用的，以及如何写一个自己的 render prop 组件。

### 使用 Render Props 来解决横切关注点（Cross-Cutting Concerns）

组件是 React 代码复用的主要单元，但如何将一个组件封装的状态或行为共享给其他需要相同状态的组件并不总是显而易见。

例如，以下组件跟踪 Web 应用程序中的鼠标位置：

```
class MouseTracker extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>
        <h1>移动鼠标!</h1>
        <p>当前的鼠标位置是 ({this.state.x}, {this.state.y})</p>
      </div>
    );
  }
}
```

当光标在屏幕上移动时，组件在 `<p>` 中显示其（x，y）坐标。

现在的问题是：我们如何在另一个组件中复用这个行为？换个说法，若另一个组件需要知道鼠标位置，我们能否封装这一行为，以便轻松地与其他组件共享它？？

由于组件是 React 中最基础的代码复用单元，现在尝试重构一部分代码使其能够在 `<Mouse>` 组件中封装我们需要共享的行为。

```
// <Mouse> 组件封装了我们需要的行为...
class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>

        {/* ...但我们如何渲染 <p> 以外的东西? */}
        <p>The current mouse position is ({this.state.x}, {this.state.y})</p>
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <>
        <h1>移动鼠标!</h1>
        <Mouse />
      </>
    );
  }
}
```

现在 `<Mouse>` 组件封装了所有关于监听 `mousemove` 事件和存储鼠标 (x, y) 位置的行为，但其仍不是真正的可复用。

举个例子，假设我们有一个 `<Cat>` 组件，它可以呈现一张在屏幕上追逐鼠标的猫的图片。我们或许会使用 `<Cat mouse={{ x, y }}` prop 来告诉组件鼠标的坐标以让它知道图片应该在屏幕哪个位置。

首先, 你或许会像这样，尝试在 `<Mouse>` 内部的渲染方法渲染 `<Cat>` 组件：:

```
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}

class MouseWithCat extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>

        {/*
          我们可以在这里换掉 <p> 的 <Cat>   ......
          但是接着我们需要创建一个单独的 <MouseWithSomethingElse>
          每次我们需要使用它时，<MouseWithCat> 是不是真的可以重复使用.
        */}
        <Cat mouse={this.state} />
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>移动鼠标!</h1>
        <MouseWithCat />
      </div>
    );
  }
}
```

这种方法适用于我们的特定用例，但我们还没有达到以可复用的方式真正封装行为的目标。现在，每当我们想要鼠标位置用于不同的用例时，我们必须创建一个新的组件（本质上是另一个 `<MouseWithCat>` ），它专门为该用例呈现一些东西.

这也是 render prop 的来历：相比于直接将 `<Cat>` 写死在 `<Mouse>` 组件中，并且有效地更改渲染的结果，我们可以为 `<Mouse>` 提供一个函数 prop 来动态的确定要渲染什么 —— 一个 render prop。

```js
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}

class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>

        {/*
          使用 `render`prop 动态决定要渲染的内容，
          而不是给出一个 <Mouse> 渲染结果的静态表示
        */}
        {this.props.render(this.state)}
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>移动鼠标!</h1>
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}
```

现在，我们提供了一个 `render` 方法 让 `<Mouse>` 能够动态决定什么需要渲染，而不是克隆 `<Mouse>` 组件然后硬编码来解决特定的用例。

更具体地说，**render prop 是一个用于告知组件需要渲染什么内容的函数 prop。**

这项技术使我们共享行为非常容易。要获得这个行为，只要渲染一个带有 `render` prop 的 `<Mouse>` 组件就能够告诉它当前鼠标坐标 (x, y) 要渲染什么。

关于 render prop 一个有趣的事情是你可以使用带有 render prop 的常规组件来实现大多数[高阶组件](https://zh-hans.reactjs.org/docs/higher-order-components.html) (HOC)。 例如，如果你更喜欢使用 `withMouse` HOC而不是 `<Mouse>` 组件，你可以使用带有 render prop 的常规 `<Mouse>` 轻松创建一个：

```
// 如果你出于某种原因真的想要 HOC，那么你可以轻松实现
// 使用具有 render prop 的普通组件创建一个！
function withMouse(Component) {
  return class extends React.Component {
    render() {
      return (
        <Mouse render={mouse => (
          <Component {...this.props} mouse={mouse} />
        )}/>
      );
    }
  }
}
```

因此，你可以将任一模式与 render prop 一起使用。

### 使用 Props 而非 `render`

重要的是要记住，render prop 是因为模式才被称为 _render_ prop ，你不一定要用名为 `render` 的 prop 来使用这种模式。事实上， [_任何_被用于告知组件需要渲染什么内容的函数 prop 在技术上都可以被称为 “render prop”](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce).

尽管之前的例子使用了 `render`，我们也可以简单地使用 `children` prop！

```
<Mouse children={mouse => (
  <p>鼠标的位置是 {mouse.x}，{mouse.y}</p>
)}/>
```

记住，`children` prop 并不真正需要添加到 JSX 元素的 “attributes” 列表中。相反，你可以直接放置到元素的_内部_！

```
<Mouse>
  {mouse => (
    <p>鼠标的位置是 {mouse.x}，{mouse.y}</p>
  )}
</Mouse>
```

你将在 [react-motion](https://github.com/chenglou/react-motion) 的 API 中看到此技术。

由于这一技术的特殊性，当你在设计一个类似的 API 时，你或许会要直接地在你的 propTypes 里声明 children 的类型应为一个函数。

```
Mouse.propTypes = {
  children: PropTypes.func.isRequired
};
```

### 注意事项

#### 将 Render Props 与 React.PureComponent 一起使用时要小心

如果你在 render 方法里创建函数，那么使用 render prop 会抵消使用 [`React.PureComponent`](https://zh-hans.reactjs.org/docs/react-api.html#reactpurecomponent) 带来的优势。因为浅比较 props 的时候总会得到 false，并且在这种情况下每一个 `render` 对于 render prop 将会生成一个新的值。

例如，继续我们之前使用的 `<Mouse>` 组件，如果 `Mouse` 继承自 `React.PureComponent` 而不是 `React.Component`，我们的例子看起来就像这样：

```
class Mouse extends React.PureComponent {
  // 与上面相同的代码......
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>

        {/*
          这是不好的！
          每个渲染的 `render` prop的值将会是不同的。
        */}
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}
```

在这样例子中，每次 `<MouseTracker>` 渲染，它会生成一个新的函数作为 `<Mouse render>` 的 prop，因而在同时也抵消了继承自 `React.PureComponent` 的 `<Mouse>` 组件的效果！

为了绕过这一问题，有时你可以定义一个 prop 作为实例方法，类似这样：

```
class MouseTracker extends React.Component {
  // 定义为实例方法，`this.renderTheCat`始终
  // 当我们在渲染中使用它时，它指的是相同的函数
  renderTheCat(mouse) {
    return <Cat mouse={mouse} />;
  }

  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <Mouse render={this.renderTheCat} />
      </div>
    );
  }
}
```

如果你无法静态定义 prop（例如，因为你需要控制组件 props 和/或 state 的暴露程度），则 `<Mouse>` 应该继承自 `React.Component`。



## 深入JSX
- JSX 仅仅只是 `React.createElement(component, props, ...children)` 函数的语法糖
- 大写字母开头的 JSX 标签意味着它们是 React 组件。这些标签会被编译为对命名变量的直接引用，所以，当你使用 JSX `<Foo />` 表达式时，`Foo` 必须包含在作用域内。
- 由于 JSX 会编译为 `React.createElement` 调用形式，所以 `React` 库也必须包含在 JSX 代码作用域内。
### React 必须在作用域内

由于 JSX 会编译为 `React.createElement` 调用形式，所以 `React` 库也必须包含在 JSX 代码作用域内。

例如，在如下代码中，虽然 `React` 和 `CustomButton` 并没有被直接使用，但还是需要导入：

```
import React from 'react';import CustomButton from './CustomButton';
function WarningButton() {
  // return React.createElement(CustomButton, {color: 'red'}, null);  return <CustomButton color="red" />;
}
```

如果你不使用 JavaScript 打包工具而是直接通过 `<script>` 标签加载 React，则必须将 `React` 挂载到全局变量中。

### 在 JSX 类型中使用点语法

在 JSX 中，你也可以使用点语法来引用一个 React 组件。当你在一个模块中导出许多 React 组件时，这会非常方便。例如，如果 `MyComponents.DatePicker` 是一个组件，你可以在 JSX 中直接使用：

```
import React from 'react';

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  }
}

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;}
```

### 用户定义的组件必须以大写字母开头

以小写字母开头的元素代表一个 HTML 内置组件，比如 `<div>` 或者 `<span>` 会生成相应的字符串 `'div'` 或者 `'span'` 传递给 `React.createElement`（作为参数）。大写字母开头的元素则对应着在 JavaScript 引入或自定义的组件，如 `<Foo />` 会编译为 `React.createElement(Foo)`。

我们建议使用大写字母开头命名自定义组件。如果你确实需要一个以小写字母开头的组件，则在 JSX 中使用它之前，必须将它赋值给一个大写字母开头的变量。

例如，以下的代码将无法按照预期运行：

```
import React from 'react';

// 错误！组件应该以大写字母开头：function hello(props) {  // 正确！这种 <div> 的使用是合法的，因为 div 是一个有效的 HTML 标签
  return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
  // 错误！React 会认为 <hello /> 是一个 HTML 标签，因为它没有以大写字母开头：  return <hello toWhat="World" />;}
```

要解决这个问题，我们需要重命名 `hello` 为 `Hello`，同时在 JSX 中使用 `<Hello />` ：

```
import React from 'react';

// 正确！组件需要以大写字母开头：function Hello(props) {  // 正确！ 这种 <div> 的使用是合法的，因为 div 是一个有效的 HTML 标签：
  return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
  // 正确！React 知道 <Hello /> 是一个组件，因为它是大写字母开头的：  return <Hello toWhat="World" />;}
```
### 在运行时选择类型

你不能将通用表达式作为 React 元素类型。如果你想通过通用表达式来（动态）决定元素类型，你需要首先将它赋值给大写字母开头的变量。这通常用于根据 prop 来渲染不同组件的情况下:

```
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 错误！JSX 类型不能是一个表达式。  return <components[props.storyType] story={props.story} />;}
```

要解决这个问题, 需要首先将类型赋值给一个大写字母开头的变量：

```
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 正确！JSX 类型可以是大写字母开头的变量。  const SpecificStory = components[props.storyType];  return <SpecificStory story={props.story} />;}
```


### JSX 中的 Props

有多种方式可以在 JSX 中指定 props。

#### JavaScript 表达式作为 Props

你可以把包裹在 `{}` 中的 JavaScript 表达式作为一个 prop 传递给 JSX 元素。例如，如下的 JSX：

```
<MyComponent foo={1 + 2 + 3 + 4} />
```

在 `MyComponent` 中，`props.foo` 的值等于 `1 + 2 + 3 + 4` 的执行结果 `10`。

`if` 语句以及 `for` 循环不是 JavaScript 表达式，所以不能在 JSX 中直接使用。但是，你可以用在 JSX 以外的代码中。比如：

```
function NumberDescriber(props) {
  let description;
  if (props.number % 2 == 0) {    description = <strong>even</strong>;  } else {    description = <i>odd</i>;  }  return <div>{props.number} is an {description} number</div>;
}
```

你可以在对应的章节中学习更多关于[条件渲染](https://zh-hans.reactjs.org/docs/conditional-rendering.html)和[循环](https://zh-hans.reactjs.org/docs/lists-and-keys.html)的内容。

#### 字符串字面量

你可以将字符串字面量赋值给 prop. 如下两个 JSX 表达式是等价的：

```
<MyComponent message="hello world" />

<MyComponent message={'hello world'} />
```

当你将字符串字面量赋值给 prop 时，它的值是未转义的。所以，以下两个 JSX 表达式是等价的：

```
<MyComponent message="&lt;3" />

<MyComponent message={'<3'} />
```

这种行为通常是不重要的，这里只是提醒有这个用法。

#### Props 默认值为 “True”

如果你没给 prop 赋值，它的默认值是 `true`。以下两个 JSX 表达式是等价的：

```
<MyTextBox autocomplete />

<MyTextBox autocomplete={true} />
```

通常，我们不建议不传递 value 给 prop，因为这可能与 [ES6 对象简写](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#New_notations_in_ECMAScript_2015)混淆，`{foo}` 是 `{foo: foo}` 的简写，而不是 `{foo: true}`。这样实现只是为了保持和 HTML 中标签属性的行为一致。

#### 属性展开

如果你已经有了一个 props 对象，你可以使用展开运算符 `...` 来在 JSX 中传递整个 props 对象。以下两个组件是等价的：

```
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};
  return <Greeting {...props} />;}
```

你还可以选择只保留当前组件需要接收的 props，并使用展开运算符将其他 props 传递下去。

```
const Button = props => {
  const { kind, ...other } = props;  const className = kind === "primary" ? "PrimaryButton" : "SecondaryButton";
  return <button className={className} {...other} />;
};

const App = () => {
  return (
    <div>
      <Button kind="primary" onClick={() => console.log("clicked!")}>
        Hello World!
      </Button>
    </div>
  );
};
```

在上述例子中，`kind` 的 prop 会被安全的保留，它将_不会_被传递给 DOM 中的 `<button>` 元素。 所有其他的 props 会通过 `...other` 对象传递，使得这个组件的应用可以非常灵活。你可以看到它传递了一个 `onClick` 和 `children` 属性。

属性展开在某些情况下很有用，但是也很容易将不必要的 props 传递给不相关的组件，或者将无效的 HTML 属性传递给 DOM。我们建议谨慎的使用该语法。


### JSX 中的子元素

包含在开始和结束标签之间的 JSX 表达式内容将作为特定属性 `props.children` 传递给外层组件。有几种不同的方法来传递子元素：

#### 字符串字面量

你可以将字符串放在开始和结束标签之间，此时 `props.children` 就只是该字符串。这对于很多内置的 HTML 元素很有用。例如：

```
<MyComponent>Hello world!</MyComponent>
```

这是一个合法的 JSX，`MyComponent` 中的 `props.children` 是一个简单的未转义字符串 `"Hello world!"`。因此你可以采用编写 HTML 的方式来编写 JSX。如下所示：

```
<div>This is valid HTML &amp; JSX at the same time.</div>
```

JSX 会移除行首尾的空格以及空行。与标签相邻的空行均会被删除，文本字符串之间的新行会被压缩为一个空格。因此以下的几种方式都是等价的：

```
<div>Hello World</div>

<div>
  Hello World
</div>

<div>
  Hello
  World
</div>

<div>

  Hello World
</div>
```

#### JSX 子元素

子元素允许由多个 JSX 元素组成。这对于嵌套组件非常有用：

```
<MyContainer>
  <MyFirstComponent />
  <MySecondComponent />
</MyContainer>
```

你可以将不同类型的子元素混合在一起，因此你可以将字符串字面量与 JSX 子元素一起使用。这也是 JSX 类似 HTML 的一种表现，所以如下代码是合法的 JSX 并且也是合法的 HTML：

```
<div>
  Here is a list:
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
</div>
```

React 组件也能够返回存储在数组中的一组元素：

```
render() {
  // 不需要用额外的元素包裹列表元素！
  return [
    // 不要忘记设置 key :)
    <li key="A">First item</li>,
    <li key="B">Second item</li>,
    <li key="C">Third item</li>,
  ];
}
```

#### JavaScript 表达式作为子元素

JavaScript 表达式可以被包裹在 `{}` 中作为子元素。例如，以下表达式是等价的：

```
<MyComponent>foo</MyComponent>

<MyComponent>{'foo'}</MyComponent>
```

这对于展示任意长度的列表非常有用。例如，渲染 HTML 列表：

```
function Item(props) {
  return <li>{props.message}</li>;}

function TodoList() {
  const todos = ['finish doc', 'submit pr', 'nag dan to review'];
  return (
    <ul>
      {todos.map((message) => <Item key={message} message={message} />)}    </ul>
  );
}
```

JavaScript 表达式也可以和其他类型的子元素组合。这种做法可以方便地替代模板字符串：

```
function Hello(props) {
  return <div>Hello {props.addressee}!</div>;}
```
 
#### 函数作为子元素

通常，JSX 中的 JavaScript 表达式将会被计算为字符串、React 元素或者是列表。不过，`props.children` 和其他 prop 一样，它可以传递任意类型的数据，而不仅仅是 React 已知的可渲染类型。例如，如果你有一个自定义组件，你可以把回调函数作为 `props.children` 进行传递：

```
// 调用子元素回调 numTimes 次，来重复生成组件
function Repeat(props) {
  let items = [];
  for (let i = 0; i < props.numTimes; i++) {    items.push(props.children(i));
  }
  return <div>{items}</div>;
}

function ListOfTenThings() {
  return (
    <Repeat numTimes={10}>
      {(index) => <div key={index}>This is item {index} in the list</div>}    </Repeat>
  );
}
```

你可以将任何东西作为子元素传递给自定义组件，只要确保在该组件渲染之前能够被转换成 React 理解的对象。这种用法并不常见，但可以用于扩展 JSX。

#### 布尔类型、Null 以及 Undefined 将会忽略

`false`, `null`, `undefined`, and `true` 是合法的子元素。但它们并不会被渲染。以下的 JSX 表达式渲染结果相同：

```
<div />

<div></div>

<div>{false}</div>

<div>{null}</div>

<div>{undefined}</div>

<div>{true}</div>
```

这有助于依据特定条件来渲染其他的 React 元素。例如，在以下 JSX 中，仅当 `showHeader` 为 `true` 时，才会渲染 `<Header />` 组件：

```
<div>
  {showHeader && <Header />}  <Content />
</div>
```

值得注意的是有一些 [“falsy” 值](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)，如数字 `0`，仍然会被 React 渲染。例如，以下代码并不会像你预期那样工作，因为当 `props.messages` 是空数组时，`0` 仍然会被渲染：

```
<div>
  {props.messages.length &&    <MessageList messages={props.messages} />
  }
</div>
```

要解决这个问题，确保 `&&` 之前的表达式总是布尔值：

```
<div>
  {props.messages.length > 0 &&    <MessageList messages={props.messages} />
  }
</div>
```

反之，如果你想渲染 `false`、`true`、`null`、`undefined` 等值，你需要先将它们[转换为字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String#String_conversion)：

```
<div>
  My JavaScript variable is {String(myVariable)}.</div>
```

## 使用PropTypes类型检查
> 注意：
> 
> 自 React v15.5 起，`React.PropTypes` 已移入另一个包中。请使用 [`prop-types` 库](https://www.npmjs.com/package/prop-types) 代替。
> 
> 我们提供了一个 [codemod 脚本](https://zh-hans.reactjs.org/blog/2017/04/07/react-v15.5.0.html#migrating-from-reactproptypes)来做自动转换。

随着你的应用程序不断增长，你可以通过类型检查捕获大量错误。对于某些应用程序来说，你可以使用 [Flow](https://flow.org/) 或 [TypeScript](https://www.typescriptlang.org/) 等 JavaScript 扩展来对整个应用程序做类型检查。但即使你不使用这些扩展，React 也内置了一些类型检查的功能。要在组件的 props 上进行类型检查，你只需配置特定的 `propTypes` 属性：

```
import PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name: PropTypes.string
};
```

在此示例中，我们使用的是 class 组件，但是同样的功能也可用于函数组件，或者是由 [`React.memo`](https://zh-hans.reactjs.org/docs/react-api.html#reactmemo)/[`React.forwardRef`](https://zh-hans.reactjs.org/docs/react-api.html#reactforwardref) 创建的组件。

`PropTypes` 提供一系列验证器，可用于确保组件接收到的数据类型是有效的。在本例中, 我们使用了 `PropTypes.string`。当传入的 `prop` 值类型不正确时，JavaScript 控制台将会显示警告。出于性能方面的考虑，`propTypes` 仅在开发模式下进行检查。

### PropTypes

以下提供了使用不同验证器的例子：

```
import PropTypes from 'prop-types';

MyComponent.propTypes = {
  // 你可以将属性声明为 JS 原生类型，默认情况下
  // 这些属性都是可选的。
  optionalArray: PropTypes.array,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  optionalNumber: PropTypes.number,
  optionalObject: PropTypes.object,
  optionalString: PropTypes.string,
  optionalSymbol: PropTypes.symbol,

  // 任何可被渲染的元素（包括数字、字符串、元素或数组）
  // (或 Fragment) 也包含这些类型。
  optionalNode: PropTypes.node,

  // 一个 React 元素。
  optionalElement: PropTypes.element,

  // 一个 React 元素类型（即，MyComponent）。
  optionalElementType: PropTypes.elementType,

  // 你也可以声明 prop 为类的实例，这里使用
  // JS 的 instanceof 操作符。
  optionalMessage: PropTypes.instanceOf(Message),

  // 你可以让你的 prop 只能是特定的值，指定它为
  // 枚举类型。
  optionalEnum: PropTypes.oneOf(['News', 'Photos']),

  // 一个对象可以是几种类型中的任意一个类型
  optionalUnion: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message)
  ]),

  // 可以指定一个数组由某一类型的元素组成
  optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

  // 可以指定一个对象由某一类型的值组成
  optionalObjectOf: PropTypes.objectOf(PropTypes.number),

  // 可以指定一个对象由特定的类型值组成
  optionalObjectWithShape: PropTypes.shape({
    color: PropTypes.string,
    fontSize: PropTypes.number
  }),

  // An object with warnings on extra properties
  optionalObjectWithStrictShape: PropTypes.exact({
    name: PropTypes.string,
    quantity: PropTypes.number
  }),

  // 你可以在任何 PropTypes 属性后面加上 `isRequired` ，确保
  // 这个 prop 没有被提供时，会打印警告信息。
  requiredFunc: PropTypes.func.isRequired,

  // 任意类型的必需数据
  requiredAny: PropTypes.any.isRequired,

  // 你可以指定一个自定义验证器。它在验证失败时应返回一个 Error 对象。
  // 请不要使用 `console.warn` 或抛出异常，因为这在 `oneOfType` 中不会起作用。
  customProp: function(props, propName, componentName) {
    if (!/matchme/.test(props[propName])) {
      return new Error(
        'Invalid prop `' + propName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  },

  // 你也可以提供一个自定义的 `arrayOf` 或 `objectOf` 验证器。
  // 它应该在验证失败时返回一个 Error 对象。
  // 验证器将验证数组或对象中的每个值。验证器的前两个参数
  // 第一个是数组或对象本身
  // 第二个是他们当前的键。
  customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/matchme/.test(propValue[key])) {
      return new Error(
        'Invalid prop `' + propFullName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  })
};
```

### 限制单个元素

你可以通过 `PropTypes.element` 来确保传递给组件的 children 中只包含一个元素。

```
import PropTypes from 'prop-types';

class MyComponent extends React.Component {
  render() {
    // 这必须只有一个元素，否则控制台会打印警告。
    const children = this.props.children;
    return (
      <div>
        {children}
      </div>
    );
  }
}

MyComponent.propTypes = {
  children: PropTypes.element.isRequired
};
```

### 默认 Prop 值

您可以通过配置特定的 `defaultProps` 属性来定义 `props` 的默认值：

```
class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

// 指定 props 的默认值：
Greeting.defaultProps = {
  name: 'Stranger'
};

// 渲染出 "Hello, Stranger"：
ReactDOM.render(
  <Greeting />,
  document.getElementById('example')
);
```

如果你正在使用像 [plugin-proposal-class-properties](https://babeljs.io/docs/en/babel-plugin-proposal-class-properties/)（之前名为 _plugin-transform-class-properties_）的 Babel 转换工具，你也可以在 React 组件类中声明 `defaultProps` 作为静态属性。此语法提案还没有最终确定，需要进行编译后才能在浏览器中运行。要了解更多信息，请查阅 [class fields proposal](https://github.com/tc39/proposal-class-fields)。

```
class Greeting extends React.Component {
  static defaultProps = {
    name: 'stranger'
  }

  render() {
    return (
      <div>Hello, {this.props.name}</div>
    )
  }
}
```

`defaultProps` 用于确保 `this.props.name` 在父组件没有指定其值时，有一个默认值。`propTypes` 类型检查发生在 `defaultProps` 赋值后，所以类型检查也适用于 `defaultProps`。

### 函数组件

如果你在常规开发中使用函数组件，那你可能需要做一些适当的改动，以保证 PropsTypes 应用正常。

假设你有如下组件：

```
export default function HelloWorldComponent({ name }) {
  return (
    <div>Hello, {name}</div>
  )
}
```

如果要添加 PropTypes，你可能需要在导出之前以单独声明的一个函数的形式，声明该组件，具体代码如下：

```
function HelloWorldComponent({ name }) {
  return (
    <div>Hello, {name}</div>
  )
}

export default HelloWorldComponent
```

接着，可以直接在 `HelloWorldComponent` 上添加 PropTypes：

```
import PropTypes from 'prop-types'

function HelloWorldComponent({ name }) {
  return (
    <div>Hello, {name}</div>
  )
}

HelloWorldComponent.propTypes = {
  name: PropTypes.string
}

export default HelloWorldComponent
```

## 受控组件
在 React 里，HTML 表单元素的工作方式和其他的 DOM 元素有些不同，这是因为表单元素通常会保持一些内部的 state。例如这个纯 HTML 表单只接受一个名称：

```
<form>
  <label>
    名字:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="提交" />
</form>
```

此表单具有默认的 HTML 表单行为，即在用户提交表单后浏览到新页面。如果你在 React 中执行相同的代码，它依然有效。但大多数情况下，使用 JavaScript 函数可以很方便的处理表单的提交， 同时还可以访问用户填写的表单数据。实现这种效果的标准方式是使用“受控组件”。

### 受控组件

在 HTML 中，表单元素（如`<input>`、 `<textarea>` 和 `<select>`）通常自己维护 state，并根据用户输入进行更新。而在 React 中，可变状态（mutable state）通常保存在组件的 state 属性中，并且只能通过使用 [`setState()`](https://zh-hans.reactjs.org/docs/react-component.html#setstate)来更新。

我们可以把两者结合起来，使 React 的 state 成为“唯一数据源”。渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。

例如，如果我们想让前一个示例在提交时打印出名称，我们可以将表单写为受控组件：

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {    this.setState({value: event.target.value});  }
  handleSubmit(event) {
    alert('提交的名字: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>        <label>
          名字:
          <input type="text" value={this.state.value} onChange={this.handleChange} />        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
```

[**在 CodePen 上尝试**](https://codepen.io/gaearon/pen/VmmPgp?editors=0010)

由于在表单元素上设置了 `value` 属性，因此显示的值将始终为 `this.state.value`，这使得 React 的 state 成为唯一数据源。由于 `handlechange` 在每次按键时都会执行并更新 React 的 state，因此显示的值将随着用户输入而更新。

对于受控组件来说，输入的值始终由 React 的 state 驱动。你也可以将 value 传递给其他 UI 元素，或者通过其他事件处理函数重置，但这意味着你需要编写更多的代码。

### textarea 标签

在 HTML 中, `<textarea>` 元素通过其子元素定义其文本:

```
<textarea>
  你好， 这是在 text area 里的文本
</textarea>
```

而在 React 中，`<textarea>` 使用 `value` 属性代替。这样，可以使得使用 `<textarea>` 的表单和使用单行 input 的表单非常类似：

```
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {      value: '请撰写一篇关于你喜欢的 DOM 元素的文章.'    };
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {    this.setState({value: event.target.value});  }
  handleSubmit(event) {
    alert('提交的文章: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          文章:
          <textarea value={this.state.value} onChange={this.handleChange} />        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
```

请注意，`this.state.value` 初始化于构造函数中，因此文本区域默认有初值。

### select 标签

在 HTML 中，`<select>` 创建下拉列表标签。例如，如下 HTML 创建了水果相关的下拉列表：

```
<select>
  <option value="grapefruit">葡萄柚</option>
  <option value="lime">酸橙</option>
  <option selected value="coconut">椰子</option>
  <option value="mango">芒果</option>
</select>
```

请注意，由于 `selected` 属性的缘故，椰子选项默认被选中。React 并不会使用 `selected` 属性，而是在根 `select` 标签上使用 `value` 属性。这在受控组件中更便捷，因为您只需要在根标签中更新它。例如：

```
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {    this.setState({value: event.target.value});  }
  handleSubmit(event) {
    alert('你喜欢的风味是: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          选择你喜欢的风味:
          <select value={this.state.value} onChange={this.handleChange}>            <option value="grapefruit">葡萄柚</option>
            <option value="lime">酸橙</option>
            <option value="coconut">椰子</option>
            <option value="mango">芒果</option>
          </select>
        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
```

[**在 CodePen 上尝试**](https://codepen.io/gaearon/pen/JbbEzX?editors=0010)

总的来说，这使得 `<input type="text">`, `<textarea>` 和 `<select>` 之类的标签都非常相似—它们都接受一个 `value` 属性，你可以使用它来实现受控组件。

> 注意
> 
> 你可以将数组传递到 `value` 属性中，以支持在 `select` 标签中选择多个选项：
> 
> 
> `<select multiple={true} value={['B', 'C']}>`

### 文件 input 标签

在 HTML 中，`<input type="file">` 允许用户从存储设备中选择一个或多个文件，将其上传到服务器，或通过使用 JavaScript 的 [File API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications) 进行控制。

```
<input type="file" />
```

因为它的 value 只读，所以它是 React 中的一个**非受控**组件。将与其他非受控组件[在后续文档中](https://zh-hans.reactjs.org/docs/uncontrolled-components.html#the-file-input-tag)一起讨论。

### 处理多个输入

当需要处理多个 `input` 元素时，我们可以给每个元素添加 `name` 属性，并让处理函数根据 `event.target.name` 的值选择要执行的操作。

例如：

```
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;
    this.setState({
      [name]: value    });
  }

  render() {
    return (
      <form>
        <label>
          参与:
          <input
            name="isGoing"            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          来宾人数:
          <input
            name="numberOfGuests"            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

[**在 CodePen 上尝试**](https://codepen.io/gaearon/pen/wgedvV?editors=0010)

这里使用了 ES6 [计算属性名称](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)的语法更新给定输入名称对应的 state 值：

例如：

```
this.setState({
  [name]: value});
```

等同 ES5:

```
var partialState = {};
partialState[name] = value;this.setState(partialState);
```

另外，由于 `setState()` 自动[将部分 state 合并到当前 state](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html#state-updates-are-merged), 只需调用它更改部分 state 即可。

### 受控输入空值

在[受控组件](https://zh-hans.reactjs.org/docs/forms.html#controlled-components)上指定 `value` 的 prop 会阻止用户更改输入。如果你指定了 `value`，但输入仍可编辑，则可能是你意外地将 `value` 设置为 `undefined` 或 `null`。

下面的代码演示了这一点。（输入最初被锁定，但在短时间延迟后变为可编辑。）

```
ReactDOM.render(<input value="hi" />, mountNode);

setTimeout(function() {
  ReactDOM.render(<input value={null} />, mountNode);
}, 1000);
```

### 受控组件的替代品

有时使用受控组件会很麻烦，因为你需要为数据变化的每种方式都编写事件处理函数，并通过一个 React 组件传递所有的输入 state。当你将之前的代码库转换为 React 或将 React 应用程序与非 React 库集成时，这可能会令人厌烦。在这些情况下，你可能希望使用[非受控组件](https://zh-hans.reactjs.org/docs/uncontrolled-components.html), 这是实现输入表单的另一种方式。

### 成熟的解决方案

如果你想寻找包含验证、追踪访问字段以及处理表单提交的完整解决方案，使用 [Formik](https://jaredpalmer.com/formik) 是不错的选择。然而，它也是建立在受控组件和管理 state 的基础之上 —— 所以不要忽视学习它们。

## 非受控组件
在大多数情况下，我们推荐使用 [受控组件](https://zh-hans.reactjs.org/docs/forms.html#controlled-components) 来处理表单数据。在一个受控组件中，表单数据是由 React 组件来管理的。另一种替代方案是使用非受控组件，这时表单数据将交由 DOM 节点来处理。

要编写一个非受控组件，而不是为每个状态更新都编写数据处理函数，你可以 [使用 ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 来从 DOM 节点中获取表单数据。

例如，下面的代码使用非受控组件接受一个表单的值：

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.input = React.createRef();  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.current.value);    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" ref={this.input} />        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

[**在 CodePen 上尝试**](https://codepen.io/gaearon/pen/WooRWa?editors=0010)

因为非受控组件将真实数据储存在 DOM 节点中，所以在使用非受控组件时，有时候反而更容易同时集成 React 和非 React 代码。如果你不介意代码美观性，并且希望快速编写代码，使用非受控组件往往可以减少你的代码量。否则，你应该使用受控组件。

如果你还是不清楚在某个特殊场景中应该使用哪种组件，那么 [这篇关于受控和非受控输入组件的文章](https://goshakkk.name/controlled-vs-uncontrolled-inputs-react/) 会很有帮助。

### 默认值

在 React 渲染生命周期时，表单元素上的 `value` 将会覆盖 DOM 节点中的值。在非受控组件中，你经常希望 React 能赋予组件一个初始值，但是不去控制后续的更新。 在这种情况下, 你可以指定一个 `defaultValue` 属性，而不是 `value`。在一个组件已经挂载之后去更新 `defaultValue` 属性的值，不会造成 DOM 上值的任何更新。

```
render() {
  return (
    <form onSubmit={this.handleSubmit}>
      <label>
        Name:
        <input
          defaultValue="Bob"          type="text"
          ref={this.input} />
      </label>
      <input type="submit" value="Submit" />
    </form>
  );
}
```

同样，`<input type="checkbox">` 和 `<input type="radio">` 支持 `defaultChecked`，`<select>` 和 `<textarea>` 支持 `defaultValue`。

### 文件输入

在 HTML 中，`<input type="file">` 可以让用户选择一个或多个文件上传到服务器，或者通过使用 [File API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications) 进行操作。

```
<input type="file" />
```

在 React 中，`<input type="file" />` 始终是一个非受控组件，因为它的值只能由用户设置，而不能通过代码控制。

您应该使用 File API 与文件进行交互。下面的例子显示了如何创建一个 [DOM 节点的 ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 从而在提交表单时获取文件的信息。

```
class FileInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.fileInput = React.createRef();  }
  handleSubmit(event) {
    event.preventDefault();
    alert(
      `Selected file - ${this.fileInput.current.files[0].name}`    );
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Upload file:
          <input type="file" ref={this.fileInput} />        </label>
        <br />
        <button type="submit">Submit</button>
      </form>
    );
  }
}

ReactDOM.render(
  <FileInput />,
  document.getElementById('root')
);
```

[**在 CodePen 上尝试**](https://zh-hans.reactjs.org/redirect-to-codepen/uncontrolled-components/input-type-file)

## Web Components

React 和 [Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components) 为了解决不同的问题而生。Web Components 为可复用组件提供了强大的封装，而 React 则提供了声明式的解决方案，使 DOM 与数据保持同步。两者旨在互补。作为开发人员，可以自由选择在 Web Components 中使用 React，或者在 React 中使用 Web Components，或者两者共存。

大多数开发者在使用 React 时，不使用 Web Components，但可能你会需要使用，尤其是在使用 Web Components 编写的第三方 UI 组件时。

### 在 React 中使用 Web Components

```
class HelloMessage extends React.Component {
  render() {
    return <div>Hello <x-search>{this.props.name}</x-search>!</div>;
  }
}
```

> 注意：
> 
> Web Components 通常暴露的是命令式 API。例如，Web Components 的组件 `video` 可能会公开 `play()` 和 `pause()` 方法。要访问 Web Components 的命令式 API，你需要使用 `ref` 直接与 DOM 节点进行交互。如果你使用的是第三方 Web Components，那么最好的解决方案是编写 React 组件包装该 Web Components。
> 
> Web Components 触发的事件可能无法通过 React 渲染树正确的传递。 你需要在 React 组件中手动添加事件处理器来处理这些事件。

常见的误区是在 Web Components 中使用的是 `class` 而非 `className`。

```
function BrickFlipbox() {
  return (
    <brick-flipbox class="demo">
      <div>front</div>
      <div>back</div>
    </brick-flipbox>
  );
}
```

### 在 Web Components 中使用 React

```
class XSearch extends HTMLElement {
  connectedCallback() {
    const mountPoint = document.createElement('span');
    this.attachShadow({ mode: 'open' }).appendChild(mountPoint);

    const name = this.getAttribute('name');
    const url = 'https://www.google.com/search?q=' + encodeURIComponent(name);
    ReactDOM.render(<a href={url}>{name}</a>, mountPoint);
  }
}
customElements.define('x-search', XSearch);
```

> 注意：
> 
> 如果使用 Babel 来转换 class，此代码将**不会**起作用。请查阅该 [issue](https://github.com/w3c/webcomponents/issues/587) 了解相关讨论。 在加载 Web Components 前请引入 [custom-elements-es5-adapter](https://github.com/webcomponents/polyfills/tree/master/packages/webcomponentsjs#custom-elements-es5-adapterjs) 来解决该 issue。

## 高阶组件
- 高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。
- 具体而言，**高阶组件是参数为组件，返回值为新组件的函数。**
- 请注意，HOC 不会修改传入的组件，也不会使用继承来复制其行为。相反，HOC 通过将组件_包装_在容器组件中来_组成_新组件。HOC 是纯函数，没有副作用。
```js
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```
- 我们之前建议使用 mixins 用于解决横切关注点相关的问题。但我们已经意识到 mixins 会产生更多麻烦。[阅读更多](https://zh-hans.reactjs.org/blog/2016/07/13/mixins-considered-harmful.html) 以了解我们为什么要抛弃 mixins 以及如何转换现有组件。
- 组件是 React 中代码复用的基本单元。但你会发现某些模式并不适合传统组件。
- 你可以想象，在一个大型应用程序中，这种订阅 `DataSource` 和调用 `setState` 的模式将一次又一次地发生。我们需要一个抽象，允许我们在一个地方定义这个逻辑，并在许多组件之间共享它。这正是高阶组件擅长的地方。
- 被包装组件接收来自容器组件的所有 prop，同时也接收一个新的用于 render 的 `data` prop。HOC 不需要关心数据的使用方式或原因，而被包装组件也不需要关心数据是怎么来的。
```js
// 此函数接收一个组件...
function withSubscription(WrappedComponent, selectData) {
  // ...并返回另一个组件...
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props)
      };
    }

    componentDidMount() {
      // ...负责订阅相关的操作...
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // ... 并使用新数据渲染被包装的组件!
      // 请注意，我们可能还会传递其他属性
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}
```
### 不要改变原始组件，而应该使用组合
不要试图在 HOC 中修改组件原型（或以其他方式改变它）。

```js
function logProps(InputComponent) {
  InputComponent.prototype.componentDidUpdate = function(prevProps) {
    console.log('Current props: ', this.props);
    console.log('Previous props: ', prevProps);
  };
  // 返回原始的 input 组件，暗示它已经被修改。
  return InputComponent;
}

// 每次调用 logProps 时，增强组件都会有 log 输出。
const EnhancedComponent = logProps(InputComponent);
```

这样做会产生一些不良后果。其一是输入组件再也无法像 HOC 增强之前那样使用了。更严重的是，如果你再用另一个同样会修改 `componentDidUpdate` 的 HOC 增强它，那么前面的 HOC 就会失效！同时，这个 HOC 也无法应用于没有生命周期的函数组件。

修改传入组件的 HOC 是一种糟糕的抽象方式。调用者必须知道他们是如何实现的，以避免与其他 HOC 发生冲突。

HOC 不应该修改传入组件，而应该使用组合的方式，通过将组件包装在容器组件中实现功能：

```js
function logProps(WrappedComponent) {
  return class extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('Current props: ', this.props);
      console.log('Previous props: ', prevProps);
    }
    render() {
      // 将 input 组件包装在容器中，而不对其进行修改。Good!
      return <WrappedComponent {...this.props} />;
    }
  }
}
```

该 HOC 与上文中修改传入组件的 HOC 功能相同，同时避免了出现冲突的情况。它同样适用于 class 组件和函数组件。而且因为它是一个纯函数，它可以与其他 HOC 组合，甚至可以与其自身组合。

您可能已经注意到 HOC 与**容器组件模式**之间有相似之处。容器组件担任将高级和低级关注点分离的责任，由容器管理订阅和状态，并将 prop 传递给处理 UI 的组件。HOC 使用容器作为其实现的一部分，你可以将 HOC 视为参数化容器组件。

### 约定：将不相关的 props 传递给被包裹的组件

HOC 为组件添加特性。自身不应该大幅改变约定。HOC 返回的组件与原组件应保持类似的接口。

HOC 应该透传与自身无关的 props。大多数 HOC 都应该包含一个类似于下面的 render 方法：

```
render() {
  // 过滤掉非此 HOC 额外的 props，且不要进行透传
  const { extraProp, ...passThroughProps } = this.props;

  // 将 props 注入到被包装的组件中。
  // 通常为 state 的值或者实例方法。
  const injectedProp = someStateOrInstanceMethod;

  // 将 props 传递给被包装组件
  return (
    <WrappedComponent
      injectedProp={injectedProp}
      {...passThroughProps}
    />
  );
}
```

这种约定保证了 HOC 的灵活性以及可复用性。

### 约定：最大化可组合性

并不是所有的 HOC 都一样。有时候它仅接受一个参数，也就是被包裹的组件：

```
const NavbarWithRouter = withRouter(Navbar);
```

HOC 通常可以接收多个参数。比如在 Relay 中，HOC 额外接收了一个配置对象用于指定组件的数据依赖：

```
const CommentWithRelay = Relay.createContainer(Comment, config);
```

最常见的 HOC 签名如下：

```
// React Redux 的 `connect` 函数
const ConnectedComment = connect(commentSelector, commentActions)(CommentList);
```

_刚刚发生了什么？！_如果你把它分开，就会更容易看出发生了什么。

```
// connect 是一个函数，它的返回值为另外一个函数。
const enhance = connect(commentListSelector, commentListActions);
// 返回值为 HOC，它会返回已经连接 Redux store 的组件
const ConnectedComment = enhance(CommentList);
```

换句话说，`connect` 是一个返回高阶组件的高阶函数！

这种形式可能看起来令人困惑或不必要，但它有一个有用的属性。 像 `connect` 函数返回的单参数 HOC 具有签名 `Component => Component`。 输出类型与输入类型相同的函数很容易组合在一起。

```js
// 不推荐如下写法...
const EnhancedComponent = withRouter(connect(commentSelector)(WrappedComponent))

// ... 建议编写组合工具函数
// compose(f, g, h) 等同于 (...args) => f(g(h(...args)))
const enhance = compose(
  // 这些都是单参数的 HOC
  withRouter,
  connect(commentSelector)
)
const EnhancedComponent = enhance(WrappedComponent)
```

（同样的属性也允许 `connect` 和其他 HOC 承担装饰器的角色，装饰器是一个实验性的 JavaScript 提案。）

许多第三方库都提供了 `compose` 工具函数，包括 lodash （比如 [`lodash.flowRight`](https://lodash.com/docs/#flowRight)）， [Redux](https://redux.js.org/api/compose) 和 [Ramda](https://ramdajs.com/docs/#compose)。

### 约定：包装显示名称以便轻松调试

HOC 创建的容器组件会与任何其他组件一样，会显示在 [React Developer Tools](https://github.com/facebook/react/tree/main/packages/react-devtools) 中。为了方便调试，请选择一个显示名称，以表明它是 HOC 的产物。

最常见的方式是用 HOC 包住被包装组件的显示名称。比如高阶组件名为 `withSubscription`，并且被包装组件的显示名称为 `CommentList`，显示名称应该为 `WithSubscription(CommentList)`：

```
function withSubscription(WrappedComponent) {
  class WithSubscription extends React.Component {/* ... */}
  WithSubscription.displayName = `WithSubscription(${getDisplayName(WrappedComponent)})`;
  return WithSubscription;
}

function getDisplayName(WrappedComponent) {
  return WrappedComponent.displayName || WrappedComponent.name || 'Component';
}
```

### 不要在 render 方法中使用 HOC

React 的 diff 算法（称为[协调](https://zh-hans.reactjs.org/docs/reconciliation.html)）使用组件标识来确定它是应该更新现有子树还是将其丢弃并挂载新子树。 如果从 `render` 返回的组件与前一个渲染中的组件相同（`===`），则 React 通过将子树与新子树进行区分来递归更新子树。 如果它们不相等，则完全卸载前一个子树。

通常，你不需要考虑这点。但对 HOC 来说这一点很重要，因为这代表着你不应在组件的 render 方法中对一个组件应用 HOC：

```
render() {
  // 每次调用 render 函数都会创建一个新的 EnhancedComponent
  // EnhancedComponent1 !== EnhancedComponent2
  const EnhancedComponent = enhance(MyComponent);
  // 这将导致子树每次渲染都会进行卸载，和重新挂载的操作！
  return <EnhancedComponent />;
}
```

这不仅仅是性能问题 - 重新挂载组件会导致该组件及其所有子组件的状态丢失。

如果在组件之外创建 HOC，这样一来组件只会创建一次。因此，每次 render 时都会是同一个组件。一般来说，这跟你的预期表现是一致的。

在极少数情况下，你需要动态调用 HOC。你可以在组件的生命周期方法或其构造函数中进行调用。

### 务必复制静态方法

有时在 React 组件上定义静态方法很有用。例如，Relay 容器暴露了一个静态方法 `getFragment` 以方便组合 GraphQL 片段。

但是，当你将 HOC 应用于组件时，原始组件将使用容器组件进行包装。这意味着新组件没有原始组件的任何静态方法。

```
// 定义静态函数
WrappedComponent.staticMethod = function() {/*...*/}
// 现在使用 HOC
const EnhancedComponent = enhance(WrappedComponent);

// 增强组件没有 staticMethod
typeof EnhancedComponent.staticMethod === 'undefined' // true
```

为了解决这个问题，你可以在返回之前把这些方法拷贝到容器组件上：

```
function enhance(WrappedComponent) {
  class Enhance extends React.Component {/*...*/}
  // 必须准确知道应该拷贝哪些方法 :(
  Enhance.staticMethod = WrappedComponent.staticMethod;
  return Enhance;
}
```

但要这样做，你需要知道哪些方法应该被拷贝。你可以使用 [hoist-non-react-statics](https://github.com/mridgway/hoist-non-react-statics) 自动拷贝所有非 React 静态方法:

```
import hoistNonReactStatic from 'hoist-non-react-statics';
function enhance(WrappedComponent) {
  class Enhance extends React.Component {/*...*/}
  hoistNonReactStatic(Enhance, WrappedComponent);
  return Enhance;
}
```

除了导出组件，另一个可行的方案是再额外导出这个静态方法。

```
// 使用这种方式代替...
MyComponent.someFunction = someFunction;
export default MyComponent;

// ...单独导出该方法...
export { someFunction };

// ...并在要使用的组件中，import 它们
import MyComponent, { someFunction } from './MyComponent.js';
```

### Refs 不会被传递

虽然高阶组件的约定是将所有 props 传递给被包装组件，但这对于 refs 并不适用。那是因为 `ref` 实际上并不是一个 prop - 就像 `key` 一样，它是由 React 专门处理的。如果将 ref 添加到 HOC 的返回组件中，则 ref 引用指向容器组件，而不是被包装组件。

这个问题的解决方案是通过使用 `React.forwardRef` API（React 16.3 中引入）。[前往 ref 转发章节了解更多](https://zh-hans.reactjs.org/docs/forwarding-refs.html)。

## 协调-DIFF算法
- React 提供的声明式 API 让开发者可以在对 React 的底层实现并不了解的情况下编写应用。在开发者编写应用时，可以保持相对简单的心智，但开发者无法了解其内部的实现原理。本文描述了在实现 React 的 “diffing” 算法过程中所作出的设计决策，以保证组件更新可预测，且在繁杂业务场景下依然保持应用的高性能。

### 设计动机

在某一时间节点调用 React 的 `render()` 方法，会创建一棵由 React 元素组成的树。在下一次 state 或 props 更新时，相同的 `render()` 方法会返回一棵不同的树。React 需要基于这两棵树之间的差别来判断如何高效的更新 UI，以保证当前 UI 与最新的树保持同步。

此算法有一些通用的解决方案，即生成将一棵树转换成另一棵树的最小操作次数。然而，即使使用[最优的算法](http://grfia.dlsi.ua.es/ml/algorithms/references/editsurvey_bille.pdf)，该算法的复杂程度仍为 O(n 3 )，其中 n 是树中元素的数量。

如果在 React 中使用该算法，那么展示 1000 个元素则需要 10 亿次的比较。这个开销实在是太过高昂。于是 React 在以下两个假设的基础之上提出了一套 O(n) 的启发式算法：

1.  两个不同类型的元素会产生出不同的树；
2.  开发者可以通过设置 `key` 属性，来告知渲染哪些子元素在不同的渲染下可以保存不变；

在实践中，我们发现以上假设在几乎所有实用的场景下都成立。


### Diffing 算法

当对比两棵树时，React 首先比较两棵树的根节点。不同类型的根节点元素会有不同的形态。

#### 对比不同类型的元素

当根节点为不同类型的元素时，React 会拆卸原有的树并且建立起新的树。举个例子，当一个元素从 `<a>` 变成 `<img>`，从 `<Article>` 变成 `<Comment>`，或从 `<Button>` 变成 `<div>` 都会触发一个完整的重建流程。

当卸载一棵树时，对应的 DOM 节点也会被销毁。组件实例将执行 `componentWillUnmount()` 方法。当建立一棵新的树时，对应的 DOM 节点会被创建以及插入到 DOM 中。组件实例将执行 `UNSAFE_componentWillMount()` 方法，紧接着 `componentDidMount()` 方法。所有与之前的树相关联的 state 也会被销毁。

在根节点以下的组件也会被卸载，它们的状态会被销毁。比如，当比对以下更变时：

```
<div>
  <Counter />
</div>

<span>
  <Counter />
</span>
```

React 会销毁 `Counter` 组件并且重新装载一个新的组件。

> 注意：
> 
> 这些方法被认为是过时的，在新的代码中应该[避免使用它们](https://zh-hans.reactjs.org/blog/2018/03/27/update-on-async-rendering.html)：
> 
> -   `UNSAFE_componentWillMount()`

#### 对比同一类型的元素

当对比两个相同类型的 React 元素时，React 会保留 DOM 节点，仅比对及更新有改变的属性。比如：

```
<div className="before" title="stuff" />

<div className="after" title="stuff" />
```

通过对比这两个元素，React 知道只需要修改 DOM 元素上的 `className` 属性。

当更新 `style` 属性时，React 仅更新有所更变的属性。比如：

```
<div style={{color: 'red', fontWeight: 'bold'}} />

<div style={{color: 'green', fontWeight: 'bold'}} />
```

通过对比这两个元素，React 知道只需要修改 DOM 元素上的 `color` 样式，无需修改 `fontWeight`。

在处理完当前节点之后，React 继续对子节点进行递归。

#### 对比同类型的组件元素

当一个组件更新时，组件实例会保持不变，因此可以在不同的渲染时保持 state 一致。React 将更新该组件实例的 props 以保证与最新的元素保持一致，并且调用该实例的 `UNSAFE_componentWillReceiveProps()`、`UNSAFE_componentWillUpdate()` 以及 `componentDidUpdate()` 方法。

下一步，调用 `render()` 方法，diff 算法将在之前的结果以及新的结果中进行递归。

> 注意：
> 
> 这些方法已过时，在新代码中应避免[使用它们](https://zh-hans.reactjs.org/blog/2018/03/27/update-on-async-rendering.html)：
> 
> -   `UNSAFE_componentWillUpdate()`
> -   `UNSAFE_componentWillReceiveProps()`

#### 对子节点进行递归

默认情况下，当递归 DOM 节点的子元素时，React 会同时遍历两个子元素的列表；当产生差异时，生成一个 mutation。

在子元素列表末尾新增元素时，更新开销比较小。比如：

```
<ul>
  <li>first</li>
  <li>second</li>
</ul>

<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```

React 会先匹配两个 `<li>first</li>` 对应的树，然后匹配第二个元素 `<li>second</li>` 对应的树，最后插入第三个元素的 `<li>third</li>` 树。

如果只是简单的将新增元素插入到表头，那么更新开销会比较大。比如：

```
<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>

<ul>
  <li>Connecticut</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
```

React 并不会意识到应该保留 `<li>Duke</li>` 和 `<li>Villanova</li>`，而是会重建每一个子元素。这种情况会带来性能问题。

#### Keys

为了解决上述问题，React 引入了 `key` 属性。当子元素拥有 key 时，React 使用 key 来匹配原有树上的子元素以及最新树上的子元素。以下示例在新增 `key` 之后，使得树的转换效率得以提高：

```
<ul>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>

<ul>
  <li key="2014">Connecticut</li>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
```

现在 React 知道只有带着 `'2014'` key 的元素是新元素，带着 `'2015'` 以及 `'2016'` key 的元素仅仅移动了。

实际开发中，编写一个 key 并不困难。你要展现的元素可能已经有了一个唯一 ID，于是 key 可以直接从你的数据中提取：

```
<li key={item.id}>{item.name}</li>
```

当以上情况不成立时，你可以新增一个 ID 字段到你的模型中，或者利用一部分内容作为哈希值来生成一个 key。这个 key 不需要全局唯一，但在列表中需要保持唯一。

最后，你也可以使用元素在数组中的下标作为 key。这个策略在元素不进行重新排序时比较合适，如果有顺序修改，diff 就会变慢。

当基于下标的组件进行重新排序时，组件 state 可能会遇到一些问题。由于组件实例是基于它们的 key 来决定是否更新以及复用，如果 key 是一个下标，那么修改顺序时会修改当前的 key，导致非受控组件的 state（比如输入框）可能相互篡改，会出现无法预期的变动。

在 Codepen 有两个例子，分别为 [展示使用下标作为 key 时导致的问题](https://zh-hans.reactjs.org/redirect-to-codepen/reconciliation/index-used-as-key)，以及[不使用下标作为 key 的例子的版本，修复了重新排列，排序，以及在列表头插入的问题](https://zh-hans.reactjs.org/redirect-to-codepen/reconciliation/no-index-used-as-key)。

### 权衡

请谨记协调算法是一个实现细节。React 可以在每个 action 之后对整个应用进行重新渲染，得到的最终结果也会是一样的。在此情境下，重新渲染表示在所有组件内调用 `render` 方法，这不代表 React 会卸载或装载它们。React 只会基于以上提到的规则来决定如何进行差异的合并。

我们定期优化启发式算法，让常见用例更高效地执行。在当前的实现中，可以理解为一棵子树能在其兄弟之间移动，但不能移动到其他位置。在这种情况下，算法会重新渲染整棵子树。

由于 React 依赖启发式算法，因此当以下假设没有得到满足，性能会有所损耗。

1.  该算法不会尝试匹配不同组件类型的子树。如果你发现你在两种不同类型的组件中切换，但输出非常相似的内容，建议把它们改成同一类型。在实践中，我们没有遇到这类问题。
2.  Key 应该具有稳定，可预测，以及列表内唯一的特质。不稳定的 key（比如通过 `Math.random()` 生成的）会导致许多组件实例和 DOM 节点被不必要地重新创建，这可能导致性能下降和子组件中的状态丢失。

## 性能优化
- UI 更新需要昂贵的 DOM 操作，因此 React 内部使用了几种巧妙的技术来最小化 DOM 操作次数。对于大部分应用而言，使用 React 时无需做大量优化工作就能拥有高性能的用户界面。尽管如此，你仍然有办法来加速你的 React 应用。

### 使用生产版本
### Create React App
### 单文件构建
我们提供了可以在生产环境使用的单文件版 React 和 React DOM：

```
<script src="https://unpkg.com/react@17/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@17/umd/react-dom.production.min.js"></script>
```

注意只有以 `.production.min.js` 为结尾的 React 文件适用于生产。

### Brunch

通过安装 [`terser-brunch`](https://github.com/brunch/terser-brunch) 插件，来获得最高效的 Brunch 生产构建：

```
# 如果你使用 npm
npm install --save-dev terser-brunch

# 如果你使用 Yarn
yarn add --dev terser-brunch
```

接着，在 `build` 命令后添加 `-p` 参数，以创建生产构建：

```
brunch build -p
```

请注意，你只需要在生产构建时这么做。你不需要在开发环境中使用 `-p` 参数或者应用这个插件，因为这会隐藏有用的 React 警告信息并使得构建速度变慢。

### Browserify

为了最高效的生产构建，需要安装一些插件：

```
# 如果你使用 npm
npm install --save-dev envify terser uglifyify

# 如果你使用 Yarn
yarn add --dev envify terser uglifyify
```

为了创建生产构建，确保你添加了以下转换器 **（顺序很重要）**：

-   [`envify`](https://github.com/hughsk/envify) 转换器用于设置正确的环境变量。设置为全局 (`-g`)。
-   [`uglifyify`](https://github.com/hughsk/uglifyify) 转换器移除开发相关的引用代码。同样设置为全局 (`-g`)。
-   最后，将产物传给 [`terser`](https://github.com/terser-js/terser) 并进行压缩（[为什么要这么做？](https://github.com/hughsk/uglifyify#motivationusage)）。

举个例子：

```
browserify ./index.js \
  -g [ envify --NODE_ENV production ] \
  -g uglifyify \
  | terser --compress --mangle > ./bundle.js
```

请注意，你只需要在生产构建时用到它。你不需要在开发环境应用这些插件，因为这会隐藏有用的 React 警告信息并使得构建速度变慢。

### Rollup

为了最高效的 Rollup 生产构建，需要安装一些插件：

```
# 如果你使用 npm
npm install --save-dev rollup-plugin-commonjs rollup-plugin-replace rollup-plugin-terser

# 如果你使用 Yarn
yarn add --dev rollup-plugin-commonjs rollup-plugin-replace rollup-plugin-terser
```

为了创建生产构建，确保你添加了以下插件 **（顺序很重要）**：

-   [`replace`](https://github.com/rollup/rollup-plugin-replace) 插件确保环境被正确设置。
-   [`commonjs`](https://github.com/rollup/rollup-plugin-commonjs) 插件用于支持 CommonJS。
-   [`terser`](https://github.com/TrySound/rollup-plugin-terser) 插件用于压缩并生成最终的产物。

```
plugins: [
  // ...
  require('rollup-plugin-replace')({
    'process.env.NODE_ENV': JSON.stringify('production')
  }),
  require('rollup-plugin-commonjs')(),
  require('rollup-plugin-terser')(),
  // ...
]
```

[点击](https://gist.github.com/Rich-Harris/cb14f4bc0670c47d00d191565be36bf0)查看完整的安装示例。

请注意，你只需要在生产构建时用到它。你不需要在开发中使用 `terser` 插件或者 `replace` 插件替换 `'production'` 变量，因为这会隐藏有用的 React 警告信息并使得构建速度变慢。

### webpack

> **注意：**
> 
> 如果你使用了 Create React App，请跟随[上面的说明](https://zh-hans.reactjs.org/docs/optimizing-performance.html#create-react-app)进行操作。  
> 只有当你直接配置了 webpack 才需要参考以下内容。

在生产模式下，Webpack v4+ 将默认对代码进行压缩：

```
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
  mode: 'production',
  optimization: {
    minimizer: [new TerserPlugin({ /* additional options here */ })],
  },
};
```

你可以在 [webpack 文档](https://webpack.js.org/guides/production/)中了解更多内容。

请注意，你只需要在生产构建时用到它。你不需要在开发中使用 `TerserPlugin` 插件，因为这会隐藏有用的 React 警告信息并使得构建速度变慢。

### 使用开发者工具中的分析器对组件进行分析

`react-dom` 16.5+ 和 `react-native` 0.57+ 加强了分析能力。在开发模式下，React 开发者工具会出现分析器标签。 你可以在[《介绍 React 分析器》](https://zh-hans.reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html)这篇博客中了解概述。 你也可以[在 YouTube 上](https://www.youtube.com/watch?v=nySib7ipZdk)观看分析器的视频指导。

如果你还未安装 React 开发者工具，你可以在这里找到它们：

-   [Chrome 浏览器扩展](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)
-   [Firefox 浏览器扩展](https://addons.mozilla.org/en-GB/firefox/addon/react-devtools/)
-   [独立 Node 包](https://www.npmjs.com/package/react-devtools)

> 注意
> 
> `react-dom` 的生产分析包也可以在 `react-dom/profiling` 中找到。 通过查阅 [fb.me/react-profiling](https://fb.me/react-profiling) 来了解更多关于使用这个包的内容。

> 注意
> 
> 在 React 17 之前，我们使用了标准的 [User Timing API](https://developer.mozilla.org/en-US/docs/Web/API/User_Timing_API)，用 chrome 的 performance 性能选项卡来配置组件。 更详细的攻略，请参阅 [Ben Schwarz 的文章](https://calibreapp.com/blog/react-performance-profiling-optimization)。

### 虚拟化长列表

如果你的应用渲染了长列表（上百甚至上千的数据），我们推荐使用“虚拟滚动”技术。这项技术会在有限的时间内仅渲染有限的内容，并奇迹般地降低重新渲染组件消耗的时间，以及创建 DOM 节点的数量。

[react-window](https://react-window.now.sh/) 和 [react-virtualized](https://bvaughn.github.io/react-virtualized/) 是热门的虚拟滚动库。 它们提供了多种可复用的组件，用于展示列表、网格和表格数据。 如果你想要一些针对你的应用做定制优化，你也可以创建你自己的虚拟滚动组件，就像 [Twitter 所做的](https://medium.com/@paularmstrong/twitter-lite-and-high-performance-react-progressive-web-apps-at-scale-d28a00e780a3)。

### 避免调停

React 构建并维护了一套内部的 UI 渲染描述。它包含了来自你的组件返回的 React 元素。该描述使得 React 避免创建 DOM 节点以及没有必要的节点访问，因为 DOM 操作相对于 JavaScript 对象操作更慢。虽然有时候它被称为“虚拟 DOM”，但是它在 React Native 中拥有相同的工作原理。

当一个组件的 props 或 state 变更，React 会将最新返回的元素与之前渲染的元素进行对比，以此决定是否有必要更新真实的 DOM。当它们不相同时，React 会更新该 DOM。

即使 React 只更新改变了的 DOM 节点，重新渲染仍然花费了一些时间。在大部分情况下它并不是问题，不过如果它已经慢到让人注意了，你可以通过覆盖生命周期方法 `shouldComponentUpdate` 来进行提速。该方法会在重新渲染前被触发。其默认实现总是返回 `true`，让 React 执行更新：

```
shouldComponentUpdate(nextProps, nextState) {
  return true;
}
```

如果你知道在什么情况下你的组件不需要更新，你可以在 `shouldComponentUpdate` 中返回 `false` 来跳过整个渲染过程。其包括该组件的 `render` 调用以及之后的操作。

在大部分情况下，你可以继承 [`React.PureComponent`](https://zh-hans.reactjs.org/docs/react-api.html#reactpurecomponent) 以代替手写 `shouldComponentUpdate()`。它用当前与之前 props 和 state 的浅比较覆写了 `shouldComponentUpdate()` 的实现。


### shouldComponentUpdate 的作用

这是一个组件的子树。每个节点中，`SCU` 代表 `shouldComponentUpdate` 返回的值，而 `vDOMEq` 代表返回的 React 元素是否相同。最后，圆圈的颜色代表了该组件是否需要被调停。

[![should component update](https://zh-hans.reactjs.org/static/5ee1bdf4779af06072a17b7a0654f6db/cd039/should-component-update.png)](https://zh-hans.reactjs.org/static/5ee1bdf4779af06072a17b7a0654f6db/cd039/should-component-update.png)

节点 C2 的 `shouldComponentUpdate` 返回了 `false`，React 因而不会去渲染 C2，也因此 C4 和 C5 的 `shouldComponentUpdate` 不会被调用到。

对于 C1 和 C3，`shouldComponentUpdate` 返回了 `true`，所以 React 需要继续向下查询子节点。这里 C6 的 `shouldComponentUpdate` 返回了 `true`，同时由于渲染的元素与之前的不同使得 React 更新了该 DOM。

最后一个有趣的例子是 C8。React 需要渲染这个组件，但是由于其返回的 React 元素和之前渲染的相同，所以不需要更新 DOM。

显而易见，你看到 React 只改变了 C6 的 DOM。对于 C8，通过对比了渲染的 React 元素跳过了渲染。而对于 C2 的子节点和 C7，由于 `shouldComponentUpdate` 使得 `render` 并没有被调用。因此它们也不需要对比元素了。

### 不可变数据的力量

避免该问题最简单的方式是避免更改你正用于 props 或 state 的值。例如，上面 `handleClick` 方法可以用 `concat` 重写：

```
handleClick() {
  this.setState(state => ({
    words: state.words.concat(['marklar'])
  }));
}
```

ES6 数组支持[扩展运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)，这让代码写起来更方便了。如果你在使用 Create React App，该语法已经默认支持了。

```
handleClick() {
  this.setState(state => ({
    words: [...state.words, 'marklar'],
  }));
};
```

你可以用类似的方式改写代码来避免可变对象的产生。例如，我们有一个叫做 `colormap` 的对象。我们希望写一个方法来将 `colormap.right` 设置为 `'blue'`。我们可以这么写：

```
function updateColorMap(colormap) {
  colormap.right = 'blue';
}
```

为了不改变原本的对象，我们可以使用 [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 方法：

```
function updateColorMap(colormap) {
  return Object.assign({}, colormap, {right: 'blue'});
}
```

现在 `updateColorMap` 返回了一个新的对象，而不是修改老对象。`Object.assign` 是 ES6 的方法，需要 polyfill。

这里有一个 JavaScript 的提案，旨在添加[对象扩展属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)以使得更新不可变对象变得更方便：

```
function updateColorMap(colormap) {
  return {...colormap, right: 'blue'};
}
```

此特性已被收录在 JavaScript 的 ES2018 中。

如果你在使用 Create React App，`Object.assign` 以及对象扩展运算符已经默认支持了。

当处理深层嵌套对象时，以 immutable （不可变）的方式更新它们令人费解。如遇到此类问题，请参阅 [Immer](https://github.com/mweststrate/immer) 或 [immutability-helper](https://github.com/kolodny/immutability-helper)。这些库会帮助你编写高可读性的代码，且不会失去 immutability （不可变性）带来的好处。

## 与第三方库协同
- React 可以被用于任何 web 应用中。它可以被嵌入到其他应用，且需要注意，其他的应用也可以被嵌入到 React。本指南将介绍一些更常见的用例，专注于与 [jQuery](https://jquery.com/) 和 [Backbone](http://backbonejs.org/) 进行整合，同样的思路还可以应用于将组件与任意现有代码集成。

### 集成带有 DOM 操作的插件
React 不会理会 React 自身之外的 DOM 操作。它根据内部虚拟 DOM 来决定是否需要更新，而且如果同一个 DOM 节点被另一个库操作了，React 会觉得困惑而且没有办法恢复。

这并不意味着 React 与其他操作 DOM 的方式不能结合，也不一定结合困难，只不过需要你去关注每个库所做的事情。

避免冲突的最简单方式就是防止 React 组件更新。你可以渲染无需更新的 React 元素，比如一个空的 `<div />`。
```js
class SomePlugin extends React.Component {
  componentDidMount() {
    this.$el = $(this.el);    this.$el.somePlugin();  }

  componentWillUnmount() {
    this.$el.somePlugin('destroy');  }

  render() {
    return <div ref={el => this.el = el} />;  }
}
```

### 和其他视图库集成
得益于 [`ReactDOM.render()`](https://zh-hans.reactjs.org/docs/react-dom.html#render) 的灵活性 React 可以被嵌入到其他的应用中。

虽然 React 通常被用来在启动的时候加载一个单独的根 React 组件到 DOM 上，`ReactDOM.render()` 同样可以在 UI 的独立部分上多次调用，这些部分可以小到一个按钮，也可以大到一个应用。

事实上，这正是 Facebook 如何使用 React 的。这让我们小块小块地在应用中使用 React，并且把他们结合到我们现存的服务端产生的模板和其他客户端代码中。

### 把 React 嵌入到 Backbone 视图
[Backbone](http://backbonejs.org/) 视图通常使用 HTML 字符串，或者产生字符串的模板函数，来创建 DOM 元素的内容。这个过程，同样的，可以通过渲染一个 React 组件来替换掉。

如下，我们会创建一个名为 `ParagraphView` 的 Backbone 视图。他会重载 Backbone 的 `render()` 函数来渲染一个 React `<Paragraph>` 组件到 Backbone (`this.el`) 提供的 DOM 元素中。这里，同样的，我们将会使用 [`ReactDOM.render()`](https://zh-hans.reactjs.org/docs/react-dom.html#render)：

```js
function Paragraph(props) {  return <p>{props.text}</p>;
}

const ParagraphView = Backbone.View.extend({  render() {
    const text = this.model.get('text');
    ReactDOM.render(<Paragraph text={text} />, this.el);    return this;
  },
  remove() {
    ReactDOM.unmountComponentAtNode(this.el);    Backbone.View.prototype.remove.call(this);
  }
});
```

### 和 Model 层集成
虽然通常是推荐使用单向数据流动的，例如 [React state](https://zh-hans.reactjs.org/docs/lifting-state-up.html)，[Flux](http://facebook.github.io/flux/)，或者 [Redux](http://redux.js.org/)，React 组件也可以使用一个其他框架和库的 Model 层。

#### 在 React 组件中使用 Backbone 的 Model

在 React 组件中使用 [Backbone](http://backbonejs.org/) 的 model 和 collection 最简单的方法就是监听多种变化事件并且手动强制触发一个更新。

负责渲染 model 的组件会监听 `'change'` 事件，而负责渲染 collection 的组件需要监听 `'add'` 和 `'remove'` 事件。在这两种情况中，调用 [`this.forceUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#forceupdate) 来使用新的数据重新渲染组件。

在下面的例子中，`List` 组件渲染一个 Backbone collection，使用 `Item` 组件来渲染独立的项。

```
class Item extends React.Component {  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange() {    this.forceUpdate();  }
  componentDidMount() {
    this.props.model.on('change', this.handleChange);  }

  componentWillUnmount() {
    this.props.model.off('change', this.handleChange);  }

  render() {
    return <li>{this.props.model.get('text')}</li>;
  }
}

class List extends React.Component {  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange() {    this.forceUpdate();  }
  componentDidMount() {
    this.props.collection.on('add', 'remove', this.handleChange);  }

  componentWillUnmount() {
    this.props.collection.off('add', 'remove', this.handleChange);  }

  render() {
    return (
      <ul>
        {this.props.collection.map(model => (
          <Item key={model.cid} model={model} />        ))}
      </ul>
    );
  }
}
```

#### 从 Backbone Model 提取数据

前面的方式需要你的 React 组件知道 Backbone 的 model 和 collection。如果你计划迁移到另一个数据管理方案，你可能希望将关于Backbone的知识集中在尽可能少的代码部分中。

其中一个解决方案就是每当 model 中的属性变化时都把它提取成简单数据，并且把这个逻辑放在一个独立的地方。下面是一个[高阶组件](https://zh-hans.reactjs.org/docs/higher-order-components.html)，它提取了 Backbone model 的所有数据存放到 state 中，并将数据传递到被包裹的组件中。

通过这种方法，只有高阶组件需要知道 Backbone model 的内部构造，而且应用中大多数的组件可以保持和 Backbone 无关。

在下面的例子中，我们会拷贝一份 model 的属性来形成初始的 state。我们订阅 `change` 事件（并且在取消挂载时停止订阅），而当变化发生时，我们使用 model 的当前属性更新这个 state。最终，我们确保了只要 `model` 属性本身变化的时候，我们不要忘了停止旧 model 的订阅并开始订阅新的 model。

请注意，这个例子并不是为了彻底完整展示如何与 Backbone 集成，而是它应该让你了解如何以通用的方式处理此问题：

```js
function connectToBackboneModel(WrappedComponent) {  return class BackboneComponent extends React.Component {
    constructor(props) {
      super(props);
      this.state = Object.assign({}, props.model.attributes);      this.handleChange = this.handleChange.bind(this);
    }

    componentDidMount() {
      this.props.model.on('change', this.handleChange);    }

    componentWillReceiveProps(nextProps) {
      this.setState(Object.assign({}, nextProps.model.attributes));      if (nextProps.model !== this.props.model) {
        this.props.model.off('change', this.handleChange);        nextProps.model.on('change', this.handleChange);      }
    }

    componentWillUnmount() {
      this.props.model.off('change', this.handleChange);    }

    handleChange(model) {
      this.setState(model.changedAttributes());    }

    render() {
      const propsExceptModel = Object.assign({}, this.props);
      delete propsExceptModel.model;
      return <WrappedComponent {...propsExceptModel} {...this.state} />;    }
  }
}
```

要演示如何使用它，我们会链接一个 `NameInput` React 组件到一个 Backbone model，并且每当输入框变化时更新它的 `firstName` 属性：

```js
function NameInput(props) {
  return (
    <p>
      <input value={props.firstName} onChange={props.handleChange} />      <br />
      My name is {props.firstName}.    </p>
  );
}

const BackboneNameInput = connectToBackboneModel(NameInput);
function Example(props) {
  function handleChange(e) {
    props.model.set('firstName', e.target.value);  }

  return (
    <BackboneNameInput      model={props.model}      handleChange={handleChange}    />
  );
}

const model = new Backbone.Model({ firstName: 'Frodo' });
ReactDOM.render(
  <Example model={model} />,
  document.getElementById('root')
);
```

## Profiler API
- `Profiler` 测量一个 React 应用多久渲染一次以及渲染一次的“代价”。 它的目的是识别出应用中渲染较慢的部分，或是可以使用[类似 memoization 优化](https://zh-hans.reactjs.org/docs/hooks-faq.html#how-to-memoize-calculations)的部分，并从相关优化中获益。
- Profiling 增加了额外的开支，所以**它在[生产构建](https://zh-hans.reactjs.org/docs/optimizing-performance.html#use-the-production-build)中会被禁用**。为了将 profiling 功能加入生产环境中，React 提供了使 profiling 可用的特殊的生产构建环境。 从 [fb.me/react-profiling](https://fb.me/react-profiling)了解更多关于如何使用这个构建环境的信息。

### 用法

`Profiler` 能添加在 React 树中的任何地方来测量树中这部分渲染所带来的开销。 它需要两个 prop ：一个是 `id`(string)，一个是当组件树中的组件“提交”更新的时候被React调用的回调函数 `onRender`(function)。

例如，为了分析 `Navigation` 组件和它的子代：

```
render(
  <App>
    <Profiler id="Navigation" onRender={callback}>      <Navigation {...props} />
    </Profiler>
    <Main {...props} />
  </App>
);
```

多个 `Profiler` 组件能测量应用中的不同部分：

```
render(
  <App>
    <Profiler id="Navigation" onRender={callback}>      <Navigation {...props} />
    </Profiler>
    <Profiler id="Main" onRender={callback}>      <Main {...props} />
    </Profiler>
  </App>
);
```

嵌套使用 `Profiler` 组件来测量相同一个子树下的不同组件：

```
render(
  <App>
    <Profiler id="Panel" onRender={callback}>      <Panel {...props}>
        <Profiler id="Content" onRender={callback}>          <Content {...props} />
        </Profiler>
        <Profiler id="PreviewPane" onRender={callback}>          <PreviewPane {...props} />
        </Profiler>
      </Panel>
    </Profiler>
  </App>
);
```

> 注意
> 
> 尽管 `Profiler` 是一个轻量级组件，我们依然应该在需要时才去使用它。对一个应用来说，每添加一些都会给 CPU 和内存带来一些负担。
### `onRender` 回调

`Profiler` 需要一个 `onRender` 函数作为参数。 React 会在 profile 包含的组件树中任何组件 “提交” 一个更新的时候调用这个函数。 它的参数描述了渲染了什么和花费了多久。

```
function onRenderCallback(
  id, // 发生提交的 Profiler 树的 “id”
  phase, // "mount" （如果组件树刚加载） 或者 "update" （如果它重渲染了）之一
  actualDuration, // 本次更新 committed 花费的渲染时间
  baseDuration, // 估计不使用 memoization 的情况下渲染整颗子树需要的时间
  startTime, // 本次更新中 React 开始渲染的时间
  commitTime, // 本次更新中 React committed 的时间
  interactions // 属于本次更新的 interactions 的集合
) {
  // 合计或记录渲染时间。。。
}
```

让我们来仔细研究一下各个 prop:

-   **`id: string`** - 发生提交的 `Profiler` 树的 `id`。 如果有多个 profiler，它能用来分辨树的哪一部分发生了“提交”。
-   **`phase: "mount" | "update"`** - 判断是组件树的第一次装载引起的重渲染，还是由 props、state 或是 hooks 改变引起的重渲染。
-   **`actualDuration: number`** - 本次更新在渲染 `Profiler` 和它的子代上花费的时间。 这个数值表明使用 memoization 之后能表现得多好。（例如 [`React.memo`](https://zh-hans.reactjs.org/docs/react-api.html#reactmemo)，[`useMemo`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usememo)，[`shouldComponentUpdate`](https://zh-hans.reactjs.org/docs/hooks-faq.html#how-do-i-implement-shouldcomponentupdate)）。 理想情况下，由于子代只会因特定的 prop 改变而重渲染，因此这个值应该在第一次装载之后显著下降。
-   **`baseDuration: number`** - 在 `Profiler` 树中最近一次每一个组件 `render` 的持续时间。 这个值估计了最差的渲染时间。（例如当它是第一次加载或者组件树没有使用 memoization）。
-   **`startTime: number`** - 本次更新中 React 开始渲染的时间戳。
-   **`commitTime: number`** - 本次更新中 React commit 阶段结束的时间戳。 在一次 commit 中这个值在所有的 profiler 之间是共享的，可以将它们按需分组。
-   **`interactions: Set`** - 当更新被制定时，[“interactions”](https://fb.me/react-interaction-tracing) 的集合会被追踪。（例如当 `render` 或者 `setState` 被调用时）。

> 注意
> 
> Interactions 能用来识别更新是由什么引起的，尽管这个追踪更新的 API 依然是实验性质的。
> 
> 从 [fb.me/react-interaction-tracing](https://fb.me/react-interaction-tracing) 了解更多


## 不使用 ES6

通常我们会用 JavaScript 的 `class` 关键字来定义 React 组件：

```
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

如果你还未使用过 ES6，你可以使用 `create-react-class` 模块：

```
var createReactClass = require('create-react-class');
var Greeting = createReactClass({
  render: function() {
    return <h1>Hello, {this.props.name}</h1>;
  }
});
```

ES6 中的 class 与 `createReactClass()` 方法十分相似，但有以下几个区别值得注意。

### 声明默认属性

无论是函数组件还是 class 组件，都拥有 `defaultProps` 属性：

```
class Greeting extends React.Component {
  // ...
}

Greeting.defaultProps = {
  name: 'Mary'
};
```

如果使用 `createReactClass()` 方法创建组件，那就需要在组件中定义 `getDefaultProps()` 函数：

```
var Greeting = createReactClass({
  getDefaultProps: function() {
    return {
      name: 'Mary'
    };
  },

  // ...

});
```

### 初始化 State

如果使用 ES6 的 class 关键字创建组件，你可以通过给 `this.state` 赋值的方式来定义组件的初始 state：

```
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: props.initialCount};
  }
  // ...
}
```

如果使用 `createReactClass()` 方法创建组件，你需要提供一个单独的 `getInitialState` 方法，让其返回初始 state：

```
var Counter = createReactClass({
  getInitialState: function() {
    return {count: this.props.initialCount};
  },
  // ...
});
```

### 自动绑定

对于使用 ES6 的 class 关键字创建的 React 组件，组件中的方法遵循与常规 ES6 class 相同的语法规则。这意味着这些方法不会自动绑定 `this` 到这个组件实例。 你需要在 constructor 中显式地调用 `.bind(this)`：

```
class SayHello extends React.Component {
  constructor(props) {
    super(props);
    this.state = {message: 'Hello!'};
    // 这一行很重要！
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    alert(this.state.message);
  }

  render() {
    // 由于 `this.handleClick` 已经绑定至实例，因此我们才可以用它来处理点击事件
    return (
      <button onClick={this.handleClick}>
        Say hello
      </button>
    );
  }
}
```

如果使用 `createReactClass()` 方法创建组件，组件中的方法会自动绑定至实例，所以不需要像上面那样做：

```
var SayHello = createReactClass({
  getInitialState: function() {
    return {message: 'Hello!'};
  },

  handleClick: function() {
    alert(this.state.message);
  },

  render: function() {
    return (
      <button onClick={this.handleClick}>
        Say hello
      </button>
    );
  }
});
```

这就意味着，如果使用 ES6 class 关键字创建组件，在处理事件回调时就要多写一部分代码。但对于大型项目来说，这样做可以提升运行效率。

如果你觉得上述写法很繁琐，那么可以尝试使用**目前还处于试验性阶段**的 Babel 插件 [Class Properties](https://babeljs.io/docs/plugins/transform-class-properties/)。

```
class SayHello extends React.Component {
  constructor(props) {
    super(props);
    this.state = {message: 'Hello!'};
  }
  // 警告：这种语法还处于试验性阶段！
  // 在这里使用箭头函数就可以把方法绑定给实例：
  handleClick = () => {
    alert(this.state.message);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Say hello
      </button>
    );
  }
}
```

请注意，上面这种语法**目前还处于试验性阶段**，这意味着语法随时都可能改变，也存在最终不被列入框架标准的可能。

为了保险起见，以下三种做法都是可以的：

-   在 constructor 中绑定方法。
-   使用箭头函数，比如：`onClick={(e) => this.handleClick(e)}`。
-   继续使用 `createReactClass`。

### Mixins

> **注意：**
> 
> ES6 本身是不包含任何 mixin 支持。因此，当你在 React 中使用 ES6 class 时，将不支持 mixins 。
> 
> **我们也发现了很多使用 mixins 然后出现了问题的代码库。[并且不建议在新代码中使用它们](https://zh-hans.reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)。**
> 
> 以下内容仅作为参考。

如果完全不同的组件有相似的功能，这就会产生[“横切关注点（cross-cutting concerns）“问题](https://en.wikipedia.org/wiki/Cross-cutting_concern)。针对这个问题，在使用 createReactClass 创建 React 组件的时候，引入 `mixins` 功能会是一个很好的解决方案。

比较常见的用法是，组件每隔一段时间更新一次。使用 `setInterval()` 可以很容易实现这个功能，但需要注意的是，当你不再需要它时，你应该清除它以节省内存。React 提供了[生命周期方法](https://zh-hans.reactjs.org/docs/react-component.html#the-component-lifecycle)，这样你就可以知道一个组件何时被创建或被销毁了。让我们创建一个简单的 mixin，它使用这些方法提供一个简单的 `setInterval()` 函数，它会在组件被销毁时被自动清理。

```
var SetIntervalMixin = {
  componentWillMount: function() {
    this.intervals = [];
  },
  setInterval: function() {
    this.intervals.push(setInterval.apply(null, arguments));
  },
  componentWillUnmount: function() {
    this.intervals.forEach(clearInterval);
  }
};

var createReactClass = require('create-react-class');

var TickTock = createReactClass({
  mixins: [SetIntervalMixin], // 使用 mixin
  getInitialState: function() {
    return {seconds: 0};
  },
  componentDidMount: function() {
    this.setInterval(this.tick, 1000); // 调用 mixin 上的方法
  },
  tick: function() {
    this.setState({seconds: this.state.seconds + 1});
  },
  render: function() {
    return (
      <p>
        React has been running for {this.state.seconds} seconds.
      </p>
    );
  }
});

ReactDOM.render(
  <TickTock />,
  document.getElementById('example')
);
```

如果组件拥有多个 mixins，且这些 mixins 中定义了相同的生命周期方法（例如，当组件被销毁时，几个 mixins 都想要进行一些清理工作），那么这些生命周期方法都会被调用的。使用 mixins 时，mixins 会先按照定义时的顺序执行，最后调用组件上对应的方法。

## 不使用 JSX 的 React
- React 并不强制要求使用 JSX。当你不想在构建环境中配置有关 JSX 编译时，不在 React 中使用 JSX 会更加方便。
- 每个 JSX 元素只是调用 `React.createElement(component, props, ...children)` 的语法糖。因此，使用 JSX 可以完成的任何事情都可以通过纯 JavaScript 完成。
例如，用 JSX 编写的代码：

```
class Hello extends React.Component {
  render() {
    return <div>Hello {this.props.toWhat}</div>;
  }
}

ReactDOM.render(
  <Hello toWhat="World" />,
  document.getElementById('root')
);
```

可以编写为不使用 JSX 的代码：

```
class Hello extends React.Component {
  render() {
    return React.createElement('div', null, `Hello ${this.props.toWhat}`);
  }
}

ReactDOM.render(
  React.createElement(Hello, {toWhat: 'World'}, null),
  document.getElementById('root')
);
```

如果你想了解更多 JSX 转换为 JavaScript 的示例，可以尝试使用 [在线 Babel 编译器](https://babeljs.io/repl/#?presets=react&code_lz=GYVwdgxgLglg9mABACwKYBt1wBQEpEDeAUIogE6pQhlIA8AJjAG4B8AEhlogO5xnr0AhLQD0jVgG4iAXyJA)。

组件可以是字符串，也可以是 `React.Component` 的子类，它还能是一个普通的函数。

如果你不想每次都键入 `React.createElement`，通常的做法是创建快捷方式：

```
const e = React.createElement;

ReactDOM.render(
  e('div', null, 'Hello World'),
  document.getElementById('root')
);
```

如果你使用了 `React.createElement` 的快捷方式，那么在没有 JSX 的情况下使用 React 几乎一样方便。

或者，你也可以参考社区项目，如：[`react-hyperscript`](https://github.com/mlmorg/react-hyperscript) 和 [`hyperscript-helpers`](https://github.com/ohanhi/hyperscript-helpers)，它们提供了更简洁的语法。


## 静态类型检查
- 像 [Flow](https://flow.org/) 和 [TypeScript](https://www.typescriptlang.org/) 等这些静态类型检查器，可以在运行前识别某些类型的问题。他们还可以通过增加自动补全等功能来改善开发者的工作流程。出于这个原因，我们建议在大型代码库中使用 Flow 或 TypeScript 来代替 `PropTypes`。

### Flow

[Flow](https://flow.org/) 是一个针对 JavaScript 代码的静态类型检测器。Flow 由 Facebook 开发，经常与 React 一起使用。Flow 通过特殊的类型语法为变量，函数，以及 React 组件提供注解，帮助你尽早地发现错误。你可以阅读 [introduction to Flow](https://flow.org/en/docs/getting-started/) 来了解它的基础知识。

完成以下步骤，便可开始使用 Flow：

-   将 Flow 添加到你的项目依赖中。
-   确保编译后的代码中去除了 Flow 语法。
-   添加类型注解并且运行 Flow 来检查它们。

下面我们会详细解释这些步骤。

#### 在项目中添加 Flow

首先，在终端中进入到项目根目录下。然后你需要执行以下命令：

如果你使用 [Yarn](https://yarnpkg.com/)，执行：

```
yarn add --dev flow-bin
```

如果你使用 [npm](https://www.npmjs.com/)，执行：

```
npm install --save-dev flow-bin
```

这个命令将在你的项目中安装最新版的 Flow。

接下来，将 `flow` 添加到项目 `package.json` 的 `"scripts"` 部分，以便能够从终端命令行中使用它：

```
{
  // ...
  "scripts": {
    "flow": "flow",    // ...
  },
  // ...
}
```

最后，执行以下命令之一：

如果你使用 [Yarn](https://yarnpkg.com/)，执行：

```
yarn run flow init
```

如果你使用 [npm](https://www.npmjs.com/)，执行：

```
npm run flow init
```

这条命令将生成你需要提交的 Flow 配置文件。

#### 从编译后的代码中去除 Flow 语法

Flow 通过这种类型注释的特殊语法扩展了 JavaScript 语言。但是，浏览器不能够解析这种语法，所以我们需要确保它不会被编译到在浏览器执行的 JavaScript bundle 中。

具体方法取决于你使用的 JavaScript 编译工具。

#### Create React App

如果你的项目使用的是 [Create React App](https://github.com/facebookincubator/create-react-app)，那么 Flow 注解默认会被去除，所以在这一步你不需要做任何事情。

#### Babel

> 注意：
> 
> 这些说明_不适用_于使用 Create React App 的用户。虽然 Create React App 底层也使用了 Babel，但它已经配置了去除 Flow。如果你_没有_使用 Create React App，请执行此步骤。

如果你的项目手动配置了 Babel，你需要为 Flow 安装一个特殊的 preset。

如果你使用 Yarn，执行：

```
yarn add --dev @babel/preset-flow
```

如果你使用 npm，执行：

```
npm install --save-dev @babel/preset-flow
```

接下来将 `flow` preset 添加到你的 [Babel 配置](https://babeljs.io/docs/usage/babelrc/) 配置中。例如，如果你通过 `.babelrc` 文件配置 Babel，它可能会如下所示：

```
{
  "presets": [
    "@babel/preset-flow",    "react"
  ]
}
```

这将让你可以在代码中使用 Flow 语法。

> 注意：
> 
> Flow 不需要 react preset，但他们经常一起使用。Flow 内置了 JSX 的语法识别。

#### 其他构建工具设置

如果没有使用 Create React App 或 Babel 来构建项目，可以通过 [flow-remove-types](https://github.com/flowtype/flow-remove-types) 去除类型注解。

#### 运行 Flow

如果你按照上面的说明操作，你应该能运行 Flow 了。

```
yarn flow
```

如果你使用 npm，执行：

```
npm run flow
```

你应该会看到如下消息：

```
No errors!
✨  Done in 0.17s.
```

#### 添加 Flow 类型注释

默认情况下，Flow 仅检查包含此注释的文件：

```
// @flow
```

通常，它位于文件的顶部。试着将其添加到项目的某些文件中，然后运行 `yarn flow` 或 `npm run flow` 来查看 Flow 是否已经发现了一些问题。

还可以通过[这个选项](https://flow.org/en/docs/config/options/#toc-all-boolean)开启_所有_文件（包括没有注解的文件）的强制检查。通过 Flow 来检查全部文件对于现有的项目来说，可能导致大量修改，但对于希望完全集成 Flow 的新项目来说开启这个选项比较合理。

现在一切就绪！我们建议你查看以下资源来了解有关 Flow 的更多信息：

-   [Flow 文档：类型注解](https://flow.org/en/docs/types/)
-   [Flow 文档：编辑器](https://flow.org/en/docs/editors/)
-   [Flow 文档：React](https://flow.org/en/docs/react/)
-   [在 Flow 中进行 lint](https://medium.com/flow-type/linting-in-flow-7709d7a7e969)

### TypeScript

[TypeScript](https://www.typescriptlang.org/) 是一种由微软开发的编程语言。它是 JavaScript 的一个类型超集，包含独立的编译器。作为一种类型语言，TypeScript 可以在构建时发现 bug 和错误，这样程序运行时就可以避免此类错误。您可以通过[此文档](https://github.com/Microsoft/TypeScript-React-Starter#typescript-react-starter) 了解更多有关在 React 中使用 TypeScript 的知识。

完成以下步骤，便可开始使用 TypeScript：

-   将 TypeScript 添加到你的项目依赖中。
-   配置 TypeScript 编译选项
-   使用正确的文件扩展名
-   为你使用的库添加定义

下面让我们详细地介绍一下这些步骤：

#### 在 Create React App 中使用 TypeScript

Create React App 内置了对 TypeScript 的支持。

需要创建一个使用 TypeScript 的**新项目**，在终端运行：

```
npx create-react-app my-app --template typescript
```

如需将 TypeScript 添加到**现有的 Create React App 项目**中，[请参考此文档](https://facebook.github.io/create-react-app/docs/adding-typescript).

> 注意：
> 
> 如果你使用的是 Create React App，可以跳过本节的其余部分。其余部分讲述了不使用 Create React App 脚手架，手动配置项目的用户。

#### 添加 TypeScript 到现有项目中

这一切都始于在终端中执行的一个命令。

如果你使用 [Yarn](https://yarnpkg.com/)，执行：

```
yarn add --dev typescript
```

如果你使用 [npm](https://www.npmjs.com/)，执行：

```
npm install --save-dev typescript
```

恭喜！你已将最新版本的 TypeScript 安装到项目中。安装 TypeScript 后我们就可以使用 `tsc` 命令。在配置编译器之前，让我们将 `tsc` 添加到 `package.json` 中的 “scripts” 部分：

```
{
  // ...
  "scripts": {
    "build": "tsc",    // ...
  },
  // ...
}
```

#### 配置 TypeScript 编译器

没有配置项，编译器提供不了任何帮助。在 TypeScript 里，这些配置项都在一个名为 `tsconfig.json` 的特殊文件中定义。可以通过执行以下命令生成该文件：

如果你使用 [Yarn](https://yarnpkg.com/)，执行：

```
yarn run tsc --init
```

如果你使用 [npm](https://www.npmjs.com/)，执行：

```
npx tsc --init
```

`tsconfig.json` 文件中，有许多配置项用于配置编译器。查看所有配置项的的详细说明，[请参考此文档](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)。

我们来看一下 `rootDir` 和 `outDir` 这两个配置项。编译器将从项目中找到 TypeScript 文件并编译成相对应 JavaScript 文件。但我们不想混淆源文件和编译后的输出文件。

为了解决该问题，我们将执行以下两个步骤：

-   首先，让我们重新整理下项目目录，把所有的源代码放入 `src` 目录中。

```
├── package.json
├── src
│   └── index.ts
└── tsconfig.json
```

-   其次，我们将通过配置项告诉编译器源码和输出的位置。

```
// tsconfig.json

{
  "compilerOptions": {
    // ...
    "rootDir": "src",    "outDir": "build"    // ...
  },
}
```

很好！现在，当我们运行构建脚本时，编译器会将生成的 javascript 输出到 `build` 文件夹。 [TypeScript React Starter](https://github.com/Microsoft/TypeScript-React-Starter/blob/master/tsconfig.json) 提供了一套默认的 `tsconfig.json` 帮助你快速上手。

通常情况下，你不希望将编译后生成的 JavaScript 文件保留在版本控制内。因此，应该把构建文件夹添加到 `.gitignore` 中。

#### 文件扩展名

在 React 中，你的组件文件大多数使用 `.js` 作为扩展名。在 TypeScript 中，提供两种文件扩展名：

`.ts` 是默认的文件扩展名，而 `.tsx` 是一个用于包含 `JSX` 代码的特殊扩展名。

#### 运行 TypeScript

如果你按照上面的说明操作，现在应该能运行 TypeScript 了。

```
yarn build
```

如果你使用 npm，执行：

```
npm run build
```

如果你没有看到输出信息，这意味着它编译成功了。

#### 类型定义

为了能够显示来自其他包的错误和提示，编译器依赖于声明文件。声明文件提供有关库的所有类型信息。这样，我们的项目就可以用上像 npm 这样的平台提供的三方 JavaScript 库。

获取一个库的声明文件有两种方式：

**Bundled** - 该库包含了自己的声明文件。这样很好，因为我们只需要安装这个库，就可以立即使用它了。要知道一个库是否包含类型，看库中是否有 `index.d.ts` 文件。有些库会在 `package.json` 文件的 `typings` 或 `types` 属性中指定类型文件。

**[DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)** - DefinitelyTyped 是一个庞大的声明仓库，为没有声明文件的 JavaScript 库提供类型定义。这些类型定义通过众包的方式完成，并由微软和开源贡献者一起管理。例如，React 库并没有自己的声明文件。但我们可以从 DefinitelyTyped 获取它的声明文件。只要执行以下命令。

```
# yarn
yarn add --dev @types/react

# npm
npm i --save-dev @types/react
```

**局部声明** 有时，你要使用的包里没有声明文件，在 DefinitelyTyped 上也没有。在这种情况下，我们可以创建一个本地的定义文件。因此，在项目的根目录中创建一个 `declarations.d.ts` 文件。一个简单的声明可能是这样的：

```
declare module 'querystring' {
  export function stringify(val: object): string
  export function parse(val: string): object
}
```

你现在已做好编码准备了！我们建议你查看以下资源来了解有关 TypeScript 的更多知识：

-   [TypeScript 文档：常用类型](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
-   [TypeScript 文档：JavaScript 迁移](https://www.typescriptlang.org/docs/handbook/migrating-from-javascript.html)
-   [TypeScript 文档：React 与 Webpack](https://www.typescriptlang.org/docs/handbook/react-&-webpack.html)

### ReScript

[ReScript](https://rescript-lang.org/) 是一门类型语言，可编译为 JavaScript。它的部分核心功能是为了保证 100% 的类型覆盖，完美支持 JSX 和[独有的 React 绑定](https://rescript-lang.org/docs/react/latest/introduction)，以允许开发者无缝集成到现有的 JS/TS 的 React 代码库中。

你可以在[此处](https://rescript-lang.org/docs/manual/latest/installation#integrate-into-an-existing-js-project)。了解更多关于如何将 ReScript 集成到现有的 JS / React 代码库的信息。

### Kotlin

[Kotlin](https://kotlinlang.org/) 是由 JetBrains 开发的一门静态类型语言。其目标平台包括 JVM、Android、LLVM 和 [JavaScript](https://kotlinlang.org/docs/reference/js-overview.html)。

JetBrains 专门为 React 社区开发和维护了几个工具：[React bindings](https://github.com/JetBrains/kotlin-wrappers) 以及 [Create React Kotlin App](https://github.com/JetBrains/create-react-kotlin-app)。后者可以通过 Kotlin 快速编写 React 应用程序，并且不需要构建配置。

### 其他语言

注意，还有其他静态类型语言可以编译成 JavaScript，也与 React 兼容。例如，和 [elmish-react](https://elmish.github.io/react) 一起使用的 [F#/Fable](https://fable.io/)。查看他们各自的网站以获取更多信息，并欢迎添加更多和与 React 结合的静态类型语言到这个页面！

## 严格模式
- `StrictMode` 是一个用来突出显示应用程序中潜在问题的工具。与 `Fragment` 一样，`StrictMode` 不会渲染任何可见的 UI。它为其后代元素触发额外的检查和警告。
- 严格模式检查仅在开发模式下运行；_它们不会影响生产构建_。

你可以为应用程序的任何部分启用严格模式。例如：

```
import React from 'react';

function ExampleApplication() {
  return (
    <div>
      <Header />
      <React.StrictMode>        <div>
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>      <Footer />
    </div>
  );
}
```

在上述的示例中，_不_会对 `Header` 和 `Footer` 组件运行严格模式检查。但是，`ComponentOne` 和 `ComponentTwo` 以及它们的所有后代元素都将进行检查。

`StrictMode` 目前有助于：

-   [识别不安全的生命周期](https://zh-hans.reactjs.org/docs/strict-mode.html#identifying-unsafe-lifecycles)
-   [关于使用过时字符串 ref API 的警告](https://zh-hans.reactjs.org/docs/strict-mode.html#warning-about-legacy-string-ref-api-usage)
-   [关于使用废弃的 findDOMNode 方法的警告](https://zh-hans.reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)
-   [检测意外的副作用](https://zh-hans.reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects)
-   [检测过时的 context API](https://zh-hans.reactjs.org/docs/strict-mode.html#detecting-legacy-context-api)

未来的 React 版本将添加更多额外功能。

### 识别不安全的生命周期

正如[这篇博文](https://zh-hans.reactjs.org/blog/2018/03/27/update-on-async-rendering.html)所述，某些过时的生命周期方法在异步 React 应用程序中使用是不安全的。但是，如果你的应用程序使用了第三方库，很难确保它们不使用这些生命周期方法。幸运的是，严格模式可以帮助解决这个问题！

当启用严格模式时，React 会列出使用了不安全生命周期方法的所有 class 组件，并打印一条包含这些组件信息的警告消息，如下所示：

[![strict mode unsafe lifecycles warning](https://zh-hans.reactjs.org/static/e4fdbff774b356881123e69ad88eda88/1e088/strict-mode-unsafe-lifecycles-warning.png)](https://zh-hans.reactjs.org/static/e4fdbff774b356881123e69ad88eda88/1628f/strict-mode-unsafe-lifecycles-warning.png)

_此时_解决项目中严格模式所识别出来的问题，会使得在未来的 React 版本中使用 concurrent 渲染变得更容易。

### 关于使用过时字符串 ref API 的警告

以前，React 提供了两种方法管理 refs 的方式：已过时的字符串 ref API 的形式及回调函数 API 的形式。尽管字符串 ref API 在两者中使用更方便，但是它有[一些缺点](https://github.com/facebook/react/issues/1373)，因此官方推荐采用[回调的方式](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html#legacy-api-string-refs)。

React 16.3 新增了第三种选择，它提供了使用字符串 ref 的便利性，并且不存在任何缺点：

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

由于对象 ref 主要是为了替换字符串 ref 而添加的，因此严格模式现在会警告使用字符串 ref。

> **注意：**
> 
> 除了新增加的 `createRef` API，回调 ref 依旧适用。
> 
> 你无需替换组件中的回调 ref。它们更灵活，因此仍将作为高级功能保留。

[在此处了解有关 `createRef` API 的更多信息](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html)

### 关于使用废弃的 findDOMNode 方法的警告

React 支持用 `findDOMNode` 来在给定 class 实例的情况下在树中搜索 DOM 节点。通常你不需要这样做，因为你可以[将 ref 直接绑定到 DOM 节点](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html#creating-refs)。

`findDOMNode` 也可用于 class 组件，但它违反了抽象原则，它使得父组件需要单独渲染子组件。它会产生重构危险，你不能更改组件的实现细节，因为父组件可能正在访问它的 DOM 节点。`findDOMNode` 只返回第一个子节点，但是使用 Fragments，组件可以渲染多个 DOM 节点。`findDOMNode` 是一个只读一次的 API。调用该方法只会返回第一次查询的结果。如果子组件渲染了不同的节点，则无法跟踪此更改。因此，`findDOMNode` 仅在组件返回单个且不可变的 DOM 节点时才有效。

你可以通过将 ref 传递给自定义组件并使用 [ref 转发](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)来将其传递给 DOM 节点。

你也可以在组件中创建一个 DOM 节点的 wrapper，并将 ref 直接绑定到它。

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.wrapper = React.createRef();  }
  render() {
    return <div ref={this.wrapper}>{this.props.children}</div>;  }
}
```

> 注意：
> 
> 在 CSS 中，如果你不希望节点成为布局的一部分，则可以使用 [`display: contents`](https://developer.mozilla.org/en-US/docs/Web/CSS/display#display_contents) 属性。

### 检测意外的副作用

从概念上讲，React 分两个阶段工作：

-   **渲染** 阶段会确定需要进行哪些更改，比如 DOM。在此阶段，React 调用 `render`，然后将结果与上次渲染的结果进行比较。
-   **提交** 阶段发生在当 React 应用变化时。（对于 React DOM 来说，会发生在 React 插入，更新及删除 DOM 节点的时候。）在此阶段，React 还会调用 `componentDidMount` 和 `componentDidUpdate` 之类的生命周期方法。

提交阶段通常会很快，但渲染过程可能很慢。因此，即将推出的 concurrent 模式 (默认情况下未启用) 将渲染工作分解为多个部分，对任务进行暂停和恢复操作以避免阻塞浏览器。这意味着 React 可以在提交之前多次调用渲染阶段生命周期的方法，或者在不提交的情况下调用它们（由于出现错误或更高优先级的任务使其中断）。

渲染阶段的生命周期包括以下 class 组件方法：

-   `constructor`
-   `componentWillMount` (or `UNSAFE_componentWillMount`)
-   `componentWillReceiveProps` (or `UNSAFE_componentWillReceiveProps`)
-   `componentWillUpdate` (or `UNSAFE_componentWillUpdate`)
-   `getDerivedStateFromProps`
-   `shouldComponentUpdate`
-   `render`
-   `setState` 更新函数（第一个参数）

因为上述方法可能会被多次调用，所以不要在它们内部编写副作用相关的代码，这点非常重要。忽略此规则可能会导致各种问题的产生，包括内存泄漏和或出现无效的应用程序状态。不幸的是，这些问题很难被发现，因为它们通常具有[非确定性](https://en.wikipedia.org/wiki/Deterministic_algorithm)。

严格模式不能自动检测到你的副作用，但它可以帮助你发现它们，使它们更具确定性。通过故意重复调用以下函数来实现的该操作：

-   class 组件的 `constructor`，`render` 以及 `shouldComponentUpdate` 方法
-   class 组件的生命周期方法 `getDerivedStateFromProps`
-   函数组件体
-   状态更新函数 (即 `setState` 的第一个参数）
-   函数组件通过使用 `useState`，`useMemo` 或者 `useReducer`

> 注意：
> 
> 这仅适用于开发模式。_生产模式下生命周期不会被调用两次。_

例如，请考虑以下代码：

```
class TopLevelRoute extends React.Component {
  constructor(props) {
    super(props);

    SharedApplicationState.recordEvent('ExampleComponent');
  }
}
```

这段代码看起来似乎没有问题。但是如果 `SharedApplicationState.recordEvent` 不是[幂等](https://en.wikipedia.org/wiki/Idempotence#Computer_science_meaning)的情况下，多次实例化此组件可能会导致应用程序状态无效。这种小 bug 可能在开发过程中可能不会表现出来，或者说表现出来但并不明显，并因此被忽视。

严格模式采用故意重复调用方法（如组件的构造函数）的方式，使得这种 bug 更容易被发现。

> 注意：
> 
> 从 React 17 开始，React 会自动修改 console 的方法，例如 `console.log()`，以在对生命周期函数的第二次调用中静默日志。然而，在某些[可以使用替代解决方案](https://github.com/facebook/react/issues/20090#issuecomment-715927125)的情况下，这可能会导致一些不期望的行为的发生。

### 检测过时的 context API

过时的 context API 容易出错，将在未来的主要版本中删除。在所有 16.x 版本中它仍然有效，但在严格模式下，将显示以下警告：

[![warn legacy context in strict mode](https://zh-hans.reactjs.org/static/fca5c5e1fb2ef2e2d59afb100b432c12/1e088/warn-legacy-context-in-strict-mode.png)](https://zh-hans.reactjs.org/static/fca5c5e1fb2ef2e2d59afb100b432c12/51800/warn-legacy-context-in-strict-mode.png)

阅读[新的 context API 文档](https://zh-hans.reactjs.org/docs/context.html)以帮助你迁移到新版本。