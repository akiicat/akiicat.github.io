---
title:  "Javascript Canvas Load Image and Cross Origin Problem"
date:   2016-10-11 04:44:11 +0800
---


## Canvas Usage

html canvas 簡單來說就是個 img，能操作的東西十分的多，建議使用 Javascript，如果用 jquery 操作會十分麻煩而且不易使用。

```js
// 建立 canvas，context 暫時沒有用到
var canvas  = document.createElement('canvas');
var context = canvas.getContext('2d');

// 設定 canvas 的大小，就像是一張圖片的大小是 400 x 300
canvas.width  = 800;
canvas.height = 300;

// css 裡的 style，用來縮放還有很多有的沒的......
canvas.style.width  = '400px';
canvas.style.height = '300px';
```

## Load Image From Url

用 Javascript Image 物件來建立圖片。

```js
var img = new Image();
img.onload = function(){
  // 完整載入圖片之後才會執行這一段
};
img.src = 'http://i.imgur.com/ImageHere';
```

<!--excerpt-->

## Render To Canvas

將圖片放到 canvas 上。

```html
<div id='myCanvas'></div>
```

```js
var img = new Image();
img.onload = function(){
  var canvas  = document.createElement('canvas');
  var context = canvas.getContext('2d');

  canvas.width  = 800;
  canvas.height = 300;
  canvas.style.width  = '400px';
  canvas.style.height = '300px';

  // 將 image 畫在 canvas 上
  context.drawImage(this, 0, 0);

  $('#myCanvas').html(canvas);
};
img.src = 'http://i.imgur.com/ImageHere';
```


## Cross Origin Security Error

如果使用 canvas 中的 `canvas.getImageData()` 獲取圖片的顏色，有可能會出現這個錯誤。

```
Uncaught SecurityError: Failed to execute 'getImageData' on 'CanvasRenderingContext2D': The canvas has been tainted by cross-origin data.
```

如果圖片中 header 中允許從任何地方存取

```
Access-Control-Allow-Origin: '*'
```

可以加上 `crossOrigin` 這個參數來抓取圖片的資訊。

```js
var img = new Image();
img.crossOrigin = 'Anonymous';
img.src = 'http://i.imgur.com/ImageHere';
```

建議第一次載入時將圖片用 `canvas.toDataURL()` 轉成 dataUrl，之後要在使用 `canvas.getImageData()` 時就不會有 SecurityError 的問題了。
