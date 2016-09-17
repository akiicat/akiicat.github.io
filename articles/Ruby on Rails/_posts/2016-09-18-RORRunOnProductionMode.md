---
title:  "Ruby on Rails 在 production mode 下跑 rails 5 專案"
date:   2016-09-18 02:07:19 +0800
---

## Production Mode
### 資料庫 Migrate

```shell
RAILS_ENV=production rake db:migrate
```

### 產生 Secret Key

```shell
# 產生 secret key
rake secret

# 將剛剛產生出來的 secret key 放入環境變數中
export SECRET_KEY_BASE=$(rake secret)
```

<!--excerpt-->

### Asset Precompile
加入需要使用的 assets 路徑。

```ruby
# config/initializers/assets.rb  
Rails.application.config.assets.precompile += %w( admin.css )
```

在 production 下 asset precompile。

```
RAILS_ENV=production rake assets:precompile
```

### Config

將以下的改成 true，預設為 false。

```ruby
# config/environments/production.rb
# rails 5
config.assets.compile = true

# rails 4
config.serve_static_assets = true
```

### Run Server

```shell
rails s -e production
```
