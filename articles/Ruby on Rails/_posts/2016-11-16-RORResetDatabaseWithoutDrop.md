---
title:  "Ruby on Rails 重新設置 Database"
date:   2016-11-16 02:16:47 +0800
---

## 重設 Database 的方法

### Status

在重新設置資料庫前先執行 `db:migrate:status`，看看現在資料庫的 migrate 的狀態是什麼。

```sh
# rake db:migrate:status
database: dev

 Status   Migration ID    Migration Name
--------------------------------------------------
   up     20161114101612  Enable uuid extension
   up     20161115193547  Create singers
   up     20161115193548  Create songs
```

### Basic

在重新設置資料庫時，最簡單的方法就是先把資料庫刪除，然後重新建一個，最後在 migrate：

- `db:migrate:reset` 執行 `db:drop` `db:create` `db:migrate`

```sh
rake db:migrate:reset
```

<!--excerpt-->

### Reset

reset 比較不同的地方是，他是載入 schema 而沒有去 migrate 新的資料，用在測試 seed.rb 的資料有沒有正確的時候非常好用。

- `db:schema:load` 依照 schema.rb 建立 table
- `db:setup` 執行 `db:create` `db:schema:load` `db:seed`
- `db:reset` 執行 `db:drop` `db:setup`

```sh
rake db:reset db:migrate
```

### Without Drop

如果不想把資料庫刪掉，又要重新設置，可以先 rollback 回最原始的樣子，然後在重新 migrate。

```sh
rake db:migrate VERSION=0
rake db:migrate
```

### Redo

或是直接使用 `db:migrate:redo`，他會執行 `db:rollback` `db:migrate`，但只會回到上一個版本，不會回到最前面，可以加上 STEP 參數選擇要回到多少個版本。

```sh
rake db:migrate:redo
rake db:migrate:redo STEP=3
```

### 新的 migration

如果是已經上線的版本就不建議 rollback 回去，因為所有資料庫裡面的資料都會消失，而是新建一個 migration 來修改現有的 `db/schema.rb`。

- [More Information](https://en.wikibooks.org/wiki/Ruby_on_Rails/ActiveRecord/Migrations)
