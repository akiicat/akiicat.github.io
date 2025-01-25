---
title: Node.js Event Loop 是如何運作的
tags:
  - NodeJS
  - Event Loop
categories:
  - NodeJS
date: 2025-01-23 20:38:14
---

Node.js 是 Single Thread 的環境執行，是如何透過 Event Loop 的方式來實現 Non-blocking I/O 的操作。

## Event Loop 運作方式

想像一個 HTTP Request 進入 Node.js http.Server 時，Event Loop 會觸發對應的 callback。假設該 callback 需要從資料庫拿到使用者資料，並從磁碟讀取電子郵件 template，然後將使用者資料填入 template 後，回傳 HTTP Response。

![Nodejs Event Loop](images/nodejs-event-loop-1.png)

在這個過程中，Event Loop 會以先進先出 (FIFO) 的方式運行 Event Queue：

1. 首先，Event Loop 包含一個 Timer，可以透過 setTimeout 或 setInterval 設定。
2. 接下來，如果 Timeout 或是有新的 I/O Event 進來，則會進入 poll phase (輪詢階段)，在此階段會安排執行 (schedule) 對應的 callback。

Event Loop 的概念不會太複雜，有 Event 進來，執行相對應的 callback。

那如果正在執行 poll phase 時，又有新的 I/O Event，Node.js 則會透過 setImmediate 設定，在當前 callback 執行完後立刻執行新進來的 I/O Event callback。

## Library

Node.js 的 Non-blocking I/O Opertaion 是透過 libuv 實現的，libuv 實現 Node.js 的 Event Loop、Non-blocking I/O、網路、磁碟、檔案系統等功能，位於下圖的左下角：

![Nodejs Library](images/nodejs-event-loop-2.png)

原先在 Node.js 程式碼是透過 C++ Binding 綁定到 libuv 或其他 library 上，這些 library 會去跟 OS 來溝通。

之後 Google 的 V8 JavaScript 引擎的出現，讓 Node 更加強大。V8 引擎厲害之處就是直接繞過 C++ binding 變成 machine code，這大幅的提升了執行效率，如上圖右半側。

## Reference

- [Node.js In Action][1]
- [libuv][2]
- [V8 Engine][2]

[1]: https://www.manning.com/books/node-js-in-action
[2]: https://github.com/libuv/libuv
[3]: https://v8.dev/blog