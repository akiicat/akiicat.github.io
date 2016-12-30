---
title:  "Css 符合螢幕大小"
date:   2016-10-17 06:04:16 +0800
---

## Viewport Percentage Lengths

Viewport Percentage Lengths 有四個不同的單位，分別如下：

- vh (viewport height): 瀏覽器高度百分比。
- vw (viewport width): 瀏覽器寬度百分比。
- vmin (viewport minimum length): vh 與 vw 中較小的值。
- vmax (viewport maximum length): vh 與 vw 中較大的值。

## 100vh 與 100% 的差別

先舉個例子，讓外層 div 的高度只有 50px，內層 p 的高度分別為 100% 與 100vh，前者會是**上一層**的高度的百分比，後者是**瀏覽器**高度的百分比。

<!--excerpt-->

```html
<div id="one">
  <p>100% Height</p>
</div>

<div id="two">
  <p>100vh Height</p>
</div>
```

```css
div {
  height: 50px;
}

#one > p { height: 100%; }
#two > p { height: 100vh; }
```

<p data-height="265" data-theme-id="0" data-slug-hash="ozKYVN" data-default-tab="result" data-user="AkiiCat" data-embed-version="2" data-pen-title="viewport" class="codepen">See the Pen <a href="http://codepen.io/AkiiCat/pen/ozKYVN/">viewport</a> by AkiiCat (<a href="http://codepen.io/AkiiCat">@AkiiCat</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## vmin 與 vmax 的用法

1vmin 的定義是 1vh 和 1vw 中最小的值，假設現在瀏覽器的高是 1100px 寬是 700px，這樣 1vh 和 1vw 就會是 11px 和 7px，所以 1vmin 就會是 7px。
