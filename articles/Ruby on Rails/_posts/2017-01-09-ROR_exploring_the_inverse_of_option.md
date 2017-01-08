---
title:  "Ruby on Rails 探索 inverse_of"
date:   2017-01-09 02:55:10 +0800
---

## inverse_of

主要功能會去通知對方自己的狀態，可以解決物件不同步的問題。

在 belongs_to 裡的 :inverse_of 會去找尋所對應的 has_one has_many 上相同的名稱，來通知他們之間的關係。

在 has_one has_many 裡的 :inverse_of 會去找尋所對應的 belongs_to 上相同的名稱，來通知他們之間的關係。

如果沒有寫 :inverse_of 這參數，rails 會使用 heuristic algorithm 去猜測名稱，但在如果不是使用標準名稱的話會失效。

## Without inverse_of

沒有 :inverse_of 會發生物件不同步的問題，可以看以下的範例：

```ruby
class Author < ActiveRecord::Base
  has_many :books
end

class Book < ActiveRecord::Base
  belongs_to :author, inverse_of: false
end
```

<!--excerpt-->

沒有寫上 `inverse_of: false` 的話，rails 會自動找尋對應的關聯，所以這邊強制把它關掉。

```ruby
a = Author.first
a.object_id # => 70133005189780
a.books.first.author.object_id # => 70133005096140
a.books.first.author.object_id == a.object_id # => false
```

這邊 `a` 跟 `a.books.first.author` 應該要是同一個東西，可是他們的 object_id 卻是不同的。

```ruby
a = Author.first
b = a.books.first
a.name == b.author.name # => true
a.name = 'Akii'
a.name == b.author.name # => false
```

所以更改了其中一個物件，不會更新到另一個物件，導致物件不同步。

## With inverse_of

用 :inverse_of 來通知他們之間的關係。

```ruby
class Author < ActiveRecord::Base
  has_many :books
end

class Book < ActiveRecord::Base
  belongs_to :author, inverse_of: :books
end
```

即使沒有寫 `inverse_of: :books`，rails 會自動找尋對應的關聯，不會影響這個範例的結果。

```ruby
a = Author.first
a.object_id # => 70133007092660
a.books.first.author.object_id # => 70133007092660
a.books.first.author.object_id == a.object_id # => true
```

這樣只會載入一個物件，避免資料不一致，還能提高程式的效率。

```ruby
a = Author.first
b = a.books.first
a.name == b.author.name # => true
a.name = 'Akii'
a.name == b.author.name # => true
```

## inverse_of 的限制：

- 不能與 :through 關聯同時使用。
- 不能與 :polymorphic 關聯同時使用。
- 不能與 :as 選項同時使用。
- 對 belongs_to 關聯，會忽略 has_many 所設定的 :inverse_of。

每種關聯皆會試著自動找到對應的關聯，並根據關聯名稱來合理地設定 :inverse_of 選項。多數使用標準名稱的關聯都會自動設定。但使用了以下選項的關聯，則無法自動設定：

- :conditions (rails 3)
- :through
- :polymorphic
- :foreign_key

## validates presence with inverse_of

當一張表單想要填入多個 table 的欄位時，model 通常會寫成這樣：

```ruby
class Author < ActiveRecord::Base
  has_many :books
  accepts_nested_attributes_for :books
end

class Book < ActiveRecord::Base
  belongs_to :author
  validates :author, presence: true
end
```

想要確保關聯的物件是否存在，可以使用 validates 後面參數加上 `presence: true`。

```ruby
params = { name: 'akii', books_attributes: [{ title: 'cat' }] }
a = Author.create(params)
a.errors.messages # => {:"books.author"=>["must exist", "can't be blank"]}
a.valid? # => false
```

當 rails 試著把 books 存入資料庫時，author 還沒有被 commit 到資料庫中，因此缺少一個 id，`presence: true` 的驗證失敗產生 `can't be blank` 的錯誤。

`must exist` 的錯誤可以選擇關掉，不用 foreign key 就能將資料存入。

```ruby
# config/initializers/new_framework_defaults.rb
Rails.application.config.active_record.belongs_to_required_by_default = false
```

為了讓他能運作，需要在 Author 裡的 `has_many :books` 加上 `inverse_of: :author`，讓它能通知正在新增的 book。

```ruby
class Author < ActiveRecord::Base
  has_many :books, inverse_of: :author
  accepts_nested_attributes_for :books
end

class Book < ActiveRecord::Base
  belongs_to :author
  validates :author, presence: true
end
```

```ruby
params = { name: 'akii', books_attributes: [{ title: 'cat' }] }
a = Author.create(params)
a.errors.messages # => {}
a.valid? # => true
```

- [Ruby on Rails API](http://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html)
- [Exploring inverse_of](https://www.viget.com/articles/exploring-the-inverse-of-option-on-rails-model-associations)
