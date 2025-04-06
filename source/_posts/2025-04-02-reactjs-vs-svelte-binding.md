---
title: React vs Svelte 開發體驗：雙向資料綁定
tags:
  - NodeJS
  - ReactJS
  - Svelte
categories:
  - NodeJS
date: 2025-04-02 03:31:24
---

資料綁定 (Data Binding) 是連結應用程式資料 (Application) 與使用者介面 (UI) 的方式或機制。React 是使用單向資料綁定 (one-way data flow)，需手動呼叫 `setState` 來更新狀態；而 Svelte 使用 `bind:` 指令來達成雙向資料綁定。我們將透過範例來了解兩者在開發體驗上的差異。

**開發體驗**

> 開發者在使用工具、框架、語言、API、系統或流程時，整體的感受、效率、流暢度與滿意度。

## 使用情境

當你在開發表單、表單輸入與使用者互動或即時資料顯示，需要反向更新狀態變數，並即時顯示給使用者 (e.g. 動態價格計算、表單即時驗證、即時預覽)。

## React：單向資料綁定

React 採用單向資料流 (one-way data flow)，透過手動呼叫 `setstate` 來更新狀態。

```jsx
import { useState } from 'react';

function InputComponent() {
  const [text, setText] = useState('');

  return (
    <>
      <input
        type="text"
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
      <p>{text}</p>
    </>
  );
}
```

- `useState` 宣告狀態變數 `text`，初始值為空字串。
- `value={text}` 綁定輸入框顯示值。
- `onChange` 在輸入變動時更新 `text`。

## Svelte：直覺式雙向綁定

Svelte 提供了 `bind:` 指令，簡化雙向資料綁定，讓變數與元件保持同步。

```jsx
<script>
  let text = '';
</script>

<input type="text" bind:value={text} />
<p>{text}</p>
```

- 使用 `let` 宣告響應式 (reactivity) 變數 `text`。
- `bind:value={text}` 自動雙向綁定輸入值與變數。
- 使用者輸入會更新 `text`，變數更新也會同步回輸入框。

## 哲學

React 與 Svelte 在資料綁定上的差異，反映了各自的框架哲學：

- React 注重控制與可預測性，適合處理複雜應用邏輯與資料流。
- Svelte 注重簡潔與開發效率，讓使用者開發時更貼近原生思維。

## 延伸閱讀

- [React 官方文件][1]
- [Svelte 官方網站][2]
- [React In Action][3]
- [React In Depth][4]
- [Svelte and Sapper in Action][5]

[1]: https://react.dev/learn
[2]: https://svelte.dev/docs/svelte/overview
[3]: https://www.manning.com/books/react-in-action
[4]: https://www.manning.com/books/react-in-depth
[5]: https://www.manning.com/books/svelte-and-sapper-in-action
