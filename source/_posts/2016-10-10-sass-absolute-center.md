---
title:  Sass 絕對置中 水平置中 垂直置中
date:   2016-10-10 02:42:06 +0800
categories:
- Programming
tags:
- Sass
- Css
---

## 絕對置中 absolute center

顧名思義就是水平置中加上垂直置中，讓內層的 `div` 會在外層的 `div` 的正中間。

### Html

```html
<div class='outer'>
  <div class='inner'>
  </div>
</div>
```

### Scss


```scss
.outer {
  position: relative;

  .inner {
    position: absolute;
    margin: auto;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
  }
}

.outer {
  width: 500px;
  height: 500px;
  background-color: rgba(0,0,0,.2);
}

.inner {
  width: 100px;
  height: 100px;
  background-color: rgba(0,0,0,.2);
}
```

任意註解掉 `top` `left` `bottom` `right` 的參數，可以將內層的 `div` 對齊在外層的九個位置上，即可解決水平置中與垂直置中的問題。

<!-- more -->

### Preview

<style>
.outer {
  width: 500px;
  height: 500px;
  background-color: rgba(0,0,0,.2);

  position: relative;

  margin-bottom: 32px;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: rgba(0,0,0,.2);

  position: absolute;
  margin: auto;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}
</style>

<div class='outer'>
  <div class='inner'>
  </div>
</div>

- [Codepen](http://codepen.io/AkiiCat/pen/EgEvbA)
