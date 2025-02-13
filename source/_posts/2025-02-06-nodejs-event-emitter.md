---
title: 使用 Node.js 的 EventEmitter 建立事件監聽機制：概念 & 實作
tags:
  - NodeJS
  - Event Emitter
  - Event Listener
categories:
  - NodeJS
date: 2025-02-13 08:38:14
---

## 概念

在 Node.js 中，建立 EventListener 可以使用 EventEmitter，概念與 publish/subscribe 非常相似，如果之前曾經使用過 Redis 的 Pub/Sub 機制，會更容易理解。

假設有一個人先訂閱（subscribe）了一個名為 'xxx' 的頻道（channel），之後當有人發布（publish）訊息到 'xxx' 頻道時，訂閱者就會看到發布者發出的訊息。

## Event Listener

回到 Node.js 的 Event Listener 機制，我們可以透過 EventEmitter 建立一個 publish/subscribe 物件。執行物件的 .on 方法就類似於訂閱（subscribe），而 .emit 方法則類似於發布（publish）。以下是範例程式碼：

```js
const EventEmitter = require('events');
const channel = new EventEmitter();

// 訂閱 'join' 事件：任何人加入時會通知
channel.on('join', (message) => {
  console.log(`Received message: ${message}`);
});

// 發布 'join' 事件
channel.emit('join', 'Hello, everyone!');
```

在這個範例中，我們使用 EventEmitter 建立了一個 channel 並且監聽 'join' 事件。當有使用者加入時，會透過 .emit('join') 通知 'join' 事件。我們可以建立一個 HTTP server 並稍微修改一下來驗證我們的想法：

```js
// server.js
const net = require('net');

const EventEmitter = require('events').EventEmitter;
const channel = new EventEmitter;

channel.clients = {};

// 訂閱 'join' 事件：任何人加入時會通知
channel.on('join', (id, client) => {
    channel.clients[id] = client;
    console.log(`${id} join`);
});

const server = net.createServer(client => {
    // 使用 `IP:Port` 當作 ID
    const id = `${client.remoteAddress}:${client.remotePort}`;

    // 通知 'join' 事件
    channel.emit('join', id, client);
})

server.listen(30000);
```

`.emit` 不僅用來觸發事件，還可以同時傳入多個參，當你呼叫 `.emit('join', arg1, arg2, ...)` 時，所有註冊了這個事件的監聽器 `.on('join', (arg1, arg2) => {...})` 都會依序被呼叫，且能接收到這些參數。

`net.createServer` 是使用 Node.js 的 net module 來建立一個 TCP server。當有 client 進來時會執行 callback function，這個 callback 第一個參數代表接收到一個 `net.Socket` 的物件。

在 socket 的 callback 中，可以透過 `client.remoteAddress` 與 `client.remotePort` 來獲取連線 client 的 IP 與 port，組合成唯一個 `id`。然後使用 `.emit('join', id, client)` 觸發一個 `join` 事件，同時把這個 `id` 與 `client` 傳入給 join 事件的 callback，最後儲存在 `channel.clients` 中。

我們來測試一下，先開一個 server，然後使用 `telnet localhost 30000` 來連到 server，在 server 上可以看到 `xxx join` 的訊息。

```shell
# 先開一個 server
$ node server.js 
::ffff:127.0.0.1:37282 join

# 然後加入使用者
$ telnet localhost 30000
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
```

## Error Event

在前面的例子，這個 `join` 事件名稱是可以自定義的，可以是任何字串，所以如果觸發一個不存在的事件 `.emit('notExist')`，其實是不會發生什麼事情的，但是有一個例外：`error` 事件。如果沒有人註冊 `error` 事件，但有人在 `error` 事件中發出訊息，程式會直接停止運行並印出錯誤訊息。

```js
const events = require('events');
const myEmitter = new events.EventEmitter();
myEmitter.on('error', err => {
    console.log(`ERROR: ${err.message}`);
});
myEmitter.emit('error', new Error('Something is wrong.'));
```

如果在執行 `error` callback 時又發生錯誤，則會丟出 `uncaughtException` 錯誤，可以透過 `process.on('uncaughtException')` 來捕捉這個錯誤。

```js
process.on('uncaughtException', err => {
  console.error('There was an uncaught error', err);
});
```

上述的例子可以增加這兩個事件：

```js
process.on('uncaughtException', err => {
    console.log("uncaughtException", err.stack);
});

channel.on('error', err => {
    console.log("channel error", err.message)
});
```

## 廣播給所有使用者

現在 server 建立好了，我們想要讓多個使用者加入，如果其中有一個 client 發話，其他 client 都可以收到訊息。

```js
const net = require('net');

const EventEmitter = require('events').EventEmitter;
const channel = new EventEmitter;

channel.clients = {};

channel.on('join', (id, client) => {
    channel.clients[id] = client;
    channel.on('broadcast', (senderId, message) => {
        if (id != senderId) {
            channel.clients[id].write(message);
        }
    });
    console.log(`${id} join`);
    channel.emit('broadcast', id, `${id} join\n`); // 通知有人加入群組
});

const server = net.createServer(client => {
    const id = `${client.remoteAddress}:${client.remotePort}`;
    channel.emit('join', id, client);
    client.on('data', data => {
        data = data.toString();
        channel.emit('broadcast', id, data);
    });
})

server.listen(30000);
```

在 `join` 事件中，當有 client 加入時，會針對每個 client 註冊 `broadcast` 事件。當有人發送廣播訊息時 `channel.emit('broadcast', ...)`，就會觸發 callback 事件。這個 callback 需要接收兩個參數：

- `senderId`：發送者的 id。
- `message`：廣播的訊息。

如果該 client 不是訊息的發送者，則會使用 `channel.clients[id].write(message)` 將訊息送給這個 client。

最後每當 client 連線到 server 並**傳送資料**時，在 `data` 事件中 `client.on('data', callback)`，這個 callback 就會被觸發。這個 callback 會透過我們前面新增的 `broadcast` 事件來廣播給所有人。

在 `.on('data', callback)` 收到的資料通常是 `Buffer`，所以會先轉成 string。

我們來測試一下：

```sh
# server
$ node server.js 
::ffff:127.0.0.1:40542 join
::ffff:127.0.0.1:40556 join
::ffff:127.0.0.1:40572 join

# user1 再另外開三個 Terminal 分別建立連線
$ telnet localhost 30000
::ffff:127.0.0.1:40556 join
::ffff:127.0.0.1:40572 join
akiicat

# user2
$ telnet localhost 30000
::ffff:127.0.0.1:40572 join
akiicat

# user3
$ telnet localhost 30000
akiicat
```

先開一個 server，然後分別加入使用者並發話，記得最後要按 enter 才會發送，就可以看到發送的訊息會廣播到其他連線的 client。

如果你持續增加使用者，超過 10 個人訂閱同一個事件時，Node.js 會印出警告你，但可以透過 `.setMaxListeners(50)` 來增加上限。

```js
eventEmitter.setMaxListeners(50);
```

## 離開群組 removeListener

現在我們的使用者可以加入群組，並且可以廣播訊息給所有人，看起來很不錯，但是使用者加入後卻沒有辦法離開。我們希望使用者輸入 leave 的時候，會將自己從群組中移除，並且斷開連線。

```js
channel.on('leave', (id) => {
    channel.emit('broadcast', id, `${id} has left\n`); // 通知有人離開
    channel.clients[id].destroy(); // 斷開連線
    delete channel.clients[id];
});

const server = net.createServer(client => {
    const id = `${client.remoteAddress}:${client.remotePort}`;
    channel.emit('join', id, client);
    client.on('data', data => {         data = data.toString();
        if (data.trim() === 'leave') { // 如果使用者輸入 leave 則觸發 leave 事件
            channel.emit('leave', id);
        } else {
            channel.emit('broadcast', id, data);
        }
    });
})
```

看起來功能已經完成了，但是有一個小問題。在 `join` 事件中，使用者訂閱的 `broadcast` 事件沒有清除。如果有人離開之後，就會在後續的廣播嘗試寫入不存在的連線，這會造成 Node.js 的錯誤。

因此我們在 `leave` 時需要用 `.removeListener` 來取消訂閱，注意 `removeListener` 帶入的 callback 必須跟 `on` 的 callback 一樣，

```js
// removeListener 帶入的 callback 需要跟 on 的 callback 一樣
const callback = () => {...}
.on('broadcast', callback)
.removeListener('broadcast', callback)
```

所以我們需要修改一下 `server.js`：
- 新增 `channel.subscriptions`，並在 `join` 事件中，每當有使用者加入時儲存每個 client 的 callback。
- 在 `leave` 事件中 `removeListener` 將對應使用者的 broadcast callback 移除。

```js
const net = require('net');

const EventEmitter = require('events').EventEmitter;
const channel = new EventEmitter;

channel.clients = {};
channel.subscriptions = {}; // 新增 subscriptions 物件用來儲存 client 的 broadcast callback

channel.on('join', (id, client) => {
    channel.clients[id] = client;
    channel.subscriptions[id] = (senderId, message) => { // 儲存 client 的 broadcast callback
        if (id != senderId) {
            channel.clients[id].write(message);
        }
    };
    channel.on('broadcast', channel.subscriptions[id]); // client 訂閱 broadcast 事件
    channel.emit('broadcast', id, `${id} join\n`); // client 訂閱 broadcast 事件
});

channel.on('leave', (id) => {
    channel.removeListener('broadcast', channel.subscriptions[id]); // client 取消訂閱 broadcast 事件
    channel.emit('broadcast', id, `${id} has left\n`);
    channel.clients[id].destroy();
    delete channel.clients[id];
    delete channel.subscriptions[id]; // 從物件中移除
});

const server = net.createServer(client => {
    const id = `${client.remoteAddress}:${client.remotePort}`;
    channel.emit('join', id, client);
    client.on('data', data => {         data = data.toString();
        if (data.trim() === 'leave') {
            channel.emit('leave', id);
        } else {
            channel.emit('broadcast', id, data);
        }
    });
})

server.listen(30000);
```

在 Terminal 測試使用者能否正常離開群組：

```sh
$ node server.js

# user1 使用者離開
$ telnet localhost 30000
::ffff:127.0.0.1:54538 join
::ffff:127.0.0.1:54554 join
Hello
leave
Connection closed by foreign host.

# user2 使用者離開後照樣可正常廣播
$ telnet localhost 30000
::ffff:127.0.0.1:54554 join
Hello
::ffff:127.0.0.1:54528 has left
Hi

# user3
$ telnet localhost 30000
Hello
::ffff:127.0.0.1:54528 has left
Hi
```

## 關閉伺服器 removeAllListeners

現在我們新增一個新功能，輸入 `shutdown` 指令讓所有人取消訂閱，並斷開所有連線。在前一章節所學的 `removeListener`，我們可以使用迴圈將每個使用者從 `broadcast` 事件移除。然而，我們可以更間單的使用 `.removeAllListeners('broadcast')` ，來一次性移除所有訂閱 `broadcast` 事件上的使用者。最後我們的完整程式碼如下：

```js
const net = require('net');

const EventEmitter = require('events').EventEmitter;
const channel = new EventEmitter;

channel.clients = {};
channel.subscriptions = {}; // 新增 subscriptions 物件用來儲存 client 的 broadcast callback

channel.on('join', (id, client) => {
    channel.clients[id] = client;
    channel.subscriptions[id] = (senderId, message) => { // 儲存 client 的 broadcast callback
        if (id != senderId) {
            channel.clients[id].write(message);
        }
    };
    channel.on('broadcast', channel.subscriptions[id]); // client 訂閱 broadcast 事件
    channel.emit('broadcast', id, `${id} join\n`); // client 訂閱 broadcast 事件
});

channel.on('leave', (id) => {
    channel.removeListener('broadcast', channel.subscriptions[id]); // client 取消訂閱 broadcast 事件
    channel.emit('broadcast', id, `${id} has left\n`);
    channel.clients[id].destroy();
    delete channel.clients[id];
    delete channel.subscriptions[id]; // 從物件中移除
});

channel.on('shutdown', () => {
    channel.emit('broadcast', '', 'Server shutdown');
    channel.removeAllListeners('broadcast');
        Object.keys(channel.clients).forEach(function(id) {         channel.clients[id].destroy();
        delete channel.clients[id];
        delete channel.subscriptions[id];
    })
});

const server = net.createServer(client => {
    const id = `${client.remoteAddress}:${client.remotePort}`;
    channel.emit('join', id, client);
    client.on('data', data => {         data = data.toString();
        if (data.trim() === 'shutdown') {
            channel.emit('shutdown', id);
        } else if (data.trim() === 'leave') {
            channel.emit('leave', id);
        } else {
            channel.emit('broadcast', id, data);
        }
    });
})

server.listen(30000);
```

測試一下，輸入 `shutdown` 後所有人將會斷開連線：

```sh
# server
$ node server.js

# user1
$ telnet localhost 30000
::ffff:127.0.0.1:51130 join
::ffff:127.0.0.1:51136 join
shutdown
Server shutdownConnection closed by foreign host.

# user2
$ telnet localhost 30000
::ffff:127.0.0.1:51136 join
Server shutdownConnection closed by foreign host.

# user3
$ telnet localhost 30000
Server shutdownConnection closed by foreign host.
```

只要 Event Loop 中還有待處理的非同步操作（例如未完成的 I/O、定時器、網絡請求等），Node.js process 就不會自動退出。這對於需要長期運行、持續監聽請求的 Web 伺服器來說是理想的行為；而對於命令行工具或一次性任務，則可能需要在所有非同步操作完成後主動退出。

## Reference

- [Node.js In Action][1]
- [Node.js Events][2]

[1]: https://www.manning.com/books/node-js-in-action
[2]: https://nodejs.org/api/events.html
