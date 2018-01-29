---
title:  "Ruby on Rails Assets Pipeline 的使用方式"
date:   2016-09-18 03:35:50 +0800
---

## Assets Pipeline

在 `config/initializers/assets.rb` 中把 assets 加入 precompile 的路徑，不建議使用 `*.css *.js` 加入所有的東西，因為會將一些有的沒的或是不需要的東西一起編譯進去。

assets pipeline 的主要好處就是把所有的 css 包成一個檔案，漸少 request 的數量，像是 `application.css` 會載入所有被 `require` 的檔案，最後只需要傳送一個 css 就行了，同理 javascript 也是如此。

### Layout

看不懂在說什麼，直接看例子比較快。

假設現在我們的 layout 裡面有兩個不同的頁面 admin.html.erb 跟 user.html.erb，要分別讓他們使用不同的 css js。

```
app
├--assets
│  ├--javascripts
│  │  ├--admin.js
│  │  ├--user.js
│  │  └--...
│  └--stylesheets
│     ├--admin.scss
│     ├--user.scss
│     └--...
└--view
   └--layout
      ├--admin.html.erb
      └--user.html.erb
```

但為了減少請求的數量，我們讓一個 layout 只需要一個 css 和一個 js，在 layout 中的話會像這樣：

```erb
# app/views/layouts/admin.html.erb
<%= stylesheet_link_tag    'admin', media: 'all' %>
<%= javascript_include_tag 'admin' %>

# app/views/layouts/user.html.erb
<%= stylesheet_link_tag    'user', media: 'all' %>
<%= javascript_include_tag 'user' %>
```

如果使用 admin 的 layout 就會載入 `admin.css` `admin.js`。

如果使用 user 的 layout 就會載入 `user.css` `user.js`。

<!--excerpt-->

### Assets

現在 layout 的頁面只會分別載入一個 css js，那如果想要使用別人寫好的套件該怎麼辦？這時候就把套件用 `require` 的方式寫在 `admin.css` `admin.js` `user.css` `user.js` 這四個檔案裡面。

```ruby
# app/assets/javascripts/user.js
//= require jquery
//= require some_javascript
//= require_tree user_js
//= require_self
```

這邊只有舉 `user.js` 的例子而已，可以使用以下的方法來把你要的 css js 加進這四個檔案。

#### 操作方法

- require [路徑] 載入某支特定檔案，如果這支檔案被載入多次，Sprockets 也會很聰明的只幫你載入一次。
- include [路徑] 與 require 一樣，差別在即使是被載入過的檔案也會再被載入。
- require_directory [路徑] 將路徑下不包含子目錄的檔案按照字母順序依次載入。
- require_tree [路徑] 會將路徑下包含子目錄的檔案全部載入。
- require_self [路徑] 告訴 Sprockets 再載入其他的檔案前，先將自己的內容插入。
- depend_on [路徑] 宣告依賴於某支 js，在需要通知某支快取的 assets 過期時非常實用。
- stub [路徑] 將路徑中的 assets 加入黑名單，所有其他的 require 都不會將他載入。

### Precompile

在執行 precompile 的指令時，rails 預設只會 precompile 這兩個檔案 `application.css` 和 `application.js`，所以像剛剛我們有使用 `admin.css` `admin.js` `user.css` `user.js` 的話，就必須告知 rails 幫我們 precompile 這四個檔案。

```ruby
# config/initializers/assets.rb  
Rails.application.config.assets.precompile += %w( admin.css admin.js user.css user.js )
```

```shell
rake assets:precompile
```

執行完後，如果在 `public/assets` 下有看到我們產生的 css 和 js，那就代表成功了。

```shell
# public/assets
application-49ba7afaed4de07ee4204756af5e037c05649e01ba0dd30caf876b4590df1abe.css
application-512da0ae9d053cd29739f8f163175aa8929a41175270655bb241a198396fe0d6.js
admin-....css
admin-....js
user-....css
user-....js
```

### 注意

假如有用到很多的套件的話，盡量使用上面的方法，而下面這是萬用的解法，會降低效能所以不建議使用。

```ruby
# config/initializers/assets.rb  
Rails.application.config.assets.precompile += %w( *.css *.js )
```
