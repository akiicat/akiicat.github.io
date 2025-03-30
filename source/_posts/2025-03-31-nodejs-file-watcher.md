---
title: 使用 Node.js 監控資料夾變更，並以 Event Emitter 封裝 File Watcher
---
tags:
  - NodeJS
  - Event Emitter
  - Event Listener
categories:
  - NodeJS
date: 2025-03-31 01:38:14
---

## Node.js 監控資料夾

在 Node.js 中，我們可以透過 `fs.watch` 來監控資料夾是否有異動，不需要寫太多程式碼。以下是一個基本範例：

```js
const fs = require('fs');
const path = require('path');
const directoryToWatch = './watched';

fs.watch(directoryToWatch, (eventType, filename) => {
  if (filename) {
    console.log(`File ${filename} has changed with event type: ${eventType}`);
  } else {
    console.log('filename not provided');
  }
});
```

這段程式會持續監控 `./watched` 資料夾的內容變化，當有新增、修改或刪除檔案時，就會觸發 callback 函式。

## 根據副檔名處理不同邏輯

如果我們想要針對不同的副檔名做不同處理，可以進一步使用 `path.extname()` 判斷檔案類型：

```js
const fs = require('fs');
const path = require('path');
const directoryToWatch = './watched';

fs.watch(directoryToWatch, (eventType, filename) => {
  if (filename) {
    const fileExtension = path.extname(filename);
    switch (fileExtension) {
      case '.js':
        // ...
        break;
      case '.txt':
        // ...
        break;
      case '.json':
        // ...
        break;
      default:
        console.log(`Ignoring file ${filename} with extension ${fileExtension}`);
    }
  } else {
    console.log('filename not provided');
  }
});

```

不過隨著專案變大，副檔名的判斷邏輯（if-else 或 switch）會越來越複雜、難以維護。這時就可以考慮封裝成 Event Driven 的架構，提升可讀性與擴充性。

## 將邏輯封裝成 Pub/Sub 模型的 Watcher

Node.js 提供的 EventEmitter 非常適合實作發布/訂閱（Publish/Subscribe）模型。EventEmitter 詳細介紹可以參考這篇文章：

{% post_link nodejs-event-emitter %}

當 `fs.watch` 偵測到檔案更動，我們使用 `.emit(extension)` 來「發布」這個事件，而訂閱該副檔名的使用者透過 `.on(extension, callback)` 就能被「通知」。這讓我們可以針對特定副檔名建立模組化的監聽邏輯，使架構更清晰、易於擴充。

我們利用 class 繼承 EventEmitter，在 constructor 參數傳入資料夾，並呼叫 `start()` 時開始監控：

```js
// watcher.js
const fs = require('fs');
const path = require('path');
const events = require('events');

class Watcher extends events.EventEmitter {
    constructor(watchDir) {
        super();
        this.watchDir = watchDir;
    }

    start() {
        fs.watch(this.watchDir, (eventType, filename) => {
            if (!filename) {
                console.warn('Warning: filename not provided');
                return;
            }
            const fileExtension = path.extname(filename);
            this.emit(fileExtension, eventType, filename);
        })
    }
}

module.exports = Watcher;
```

這段程式碼會根據檔案副檔名觸發對應的事件，達成根據檔案類型處理相對應的 callback。我們建立一個 watcher 並註冊感興趣的副檔名，並呼叫 `start()` 開始監控的資料夾：

```js
// index.js
const Watcher = require('./watcher');

const directoryToWatch = './watched';
const watcher = new Watcher(directoryToWatch);

watcher.on('.js', (eventType, filename) => {
    // ...
});

watcher.on('.txt', (eventType, filename) => {
    // ...
});

watcher.on('.json', (eventType, filename) => {
    // ...
});

watcher.start();
```

這樣的寫法使得每個副檔名的處理邏輯可以被模組化，也可以讓多個檔案處理者獨立訂閱自己關心的事件。

## Conclusion

透過封裝 `fs.watch` 並結合 EventEmitter 的事件機制，我們實作了一個簡單且可擴充的資料夾監控系統。這個模式讓你可以專注在各類檔案的邏輯處理，而不需要在程式碼中寫太複雜的 `if-else` 部分。這樣的架構能讓你在不干擾其他邏輯的情況下，自由地新增更多檔案類型處理器，提升模組化與維護性。

## Reference

- [Node.js In Action][1]
- [Node.js Events][2]

[1]: https://www.manning.com/books/node-js-in-action
[2]: https://nodejs.org/api/events.html
