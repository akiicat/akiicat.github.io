---
title:  "Ruby on Rails 設定 Devise 與 註冊 登入 登出"
date:   2016-09-16 01:42:19 +0800
---

## Gemfile

測試環境為 rails 5

```ruby
gem 'devise'
```

然後執行 `bundle`

## Configuration

安裝 devise 的設定檔

```sh
rails generate devise:install
```

然後在 `config/environments/development.rb` 加上寄信認證的設定

```ruby
# config/environments/development.rb
# config/environments/test.rb
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
```

<!--excerpt-->

## Model

這裡的 model 是使用 `User` 當作範例

```sh
rails generate devise User
```

編輯 `db/migrate/xxxxxxxxxxxxxx_add_devise_to_users.rb` 調整需要使用的功能，然後 migrate。

```sh
rake db:migrate
```

因為 Devise 還不支援 rails 5 所以需要使用的話要在 `app/models/application_record.rb` 加上以下這行。

```ruby
extend Devise::Models
```

在 model 中調整需要使用的功能

```ruby
# app/models/user.rb
devise :database_authenticatable, :registerable,
       :recoverable, :rememberable, :trackable, :validatable,
       :confirmable, :lockable, :timeoutable, :omniauthable
```

在 rails console 中測試

```ruby
# rails c
User.create(email: 'email@example.com', password: '124356', password_confirmation: '123456')
```

## 登入驗證

在需要使用者驗證的 `controller` 裡面加上以下這行。

```ruby
# your controllers
before_action :authenticate_user!
```

Rails 5 在使用的時候需要注意，需要將 `authenticate_user` 放在 `protect_from_forgery` 之後，不然無法驗證 CSRF，要解決這個問題可以更改它的順序或是使用 `protect_from_forgery prepend: true`。

### 取得資料

在 controller 中可以取得登入的使用者資料。

```ruby
user_signed_in?         
# => true

current_user
# => #<User id: "185b2601-02e8-4905-9839-25374d6bcdc8", name: nil, created_at: "2016-09-15 09:11:56", updated_at: "2016-09-15 09:15:09", email: "aaaa1379@gmail.com">

user_session
# => {}
```

### 登入/登出

在 controller 中操作只用者的登入與登出。

```ruby
sign_in @user
sign_out @user
```

## Views

產生 devise 的 template。

```sh
rails generate devise:views users
```

產生 template 後，將以下的設定打開，打開的話在 render `sessions/new` 之前會先去檢查 `users/sessions/new` 如果沒有的話則會使用預設的 views。

```ruby
# config/initializers/devise.rb
# default false
config.scoped_views = true
```

### sign_up
在首頁的地方加上註冊。

```erb
# views/devise/menu/_registration_items.html.erb
<% if user_signed_in? %>
  <li> <%= link_to('Edit registration', edit_user_registration_path) %> </li>
<% else %>
  <li> <%= link_to('Register', new_user_registration_path) %> </li>
<% end %>
```

在 layout 的地方增加註冊的連結。

```erb
# layouts/application.html.erb
<%= render 'devise/menu/login_items' %>
```

### sign_out

在需要登出的地方加上以下片段，注意登出的 method 為 `:delete`。

```erb
# views/users/menu/_login_items.html.erb
<% if user_signed_in? %>
  <li> <%= link_to('Logout', destroy_user_session_path, :method => :delete) %> </li>
<% else %>
  <li> <%= link_to('Login', new_user_session_path) %> </li>
<% end %>
```

如果要更改登出的 method 為 `:get` 則在 `config/initializers/devise.rb` 更改。

```ruby
# config/initializers/devise.rb
# The default HTTP method used to sign out a resource. Default is :delete.
config.sign_out_via = :get
```

在 layout 的地方增加登出的按鈕。

```erb
# layouts/application.html.erb
<%= render 'devise/menu/registration_items' %>
```
