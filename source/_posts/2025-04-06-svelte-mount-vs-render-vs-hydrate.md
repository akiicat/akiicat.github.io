---
title: Svelte 比較掛載元件的三個方法：mount vs render vs `hydrate`
tags:
  - NodeJS
  - Svelte
categories:
  - Svelte
date: 2025-04-06 12:46:11
---

Svelte Version: 5.25.7

## Outline

- `render()`：在 Server 端上，將從 Component 產生 HTML。
- `mount()`：在 Client 端上，從空的 DOM 節點建立 Component 的 DOM 附加到 HTML。
- `hydrate()`：混合 `render()` 與 `mount()`。功能上類似 `mount()`，但不是從空的 DOM 節點開始，而是從 Server 端 `render()` 過的資料開始建立。

![Svelte Render VS Render VS Hydrate](images/svelte-render-vs-render-vs-hydrate.png)

<!-- 
https://mermaid.live/edit#pako:eNptkctu00AUhl9ldFYgpZHt2o7jBYjarVhQsUhX1F2M6kkcyZdqsAslipTeVAiVWqkBQYXawAJVqiCoQYAaKA9Dx45XvAITx226YFbznznfufzTgOXAJqBD1Q2eLDuYhmjBtHzEz73FCqGrhKLktPf35yEbnCenL1FllbghQWx783LwbWmcObNoweWv30nnBFHi24Teuo2Szoekc4zuL8w_-NNaNyoVxPbayCHYRsP-zvCkbUFOGzf6sI1-2lpnn3fjV18zFg13-shw68QPR-85YjYmSPymx_Y_su9naXefbT-Pf5zFxwcZe7c5zp6djOes2RSHhM-Xvt1i7e7w_W6ycc57JHsX8bsXyHw4j5JeKx0cXY83N6G9IPJDzrKLo7T7aWxI_PpLcrj1PzB3EU1N3UEzuVGZMPK9M5GbbXKB-CpZbPZGjO-WxeauikIBarRugx7SiBTAI9TDIwmNUYYFoUM8YoHOrzap4sgNLbD8JsdWsP8oCLwrkgZRzQG9it3HXEUrNjfGrOMaxd51dPydxmhv0FV1OisCegOegi4pYlFVNEWWJHlaLiulAqyBrshFTZVFTZQEuSTKQqlZgGdZV6GolRRBEESxLAllSSurzX_vtPZa -->

## 使用時機

如果需要在 server 端渲染 e.g. SEO 的需求，可以使用 `render()`，Server 端會將完整的 HTML 輸出至 Client 端。而 `mount()` 完全相反，僅在 Client 端時建立 DOM 節點。而 `hydrate()` 同時兼顧了 Server 端 SEO 的需求與 Client 端與使用者互動的需求，

## render：Server 端渲染

當您在 Server 端上使用 `render()` 功能時，Component 將被編譯為 HTML 字串，可以將其傳送到 Client 端：

```jsx
// Server-side code (e.g., in a Node server)
import { render } from 'svelte/server';
import App from './App.svelte';

const result = render(App, {
	props: { some: 'property' }
});

// Then send `result` as the response to the client.
result.body; // HTML <body> tag
result.head; // HTML <head> tag
```

App 為 Svelte 的 Componenet，props 則是傳入 App 的屬性參數。

## mount：Client 端渲染

如果你有一個空 DOM 節點或是即沒有預先渲染的 HTML e.g. `<div id="app"></div>` 並且希望 Svelte 在 Client 端上從頭建立 DOM，可以使用 `mount()`：

```jsx
import { mount } from 'svelte';
import App from './App.svelte';

const app = mount(App, {
	target: document.querySelector('#app'),
	props: { some: 'property' }
});
```

## hydrate：升級伺服器渲染的 HTML

假設以下程式碼已在 Server 端產生的 HTML 並傳送到 Client 端：

```html
<div id="app">something</div>
```

我們可以在 Client 端上使用 `hydrate()` 來讓 Server 端渲染的物件具有互動性 (interactive)。

```jsx
import { hydrate } from 'svelte';
import App from './App.svelte';

const app = hydrate(App, {
	target: document.querySelector('#app'),
	props: { some: 'property' }
});
```

可以想像成 Server 端已經渲染好 HTML，用 `hydrate()` 附加額外邏輯和事件處理來「提升」此靜態 HTML 的功能：

## Reference

- [Svelte Imperative component API][1]
- [Svelte and Sapper in Action][2]

[1]: https://svelte.dev/docs/svelte/imperative-component-api
[2]: https://www.manning.com/books/svelte-and-sapper-in-action
