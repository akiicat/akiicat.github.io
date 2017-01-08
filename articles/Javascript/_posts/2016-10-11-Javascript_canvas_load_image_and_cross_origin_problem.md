---
title:  "Javascript Canvas Load Image with Cross Origin"
date:   2016-10-11 04:44:11 +0800
---


## Canvas Usage

html canvas 簡單來說就是個 img，能操作的東西十分的多，建議使用 Javascript，如果用 jquery 操作會十分麻煩而且不易使用。

```js
// 建立 canvas，context 暫時沒有用到
var canvas  = document.createElement('canvas');
var context = canvas.getContext('2d');

// 設定 canvas 的大小，就像是一張圖片的大小是 800 x 400
canvas.width  = 800;
canvas.height = 400;

// css 裡的 style，用來縮放，還有很多有的沒的......
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
  canvas.height = 400;
  canvas.style.width  = '400px';
  canvas.style.height = '300px';

  // 將 image 畫在 canvas 上
  context.drawImage(this, 0, 0);

  $('#myCanvas').html(canvas);
};
img.src = 'http://i.imgur.com/ImageHere';
```


## Cross Origin Security Error

`canvas.getImageData()` 是來獲取 canvas 圖片中一小塊的資料，像是顏色等等，但如果這時候使用這個方法來獲取圖片的顏色，有可能會出現這個錯誤。

```
Uncaught SecurityError: Failed to execute 'getImageData' on 'CanvasRenderingContext2D': The canvas has been tainted by cross-origin data.
```

這時可以在 chrome developer tools -> network 看圖片載入的時的訊息，如果圖片中 header 中允許從任何地方存取，如下：

```
Access-Control-Allow-Origin: '*'
```

可以加上 `crossOrigin` 這個參數來抓取圖片的資訊。

```js
var img = new Image();
img.onload = function(){
  // 完整載入圖片之後才會執行這一段
};
img.crossOrigin = 'Anonymous';
img.src = 'http://i.imgur.com/ImageHere';
```

如果有需要用到重複的同一張圖片的話，建議第一次載入時先將圖片用 `canvas.toDataURL()` 轉成 dataUrl，之後要在使用 `canvas.getImageData()` 時就不會有 SecurityError 的問題了。

```js
var image = new Image();
image.onload = function(){
  var canvas = document.createElement('canvas');
  var context = canvas.getContext('2d');

  // 將 image 畫在 canvas 上
  context.drawImage(this, 0, 0);

  var imgTag = document.getElementById('myImg');
  var dataUrl = canvas.toDataURL();
  imgTag.src = dataURL;
};
image.crossOrigin = 'Anonymous';
image.src = url;
```

- [cross origin me](http://crossorigin.me/)
