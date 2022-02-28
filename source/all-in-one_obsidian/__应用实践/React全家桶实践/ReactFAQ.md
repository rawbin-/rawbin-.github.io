# AJAX and APIs

### [](https://zh-hans.reactjs.org/docs/faq-ajax.html#how-can-i-make-an-ajax-call)如何在 React 中发起 AJAX 请求？

在 React 开发中，你能使用任何你喜欢的 AJAX 库，比如社区比较流行的 [Axios](https://github.com/axios/axios)，[jQuery AJAX](https://api.jquery.com/jQuery.ajax/)，或者是浏览器内置的 [window.fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)。

### [](https://zh-hans.reactjs.org/docs/faq-ajax.html#where-in-the-component-lifecycle-should-i-make-an-ajax-call)应该在 React 组件的哪个生命周期函数中发起 AJAX 请求？

我们推荐你在 [`componentDidMount`](https://zh-hans.reactjs.org/docs/react-component.html#mounting) 这个生命周期函数中发起 AJAX 请求。这样做你可以拿到 AJAX 请求返回的数据并通过 `setState` 来更新组件。

### [](https://zh-hans.reactjs.org/docs/faq-ajax.html#example-using-ajax-results-to-set-local-state)示例：使用 AJAX 请求结果去改变组件内部 state

下面这个组件演示了如何在 `componentDidMount` 中发起 AJAX 请求去更新组件的 state 。

示例 API 返回如下的 JSON 对象：

```
{
  "items": [
    { "id": 1, "name": "Apples",  "price": "$2" },
    { "id": 2, "name": "Peaches", "price": "$5" }
  ] 
}
```

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      error: null,
      isLoaded: false,
      items: []
    };
  }

  componentDidMount() {
    fetch("https://api.example.com/items")
      .then(res => res.json())
      .then(
        (result) => {
          this.setState({
            isLoaded: true,
            items: result.items
          });
        },
        // 注意：需要在此处处理错误
        // 而不是使用 catch() 去捕获错误
        // 因为使用 catch 去捕获异常会掩盖掉组件本身可能产生的 bug
        (error) => {
          this.setState({
            isLoaded: true,
            error
          });
        }
      )
  }

  render() {
    const { error, isLoaded, items } = this.state;
    if (error) {
      return <div>Error: {error.message}</div>;
    } else if (!isLoaded) {
      return <div>Loading...</div>;
    } else {
      return (
        <ul>
          {items.map(item => (
            <li key={item.id}>
              {item.name} {item.price}
            </li>
          ))}
        </ul>
      );
    }
  }
}
```

这里与 [Hook](https://zh-hans.reactjs.org/docs/hooks-intro.html) 的实现相同：

```
function MyComponent() {
  const [error, setError] = useState(null);
  const [isLoaded, setIsLoaded] = useState(false);
  const [items, setItems] = useState([]);

  // 注意：空的依赖数组 []
  // 表示这个 useEffect
  // 与 componentDidMount() 类似，只运行一次
  useEffect(() => {
    fetch("https://api.example.com/items")
      .then(res => res.json())
      .then(
        (result) => {
          setIsLoaded(true);
          setItems(result);
        },
        // 注意：需要在此处处理错误
        // 而不是使用 catch() 去捕获错误
        // 因为使用 catch 去捕获异常会掩盖掉组件本身可能产生的 bug
        (error) => {
          setIsLoaded(true);
          setError(error);
        }
      )
  }, [])

  if (error) {
    return <div>Error: {error.message}</div>;
  } else if (!isLoaded) {
    return <div>Loading...</div>;
  } else {
    return (
      <ul>
        {items.map(item => (
          <li key={item.id}>
            {item.name} {item.price}
          </li>
        ))}
      </ul>
    );
  }
}
```


# Babel，JSX 及构建过程

### [](https://zh-hans.reactjs.org/docs/faq-build.html#do-i-need-to-use-jsx-with-react)必须在 React 中使用 JSX 吗？

不是必须的！请查阅 [“不使用 JSX”](https://zh-hans.reactjs.org/docs/react-without-jsx.html) 以了解更多信息。

### [](https://zh-hans.reactjs.org/docs/faq-build.html#do-i-need-to-use-es6--with-react)必须在 React 中使用 ES6 (+) 吗？

不是必须的！请查阅 [“不使用 ES6”](https://zh-hans.reactjs.org/docs/react-without-es6.html) 以了解更多信息。

### [](https://zh-hans.reactjs.org/docs/faq-build.html#how-can-i-write-comments-in-jsx)怎样才能在 JSX 中编写注释？

```
<div>
  {/* 注释写在这里 */}
  Hello, {name}!
</div>
```

```
<div>
  {/* 多行注释 
  也同样有效。 */}
  Hello, {name}! 
</div>
```

# 传递函数给组件

### [](https://zh-hans.reactjs.org/docs/faq-functions.html#how-do-i-pass-an-event-handler-like-onclick-to-a-component)如何将事件处理器（比如 onClick）传递给组件？

可以将事件处理器和其他函数作为 props 传递给子组件：

```
<button onClick={this.handleClick}>
```

如果需要在事件处理器中访问父组件，还需要为该函数绑定组件实例（参见下文）。

### [](https://zh-hans.reactjs.org/docs/faq-functions.html#how-do-i-bind-a-function-to-a-component-instance)如何为函数绑定组件实例？

有以下几种方式可以确保函数可以访问组件属性，比如 `this.props` 和 `this.state`，这取决于使用的语法和构建步骤。

#### [](https://zh-hans.reactjs.org/docs/faq-functions.html#bind-in-constructor-es2015)在构造函数中绑定（ES2015）

```
class Foo extends Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick() {
    console.log('Click happened');
  }
  render() {
    return <button onClick={this.handleClick}>Click Me</button>;
  }
}
```

#### [](https://zh-hans.reactjs.org/docs/faq-functions.html#class-properties-stage-3-proposal)class 属性（第三阶段提案）

```
class Foo extends Component {
  // Note: this syntax is experimental and not standardized yet.
  handleClick = () => {
    console.log('Click happened');
  }
  render() {
    return <button onClick={this.handleClick}>Click Me</button>;
  }
}
```

#### [](https://zh-hans.reactjs.org/docs/faq-functions.html#bind-in-render)在 Render 中的绑定

```
class Foo extends Component {
  handleClick() {
    console.log('Click happened');
  }
  render() {
    return <button onClick={this.handleClick.bind(this)}>Click Me</button>;
  }
}
```

> **注意：**
> 
> 在 render 方法中使用 `Function.prototype.bind` 会在每次组件渲染时创建一个新的函数，可能会影响性能（参见下文）。

#### [](https://zh-hans.reactjs.org/docs/faq-functions.html#arrow-function-in-render)在 Render 中使用箭头函数

```
class Foo extends Component {
  handleClick() {
    console.log('Click happened');
  }
  render() {
    return <button onClick={() => this.handleClick()}>Click Me</button>;
  }
}
```

> **注意：**
> 
> 在 render 方法中使用箭头函数也会在每次组件渲染时创建一个新的函数，这会破坏基于恒等比较的性能优化。

### [](https://zh-hans.reactjs.org/docs/faq-functions.html#is-it-ok-to-use-arrow-functions-in-render-methods)可以在 render 方法中使用箭头函数吗？

一般来说是可以的，并且使用箭头函数是向回调函数传递参数的最简单的办法。

但是如果遇到了性能问题，一定要进行优化！

### [](https://zh-hans.reactjs.org/docs/faq-functions.html#why-is-binding-necessary-at-all)为什么绑定是必要的？

在JavaScript中，以下两种写法是**不**等价的：

```
obj.method();
```

```
var method = obj.method;
method();
```

bind 方法确保了第二种写法与第一种写法相同。

使用 React，通常只需要绑定_传递_给其他组件的方法。例如，`<button onClick={this.handleClick}>` 是在传递 `this.handleClick` ，所以需要绑定它。但是，没有必要绑定 `render` 方法或生命周期方法：我们并没有将它们传递给其他的组件。

[Yehuda Katz 的文章](https://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)详细解释了什么是绑定，以及函数在 JavaScript 中怎么起作用。

### [](https://zh-hans.reactjs.org/docs/faq-functions.html#why-is-my-function-being-called-every-time-the-component-renders)为什么我的函数每次组件渲染时都会被调用？

确保你在传递一个函数给组件时，没有_调用这个函数_：

```
render() {
  // Wrong: handleClick is called instead of passed as a reference!
  return <button onClick={this.handleClick()}>Click Me</button>
}
```

正确做法是，_传递函数本身_（不带括号）：

```
render() {
  // Correct: handleClick is passed as a reference!
  return <button onClick={this.handleClick}>Click Me</button>
}
```

### [](https://zh-hans.reactjs.org/docs/faq-functions.html#how-do-i-pass-a-parameter-to-an-event-handler-or-callback)如何传递参数给事件处理器或回调？

可以使用箭头函数包裹事件处理器，并传递参数:

```
<button onClick={() => this.handleClick(id)} />
```

以上代码和调用 `.bind` 是等价的：

```
<button onClick={this.handleClick.bind(this, id)} />
```

#### [](https://zh-hans.reactjs.org/docs/faq-functions.html#example-passing-params-using-arrow-functions)示例：通过箭头函数传递参数

```
const A = 65 // ASCII character code

class Alphabet extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      justClicked: null,
      letters: Array.from({length: 26}, (_, i) => String.fromCharCode(A + i))
    };
  }
  handleClick(letter) {
    this.setState({ justClicked: letter });
  }
  render() {
    return (
      <div>
        Just clicked: {this.state.justClicked}
        <ul>
          {this.state.letters.map(letter =>
            <li key={letter} onClick={() => this.handleClick(letter)}>
              {letter}
            </li>
          )}
        </ul>
      </div>
    )
  }
}
```

#### [](https://zh-hans.reactjs.org/docs/faq-functions.html#example-passing-params-using-data-attributes)示例：通过 data-attributes 传递参数

同样的，也可以使用 DOM API 来存储事件处理器需要的数据。如果需要优化大量元素或使用依赖于 `React.PureComponent` 相等性检查的渲染树，请考虑使用此方法。

```
const A = 65 // ASCII character code

class Alphabet extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
    this.state = {
      justClicked: null,
      letters: Array.from({length: 26}, (_, i) => String.fromCharCode(A + i))
    };
  }

  handleClick(e) {
    this.setState({
      justClicked: e.target.dataset.letter
    });
  }

  render() {
    return (
      <div>
        Just clicked: {this.state.justClicked}
        <ul>
          {this.state.letters.map(letter =>
            <li key={letter} data-letter={letter} onClick={this.handleClick}>
              {letter}
            </li>
          )}
        </ul>
      </div>
    )
  }
}
```

### [](https://zh-hans.reactjs.org/docs/faq-functions.html#how-can-i-prevent-a-function-from-being-called-too-quickly-or-too-many-times-in-a-row)怎样阻止函数被调用太快或者太多次？

如果你有一个 `onClick` 或者 `onScroll` 这样的事件处理器，想要阻止回调被触发的太快，那么可以限制执行回调的速度，可以通过以下几种方式做到这点：

-   **节流**：基于时间的频率来进行抽样更改 (例如 [`_.throttle`](https://lodash.com/docs#throttle))
-   **防抖**：一段时间的不活动之后发布更改 (例如 [`_.debounce`](https://lodash.com/docs#debounce))
-   **`requestAnimationFrame` 节流**：基于 requestAnimationFrame 的抽样更改 (例如 [`raf-schd`](https://github.com/alexreardon/raf-schd))

可以看这个比较 throttle 和 debounce 的[可视化页面](http://demo.nimius.net/debounce_throttle/)

> **注意：**
> 
> `_.debounce`、`_.throttle` 和 `raf-schd` 都提供了一个 `cancel` 方法来取消延迟回调。你需要在 `componentWillUnmount` 中调用该方法，或者对代码进行检查来保证在延迟函数有效期间内组件始终挂载。

#### [](https://zh-hans.reactjs.org/docs/faq-functions.html#throttle)节流

节流阻止函数在给定时间窗口内被调不能超过一次。下面这个例子会节流 “click” 事件处理器，使其每秒钟的只能调用一次。

```
import throttle from 'lodash.throttle';

class LoadMoreButton extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
    this.handleClickThrottled = throttle(this.handleClick, 1000);
  }

  componentWillUnmount() {
    this.handleClickThrottled.cancel();
  }

  render() {
    return <button onClick={this.handleClickThrottled}>Load More</button>;
  }

  handleClick() {
    this.props.loadMore();
  }
}
```

#### [](https://zh-hans.reactjs.org/docs/faq-functions.html#debounce)防抖

防抖确保函数不会在上一次被调用之后一定量的时间内被执行。当必须进行一些费时的计算来响应快速派发的事件时（比如鼠标滚动或键盘事件时），防抖是非常有用的。下面这个例子以 250ms 的延迟来改变文本输入。

```
import debounce from 'lodash.debounce';

class Searchbox extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.emitChangeDebounced = debounce(this.emitChange, 250);
  }

  componentWillUnmount() {
    this.emitChangeDebounced.cancel();
  }

  render() {
    return (
      <input
        type="text"
        onChange={this.handleChange}
        placeholder="Search..."
        defaultValue={this.props.value}
      />
    );
  }

  handleChange(e) {
    this.emitChangeDebounced(e.target.value);
  }

  emitChange(value) {
    this.props.onChange(value);
  }
}
```

#### [](https://zh-hans.reactjs.org/docs/faq-functions.html#requestanimationframe-throttling)`requestAnimationFrame` 节流

[`requestAnimationFrame`](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) 是在浏览器中排队等待执行的一种方法，它可以在呈现性能的最佳时间执行。一个函数被 `requestAnimationFrame` 放入队列后将会在下一帧触发。浏览器会努力确保每秒 60 帧（60fps）。然而，如果浏览器无法确保，那么自然会_限制_每秒的帧数。例如，某个设备可能只能处理每秒 30 帧，所以每秒只能得到 30 帧。使用 `requestAnimationFrame` 来节流是一种有用的技术，它可以防止在一秒中进行 60 帧以上的更新。如果一秒钟内完成 100 次更新，则会为浏览器带来额外的负担，而用户却无法感知到这些工作。

> **注意：**
> 
> 使用这个方法时只能获取某一帧中最后发布的值。也可以在 [`MDN`](https://developer.mozilla.org/en-US/docs/Web/Events/scroll) 中看优化的示例。

```
import rafSchedule from 'raf-schd';

class ScrollListener extends React.Component {
  constructor(props) {
    super(props);

    this.handleScroll = this.handleScroll.bind(this);

    // Create a new function to schedule updates.
    this.scheduleUpdate = rafSchedule(
      point => this.props.onScroll(point)
    );
  }

  handleScroll(e) {
    // When we receive a scroll event, schedule an update.
    // If we receive many updates within a frame, we'll only publish the latest value.
    this.scheduleUpdate({ x: e.clientX, y: e.clientY });
  }

  componentWillUnmount() {
    // Cancel any pending updates since we're unmounting.
    this.scheduleUpdate.cancel();
  }

  render() {
    return (
      <div
        style={{ overflow: 'scroll' }}
        onScroll={this.handleScroll}
      >
        <img src="/my-huge-image.jpg" />
      </div>
    );
  }
}
```

#### [](https://zh-hans.reactjs.org/docs/faq-functions.html#testing-your-rate-limiting)测试速率限制

在测试速率限制的代码是否正确工作的时候，如果可以（对动画或操作）进行快进将会很有帮助。如果正在使用 [`jest`](https://facebook.github.io/jest/) ，那么可以使用 [`mock timers`](https://facebook.github.io/jest/docs/en/timer-mocks.html) 来快进。如果正在使用 `requestAnimationFrame` 节流，那么就会发现 [`raf-stub`](https://github.com/alexreardon/raf-stub) 是一个控制动画帧的十分有用的工具。

# 组件状态

### [](https://zh-hans.reactjs.org/docs/faq-state.html#what-does-setstate-do)`setState` 实际做了什么？

`setState()` 会对一个组件的 `state` 对象安排一次更新。当 state 改变了，该组件就会重新渲染。

### [](https://zh-hans.reactjs.org/docs/faq-state.html#what-is-the-difference-between-state-and-props)`state` 和 `props` 之间的区别是什么？

[`props`](https://zh-hans.reactjs.org/docs/components-and-props.html)（“properties” 的缩写）和 [`state`](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html) 都是普通的 JavaScript 对象。它们都是用来保存信息的，这些信息可以控制组件的渲染输出，而它们的一个重要的不同点就是：`props` 是传递_给_组件的（类似于函数的形参），而 `state` 是在组件_内_被组件自己管理的（类似于在一个函数内声明的变量）。

下面是一些不错的资源，可以用来进一步了解使用 `props` 或 `state` 的最佳时机：

-   [Props vs State](https://github.com/uberVU/react-guide/blob/master/props-vs-state.md)
-   [ReactJS: Props vs. State](https://lucybain.com/blog/2016/react-state-vs-pros/)

### [](https://zh-hans.reactjs.org/docs/faq-state.html#why-is-setstate-giving-me-the-wrong-value)为什么 `setState` 给了我一个错误的值？

在 React 中，`this.props` 和 `this.state` 都代表着_已经被渲染了的_值，即当前屏幕上显示的值。

调用 `setState` 其实是异步的 —— 不要指望在调用 `setState` 之后，`this.state` 会立即映射为新的值。如果你需要基于当前的 state 来计算出新的值，那你应该传递一个函数，而不是一个对象（详情见下文）。

代码_不会_像预期那样运行的示例：

```
incrementCount() {
  // 注意：这样 *不会* 像预期的那样工作。
  this.setState({count: this.state.count + 1});
}

handleSomething() {
  // 假设 `this.state.count` 从 0 开始。
  this.incrementCount();
  this.incrementCount();
  this.incrementCount();
  // 当 React 重新渲染该组件时，`this.state.count` 会变为 1，而不是你期望的 3。

  // 这是因为上面的 `incrementCount()` 函数是从 `this.state.count` 中读取数据的，
  // 但是 React 不会更新 `this.state.count`，直到该组件被重新渲染。
  // 所以最终 `incrementCount()` 每次读取 `this.state.count` 的值都是 0，并将它设为 1。

  // 问题的修复参见下面的说明。
}
```

参见下面的说明来修复这个问题。

### [](https://zh-hans.reactjs.org/docs/faq-state.html#how-do-i-update-state-with-values-that-depend-on-the-current-state)我应该如何更新那些依赖于当前的 state 的 state 呢？

给 `setState` 传递一个函数，而不是一个对象，就可以确保每次的调用都是使用最新版的 state（见下面的说明）。

### [](https://zh-hans.reactjs.org/docs/faq-state.html#what-is-the-difference-between-passing-an-object-or-a-function-in-setstate)给 `setState` 传递一个对象与传递一个函数的区别是什么？

传递一个函数可以让你在函数内访问到当前的 state 的值。因为 `setState` 的调用是分批的，所以你可以链式地进行更新，并确保它们是一个建立在另一个之上的，这样才不会发生冲突：

```
incrementCount() {
  this.setState((state) => {
    // 重要：在更新的时候读取 `state`，而不是 `this.state`。
    return {count: state.count + 1}
  });
}

handleSomething() {
  // 假设 `this.state.count` 从 0 开始。
  this.incrementCount();
  this.incrementCount();
  this.incrementCount();

  // 如果你现在在这里读取 `this.state.count`，它还是会为 0。
  // 但是，当 React 重新渲染该组件时，它会变为 3。
}
```

[学习更多有关 setState 的内容](https://zh-hans.reactjs.org/docs/react-component.html#setstate)

### [](https://zh-hans.reactjs.org/docs/faq-state.html#when-is-setstate-asynchronous)`setState` 什么时候是异步的？

目前，在事件处理函数内部的 `setState` 是异步的。

例如，如果 `Parent` 和 `Child` 在同一个 click 事件中都调用了 `setState` ，这样就可以确保 `Child` 不会被重新渲染两次。取而代之的是，React 会将该 state “冲洗” 到浏览器事件结束的时候，再统一地进行更新。这种机制可以在大型应用中得到很好的性能提升。

这只是一个实现的细节，所以请不要直接依赖于这种机制。在以后的版本当中，React 会在更多的情况下静默地使用 state 的批更新机制。

### [](https://zh-hans.reactjs.org/docs/faq-state.html#why-doesnt-react-update-thisstate-synchronously)为什么 React 不同步地更新 `this.state`？

如前面章节解释的那样，在开始重新渲染之前，React 会有意地进行“等待”，直到所有在组件的事件处理函数内调用的 `setState()` 完成之后。这样可以通过避免不必要的重新渲染来提升性能。

但是，你可能还是会想，为什么 React 不能立即更新 `this.state`，而不对组件进行重新渲染呢。

主要有两个原因：

-   这样会破坏掉 `props` 和 `state` 之间的一致性，造成一些难以 debug 的问题。
-   这样会让一些我们正在实现的新功能变得无法实现。

这个 [GitHub 评论](https://github.com/facebook/react/issues/11527#issuecomment-360199710) 深入了该特殊示例。

### [](https://zh-hans.reactjs.org/docs/faq-state.html#should-i-use-a-state-management-library-like-redux-or-mobx)我应该使用一个像 Redux 或 MobX 那样的 state 管理库吗？

[或许需要。](https://redux.js.org/faq/general#when-should-i-use-redux)

在添加额外的库之前，最好先了解清楚 React 能干什么。你也可以只使用 React 来构建出一个比较复杂的应用。

# 样式与 CSS

### [](https://zh-hans.reactjs.org/docs/faq-styling.html#how-do-i-add-css-classes-to-components)如何为组件添加 CSS 的 class？

传递一个字符串作为 `className` 属性：

```
render() {
  return <span className="menu navigation-menu">Menu</span>
}
```

CSS 的 class 依赖组件的 props 或 state 的情况很常见：

```
render() {
  let className = 'menu';
  if (this.props.isActive) {
    className += ' menu-active';
  }
  return <span className={className}>Menu</span>
}
```

> 提示
> 
> 如果你经常发现自己写类似这样的代码，[classnames](https://www.npmjs.com/package/classnames#usage-with-reactjs) 的 package 可以简化这一过程。

### [](https://zh-hans.reactjs.org/docs/faq-styling.html#can-i-use-inline-styles)可以使用行内样式吗？

可以，在[此处](https://zh-hans.reactjs.org/docs/dom-elements.html#style)查看关于样式的文档。

### [](https://zh-hans.reactjs.org/docs/faq-styling.html#are-inline-styles-bad)行内样式不好 (bad) 吗？

从性能角度来说，CSS 的 class 通常比行内样式更好。

### [](https://zh-hans.reactjs.org/docs/faq-styling.html#what-is-css-in-js)什么是 CSS-in-JS?

“CSS-in-JS” 是指一种模式，其中 CSS 由 JavaScript 生成而不是在外部文件中定义。

_注意此功能并不是 React 的一部分，而是由第三方库提供。_ React 对样式如何定义并没有明确态度；如果存在疑惑，比较好的方式是和平时一样，在一个单独的 `*.css` 文件定义你的样式，并且通过 [`className`](https://zh-hans.reactjs.org/docs/dom-elements.html#classname) 指定它们。

### [](https://zh-hans.reactjs.org/docs/faq-styling.html#can-i-do-animations-in-react)可以在 React 中实现动画效果吗？

React 可以被用来实现强大的动画效果。参见 [React Transition Group](https://reactcommunity.org/react-transition-group/)、[React Motion](https://github.com/chenglou/react-motion) 以及 [React Spring](https://github.com/react-spring/react-spring) 等示例。

# 项目文件结构

### [](https://zh-hans.reactjs.org/docs/faq-structure.html#is-there-a-recommended-way-to-structure-react-projects)是否有一种推荐的方式来组织 React 的项目文件结构呢？

React 对如何将文件放入文件夹中没有意见。也就是说，你可以参考使用生态系统中一些常见的组织项目文件结构的方式。

#### [](https://zh-hans.reactjs.org/docs/faq-structure.html#grouping-by-features-or-routes)按功能或路由组织

组织项目文件结构的一种常见方法是将 CSS、JS 和测试文件一起按照功能或路由进行组织。

```
common/
  Avatar.js
  Avatar.css
  APIUtils.js
  APIUtils.test.js
feed/
  index.js
  Feed.js
  Feed.css
  FeedStory.js
  FeedStory.test.js
  FeedAPI.js
profile/
  index.js
  Profile.js
  ProfileHeader.js
  ProfileHeader.css
  ProfileAPI.js
```

一个“功能”的定义因人而异，你可以自行选择其粒度。如果你想不出如何设计顶层目录，则可以通过向产品用户询问这个产品所包含的主要部分，并将反馈的想法用作设计蓝本。

#### [](https://zh-hans.reactjs.org/docs/faq-structure.html#grouping-by-file-type)按文件类型组织

另一种组织项目文件结构的常用方法是将类似的文件组织在一起，例如：

```
api/
  APIUtils.js
  APIUtils.test.js
  ProfileAPI.js
  UserAPI.js
components/
  Avatar.js
  Avatar.css
  Feed.js
  Feed.css
  FeedStory.js
  FeedStory.test.js
  Profile.js
  ProfileHeader.js
  ProfileHeader.css
```

一些人还喜欢更进一步，他们根据组件在应用程序中的角色将组件文件组织到不同的目录中去。例如，[原子设计](http://bradfrost.com/blog/post/atomic-web-design/)就是一种基于此原理的设计方法。请记住，将这些方法作为有用的示例而非必须严格遵守的规则，通常会更富成效。

#### [](https://zh-hans.reactjs.org/docs/faq-structure.html#avoid-too-much-nesting)避免多层嵌套

JavaScript 项目中的深层目录嵌套会带来许多痛点。在编写相对路径导入，或是在文件移动后更新这些导入将变得更加困难。除非你有非常令人信服的理由来使用深层目录嵌套，否则请考虑将单个项目中的目录嵌套控制在最多三到四个层级内。当然，这只是一个建议，它可能与你的项目无关。

#### [](https://zh-hans.reactjs.org/docs/faq-structure.html#dont-overthink-it)不要过度思考

如果你刚刚开始一个项目，[不要花超过五分钟](https://en.wikipedia.org/wiki/Analysis_paralysis)在选择项目文件组织结构上。选择上述任何方式（或提出自己的方式）并开始编写代码！因为，在你编写了一些真正的代码之后，你将很有可能会重新考虑它。

如果您感觉完全卡住，请先将所有文件保存在同一个文件夹中。它最终会变得足够大，以至于让你想要将其中一些文件拆分出去。到那时，你将有足够的知识去区分你最频繁编辑的文件。通常，将经常一起变化的文件组织在一起是个好主意。这个原则被称为 “colocation”。

随着项目规模的扩大，人们通常会在实践中混搭使用上述这些方式。因此，在开始时选择“正确”的那个方式并不是很重要。

# Virtual DOM 及内核

### [](https://zh-hans.reactjs.org/docs/faq-internals.html#what-is-the-virtual-dom)什么是 Virtual DOM？

Virtual DOM 是一种编程概念。在这个概念里， UI 以一种理想化的，或者说“虚拟的”表现形式被保存于内存中，并通过如 ReactDOM 等类库使之与“真实的” DOM 同步。这一过程叫做[协调](https://zh-hans.reactjs.org/docs/reconciliation.html)。

这种方式赋予了 React 声明式的 API：您告诉 React 希望让 UI 是什么状态，React 就确保 DOM 匹配该状态。这使您可以从属性操作、事件处理和手动 DOM 更新这些在构建应用程序时必要的操作中解放出来。

与其将 “Virtual DOM” 视为一种技术，不如说它是一种模式，人们提到它时经常是要表达不同的东西。在 React 的世界里，术语 “Virtual DOM” 通常与 [React 元素](https://zh-hans.reactjs.org/docs/rendering-elements.html)关联在一起，因为它们都是代表了用户界面的对象。而 React 也使用一个名为 “fibers” 的内部对象来存放组件树的附加信息。上述二者也被认为是 React 中 “Virtual DOM” 实现的一部分。

### [](https://zh-hans.reactjs.org/docs/faq-internals.html#is-the-shadow-dom-the-same-as-the-virtual-dom)Shadow DOM 和 Virtual DOM 是一回事吗？

不，他们不一样。Shadow DOM 是一种浏览器技术，主要用于在 web 组件中封装变量和 CSS。Virtual DOM 则是一种由 Javascript 类库基于浏览器 API 实现的概念。

### [](https://zh-hans.reactjs.org/docs/faq-internals.html#what-is-react-fiber)什么是 “React Fiber”？

Fiber 是 React 16 中新的协调引擎。它的主要目的是使 Virtual DOM 可以进行增量式渲染。[了解更多](https://github.com/acdlite/react-fiber-architecture).