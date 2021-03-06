---
title:  Ruby on Rails 5 api subdomain
date:   2017-01-22 13:13:27 +0800
categories:
- Programming
tags:
- Ruby
- Ruby on Rails
- Api
---

為了要 demo api subdomain 的頁面是否能夠運作，這邊用鷹架隨便建立個東西。

```sh
rails g scaffold api::v1::books title
rake db:migrate
```

使用 constraints 限制 `subdomain: 'api'`。

然後用 `scope module: 'api'` 加上 app/controllers/api 這個資料夾。

```ruby
# config/routes.rb
Rails.application.routes.draw do
  constraints subdomain: 'api' do
    scope module: 'api' do
      namespace :v1 do
        resources :books
      end
    end
  end
end
```

<!-- more -->

現在用瀏覽器去 `http://api.localhost:3000/v1/books` 頁面，會跳出 Routing Error 的錯誤。可是使用 `rake routes` 觀察的路徑卻是正確的。

```sh
# rake routes
          Prefix Verb   URI Pattern                  Controller#Action
    api_v1_books GET    /v1/books(.:format)          api/v1/books#index {:subdomain=>"api"}
                 POST   /v1/books(.:format)          api/v1/books#create {:subdomain=>"api"}
 new_api_v1_book GET    /v1/books/new(.:format)      api/v1/books#new {:subdomain=>"api"}
edit_api_v1_book GET    /v1/books/:id/edit(.:format) api/v1/books#edit {:subdomain=>"api"}
     api_v1_book GET    /v1/books/:id(.:format)      api/v1/books#show {:subdomain=>"api"}
                 PATCH  /v1/books/:id(.:format)      api/v1/books#update {:subdomain=>"api"}
                 PUT    /v1/books/:id(.:format)      api/v1/books#update {:subdomain=>"api"}
                 DELETE /v1/books/:id(.:format)      api/v1/books#destroy {:subdomain=>"api"}
```

要解決這個錯誤，有兩個方法：

1. 用 `lvh.me`，去瀏覽 `http://api.lvh.me:3000/v1/books` 就能運作了。`lvh.me` 通常用來測試本地的網頁程式，解析的網址會是 `127.0.0.1` 可以用來測試 subdomain。

2. 在 `config/environments/development.rb` 調整 `config.action_dispatch.tld_length = 0` 預設為 1。然後去查看 `https://api.localhost:3000/v1/books` 頁面。

tld 是指 top level domain，以下是比較 `tld_length` 的差別：

```ruby
# config.action_dispatch.tld_length = 1
request.host # => "api.localhost"
request.domain # => "api.localhost"
request.subdomain # => ""
request.subdomains # => []

# config.action_dispatch.tld_length = 0
request.host # => "localhost"
request.domain # => "localhost"
request.subdomain # => "api"
request.subdomains # => ["api"]
```

- [Subdomaining Localhost with Rails 5](https://gist.github.com/indiesquidge/b836647f851179589765)
- [Rails request API](http://api.rubyonrails.org/classes/ActionDispatch/Request.html)
