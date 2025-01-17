---
title: Flux 跟 Redux 之間的關係
tags:
  - ReactJS
  - Flux
  - Redux
categories:
  - ReactJS
date: 2025-01-15 02:38:14
---

Flux 和 Redux 都是用來管理 application 狀態的架構，差異是不同的設計理念和實做方式：

## Flux

Flux 是 Facebook 提出的架構模式，用於解決複雜的 data flow 問題，Flux 的架構如下：

1. Action: 描述 application 的事件或行為。
2. Dispatcher: 分發 Action 給 Store。它是 Flux 架構中的中央樞紐。
3. Store: 儲存 application 狀態和邏輯。每個 Store 負責 application 一部分狀態。
4. View: 展示 application UI，並且可以根據 Store 的變化來更新。

Flux 的 data flow 是單向的，從 Action 到 Dispatcher，再到 Store，最後到 view。

<!-- Fig 10.1 -->

## Redux

Redux 參考並簡化 Flux 管理狀態概念，Redux 的架構如下：

1. Action: 與 Flux 中的 Action 類似，用於描述 application 的事件或行為。
2. Reducer: 是一個 function，負責根據 Action 來更新 application 的狀態。Redux 中沒有 Sispatcher，取而代之的是 Reducer。
3. Store: 儲存 application 的狀態。Redux 中只有一個單一的 Store，與 Flux 有多個 Store 不同。而且 Redux 只能透過 Action 可以修改 Store 的資料。
4. Middleware: 用於處理異步操作或其他事件 e.g. Error Handling。

Redux 的 data flow 也是單向的，並且強調使用 function 來更新狀態。

<!-- Fig 10.2 -->

Redux 使用單一集中狀態的物件，並以特定的方式進行更新。當你想要更新狀態時（e.g click event），會創建一個 Action 並由某個 Reducer 處理。Reducer 會複製當前狀態，且使用 Action 中的資料進行修改，然後返回新的狀態。當 Store 更新時，可以監聽事件並更新

Redux 跟 React 的差別是：React 是一個 front-end library；Redux 是一個架構，可以不用跟 React 一起使用，也可以跟 Vue 或 Angular 搭配。這篇是一個 React Redux 的範例：

{% post_link reactjs-redux-example %}

## Compare

- **Redux 使用單一的 store**: 與 Flux 在中多個 Store 中定位狀態信息不同，Redux 將所有內容保存在一個地方。在 Flux 中，可以有許多不同的 store。Redux 打破了這一點，強制使用單一 global Store。
- **Redux 使用 reducers**: Reducers 是以不更動原本資料的方式來更新資料。在 Redux 中，行為是以可預測的，因為所有的改動都要經過 Reducer，且每一次的改動只會更新一次 global Store。
- **Redux 使用 middleware**: 由於 Action 和資料以單向方式流動，我們可以透過 Redux 增加 middleware，並在資料更新時加上客製化的行為 e.g. Log or Catch Error。
- **Redux decouple Action 與 Store**: 建立 Action 時不會向通知 Store 任何東西，反而是回傳 Action 物件；Flux 的 Action 則會直接修改 Store。

## Reference

- [React in Action][1]
- [Redux Devtools Extension][2]

[1]: https://www.manning.com/books/react-in-action
[2]: https://github.com/zalmoxisus/redux-devtools-extension