---
title:  "Sass 整理一些常用的方法"
date:   2016-09-19 16:25:27 +0800
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

<!--excerpt-->

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
