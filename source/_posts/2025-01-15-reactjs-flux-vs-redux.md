---
title: React Flux 跟 Redux 之間的關係
tags:
  - ReactJS
  - Flux
  - Redux
categories:
  - ReactJS
date: 2025-01-15 02:38:14
---

Flux 和 Redux 都是用來管理 application 狀態的架構，差異是不同的設計理念和實現方式：

## Flux

Flux 是 Facebook 提出的架構模式，用於解決複雜的 data flow 問題，Flux 的核心概念如下：

1. Action: 描述 application 的事件或行為。
2. Dispatcher: 分發 actions 給 stores。它是 Flux 架構中的中央樞紐。
3. Store: 儲存 application 狀態和邏輯。每個 store 負責 application 一部分狀態。
4. View: 展示 application UI，並且可以根據 store 的變化來更新。

Flux 的 data flow 是單向的，從 action 到 dispatcher，再到 store，最後到 view。

<!-- Fig 10.1 -->

## Redux

Redux 是受 Flux 啟發的狀態管理 library，但它簡化了 Flux 的概念，並引入了一些新的設計理念，Redux 的核心概念如下：

1. Action: 與 Flux 中的 Action 類似，用於描述 application 的事件或行為。
2. Reducer: 是一個 function，負責根據 Action 來更新 application 的狀態。Redux 中沒有 dispatcher，取而代之的是 reducer。
3. Store: 儲存 application 的狀態。Redux 中只有一個單一的 Store，這與 Flux 中的多個 Store 不同。而且只有 Action 可以修改 Store 的資料。
4. Middleware: 用於處理異步操作或其他事件。

Redux 的 data flow 也是單向的，但它簡化了 Flux 的架構，並且強調使用 function 來更新狀態。

<!-- Fig 10.2 -->

Redux 使用單一集中狀態的物件，並以特定的方式進行更新。當你想要更新狀態時（e.g click event），會創建一個 Action 並由某個 Reducer 處理。Reducer 會複製當前狀態，且使用 Action 中的資料進行修改，然後返回新的狀態。當 Store 更新時，可以監聽事件並更新

## Compare

- **Redux 使用單一的 store**: 與 Flux 在中多個 Store 中定位狀態信息不同，Redux 將所有內容保存在一個地方。在 Flux 中，可以有許多不同的 store。Redux 打破了這一點，強制使用單一的全局 Store。
- **Redux 使用 reducers**: Reducers 是以 immutable approach 的方式來改動資料。在 Redux 中，State 是以可預測的方式改變，且一次改變狀態的一部分，並且只在全局 Store 進行。
- **Redux 使用 middleware**: 由於 Action 和資料以單向方式流動，我們可以透過 Redux 增加 middleware，並在資料更新時加上客製化的行為。
- **Redux decouple Action 與 Store**: 建立 Action 時不會向通知 Store 任何東西。反而是回傳 Action 物件。

## Reference

- [React in Action][1]
- [Redux Devtools Extension][2]

[1]: https://www.manning.com/books/react-in-action
[2]: https://github.com/zalmoxisus/redux-devtools-extension