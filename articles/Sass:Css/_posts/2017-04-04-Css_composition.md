---
title:  "Css 五倍紅寶石之排版歷史"
date:   2017-04-04 18:58:02 +0800
---

之前在學習 html css 都是網路上找一找，看別人怎麼寫，然後模仿他們的這樣學會的。我想沒有開始我的[五倍紅寶石](https://5xruby.tw/)實習之旅，也不太有機會聽到 [Amos](https://www.facebook.com/banPrint?fref=ts)老師的課。

自己雖然有寫過 css，但是跟講師最大的差別就是，不論是多簡單還是多難的東西，都可以很清楚有條理的陳述出來，說實在的聽完會有一種功力大增的感覺，對於完全不會的新手或是不知道自己有沒有漏學到什麼的老手來說，都是一門非常值得去聽的課。

![5xruby]({{ site.url }}/assets/images/5xruby/5xruby.jpg)

## 排版歷史

在 css 的戰國時代裡，大部分在網路上找到的排版方法，大多是 float 跟 inline-block 這兩種，沒聽課的話還真的不知道排版的歷史，下面是依序由早到晚的排版順序：

- table
- float
- inline-block
- flex
- grid

之後會想來玩玩看 flex 跟 grid，反正是自己的部落格，IE8 以下的用戶就不好意思了。用 table 排版已經太舊太久遠就先不管。然而現在最常用的兩種排版方法 float 跟 inline-block ，還是會有一些問題存在：

<!--excerpt-->

### float 問題

水平置中困難，需要精算推擠的距離。

假設寬度為 100px 的區塊，在 `left: 50%` 置中過後，則必須使用 `margin-left: -50px` 把區塊給推回來。

```html
<div class="center">center</div>
```

```css
div {
  display: inline-block;
  background: #ccc;
  width: 100px;
  height: 50px;
  text-align: center;
  line-height: 50px;
}

.center {
  position: relative;
  left: 50%;
  margin-left: -50px;
}
```

<style>
  .float-problem {
    width: 100%;
    border: 1px solid black;
  }

  .float-problem .center {
    display: inline-block;
    background: #ccc;
    width: 100px;
    height: 50px;
    text-align: center;
    line-height: 50px;

    position: relative;
    left: 50%;
    margin-left: -50px;
  }
</style>

<div class="float-problem">
  <div class="center">center</div>
</div>

這麼做雖然可以置中，但是只要改變了一下寬度或是加個 padding，版面一下子就跑掉了。要這麼做不如使用 [absolute](/blogger/2016/10/09/Sass_absolute_center/) 這種強大的定位方式，不管你怎麼加 padding 或是改變寬度，都不會發生問題。

### inline-block 問題

在 inline-block 的物件之間，會有一個的空白文字大小的寬度(大約 4px)：

```html
<ul>
  <li>a</li>
  <li>b</li>
  <li>c</li>
</ul>
```

```css
li {
  display: inline-block;
  background: #ccc;
  padding: 10px 30px;
}
```

<style>
  .inline-block-problem li {
    display: inline-block;
    background: #ccc;
    padding: 10px 30px;
  }
</style>

<div class="inline-block-problem">
  <ul>
    <li>a</li>
    <li>b</li>
    <li>c</li>
  </ul>
</div>

不想要那醜醜 4px 的間隔，就必需要在排版的時候調整文字大小：

```html
<ul>
  <li>a</li>
  <li>b</li>
  <li>c</li>
</ul>
```

```css
ul {
  font-size: 0;
}
li {
  display: inline-block;
  background: #ccc;
  padding: 10px 30px;
  font-size: 16px;
}
```

<style>
  .inline-block-solve ul {
    font-size: 0;
  }
  .inline-block-solve li {
    display: inline-block;
    background: #ccc;
    padding: 10px 30px;
    font-size: 16px;
  }
</style>

<div>
  <div class="inline-block-solve">
    <ul>
      <li>a</li>
      <li>b</li>
      <li>c</li>
    </ul>
  </div>
</div>

但仔細想一下，為什麼要在排版的時候修改文字的大小啊，雖然問題是解決了，可是這根本是降低程式碼維護的東西啊，而且如果那天想要修改文字大小......恩 就到時候再說。

## Summary

之後就來做一些些的 block, inline, inline-block 小整理：

- float 問題： 水平置中困難，需要精算推擠的距離
- inline 問題： 垂直高度不會推擠到，可能會吃到下面的文字
- inline-block 問題：中間會有一個文字的空格，需要調整文字大小

|              | block | inline       | inline-block |
| ------------ | ----- | ------------ | ------------ |
| width height | v     | x            | v            |
| padding      | v     | left right   | v            |
| margin       | v     | left right   | v            |
| 水平排列      | float | default      | default      |
| 空白字元      | 清除   | 保留          | 保留         |
| 水平置中      | x     | text-align   | text-align   |
| 垂直對象      | x     | v            | v            |

- [5xruby](https://5xruby.tw/)
- [Amos](https://www.facebook.com/banPrint?fref=ts)
