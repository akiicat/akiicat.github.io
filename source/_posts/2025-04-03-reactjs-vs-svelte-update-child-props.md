---
title: React vs Svelte 開發體驗：子元件暫時更新父層資料
tags:
  - NodeJS
  - ReactJS
  - Svelte
categories:
  - NodeJS
date: 2025-04-03 01:31:24
---

當程式規模慢慢變大時，為了管理與維護會將大元件拆分成許多小元件，在父子元件之間傳遞資料是開發時很常見的。但在傳遞資料上，React 跟 Svelte 沒什麼區別，同樣都是將資料傳入元件的 attribute 中，子元件透過 props 來獲得父層傳入的資料。所以我們這章舉一個比較複雜一點的例子：讓子元件可以暫時更新資料，但如果父元件的資料更新時，子元件也要更新為父元件元件的值。我們將透過範例比較兩者在開發體驗上的差異。

**開發體驗**

> 開發者在使用工具、框架、語言、API、系統或流程時，整體的感受、效率、流暢度與滿意度。

## 使用情境

當你在開發表單、步驟流程或是可編輯區塊時，子元件常會需要「暫時修改」父元件提供的資料。例如使用者輸入值，但尚未儲存前，這些變更應該是本地的。這時候就需要讓子元件在不直接修改父層狀態的前提下，保有一定程度的控制權。

## React：需要手動管理狀態同步

React 採用單向資料流設計，而且我們無法直接修改 `props`。在子元件想要暫時更新資料時，必須透過 `useState` 加上 `useEffect` 來同步 `props` 的變化 ([demo](https://reactplayground.vercel.app/#N4IgLgziBcBmCGAbCBTANCAbrK1QEsA7AExQA8A6AK1xHwFsAHAewCcwACAQUcY9lbN6HAOQUA9D0bUIZEQB1CDFuw4AlFPADGnAUNGtNOhUqZtOwDlsPwwKNc2acAvv0HCRNnQFpiQ8VqI+CiEYCaKWsyEEJyRofBEKKwcALwcfloArvQhYBQA5ihgAKKIKDmhAEIAngCSxAAUno5hAJQRUTEcgk6pVjZ2Dk4NcWAJhEnthD15hiRJDYoc6kZ51pp2peW5i4TLyxraeQDKYKz4OgCyzKRoS-uEmYiId3v7hzoU67YoWxVgDSkU2WU1aIAwUhkZBgdDMqg+YDQHEsmVQGlgSNRKFOP0xqGKsFgKB0HFceg8XjCimU5g4AGEokSwGB8G59CIaN44kyWSYaaoxOIYtUyl8IBBwoRYJlCDp8FEOAAFeBzMAMswTUINVrI+5xLoAbUiMsRHFQauYJoAun0sTi7A0AAytADcinuhjAmVYe12+w4AB5iPhMAA+e7+gMAI0yzIVUTpQS0AGsUsBtalQ2aigyTSNLaEOABqDgARlaznDb39y0CF2TEA4DUYKtyrWgyONoWcEf2AfEMbjhCrNcDdIAFvhEMR1SxNbEC2A012wK5xCO++Jg2H7q7FD3CIppbKWQqJ1OZ0I5ztLCvSTrgHrOpwDYhmFokLnQkjzQAZd+fouNppHaYwOiue6Hm8WIEkSOgNBmKRZo+1bZmA-4fogX4AhBbpvM4SJGkBkFPtEnBEOs-x9IhyG9n+AFYYuDRvph2HFmWkHLM4eEekU3q+r20axmA8aEIm9ZphRhj-JWva1kmDZNlok7Tu2yIsYBJoHpGA7CVEG6QQeijkConCkAgTycMqqqzlEuQuuCIDCqKWjijCFBRmAewocssBRGA3gQPgABeKAdgATNJeHOCAzjOEAA))。

**ParentComponent.js**

```jsx
import React, { useState } from 'react';
import ChildComponent from './ChildComponent';

function ParentComponent() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        clicks (parent): {count}
      </button>
      <ChildComponent count={count} />
    </div>
  );
}

export default ParentComponent;
```

**ChildComponent.js**

```jsx
import React, { useState, useEffect } from 'react';

function ChildComponent({ count }) {
  const [localCount, setLocalCount] = useState(count); // create a local count for temporary use

  useEffect(() => {
    setLocalCount(count);
  }, [count]);

  const increment = () => {
    setLocalCount(localCount + 1);
  };

  return (
    <button onClick={increment}>
      clicks (child): {localCount}
    </button>
  );
}

export default ChildComponent;
```

在這個範例中：

- 子元件透過 `useState` 創建一個本地的 `localCount`，用來「暫時更新」畫面上的計數值。
- 當父元件的 `count` 改變時，`useEffect` 會同步更新 `localCount`。
- 子元件的操作不會影響父元件的實際資料。

## Svelte：直覺操作、語法簡潔

Svelte 允許我們在子元件中直接修改 props，而不需要額外建立本地狀態。這種語法大幅簡化了開發流程，對開發者非常友善 ([demo](https://svelte.dev/playground/untitled?version=5.25.3#H4sIAAAAAAAAE6WQ0WrDMAxFf0WIQR0Wmu3VTQJln7HsIfVcZubIxlbGRvC_DzuBraN92qPula50tODZWB1RPi_IX16jLALWSOOUq6P3-_ihLWftNEZ9TVeOWBNHlNhGFYznfqCBzeRdYHh6M_YVzsFNsNs3pdpGd4eBcqPVDMrNxNDBXeSRtXioDgO1zU8ataeZ2RE4Utao924RFXQ9iHXwvoPHKpW1xY4g_Bg0cSVhKS0p560Za95612ZC02ONrD8ZJYdZp_rGQ37ff_mSP86Np2TWZaNNmdcH56P4P67K66_SXoK9pG-5dF5Z9QEAAA==))。

**Parent.svelte**

```jsx
<script>
	import Child from './Child.svelte';

	let count = 0;
</script>

<button onclick={() => (count += 1)}>
	clicks (parent): {count}
</button>

<Child {count} />
```

**Child.svelte**

```jsx
<script>
	let { count } = $props();
</script>

<button onclick={() => (count += 1)}>
	clicks (child): {count}
</button>
```

- 無需 `useEffect` 或本地狀態。
- 可直接操作傳入的 `count`，操作簡單明瞭。
- 預設變更僅限於子元件本地，不會同步到父層（除非你明確綁定 `bind:` 或使用事件）。

以上 React 與 Svelte 兩個例子功能相同，但 Svelte 的語法設計，讓開發者可以更快速專注在邏輯本身，而非額外的狀態管理機制。

## 延伸閱讀

- [React 官方文件][1]
- [Svelte 官方網站][2]
- [React In Action][3]
- [React In Depth][4]
- [Svelte and Sapper in Action][5]

[1]: https://react.dev/learn
[2]: https://svelte.dev/docs/svelte/$props
[3]: https://www.manning.com/books/react-in-action
[4]: https://www.manning.com/books/react-in-depth
[5]: https://www.manning.com/books/svelte-and-sapper-in-action
