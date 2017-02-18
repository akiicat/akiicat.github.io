---
title:  "Ruby on Rails 中使用 UUID"
date:   2016-09-02 01:17:02 +0800
---

## PostgreSQL

在 PostgreSQL 中有支援 UUID 為唯一 ID，所以在 PostgreSQL 使用 UUID 是相對簡單的。在
migration 裡面，我們要告訴 PostgreSQL 使用 UUID extension，這樣能夠讓 PostgreSQL
自動對每一個物件建立唯一的 UUID，而不是讓 Ruby On Rails 花費額外的時間來處理。

使用 PostgreSQL 前在 Gemfile 中加上以下這行。

```ruby
# Gemfile
gem 'pg'
```

設定 adapter 為 postgresql

```yml
default: &default
  adapter: postgresql
  encoding: unicode
  host: localhost
  port: 5432
  pool: 5
  username: akii
  password: <%= ENV['PG_PASSWORD'] %>

development:
  <<: *default
  database: development

test:
  <<: *default
  database: test

production:
  <<: *default
  database: production
  # pool: 5
  # url: <%= ENV['DATABASE_URL'] %>
```

設定 postgresql 的密碼

```sh
export PG_PASSWORD=xxxxxx
```

<!--excerpt-->

然後新增一個 migration 說我們要使用 uuid

```ruby
rails g migration enable_uuid_extension

# db/migrate/xxxxxxxxxxxxxx_enable_uuid_extension.rb
class EnableUuidExtension < ActiveRecord::Migration[5.0]
  def change
    enable_extension 'uuid-ossp'
  end
end
```

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
# rails console
irb(main):001:0> Book.create(:title => 'hi')
   (0.3ms)  BEGIN
  SQL (3.4ms)  INSERT INTO "books" ("title", "created_at", "updated_at") VALUES ($1, $2, $3) RETURNING "id"  [["title", "hi"], ["created_at", 2016-09-01 16:18:04 UTC], ["updated_at", 2016-09-01 16:18:04 UTC]]
   (2.4ms)  COMMIT
=> #<Book id: "72886b25-9463-4ecd-acb5-4315ebdd53b9", title: "hi", created_at: "2016-09-01 16:18:04", updated_at: "2016-09-01 16:18:04">
```

## UUID Relation

假設現在要將 `author_id` 添加到 Book model 上，如果使用 add_references 的話，預設的型態會是 `Integer`，可是在資料庫 id 的型態是 `uuid`，所以在新增 `author_id` 這個外來鍵的時候型態要主動換成 `uuid` 的型態。

```ruby
rails g migration add_author_id_to_books author_id:uuid:books
```

```ruby
class AddAuthorIdToBooks < ActiveRecord::Migration[5.0]
  def change
    # errors
    # add_reference :books, :author, index: true
    add_column :books, :author_id, :uuid
  end
end
```
