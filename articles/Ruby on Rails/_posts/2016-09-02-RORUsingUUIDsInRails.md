---
title:  "Ruby on Rails 中使用 UUID"
date:   2016-09-02 01:17:02 +0800
---

## PostgreSQL

在 PostgreSQL 中有支援 UUID 為唯一 ID，所以在 PostgreSQL 使用 UUID 是相對簡單的。在
migration 裡面，我們要告訴 PostgreSQL 使用 UUID extension，這樣能夠讓 PostgreSQL
自動對每一個物件建立唯一的 UUID，而不是讓 Ruby On Rails 花費額外的時間來處理。

```ruby
rails g migration enable_uuid_extension

# db/migrate/xxxxxxxxxxxxxx_enable_uuid_extension.rb
class EnableUuidExtension < ActiveRecord::Migration[5.0]
  def change
    enable_extension 'uuid-ossp'
  end
end
```
<!--excerpt-->
現在需要把 `id` 改變成 `uuid` datatype，而不是讓 Rails 自動指派。

```ruby
rails g model Book title:string

# db/migate/xxxxxxxxxxxxxx_book.rb
class CreateBooks < ActiveRecord::Migration[5.0]
  def change
    create_table :books, id: :uuid  do |t|
      t.string :title
      t.timestamps null: false
    end
  end
end
```

Rails 生成 UUID 的預設版本是 `uuid_generate_v4()`，能有效率地避免碰撞。所產生的 id
不再是整數遞升，而是一個 16 進位的字串。

```ruby
irb(main):001:0> Book.create(:title => 'hi')
   (0.3ms)  BEGIN
  SQL (3.4ms)  INSERT INTO "books" ("title", "created_at", "updated_at") VALUES ($1, $2, $3) RETURNING "id"  [["title", "hi"], ["created_at", 2016-09-01 16:18:04 UTC], ["updated_at", 2016-09-01 16:18:04 UTC]]
   (2.4ms)  COMMIT
=> #<Book id: "72886b25-9463-4ecd-acb5-4315ebdd53b9", title: "hi", created_at: "2016-09-01 16:18:04", updated_at: "2016-09-01 16:18:04">
```

## UUID Relation

現在有一個 `author` 的 model，且一般在新增關聯會像下面這樣，但是這樣會有個問題：

```ruby
rails g model authors name:string
rails g migration AddAuthorRefToBook

# db/migrate/xxxxxxxxxxxxxx_add_author_ref_to_book.rb
class AddAuthorRefToBook < ActiveRecord::Migration[5.0]
  def change
    add_reference :books, :author, index: true
  end
end
```

```ruby
# app/models/book.rb
class Book < ApplicationRecord
  belongs_to :author
end

# app/models/author.rb
class Author < ApplicationRecord
  has_many :books
end
```

Rails 中 add_reference 的型態 (datatype) 為 `Integer`，可是在資料庫 id 的型態是 `uuid`
，所以在新增 `author_id` 這個外來鍵的時候型態要主動換成 `uuid` 的型態：

```ruby
# create_table 時，先指定好 author_id datatype
rails g model authors name:string author_id:uuid

# add_column 時新增欄位，而非 add_reference
class AddAuthorRefToBook < ActiveRecord::Migration[5.0]
  def change
    add_column :books, :author_id, :uuid
  end
end
```
