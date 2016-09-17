---
title:  "Ruby on Rails Assets Pipeline 的使用方式"
date:   2016-09-18 03:35:50 +0800
---

## Assets Pipeline
在把 assets 加入 precompile 的路徑，不建議使用 `*.css *.js` 加入所有的東西，因為會將一些有的沒的或是不需要的東西一起編譯進去。

assets pipeline 的主要好處就是把所有的 css 包成一個檔案，漸少 request 的數量，像是 `application.css` 會載入所有被 `require` 的檔案，最後只需要傳送一個 css 就行了，javascript 也是，而現在又在 precompile 的時候把所有的東西編譯進去豈不是本末倒置嗎。

### Controller
在講之前先來設定 controller 要使用那個 layout，假設現在有 admin 跟 user 兩個不同的 layout。

```ruby
# admin controller
layout 'admin'

# user controller
layout 'user'
```

### Layout
再來看我們的 layout，讓他們分別使用不同的 css js，而且一個 layout 只需要一個 css 和一個 js，這麼做是為了要讓我們 request 的數量減少，所以其他 css js 不要從這裡加入。

```erb
# app/views/layouts/admin.html.erb
<%= stylesheet_link_tag    'admin', media: 'all', 'data-turbolinks-track': 'reload' %>
<%= javascript_include_tag 'admin', 'data-turbolinks-track': 'reload' %>

# app/views/layouts/user.html.erb
<%= stylesheet_link_tag    'user', media: 'all', 'data-turbolinks-track': 'reload' %>
<%= javascript_include_tag 'user', 'data-turbolinks-track': 'reload' %>
```

### Assets
這邊就是我們要加入其他 css js 的所在位置，admin 與 user 分別把需要使用的套件，利用 require 的方式加進來，這樣才能把東西包起來，產生我們只需要的四個檔案 `admin.css` `admin.js` `user.css` ` user.js`。

```ruby
# app/assets/stylesheets/admin.css
 *= require_tree ./admin

# app/assets/stylesheets/user.css
 *= require_tree ./user

# app/assets/javascripts/admin.js
//= require_tree ./admin

# app/assets/javascripts/user.js
//= require_tree ./user
```

這邊的範例是 admin.css 會把 admin 這個資料夾下的所以東西都加進去，但若有共同的東西，像是某個套件之類的，就不需要丟入分類的資料夾中了，直接 require 那個套件就行了。

#### 操作方法

- require [路徑] 載入某支特定檔案，如果這支檔案被載入多次，Sprockets 也會很聰明的只幫你載入一次。
- include [路徑] 與 require 一樣，差別在即使是被載入過的檔案也會再被載入。
- require_directory [路徑] 將路徑下不包含子目錄的檔案按照字母順序依次載入。
- require_tree [路徑] 會將路徑下包含子目錄的檔案全部載入。
- require_self [路徑] 告訴 Sprockets 再載入其他的檔案前，先將自己的內容插入。
- depend_on [路徑] 宣告依賴於某支 js，在需要通知某支快取的 assets 過期時非常實用。
- stub [路徑] 將路徑中的 assets 加入黑名單，所有其他的 require 都不會將他載入。


### Precompile

不使用 `*.css *.js` 的方式，加入 precompile 的路徑，這樣會讓之後產生出來的 `public/assets` 乾淨許多。

```ruby
# config/initializers/assets.rb  
Rails.application.config.assets.precompile += %w( admin.css admin.js )
Rails.application.config.assets.precompile += %w( user.css user.js )
```

development 的模式重新啟動 server 就行了，而 production 的模式還需要 precompile 剛剛的四個檔案 `admin.css` `admin.js` `user.css` `user.js`。

```shell
# production mode
RAILS_ENV=production rake assets:precompile
```
