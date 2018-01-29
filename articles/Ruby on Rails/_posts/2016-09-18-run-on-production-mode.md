---
title:  "Ruby on Rails 在 production 環境下跑 rails 5 專案"
date:   2016-09-18 02:07:19 +0800
---

## Production Mode

### 資料庫 Migrate

migrate production 環境下的資料庫，至於設定檔就不在這邊多說了。

```shell
RAILS_ENV=production rake db:migrate
```

### 產生 Secret Key

在 `config/secret.yml` 這個檔案裡面需要 production 環境的 secret key。

```yml
# config/secret.yml
production:
  secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>
```

用終端機產生 secret key，然後放置環境變數中。

```shell
# 產生 secret key
rake secret

# 將剛剛產生出來的 secret key 放入環境變數中
export SECRET_KEY_BASE=$(rake secret)
```

<!--excerpt-->

### Asset Precompile

如果不太清楚的話，可以先看[這篇](/blogger/2016/09/17/assets-css-js-config/)，加入需要使用的 assets 路徑。

```ruby
# config/initializers/assets.rb  
Rails.application.config.assets.precompile += %w( admin.css )
```

```shell
rake assets:precompile
```

### Config

因為在 production 環境中是不處理靜態檔案的，所以把這個 `RAILS_SERVE_STATIC_FILES` 環境變數設為 true。

```
export RAILS_SERVE_STATIC_FILES=true
```

或是直接到 production 的設定檔 `config/environments/production.rb` 把 `config.public_file_server.enabled` 改為 true。

```ruby
# config/environments/production.rb
# rails 5
config.public_file_server.enabled = ENV['RAILS_SERVE_STATIC_FILES'].present?

# rails 4
config.serve_static_assets = true
```

### Run Server

```shell
rails s -e production
```
