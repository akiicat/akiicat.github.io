---
title:  Sass 整理一些常用的方法
date:   2016-09-19 16:25:27 +0800
categories:
- Programming
tags:
- Sass
- Css
---

## Sass 整理

每次都要找實在是有點浪費時間，不如乾脆一次整理完。

### If ... Else ...

```scss
$boolean: true !default;

div {
  @if( $boolean ){
    display: block;
  }
  @else {
    display: none;
  }
}
```

### For

```scss
@for $i from 1 through 5 {
  .wd-#{$i} {
    width: #{$i * 20%};
  }
}
```

<!-- more -->

### List and Each

```scss
$list: adam john wynn mason kuroir;

@each $author in $list {
  .photo-#{$author} {
    background: image-url("avatars/#{$author}.png") no-repeat;
  }
}
````

### nth List

```scss
$list: (
  one   black,
  two   black + 20,
  three black + 40,
  four  black + 60
);

@each $p in $list {
  .#{ nth($p, 1) } {
    background: #{ nth($p, 2) };
  }
}
```

### Map

```scss
$icons: (
  1: #07171f,
  2: #2c678d,
  3: #4ab9aa,
  4: #a2e27a,
  5: #cfff76,
  6: #fbffa0
);

@each $index, $color in $icons {
  .bg-#{$index} {
    background-color: $color;
  }
}

.test {
  background-color: map-get($icons, 1);
}
```

```css
.bg-1 { background-color: #07171f; }
.bg-2 { background-color: #2c678d; }
.bg-3 { background-color: #4ab9aa; }
.bg-4 { background-color: #a2e27a; }
.bg-5 { background-color: #cfff76; }
.bg-6 { background-color: #fbffa0; }
.test { background-color: #07171f; }
```

### Mixin

混入 class 中，可以傳入參數。

```scss
@mixin width-add($a, $b: 20px){
  width: $a + $b;
}

.my-module {
  @include width-add(10px);
}
```

### Function

回傳特定值，有複雜的計算時。

```scss
@function width-add($a, $b: 20px){
  @return $a + $b;
}

.my-module {
  width: width-add(10px);
}
```

### Extend

合併 class 時使用，不可傳入參數。

```scss
%all-h1 {
  font-size: 20px;
}

.header h1 {
  @extend %all-h1;
  color: #000;
}

.content h1 {
  @extend %all-h1;
  color: green;
}
```


- [Sass Online Playground](http://www.sassmeister.com/)
- [Sass Built-in Function](http://sass-lang.com/documentation/Sass/Script/Functions.html)
