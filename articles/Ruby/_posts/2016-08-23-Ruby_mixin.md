---
title:  "Ruby Mix-in Module include 的規則"
date:   2016-08-23 17:18:32 +0800
---

## 繼承關係

Ruby 只能繼承唯一一個 parent 的單純繼承，但藉由 Mix-in 機制，可以在單純繼承的架構下，在多個類別之間共享一些工能。

```ruby
class Book
  include Comparable
end

Book.ancestors      # => [Book, Comparable, Object, Kernel]
```
Comparable 雖然不是 parent，但是運作情形差不多

```sh
┌────────────┐                                    ┌────────────┐
│   Object   │                                    │   Object   │
└────────────┘                                    └────────────┘
      ↑                                                 ↑
      │           ┌────────────┐                  ┌────────────┐
      │←──────────│ Comparable │                  │ Comparable │
      │           └────────────┘                  └────────────┘
      │                                                 ↑
┌────────────┐                                    ┌────────────┐
│    Book    │                                    │    Book    │
└────────────┘                                    └────────────┘
```

<!--excerpt-->

## Module include 的規則

Ruby 在 Mix-in 時方法搜尋的順序。

### 方法名重複

與繼承關係相同，當類別本身定義有同名的方法時，以類別本身的為主。

```ruby
module M
  def meth
    puts "M meth"
  end
end
class C
  include M         # => 讀入 M
  def meth
    puts "C meth"
  end
end

c = C.new
c.meth              # => "C meth"
```

### 優先順序

在一個類別裡讀入不只一個 module 時，以最後讀入的優先。

```ruby
module M1
  # ...
end
module M2
  # ...
end

class C
  include M1        # => 讀入 M1
  include M2        # => 讀入 M2
end

p C.ancestors       # => [C, M2, M1, Object, Kernel, BasicObject]
```

```sh
┌────────────┐                                    ┌────────────┐
│   Object   │                                    │   Object   │
└────────────┘                                    └────────────┘
      ↑                                                 ↑
      │           ┌──────┐                           ┌──────┐
      │←──────────│  M1  │                           │  M1  │
      │           └──────┘                           └──────┘
      │                                                 ↑
      │           ┌──────┐                           ┌──────┐
      │←──────────│  M2  │                           │  M2  │
      │           └──────┘                           └──────┘
      │                                                 ↑
┌────────────┐                                    ┌────────────┐
│    Book    │                                    │    Book    │
└────────────┘                                    └────────────┘
```


#### 多層 Module

當讀入有多層現象時，搜尋順序拉成現狀。

```ruby
module M1
  # ...
end
module M2
  # ...
end
module M3
  include M2        # => 讀入 M2
end

class C
  include M1        # => 讀入 M1
  include M3        # => 讀入 M3
end

p C.ancestors       # => [C, M3, M2, M1, Object, Kernel, BasicObject]
```

```sh
┌────────────┐                                    ┌────────────┐
│   Object   │                                    │   Object   │
└────────────┘                                    └────────────┘
      ↑                                                 ↑
      │         ┌──────┐                             ┌──────┐
      │←────────│  M1  │                             │  M1  │
      │         └──────┘                             └──────┘
      │                                                 ↑
      │         ┌──────┐         ┌──────┐            ┌──────┐
      │←────────│  M3  │←────────│  M2  │            │  M2  │
      │         └──────┘         └──────┘            └──────┘
      │                                                 ↑
┌────────────┐                                       ┌──────┐
│    Book    │                                       │  M3  │
└────────────┘                                       └──────┘
                                                        ↑
                                                  ┌────────────┐
                                                  │    Book    │
                                                  └────────────┘
```

#### Module 重複忽略

讀入相同的模組兩次以上時，第二次會被忽略。

```ruby
module M1
  # ...
end
module M2
  # ...
end

class C
  include M1        # => 讀入 M1
  include M2        # => 讀入 M2
  include M1        # => 讀入 M1，重複忽略
end

p C.ancestors       # => [C, M2, M1, Object, Kernel, BasicObject]
```
