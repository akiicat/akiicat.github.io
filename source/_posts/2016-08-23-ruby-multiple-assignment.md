---
title:  Ruby 多重指派 Multiple assignment
date:   2016-08-23 04:50:24 +0800
categories:
- Programming
tags:
- Ruby
---

## 多重指派 Multiple assignment

#### 一口氣做不只一個指派的動作時

盡量用在有關係的變數上，互相沒有關係的多重指派，只會讓程式很難懂。

```ruby
a, b, c = 1, 2, 3
```

#### 回傳方法不只一個

定義 Ruby 的方法時，有時候會想要傳回不只一個物件。這時傳回值也可以使用多重指派的方式取得回傳值。

```ruby
def location()
  ...
  return x, y, z
end

a, b, c = location()
```

<!-- more -->

#### 取得陣列的元素

左邊的變數不只一個的時候，則會自動進行多重指派：

```ruby
arr = [1, 2]
a, b = ary
p a         # => 1
p b         # => 2
```
若只想取得第一個元素時：

```ruby
arr = [1, 2]
a, = ary
p a         # => 1
```
展開 array 作為 method 的引數：

```ruby
def foo(a, b)
  a + b
end

ary = [1, 2]
p foo(*ary)         # => 3
```
相反的，如果無法確定有多少參數的方法，可以將方法的參數作為陣列處理：

```ruby
def foo(*args)
  sum = 0
  args.each { |i| sum += i }
  return sum
end

ary = [1, 2, 3]
p foo(*ary)         # => 6
```
至少會有一個引數的方法時：

```ruby
def meth(arg, *args)
  p arg
  p args
end

meth(1)         # => 1
                # => []
meth(1,2,3)     # => 1
                # => [2, 3]
```
數量不定的引數全部以陣列的形式傳遞給參數 args
