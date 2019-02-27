---
title:  Ruby on Rails 5 dynamic subdomains
date:   2017-01-27 17:25:12 +0800
categories:
- Programming
tags:
- Ruby
- Ruby on Rails
- Subdomain
---

一個使用者有名字 name 還有自己網頁的子網域 subdomain，我們想要的結果是讓每一位使用者有自己的子網域，而且還可以隨時更改。

假設現在有個使用者是 `User.create(name: "akii", subdomain: "akiicat")`，在 rails 預設 show 頁面會是 `http://localhost:3000/users/1`，但我們想把網址變成個人的子網域 `http://akiicat.localhost:3000`。

接下來會用鷹架建立使用者來做示範：

```sh
rails g scaffold user name subdomain
rake db:migrate
```

修改 `routes.rb`，加上首頁，然後把 show 的頁面加上 subdomains 的限制。

```ruby
# config/routes.rb
Rails.application.routes.draw do
  resources :users
  constraints subdomains: /.+/ do
    get '', to: 'users#show'
  end
  root to: 'users#index'
end
```

<!-- more -->

調整 development 的 top level domain 或是直接使用 `lvh.me` 當作 localhost 也可以，不清楚的話可以看[這篇](/blogger/2017/01/22/api-subdomains/)。

```ruby
# config/environments/development.rb
config.action_dispatch.tld_length = 0
```

subdomain 傳入的參數會在 `request.subdomain` 裡，如果想要陣列的資料可以從 `request.subdomains` 取得，最後讓 show 使用 subdomain 來找尋使用者。

```ruby
# app/controllers/users_controller.rb
before_action :set_user, only: [:edit, :update, :destroy]
before_action :set_user_subdomain, only: [:show]

def set_user
  @user = User.find(params[:id])
end

def set_user_subdomain
  @user = User.find_by_subdomain(request.subdomain)
end
```

然後把所有 show 的 redirect_to 和 link_to 的連結指到 `root_url(subdomain: @user.subdomain)` 就完成拉。

```ruby
# app/views/users/index.html.erb
link_to 'Show', root_url(subdomain: user.subdomain)
```

剩下的是略過特殊的 subdomains 像是 `www` `api`，傳入的參數會是 array 的型態，包含所有的 subdomains。

假設網址是 `akii.cat.localhost`，top level domain 的長度 `config.action_dispatch.tld_length` 為 0，傳入的參數就會是 `['akii', 'cat']`。

```ruby
constraints subdomains: lambda { |s| s.present? && ['www', 'api'].exclude?(s.last) } do
  ...
end
```

- [Railscasts Subdomains](https://www.youtube.com/watch?v=O2bBcTPj0sI)
