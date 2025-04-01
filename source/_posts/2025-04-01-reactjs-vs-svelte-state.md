---
title: React vs Svelte 開發體驗：狀態管理
tags:
  - NodeJS
  - ReactJS
  - Svelte
categories:
  - NodeJS
date: 2025-04-01 01:04:47
---

狀態管理是在處理資料時一個不可或缺的核心：React 採用的是 `useState` Hook，而 Svelte 則是直接使用變數，使用起來更直觀。我們將透過範例來了解兩者在開發體驗上的差異。

## React 狀態管理：使用 `useState` Hook

React 讓元件可以擁有內部狀態的方式是透過 `useState` Hook 來完成。

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Counter：{count}
    </button>
  );
}

export default Counter;
```

- `useState(0)`：初始化 `count` 為 0。
- `setCount`：更新 `count` 的函式，每次按鈕點擊讓值加 1。
- 透過 React 的 Virtual DOM，變化的值會觸發元件重新渲染。

### 非同步更新注意事項

React 的狀態更新是非同步的，因此如果在同一函式中連續呼叫多次 `setCount(count + 1)`，可能無法即時反映最新的狀態。建議改用以下方式來避免此問題：

```jsx
setCount(prevCount => prevCount + 1);
```

這樣可以保證邏輯永遠是基於最新狀態更新。

## Svelte 狀態管理：響應式變數

Svelte 採用一種更直覺的狀態處理方式。它不需要額外的 Hook，直接使用變數即可自動觸發畫面更新。

```jsx
<script>
  let count = 0;
</script>

<button on:click={() => count++}>
  Counter：{count}
</button>
```

- 使用 `let count = 0` 宣告變數。
- 直接對 `count` 進行操作，例如 `count++`，Svelte 就能自動感知變化並更新 DOM。
- 這是透過 Svelte 的 編譯時響應式系統 (reactivity system) 實現的，完全不需要 Virtual DOM。

Note: React.js 使用 Virtual DOM 來追蹤是否元件需要被更新；而 Svelte 沒有使用 Virtual DOM 而是直些更新在 DOM 上。

## React vs Svelte 狀態更新差異

| 功能面向     | React                    | Svelte        |
|----------|--------------------------|---------------|
| 語法複雜度    | 使用 Hook，語法稍多             | 直覺操作變數，語法簡潔   |
| DOM 更新機制 | Virtual DOM 比對再更新        | 編譯階段生成 DOM 操作 |
| 狀態更新方式   | 必須使用 setState / setCount | 直接修改變數        |
| 更新非同步處理  | 是，需注意資料一致性               | 否，變數改變即更新     |
| 可讀性與維護性  | 中等                       | 高             |

如果追求極簡語法與優化效能，Svelte 提供了非常流暢的開發體驗。而 React 則擁有強大的生態系與社群支持。

## 延伸閱讀

- [React 官方文件][1]
- [Svelte 官方網站][2]
- [React In Action][3]
- [React In Depth][4]
- [Svelte and Sapper in Action][5]

[1]: https://react.dev/learn
[2]: https://svelte.dev/docs/svelte/$state
[3]: https://www.manning.com/books/react-in-action
[4]: https://www.manning.com/books/react-in-depth
[5]: https://www.manning.com/books/svelte-and-sapper-in-action
