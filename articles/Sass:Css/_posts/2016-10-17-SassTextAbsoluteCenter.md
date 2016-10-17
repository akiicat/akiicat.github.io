---
title:  "Sass 文字置中"
date:   2016-10-17 06:04:16 +0800
---


## 文字絕對置中

範例如下，`div` 內部的文字置中，`h1` 只有將字型放大的功能。

### Html

```html
<div class='abs-text-center'>
  <h1>
    Hello World!
  </h1>
</div>
```

### Scss

- 垂直置中：讓 `height` 跟 `line-height` 的高度相同。
- 水平置中：讓 `text-align` 為 `center`。

```scss
$box-height: 100px;

.abs-text-center {
  height: $box-height;
  line-height: $box-height;

  text-align: center;
}
```

<!--excerpt-->

### Preview

<style>
.abs-text-center {
  width: 100%;
  color: #FFFFFF;
  background: #333333;
  font-size: 36px;

  height: 100px;
  line-height: 100px;

  text-align: center;
}
</style>

<div class='abs-text-center'>
  <span>
    Hello World!
  </span>
</div>

## 右側垂直置中

### Scss

```scss
$box-height: 100px;

.text-center {
  height: $box-height;
  line-height: $box-height;

  text-align: center;

  float: right;
}
```

<style>
.text-center {
  width: 100%;
  color: #FFFFFF;
  background: #333333;
  font-size: 36px;

  height: 100px;
  line-height: 100px;
}

.text-center > span {
  float: right;
}
</style>

<div class='text-center'>
  <span>
    Hello World!
  </span>
</div>


## 右側垂直置中與 <h1> 同行

### Html

```html
<div class='inline-text-center'>
  <h1>Hello World!</h1>
  <span>Hi</span>
</div>
```

### Scss

```scss
$box-height: 100px;

.inline-text-center {
  height: $box-height;
  line-height: $box-height;

  text-align: center;
  float: right;
}
```

<style>
.inline-text-center {
  width: 100%;
  color: #FFFFFF;
  background: #333333;
  font-size: 36px;

  height: 100px;
  line-height: 100px;
}
.inline-text-center > span {
  float: right;
}
</style>

<div class='inline-text-center'>
  <h1>Hello World!</h1>
  <span>Hi</span>
</div>

- [Codepen](http://codepen.io/AkiiCat/pen/WGKaGp)
