---
title: 何時該使用 React Redux
tags:
  - ReactJS
  - Redux
categories:
  - ReactJS
date: 2025-01-17 09:38:14
---

<!-- React in Action page 265 -->

今天這章會討論什麼時候要把資料放在 Redux 裡面，什麼時候要將資料放在 React 的 components 裡。

React 將 components 分成 Presentational Components (展示組件) 與 Container Components (容器組件)。

## Presentational Components

Presentational Components 主要負責 UI 的部分，通常不會有複雜 application 狀態管理的資料，通過會由 props 傳入無狀態 (stateless) 的資料，像是：

- 跟 UI 相關的資料 e.g. 顏色的使用
- 只有在必要時才擁有自己的狀態 e.g. 下拉式選單的狀態
- 需要手動建立新的東西 e.g. new post 的資料儲存

## Container Components

Container Components 主要負責 application 的狀態，通常會使用 Redux 來管理，然後在 render 的時候會資料傳給 Presentational Components，通常會儲存有狀態的資料：

- application data flow
- 使用者相關的資訊 e.g. 最喜歡的顏色

## Compare

| 特性 | Presentational | Container |
| ---- | -------------- | ----------- |
| 主要用途 | UI render | application state |
| 狀態 | 無狀態或是透過 props 獲得狀態 | 通常有狀態，透過 Redux 管理 |
| 類別 | smart | dumb |
| 儲存類型 | 儲存複雜的東西 | 儲存簡單的東西 |

## Reference

- [React in Action][1]

[1]: https://www.manning.com/books/react-in-action
