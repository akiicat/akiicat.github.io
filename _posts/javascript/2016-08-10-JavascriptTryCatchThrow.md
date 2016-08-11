---
title:  "Javascript try catch throw 使用"
date:   2016-08-10 22:26:29 +0800
---

## 基本用法
程式在 `try` 中執行，如過在 `try` 發生錯誤則會把錯誤丟到 `catch`，而 `final` 不管有沒有錯誤都會執行

```js
try {
    ...
}
catch(e) {
    console.log(e);
}
final {
    ...
}
```

## throw
`throw` 則會把錯誤丟到外層的 `catch` 中

```js
try {
    ...
    try {
        ...
    }
        catch(e) {
        console.log(e);
        throw(e);       // 把錯誤丟到外層
    }

    if (something failed) {
      throw(sth);       // throw something;
    }
}
catch(e) {
    console.log(e);
}
final {
    ...
}
```
