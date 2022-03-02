# 测试概览

你可以用像测试其他 JavaScript 代码类似的方式测试 React 组件。

现在有许多种测试 React 组件的方法。大体上可以被分为两类：

-   **渲染组件树** 在一个简化的测试环境中渲染组件树并对它们的输出做断言检查。
-   **运行完整应用** 在一个真实的浏览器环境中运行整个应用（也被称为“端到端（end-to-end）”测试）。

本章节主要专注于第一种情况下的测试策略。虽然完整的端到端测试在防止对重要工作流的多次回归方面很有价值，但对 React 组件来说这类测试并不怎么需要关注，因此不在本章节的讨论范围之内。

### 权衡

当挑选测试工具的时候，有些细节值得我们权衡考虑：

-   **迭代速度 vs 真实环境：** 一些工具在做出改动和看到结果之间提供了非常快速的反馈循环，但没有精确的模拟浏览器的行为。另一些工具，也许使用了真实的浏览器环境，但却降低了迭代速度，而且在持续集成服务器中不太可靠。
-   **mock 到什么程度：** 对组件来说，“单元测试”和“集成测试”之间的差别可能会很模糊。如果你在测试一个表单，用例是否应该也测试表单里的按钮呢？一个按钮组件又需不需要有他自己的测试套件？重构按钮组件是否应该影响表单的测试用例？

不同的团队或产品可能会得出不同的答案。

### 推荐的工具

**[Jest](https://facebook.github.io/jest/)** 是一个 JavaScript 测试运行器。它允许你使用 [`jsdom`](https://zh-hans.reactjs.org/docs/testing-environments.html#mocking-a-rendering-surface) 操作 DOM 。尽管 jsdom 只是对浏览器工作表现的一个近似模拟，对测试 React 组件来说它通常也已经够用了。Jest 有着十分优秀的迭代速度，同时还提供了若干强大的功能，比如它可以模拟 [modules](https://zh-hans.reactjs.org/docs/testing-environments.html#mocking-modules) 和 [timers](https://zh-hans.reactjs.org/docs/testing-environments.html#mocking-timers) 让你更精细的控制代码如何执行。

**[React 测试库](https://testing-library.com/react)**是一组能让你不依赖 React 组件具体实现对他们进行测试的辅助工具。它让重构工作变得轻而易举，还会推动你拥抱有关无障碍的最佳实践。虽然它不能让你省略子元素来浅（shallowly）渲染一个组件，但像 Jest 这样的测试运行器可以通过 [mocking](https://zh-hans.reactjs.org/docs/testing-recipes.html#mocking-modules) 让你做到。

### 了解更多

这一章节被划分成了两页内容：

-   [技巧](https://zh-hans.reactjs.org/docs/testing-recipes.html)：为 React 组件编写测试时的常见模式。
-   [环境](https://zh-hans.reactjs.org/docs/testing-environments.html)：为 React 组件搭建测试环境的时候有哪些要考虑的东西。


# 测试技巧

React 组件的常见测试模式。

> 注意：
> 
> 此章节假设你正在使用 [Jest](https://jestjs.io/) 作为测试运行器。如果你使用不同的测试运行器，你可能需要调整 API，但整体的解决方案是相同的。在[测试环境](https://zh-hans.reactjs.org/docs/testing-environments.html)章节阅读更多关于设置测试环境的细节。

在本章中，我们将主要使用函数组件。然而，这些测试策略并不依赖于实现细节，它对于 class 组件也同样有效。

-   [创建/清理](https://zh-hans.reactjs.org/docs/testing-recipes.html#setup--teardown)
-   [`act()`](https://zh-hans.reactjs.org/docs/testing-recipes.html#act)
-   [渲染](https://zh-hans.reactjs.org/docs/testing-recipes.html#rendering)
-   [数据获取](https://zh-hans.reactjs.org/docs/testing-recipes.html#data-fetching)
-   [mock 模块](https://zh-hans.reactjs.org/docs/testing-recipes.html#mocking-modules)
-   [事件](https://zh-hans.reactjs.org/docs/testing-recipes.html#events)
-   [计时器](https://zh-hans.reactjs.org/docs/testing-recipes.html#timers)
-   [快照测试](https://zh-hans.reactjs.org/docs/testing-recipes.html#snapshot-testing)
-   [多渲染器](https://zh-hans.reactjs.org/docs/testing-recipes.html#multiple-renderers)
-   [缺少什么?](https://zh-hans.reactjs.org/docs/testing-recipes.html#something-missing)

---

### 创建/清理

对于每个测试，我们通常希望将 React 树渲染给附加到 `document`的 DOM 元素。这点很重要，以便它可以接收 DOM 事件。当测试结束时，我们需要“清理”并从 `document` 中卸载树。

常见的方法是使用一对 `beforeEach` 和 `afterEach` 块，以便它们一直运行，并隔离测试本身造成的影响：

```
import { unmountComponentAtNode } from "react-dom";

let container = null;
beforeEach(() => {
  // 创建一个 DOM 元素作为渲染目标
  container = document.createElement("div");
  document.body.appendChild(container);
});

afterEach(() => {
  // 退出时进行清理
  unmountComponentAtNode(container);
  container.remove();
  container = null;
});
```

你可以使用不同的测试模式，但请注意，_即使测试失败_，也需要执行清理。否则，测试可能会导致“泄漏”，并且一个测试可能会影响另一个测试的行为。这使得其难以调试。

---

### `act()`

在编写 UI 测试时，可以将渲染、用户事件或数据获取等任务视为与用户界面交互的“单元”。`react-dom/test-utils` 提供了一个名为 [`act()`](https://zh-hans.reactjs.org/docs/test-utils.html#act) 的 helper，它确保在进行任何断言之前，与这些“单元”相关的所有更新都已处理并应用于 DOM：

```
act(() => {
  // 渲染组件
});
// 进行断言
```

这有助于使测试运行更接近真实用户在使用应用程序时的体验。这些示例的其余部分使用 `act()` 来作出这些保证。

你可能会发现直接使用 `act()` 有点过于冗长。为了避免一些样板代码，你可以使用 [React 测试库](https://testing-library.com/react)，这些 helper 是使用 `act()` 函数进行封装的。

> 注意：
> 
> `act` 名称来自 [Arrange-Act-Assert](http://wiki.c2.com/?ArrangeActAssert) 模式。

---

### 渲染

通常，你可能希望测试组件对于给定的 prop 渲染是否正确。此时应考虑实现基于 prop 渲染消息的简单组件：

```
// hello.js

import React from "react";

export default function Hello(props) {
  if (props.name) {
    return <h1>你好，{props.name}！</h1>;
  } else {
    return <span>嘿，陌生人</span>;
  }
}
```

我们可以为这个组件编写测试：

```
// hello.test.js

import React from "react";
import { render, unmountComponentAtNode } from "react-dom";
import { act } from "react-dom/test-utils";

import Hello from "./hello";

let container = null;
beforeEach(() => {
  // 创建一个 DOM 元素作为渲染目标
  container = document.createElement("div");
  document.body.appendChild(container);
});

afterEach(() => {
  // 退出时进行清理
  unmountComponentAtNode(container);
  container.remove();
  container = null;
});

it("渲染有或无名称", () => {
  act(() => {    render(<Hello />, container);  });  expect(container.textContent).toBe("嘿，陌生人");
  act(() => {
    render(<Hello name="Jenny" />, container);
  });
  expect(container.textContent).toBe("你好，Jenny！");

  act(() => {
    render(<Hello name="Margaret" />, container);
  });
  expect(container.textContent).toBe("你好，Margaret！");
});
```

---

### 数据获取

你可以使用假数据来 mock 请求，而不是在所有测试中调用真正的 API。使用“假”数据 mock 数据获取可以防止由于后端不可用而导致的测试不稳定，并使它们运行得更快。注意：你可能仍然希望使用一个[“端到端”](https://zh-hans.reactjs.org/docs/testing-environments.html#end-to-end-tests-aka-e2e-tests)的框架来运行测试子集，该框架可显示整个应用程序是否一起工作。

```
// user.js

import React, { useState, useEffect } from "react";

export default function User(props) {
  const [user, setUser] = useState(null);

  async function fetchUserData(id) {
    const response = await fetch("/" + id);
    setUser(await response.json());
  }

  useEffect(() => {
    fetchUserData(props.id);
  }, [props.id]);

  if (!user) {
    return "加载中...";
  }

  return (
    <details>
      <summary>{user.name}</summary>
      <strong>{user.age}</strong> 岁
      <br />
      住在 {user.address}
    </details>
  );
}
```

我们可以为它编写测试：

```
// user.test.js

import React from "react";
import { render, unmountComponentAtNode } from "react-dom";
import { act } from "react-dom/test-utils";
import User from "./user";

let container = null;
beforeEach(() => {
  // 创建一个 DOM 元素作为渲染目标
  container = document.createElement("div");
  document.body.appendChild(container);
});

afterEach(() => {
  // 退出时进行清理
  unmountComponentAtNode(container);
  container.remove();
  container = null;
});

it("渲染用户数据", async () => {
  const fakeUser = {    name: "Joni Baez",    age: "32",    address: "123, Charming Avenue"  };  jest.spyOn(global, "fetch").mockImplementation(() =>    Promise.resolve({      json: () => Promise.resolve(fakeUser)    })  );
  // 使用异步的 act 应用执行成功的 promise
  await act(async () => {
    render(<User id="123" />, container);
  });

  expect(container.querySelector("summary").textContent).toBe(fakeUser.name);
  expect(container.querySelector("strong").textContent).toBe(fakeUser.age);
  expect(container.textContent).toContain(fakeUser.address);

  // 清理 mock 以确保测试完全隔离  global.fetch.mockRestore();});
```

---

### mock 模块

有些模块可能在测试环境中不能很好地工作，或者对测试本身不是很重要。使用虚拟数据来 mock 这些模块可以使你为代码编写测试变得更容易。

考虑一个嵌入第三方 `GoogleMap` 组件的 `Contact` 组件：

```
// map.js

import React from "react";

import { LoadScript, GoogleMap } from "react-google-maps";
export default function Map(props) {
  return (
    <LoadScript id="script-loader" googleMapsApiKey="YOUR_API_KEY">
      <GoogleMap id="example-map" center={props.center} />
    </LoadScript>
  );
}

// contact.js

import React from "react";
import Map from "./map";

export default function Contact(props) {
  return (
    <div>
      <address>
        联系 {props.name}，通过{" "}
        <a data-testid="email" href={"mailto:" + props.email}>
          email
        </a>
        或者他们的 <a data-testid="site" href={props.site}>
          网站
        </a>。
      </address>
      <Map center={props.center} />
    </div>
  );
}
```

如果不想在测试中加载这个组件，我们可以将依赖 mock 到一个虚拟组件，然后运行我们的测试：

```
// contact.test.js

import React from "react";
import { render, unmountComponentAtNode } from "react-dom";
import { act } from "react-dom/test-utils";

import Contact from "./contact";
import MockedMap from "./map";

jest.mock("./map", () => {  return function DummyMap(props) {    return (      <div data-testid="map">        {props.center.lat}:{props.center.long}      </div>    );  };});
let container = null;
beforeEach(() => {
  // 创建一个 DOM 元素作为渲染目标
  container = document.createElement("div");
  document.body.appendChild(container);
});

afterEach(() => {
  // 退出时进行清理
  unmountComponentAtNode(container);
  container.remove();
  container = null;
});

it("应渲染联系信息", () => {
  const center = { lat: 0, long: 0 };
  act(() => {
    render(
      <Contact
        name="Joni Baez"
        email="test@example.com"
        site="http://test.com"
        center={center}
      />,
      container
    );
  });

  expect(
    container.querySelector("[data-testid='email']").getAttribute("href")
  ).toEqual("mailto:test@example.com");

  expect(
    container.querySelector('[data-testid="site"]').getAttribute("href")
  ).toEqual("http://test.com");

  expect(container.querySelector('[data-testid="map"]').textContent).toEqual(
    "0:0"
  );
});
```

---

### Events

我们建议在 DOM 元素上触发真正的 DOM 事件，然后对结果进行断言。考虑一个 `Toggle` 组件：

```
// toggle.js

import React, { useState } from "react";

export default function Toggle(props) {
  const [state, setState] = useState(false);
  return (
    <button
      onClick={() => {
        setState(previousState => !previousState);
        props.onChange(!state);
      }}
      data-testid="toggle"
    >
      {state === true ? "Turn off" : "Turn on"}
    </button>
  );
}
```

我们可以为它编写测试：

```
// toggle.test.js

import React from "react";
import { render, unmountComponentAtNode } from "react-dom";
import { act } from "react-dom/test-utils";

import Toggle from "./toggle";

let container = null;
beforeEach(() => {
  // 创建一个 DOM 元素作为渲染目标
  container = document.createElement("div");
  document.body.appendChild(container);});
afterEach(() => {
  // 退出时进行清理
  unmountComponentAtNode(container);
  container.remove();
  container = null;
});

it("点击时更新值", () => {
  const onChange = jest.fn();
  act(() => {
    render(<Toggle onChange={onChange} />, container);
  });

  // 获取按钮元素，并触发点击事件
  const button = document.querySelector("[data-testid=toggle]");
  expect(button.innerHTML).toBe("Turn on");

  act(() => {
    button.dispatchEvent(new MouseEvent("click", { bubbles: true }));
  });
  expect(onChange).toHaveBeenCalledTimes(1);
  expect(button.innerHTML).toBe("Turn off");

  act(() => {
    for (let i = 0; i < 5; i++) {
      button.dispatchEvent(new MouseEvent("click", { bubbles: true }));
    }  });

  expect(onChange).toHaveBeenCalledTimes(6);
  expect(button.innerHTML).toBe("Turn on");
});
```

[MDN](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent)描述了不同的 DOM 事件及其属性。注意，你需要在创建的每个事件中传递 `{ bubbles: true }` 才能到达 React 监听器，因为 React 会自动将事件委托给 root。

> 注意：
> 
> React 测试库为触发事件提供了一个[更简洁 helper](https://testing-library.com/docs/dom-testing-library/api-events)。

---

### 计时器

你的代码可能会使用基于计时器的函数（如 `setTimeout`）来安排将来更多的工作。在这个例子中，多项选择面板等待选择并前进，如果在 5 秒内没有做出选择，则超时：

```
// card.js

import React, { useEffect } from "react";

export default function Card(props) {
  useEffect(() => {
    const timeoutID = setTimeout(() => {
      props.onSelect(null);
    }, 5000);
    return () => {
      clearTimeout(timeoutID);
    };
  }, [props.onSelect]);

  return [1, 2, 3, 4].map(choice => (
    <button
      key={choice}
      data-testid={choice}
      onClick={() => props.onSelect(choice)}
    >
      {choice}
    </button>
  ));
}
```

我们可以利用 [Jest 的计时器 mock](https://jestjs.io/docs/en/timer-mocks) 为这个组件编写测试，并测试它可能处于的不同状态。

```
// card.test.js

import React from "react";
import { render, unmountComponentAtNode } from "react-dom";
import { act } from "react-dom/test-utils";

import Card from "./card";
let container = null;
beforeEach(() => {
  // 创建一个 DOM 元素作为渲染目标
  container = document.createElement("div");
  document.body.appendChild(container);
  jest.useFakeTimers();
});

afterEach(() => {
  // 退出时进行清理
  unmountComponentAtNode(container);
  container.remove();
  container = null;
  jest.useRealTimers();
});

it("超时后应选择 null", () => {
  const onSelect = jest.fn();
  act(() => {
    render(<Card onSelect={onSelect} />, container);
  });

  // 提前 100 毫秒执行  act(() => {
    jest.advanceTimersByTime(100);
  });
  expect(onSelect).not.toHaveBeenCalled();

  // 然后提前 5 秒执行  act(() => {
    jest.advanceTimersByTime(5000);
  });
  expect(onSelect).toHaveBeenCalledWith(null);
});

it("移除时应进行清理", () => {
  const onSelect = jest.fn();
  act(() => {
    render(<Card onSelect={onSelect} />, container);
  });
  act(() => {
    jest.advanceTimersByTime(100);
  });
  expect(onSelect).not.toHaveBeenCalled();

  // 卸载应用程序
  act(() => {
    render(null, container);
  });
  act(() => {
    jest.advanceTimersByTime(5000);
  });
  expect(onSelect).not.toHaveBeenCalled();
});

it("应接受选择", () => {
  const onSelect = jest.fn();
  act(() => {
    render(<Card onSelect={onSelect} />, container);
  });

  act(() => {
    container
      .querySelector("[data-testid='2']")
      .dispatchEvent(new MouseEvent("click", { bubbles: true }));
  });

  expect(onSelect).toHaveBeenCalledWith(2);
});
```

你只能在某些测试中使用假计时器。在上面，我们通过调用 `jest.useFakeTimers()` 来启用它们。它们提供的主要优势是，你的测试实际上不需要等待 5 秒来执行，而且你也不需要为了测试而使组件代码更加复杂。

---

### 快照测试

像 Jest 这样的框架还允许你使用 [`toMatchSnapshot` / `toMatchInlineSnapshot`](https://jestjs.io/docs/en/snapshot-testing) 保存数据的“快照”。有了这些，我们可以“保存”渲染的组件输出，并确保对它的更新作为对快照的更新显式提交。

在这个示例中，我们渲染一个组件并使用 [`pretty`](https://www.npmjs.com/package/pretty) 包对渲染的 HTML 进行格式化，然后将其保存为内联快照：

```
// hello.test.js, again

import React from "react";
import { render, unmountComponentAtNode } from "react-dom";
import { act } from "react-dom/test-utils";
import pretty from "pretty";

import Hello from "./hello";

let container = null;
beforeEach(() => {
  // 创建一个 DOM 元素作为渲染目标
  container = document.createElement("div");
  document.body.appendChild(container);
});

afterEach(() => {
  // 退出时进行清理
  unmountComponentAtNode(container);
  container.remove();
  container = null;
});

it("应渲染问候语", () => {
  act(() => {
    render(<Hello />, container);
  });

  expect(    pretty(container.innerHTML)  ).toMatchInlineSnapshot(); /* ... 由 jest 自动填充 ... */
  act(() => {
    render(<Hello name="Jenny" />, container);
  });

  expect(
    pretty(container.innerHTML)
  ).toMatchInlineSnapshot(); /* ... 由 jest 自动填充 ... */

  act(() => {
    render(<Hello name="Margaret" />, container);
  });

  expect(
    pretty(container.innerHTML)
  ).toMatchInlineSnapshot(); /* ... 由 jest 自动填充 ... */
});
```

通常，进行具体的断言比使用快照更好。这类测试包括实现细节，因此很容易中断，并且团队可能对快照中断不敏感。选择性地 [mock 一些子组件](https://zh-hans.reactjs.org/docs/testing-recipes.html#mocking-modules)可以帮助减小快照的大小，并使它们在代码评审中保持可读性。

---

### 多渲染器

在极少数情况下，你可能正在使用多个渲染器的组件上运行测试。例如，你可能正在使用 `react-test-renderer` 组件上运行快照测试，该组件内部使用子组件内部的 `ReactDOM.render` 渲染一些内容。在这个场景中，你可以使用与它们的渲染器相对应的 `act()` 来包装更新。

```
import { act as domAct } from "react-dom/test-utils";
import { act as testAct, create } from "react-test-renderer";
// ...
let root;
domAct(() => {
  testAct(() => {
    root = create(<App />);
  });
});
expect(root).toMatchSnapshot();
```

---

### 缺少什么？

如果有一些常见场景没有覆盖，请在文档网站的 [issue 跟踪器](https://github.com/reactjs/reactjs.org/issues)上告诉我们。


# 测试环境

本章节介绍了可能会影响你测试环境的因素，并包含某些场景下的建议。

### 测试运行器

使用 [Jest](https://jestjs.io/)，[mocha](https://mochajs.org/)，[ava](https://github.com/avajs/ava) 等测试运行器能像编写 JavaScript 一样编写测试套件，并将其作为开发过程的环节运行。此外，测试套件也将作为持续集成的环节运行。

-   Jest 与 React 项目广泛兼容，支持诸如模拟 [模块](https://zh-hans.reactjs.org/docs/testing-environments.html#mocking-modules)、[计时器](https://zh-hans.reactjs.org/docs/testing-environments.html#mocking-timers) 和 [`jsdom`](https://zh-hans.reactjs.org/docs/testing-environments.html#mocking-a-rendering-surface) 等特性。**如果你使用 Create React App，[Jest 已经能够开箱即用](https://facebook.github.io/create-react-app/docs/running-tests)且包含许多实用的默认配置。**
-   像 [mocha](https://mochajs.org/#running-mocha-in-the-browser) 这样的库在真实浏览器环境下运行良好，并且可以为明确需要它的测试提供帮助。
-   端对端测试用于测试跨多个页面的长流程，并且需要[不同的设置](https://zh-hans.reactjs.org/docs/testing-environments.html#end-to-end-tests-aka-e2e-tests)。

### 模拟渲染表面

测试通常在无法访问真实渲染表面（如浏览器）的环境中运行。对于这些环境，我们建议使用 [`jsdom`](https://github.com/jsdom/jsdom) 来模拟浏览器，这是一个在 Node.js 内运行的轻量级浏览器实现。

在大多数情况下，jsdom 的行为类似于常规浏览器，但不具备如[布局和导航](https://github.com/jsdom/jsdom#unimplemented-parts-of-the-web-platform)的功能。这对于大多数基于 Web 的组件测试仍然有用，因为它的运行比为每个测试启动浏览器的方式效率更高。并且由于它与你编写的测试运行在同一个进程中，所以你能够编写代码来检查和断言渲染的 DOM。

就像在真实的浏览器中一样，jsdom 让我们模拟用户交互；测试可以在 DOM 节点上派发事件，然后观察并断言这些操作的副作用[(例子)](https://zh-hans.reactjs.org/docs/testing-recipes.html#events)。

可以使用上述设置编写大部分 UI 测试：使用 Jest 作为测试运行器，渲染到 jsdom，使用 `act()` 辅助函数[(例子)](https://zh-hans.reactjs.org/docs/testing-recipes.html)提供的能力通过一系列的浏览器事件来模拟用户交互行为。例如，大量 React 自己的测试都是用这种组合编写的。

如果您正在编写一个主要测试浏览器特定行为的库，并且需要布局或真实输入等原生浏览器行为，那么你可以使用像 [mocha](https://mochajs.org/) 这样的框架。

在你 _无法_ 模拟 DOM 环境（例如，在 Node.js 上测试 React Native 组件）的情况下，可以使用 [事件模拟辅助函数](https://zh-hans.reactjs.org/docs/test-utils.html#simulate) 来模拟与元素的交互。或者，你也可以使用 [`@testing-library/react-native`](https://testing-library.com/docs/react-native-testing-library/intro) 中的 `fireEvent` 辅助函数。

诸如 [Cypress](https://www.cypress.io/)，[puppeteer](https://github.com/GoogleChrome/puppeteer) 和 [webdriver](https://www.seleniumhq.org/projects/webdriver/) 等框架对于运行[端对端测试](https://zh-hans.reactjs.org/docs/testing-environments.html#end-to-end-tests-aka-e2e-tests) 都非常有用。

### 模拟功能

在编写测试的时候，我们希望模拟代码在测试环境较真实环境中缺失的等效部分（例如，在 Node.js 中检查 `navigator.onLine` 的状态）。测试还可以监视某些功能，并观察测试的其他部分如何与它们进行交互。有选择的将这些功能模拟为测试友好的版本是很有用的。

这对于数据获取尤其有用。通常最好使用“假”数据进行测试，以避免从实际 API 端获取数据可能导致的缓慢和不稳定[（例子）](https://zh-hans.reactjs.org/docs/testing-recipes.html#data-fetching)。这样做有助于让测试变得可预测。像 [Jest](https://jestjs.io/) 与 [sinon](https://sinonjs.org/) 这样的类库，支持模拟功能。对于端对端测试，虽然模拟网络可能更加困难，但你可能还想对真实的 API 端进行测试。

### 模拟模块

一些组件可能会依赖在测试环境中无法正常运行的模块，或者说这些模块对于我们的测试并不必要。那么，通过选择性地模拟来替换这些模块是很有用的[（例子）](https://zh-hans.reactjs.org/docs/testing-recipes.html#mocking-modules)。

在 Node.js 中，测试运行器如 Jest [支持模拟模块](https://jestjs.io/docs/en/manual-mocks)。你也可以使用像 [`mock-require`](https://www.npmjs.com/package/mock-require) 这样的类库。

### 模拟计时器

组件可能会使用基于时间的函数如 `setTimeout`、`setInterval` 和 `Date.now` 等。在测试环境中，使用可以手动“推进”时间的替代物来模拟这些功能会很有帮助。它会确保你的测试快速运行！依赖于计时器的测试仍将按照顺序解析，但会更快[（例子）](https://zh-hans.reactjs.org/docs/testing-recipes.html#timers)。大部分测试框架，包括 [Jest](https://jestjs.io/docs/en/timer-mocks)、[sinon](https://sinonjs.org/releases/v7.3.2/fake-timers/) 和 [lolex](https://github.com/sinonjs/lolex) 都允许你在测试中模拟计时器。

有些时候可能你不想要模拟计时器。例如，在你测试动画时，或是交互端对时间较为敏感的情况下（如 API 访问速率限制器）。具有计时器模拟的库允许你在每个测试/套件上启用或禁用这个功能，因此你可以明确地选择这些测试的运行方式。

### 端对端测试

端对端测试对于测试更长的工作流程非常有用，特别是当它们对于你的业务（例如付款或注册）特别重要时。对于这些测试，你可能会希望测试真实浏览器如何渲染整个应用、从真实的 API 端获取数据、使用 session 和 cookies 以及在不同的链接间导航等功能。你可能还希望不仅在 DOM 状态上进行断言，而同时也在后端数据上进行校验（例如，验证更新是否已经在数据库中持久化）。

在这种场景下，你可以使用像 [Cypress](https://www.cypress.io/)，[Playwright](https://playwright.dev/) 等类似框架或者使用 [Puppeteer](https://github.com/GoogleChrome/puppeteer) 这样的库，这样你就可以在多个路由之间导航切换，并且不仅能够在浏览器中对副作用进行断言也能够在后端这么做。