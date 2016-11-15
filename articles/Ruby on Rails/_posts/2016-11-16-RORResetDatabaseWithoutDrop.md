---
title:  "Ruby on Rails 重新設置 Database"
date:   2016-11-16 02:16:47 +0800
---

## 重設 Database 的方法

### Basic

在用 Ruby On Rails 難免都需要會重新設定資料庫，最基本的方法如下：

```sh
rake db:drop db:create db:migrate
```

### Reset

reset 比較不同的地方是，他是載入 schema 而沒有去 migrate 新的資料

- `db:schema:load` 依照 schema.rb 建立 table
- `db:setup` 執行 db:create db:schema:load db:seed
- `db:reset` 執行 db:drop db:setup

```sh
rake db:reset db:migrate
```

<!--excerpt-->

### Without Drop

如果不想把 database 刪除掉，又要重新設定資料庫，那要先把資料庫 rollback 回最原始的樣子，然後在重新 migrate

```sh
rake db:migrate VERSION=0
rake db:migrate
```
