---
title:  "Sass 文字置中"
date:   2016-10-10 02:42:06 +0800
---


## 文字置中

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


- [Codepen](http://codepen.io/AkiiCat/pen/WGKaGp)
