---
title:  "Ruby on Rails foreign key must exist 的問題"
date:   2017-01-11 20:24:53 +0800
---

rails 在[這次](https://github.com/rails/rails/pull/18937)的更動之後，如果 belongs_to 的 foreign key 不存在的時，會產生驗證錯誤。

```ruby
class Author < ActiveRecord::Base
end

class Book < ActiveRecord::Base
  belongs_to :author
end

book = Book.create(title: "book")
book.errors.full_messages # => ["Author must exist"]
```

上面的例子可以看到，我們沒辦法建立 Book 的紀錄。

## options

- `required`: 如果設為 `true` 的話，會驗證關聯是否存在。在 rails 5 預設為 `true`。
- `optional`: 如果設為 `true` 的話，不會驗證關聯是否存在。

上面兩個選項都是驗證關聯本身，不是驗證 foreign key 的 id。

<!--excerpt-->

```ruby
class Book < ActiveRecord::Base
  belongs_to :author, required: true
end

book = Book.create(title: "book")
# => #<Book id: nil, title: "book", author_id: nil, created_at: nil, updated_at: nil>

book.errors.full_messages
# => ["Author must exist"]
```

rails 5 裡預設 `required` 的選項為 `true`，會驗證關聯是否存在，否則跳出 `must exist` 的錯誤。

```ruby
class Book < ActiveRecord::Base
  belongs_to :author, optional: true
end

book = Book.create(title: "book")
# => #<Book id: 1, title: "book", author_id: nil, created_at: nil, updated_at: nil>

book.errors.full_messages
# => []
```

`optional` 為 `true` 不會驗證關聯是否存在。

## default options

那如果我們想要在整個程式中使用同一種行為，可以在 `config/initializers/new_framework_defaults.rb` 裡面修改以下的選項：

```ruby
# config/initializers/new_framework_defaults.rb
# Require `belongs_to` associations by default. Previous versions had false.
Rails.application.config.active_record.belongs_to_required_by_default = true
```

rails 5 裡預設為 `true` 會驗證關聯是否存在。將此設為 `false` 可以關掉這個驗證。
