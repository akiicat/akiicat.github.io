---
title:  "Css 網面符合瀏覽器大小"
date:   2016-12-30 22:19:27 +0800
---

## Viewport Percentage Lengths

Viewport Percentage Lengths 有四個不同的單位，分別如下：

- vh (viewport height): 瀏覽器高度百分比。
- vw (viewport width): 瀏覽器寬度百分比。
- vmin (viewport minimum length): vh 與 vw 中較小的值。
- vmax (viewport maximum length): vh 與 vw 中較大的值。

如果想要讓某個元素佔滿整個瀏覽器，就只要像下面這樣寫，很簡單的：

```css
div {
  width: 100vw;
  height: 100vh;
}
```

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

## vmin 與 vmax 的差別與用法

1vmin 的定義是 1vh 和 1vw 中最小的值，假設現在瀏覽器的高是 1100px 寬是 700px，這樣 1vh 1vw 就會是 11px 7px，所以 1vmin 就會是 7px。相反的 vmax 則會挑選最大的值。

如果讓 div 的寬與高都是 100vmin：

```css
div {
  width: 100vmin;
  height: 100vmin;
}
```

![Pic1]({{ site.url }}/assets/images/css/fit_browser_vmin.svg)

{% capture svg %}{% include assets/images/css/fit_browser_vmin.svg %}{% endcapture %}
{% assign svgsplit = svg | split: 'svg11.dtd">' %}
{% assign svgpart = svgsplit[1] | escape %}
{{ svgpart }}

如果讓 div 的寬與高都是 100vmax：

```css
div {
  width: 100vmax;
  height: 100vmax;
}
```

![Pic1]({{ site.url }}/assets/images/css/fit_browser_vmax.svg)

{% capture svg %}{% include assets/images/css/fit_browser_vmax.svg %}{% endcapture %}
{% assign svgsplit = svg | split: 'svg11.dtd">' %}
{% assign svgpart = svgsplit[1] | escape %}
{{ svgpart }}