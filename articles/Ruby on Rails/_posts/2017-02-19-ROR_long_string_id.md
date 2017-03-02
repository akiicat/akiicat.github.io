---
title:  "Ruby on Rails long string id"
date:   2017-02-19 22:48:09 +0800
---

之前有講到使用 [UUID](/blogger/2016/09/01/ROR_using_uuid_in_rails) 當作 primary key 的方法，因為 postgresql 有提供 uuid，可以直接拿來使用。

接下來的這個方法適用於所有的資料庫，在物件儲存之前先給定它的 id，如此一來就能定義我們所想要的內容了。

先隨便用鷹架產生點東西：

```sh
rails g scaffold book title
```

加上 `id: :string` 讓 id 的型態為 string。

```ruby
# db/migrate/20170218105724_create_books.rb
class CreateBooks < ActiveRecord::Migration[5.0]
  def change
    create_table :books, id: :string do |t|
      t.string :title
      t.timestamps
    end
  end
end
```

修改好後 migrate 我們的資料庫。

```sh
rake db:migrate
```

在 models 中要儲存物件之前，先給定 id 為 `SecureRandom.hex(50)`，這邊 `assign_unique_token` 可以自己修改成你想要的。最後 `unique_token?` 會檢查 id 是否已經被使用過了，如果使用過了，就重新再產生一次 id。

```ruby
# app/models/application_record.rb
class ApplicationRecord < ActiveRecord::Base
  before_create :assign_unique_token

  private

  def assign_unique_token
    begin
      self.id = SecureRandom.hex(50)
    end until unique_token?
  end

  def unique_token?
    self.class.where(:id => id).count == 0
  end
end
```

## Relation

假設現在有個 Author 的 model，如果要在 Book 裡面加上 `author_id` 的話，記得要把型態改成 string。

```sh
rails g migration add_author_id_to_books author_id:string:books
```

```ruby
# app/models/author.rb
class Author < ApplicationRecord
  has_many :books
end

# app/models/book.rb
class Book < ApplicationRecord
  belongs_to :author
end
```

- [long string id](http://stackoverflow.com/questions/2125384/assigning-each-user-a-unique-100-character-hash-in-ruby-on-rails)
