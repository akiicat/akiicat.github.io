---
title:  "Ruby on Rails 重新設置 Database"
date:   2016-11-16 02:16:47 +0800
---

## 重設 Database 的方法

### Drop DB

在用 Ruby On Rails 難免都需要會重新設定資料庫，最基本的方法如下：

```sh
rake db:drop db:create db:migrate
```

或者是使用 reset 指令，會先 drop 後馬上 create，跟上面的指令沒什麼不同：

```sh
rake db:reset db:migrate
```

### Without Drop

如果不想把 database 刪除掉，又要重新設定資料庫，那要先把資料庫 rollback 回最原始的樣子，然後在重新 migrate

```sh
rake db:migrate VERSION=0
rake db:migrate
```

<!--excerpt-->
