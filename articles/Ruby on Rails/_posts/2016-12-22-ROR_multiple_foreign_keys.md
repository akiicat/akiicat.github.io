---
title:  "Ruby on Rails Multiple Foreign Keys"
date:   2016-12-22 17:54:32 +0800
---

這篇主要是在講說，在一個 Table 裡面有多個 Foreign Keys 會指向同一個 table。例子如下，一首歌 Song 會有歌手 (singer_id) 跟作曲者 (composer_id)，都會指向同一個 Singer 的 table。

```
┌──────────────────┐                ┌─────────────────────┐
│      Singer      │                │        Song         │
├──────────────────┤                ├─────────────────────┤
│   id:integer     │←───────┐       │ id:integer          │
│   name:string    │        ├───────│ singer_id:integer   │
│                  │        └───────│ composer_id:integer │
│                  │                │ title:string        │
└──────────────────┘                └─────────────────────┘
```

<!--excerpt-->

## Models

belongs_to 的寫法如下，讓兩個不同的 Foreign Key 指向同一個 Singer 的 Table。

```ruby
class Song < ApplicationRecord
  belongs_to :singer  , class_name: "Singer", foreign_key: "singer_id"
  belongs_to :composer, class_name: "Singer", foreign_key: "composer_id"
end
```

has_many 提供兩種寫法，這兩種都可以，但是在 joins 的時候要稍微注意自己是用哪種寫法。

```ruby
class Singer < ApplicationRecord
  has_many :songs
  has_many :composers_songs, foreign_key: :composer_id, class_name: 'Song'
end

class Singer < ApplicationRecord
  has_many :singers_songs  , foreign_key: :singer_id  , class_name: 'Song'
  has_many :composers_songs, foreign_key: :composer_id, class_name: 'Song'
end
```

接下來的 has_many 都是使用第一種寫法。

## includes

這個有沒有加上不會影響程式執行的結果，但是會你在執行 SQL 時會[一次抓完所有的東西](/blogger/2016/11/21/ROR_speed_up_eager_loading/)，避免 N+1 queries 的問題。

```ruby
class Song < ApplicationRecord
  default_scope { includes(:singer, :composer) }
  # default_scope { includes(:singer, :composer).order("lower(singers.name)", "title").references(:singers) }
end
```

## joins

要搜尋 Singer 跟 Song 所有的東西，就會需要用到 joins，寫法如下。

- `singers.name`: 代表歌手
- `composers_songs.name`: 代表作曲者
- `songs.title`: 代表歌名

將這三個把它連接 `CONCAT` 起來，再用 `ILIKE` 搜尋字串。`array[:search]` 是搜尋一整個 array 也就是能搜尋多個字串。

```ruby
class Song < ApplicationRecord
  def self.search(search)
    if search.blank?
      all
    else
      sql = 'CONCAT(singers.name, \' \',
                    composers_songs.name, \' \',
                    songs.title)
             ILIKE all (array[:search])'
      joins(:singer, :composer).where(sql, search: search.split.map{ |s| "%#{s}%" })
    end
  end
end
```
