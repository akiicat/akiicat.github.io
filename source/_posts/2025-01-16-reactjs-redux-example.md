---
title: React Redux Example App 範例程式碼
tags:
  - ReactJS
  - Redux
categories:
  - ReactJS
date: 2025-01-16 09:38:14
---

前一篇介紹 Redux 是參考 Flux 的架構而設計的，這篇要用 Redux 來寫一個簡單的 React Counter App。

{% post_link install-docker-ce-on-ubuntu %}

再提醒一下，React 跟 Redux 的差別是：React 是一個 front-end library；Redux 是一個架構，可以不用跟 React 一起使用，也可以跟 Vue 或 Angular 搭配。

## Counter App

### Install

建立一個 React App 然後安裝 Redux：

```
npx create-react-app counter-app
cd counter-app
npm install --save redux react-redux
```

### Actions

建立 Redux 的 Action，Action 一定要回傳 type 讓 Reducer 去做對應的資料更新，Middleware 會在送到 Reducer 之前執行。

```js
// actions.js
export const increment = () => ({
  type: 'INCREMENT'
});

export const decrement = () => ({
  type: 'DECREMENT'
});
```

### Reducer

建立 Redux 的 Reducer，Reducer 不能修改原本的 state，必須複製一份，並且回傳最後 state 應該要的資料。

```js
// reducer.js
const initialState = {
  count: 0
};

const counterReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return {
        ...state,
        count: state.count + 1
      };
    case 'DECREMENT':
      return {
        ...state,
        count: state.count - 1
      };
    default:
      return state;
  }
};

export default counterReducer;
```

### Store

建立 Redux 的 Store，用來儲存 state 的資料

```js
// store.js
import { createStore } from 'redux';
import counterReducer from './reducer';

const store = createStore(counterReducer);

export default store;
```

### Counter

建立 React Counter Element，而且注意這邊需要將 Action 傳入 Dispatcher 來讓後續的 Reducer 來更新狀態。

```js
// Counter.js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './actions';

const Counter = () => {
  const count = useSelector(state => state.count);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => dispatch(increment())}>Increment</button>
      <button onClick={() => dispatch(decrement())}>Decrement</button>
    </div>
  );
};

export default Counter;
```

### App

最後 react-redux library 透過 Provider Component 來跟 React 串接，Provider 會提供 Store 的資料。

```js
// App.js
import React from 'react';
import { Provider } from 'react-redux';
import store from './store';
import Counter from './Counter';

const App = () => (
  <Provider store={store}>
    <Counter />
  </Provider>
);

export default App;
```

## Reference

- [Github - Redux][1]
- [Github - React Redux][2]

[1]: https://github.com/reduxjs/redux
[2]: https://github.com/reduxjs/react-redux

