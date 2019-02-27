---
title:  Ruby 使用 attr accessor reader writer
date:   2016-09-29 03:10:39 +0800
categories:
- Programming
tags:
- Ruby
---

## attr

假設我們有一個 Book 的 class，裡面有他的參數名稱 `name`，然後想要對他存取參數的時候，會像這樣寫

```ruby
class Book
  @name = nil
  def name
    @name
  end
  def name=(name)
    @name = name
  end
  # ....
end

book = Book.new
book.name           # => nil
book.name = 'Cat'
book.name           # => 'Cat'
```

<!-- more -->

可是萬一有很多個參數的時候，像是 `author`, `publish`, ... 就會變得很麻煩，而 ruby 只需要短短的一行 `attr_accessor` 將它來簡化。

```ruby
class Book
  attr_accessor :author
  attr_accessor :publish
  attr_accessor :name
  # @name = nil
  # def name
  #   @name
  # end
  # def name=(name)
  #   @name = name
  # end
end

book = Book.new
book.name = 'Cat'
book.publish = true
book.author = 'Akii'
puts book.name, book.author, book.publish
# Cat Akii true
```

還有其他可以使用，但是 `attr_accessor` 算是最常用使用的一個。

- `attr_accessor`: 讀取跟寫入
- `attr_reader`: 只能讀取 read only
- `attr_writer`: 只能寫入 write only
