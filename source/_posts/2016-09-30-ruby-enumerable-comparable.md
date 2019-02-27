---
title:  Ruby 使用 Enumerable 和 Comparable
date:   2016-09-30 23:11:39 +0800
categories:
- Programming
tags:
- Ruby
---

## Enumerable

要使用 Enumerable Module 時，只需要兩個步驟：首先 include Enumerable Module，然後在定義 `each` 方法。

```ruby
class Book
  include Enumerable

  attr_accessor :tags

  def each
    @tags.each do |tag|
      yield tag
    end
  end
end

books = Books.new
books.count
```

這麼做的好處是，這樣就能使用所有 Enumerable Module 裡的 methods。

<!-- more -->

```ruby
Enumerable.instance_methods.sort
# => [:all?, :any?, :chunk, :chunk_while, :collect, :collect_concat, :count, :cycle, :detect, :drop, :drop_while, :each_cons, :each_entry, :each_slice, :each_with_index, :each_with_object, :entries, :find, :find_all, :find_index, :first, :flat_map, :grep, :grep_v, :group_by, :include?, :inject, :lazy, :map, :max, :max_by, :member?, :min, :min_by, :minmax, :minmax_by, :none?, :one?, :partition, :reduce, :reject, :reverse_each, :select, :slice_after, :slice_before, :slice_when, :sort, :sort_by, :take, :take_while, :to_a, :to_h, :zip]
```

## Comparable

Comparable 也是一樣，include Comparable Module，然後在定義 `<=>` 方法。

```ruby
class Book
  include Comparable

  attr_accessor :name

  def <=>(other)
    @name <=> other.name
  end
end

a = Book.new
b = Book.new
c = Book.new
a.name = 'akii'
b.name = 'books'
c.name = 'cat'

a < b                  # => true
b.between?(a, c)       # => true
[c, b, a].sort         # => "akii", "books", "cat"
```

像是 array 裡的 sort 則是使用 `<=>` 來定義的，所以繼承 Array 的時候，不需要改到 sort 裡面的方法，只需要將這個符號 `<=>` 定義好就能使用囉。

```ruby
Comparable.instance_methods.sort
# => [:<, :<=, :==, :>, :>=, :between?]
# sort, sort_by
```
