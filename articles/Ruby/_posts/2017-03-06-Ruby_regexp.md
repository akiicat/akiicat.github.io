---
title:  "Ruby Regexp MatchData"
date:   2017-03-06 14:49:35 +0800
---

## =~ Operator

回傳比對的位置到的起始位置，沒有比對到則回傳 nil

```ruby
/cat/ =~ 'hiakiicat'               # => 6
/tac/ =~ 'hiakiicat'               # => nil
```

## match method

有 match 到會回傳 MatchData，如果沒有 match 到則會回傳 nil。

```ruby
m = /akii(.+)$/.match('hiakiicat')
# => #<MatchData "akiicat" 1:"cat">
```

括號起來的資料 `(...)` 才會另外存成一個值。如果不想要另外存成一個值，可以在括號裡面打 `(?:...)`。

獲取資料：

- `m[0]` 為 match 到的所有資料。
- `m[1]` 為第一個括號裡面所比對到的資料，以此類推。

```ruby
m[0]            # => "akiicat"
m[1]            # => "cat"
m[2]            # => nil
```

<!--excerpt-->

使用 captures 的話，可以以 array 的形式拿到括號內的資料。

```ruby
m.captures
# => ["cat"]
```

獲得起始位置和結束位置：

```ruby
m.begin(0)              # => 2
m.end(0)                # => 9
m.offset(0)             # => [2, 9]

m.begin(1)              # => 6
m.end(1)                # => 9
m.offset(1)             # => [6, 9]
```

其他用法：

```ruby
m.length                # => 2
m.regexp                # => /akii(.+)$/
m.pre_match             # => "hi"   # match 之前的字串
m.post_match            # => ""     # match 之後的字串
m.names                 # => []     # 需要使用 (?<foo>...)
```

範例：

```ruby
if m = /akii(.+)$/.match('hiakiicat')
  puts m[1]
end
```
