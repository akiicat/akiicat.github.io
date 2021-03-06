---
title:  Sass 圓形階層漸層
date:   2016-10-07 22:21:46 +0800
categories:
- Programming
tags:
- Sass
- Css
---

## Circle Gradient

### Sass

參數說明：

- shift: 從中心點移動多少位置，如果是 `120px` 則會從上一層的 `div` 左上角的原點，往右邊且往下個移 `120px`。
- step: 多少 pixel 換另一個顏色。
- colors: 每一層的顏色，由內到外排序。

```scss
$shift: 120px;
$step: 30px;
$colors: ( #fbffa0 #cfff76 #a2e27a #4ab9aa #2c678d #07171f );

@for $i from 0 to length($colors) {
  $color: unquote(nth($colors, $i + 1));

  .layer-#{$i} {
    position: absolute;
    background: transparent;

    border-radius: ($i + 1) * $step;
    border: $step solid $color;
    padding: 0px;

    top: $shift - ($i + 1) * $step;
    left: $shift - ($i + 1) * $step;
    width: 2 * $i * $step;
    height: 2 * $i * $step;
  }
}
```

<!-- more -->

### Html

```html
<div>
  <div class='layer-0'></div>
  <div class='layer-1'></div>
  <div class='layer-2'></div>
  <div class='layer-3'></div>
  <div class='layer-4'></div>
  <div class='layer-5'></div>
</div>
```

### Preview

<style>
.layer-0 {
  position: absolute;
  background: transparent;
  border-radius: 30px;
  border: 30px solid #fbffa0;
  padding: 0px;
  top: 90px;
  left: 90px;
  width: 0px;
  height: 0px;
}

.layer-1 {
  position: absolute;
  background: transparent;
  border-radius: 60px;
  border: 30px solid #cfff76;
  padding: 0px;
  top: 60px;
  left: 60px;
  width: 60px;
  height: 60px;
}

.layer-2 {
  position: absolute;
  background: transparent;
  border-radius: 90px;
  border: 30px solid #a2e27a;
  padding: 0px;
  top: 30px;
  left: 30px;
  width: 120px;
  height: 120px;
}

.layer-3 {
  position: absolute;
  background: transparent;
  border-radius: 120px;
  border: 30px solid #4ab9aa;
  padding: 0px;
  top: 0px;
  left: 0px;
  width: 180px;
  height: 180px;
}

.layer-4 {
  position: absolute;
  background: transparent;
  border-radius: 150px;
  border: 30px solid #2c678d;
  padding: 0px;
  top: -30px;
  left: -30px;
  width: 240px;
  height: 240px;
}

.layer-5 {
  position: absolute;
  background: transparent;
  border-radius: 180px;
  border: 30px solid #07171f;
  padding: 0px;
  top: -60px;
  left: -60px;
  width: 300px;
  height: 300px;
}

.layer {
  position: relative;
  height: 330px;
  margin: 120px 0 0 120px;
}
</style>

<div class='layer'>
  <div class='layer-0'></div>
  <div class='layer-1'></div>
  <div class='layer-2'></div>
  <div class='layer-3'></div>
  <div class='layer-4'></div>
  <div class='layer-5'></div>
</div>

- [Codepen](http://codepen.io/AkiiCat/pen/BLYGrK)
