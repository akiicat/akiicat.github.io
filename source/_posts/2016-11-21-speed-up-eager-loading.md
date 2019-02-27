---
title:  Ruby on Rails Eager Loading 加速：一次拿取所以資料
date:   2016-11-21 14:25:11 +0800
categories:
- Programming
tags:
- Ruby
- Ruby on Rails
- Eager Loading
---

這個在 rails 裡面，資料有關聯的時候，會產生的一些效能上的問題，假設我們的例子如下：

```
┌──────────────────┐                ┌───────────────────┐
│      Author      │                │       Book        │
├──────────────────┤                ├───────────────────┤
│   id:integer     │←───────┐       │ id:integer        │
│   name:string    │        └───────│ author_id:integer │
│                  │                │ title:string      │
└──────────────────┘                └───────────────────┘
```

當我們在 books controllers 拿了一群東西，像是有 **all** 或是 **where**

```ruby
@books = Book.all
@books = Book.where(author: @author)
```

常常接著又在 view 裡面使用 **each** 抓取了關聯的東西 **author**，這時 **@books** 不知道 **author** 的內容所以又必須呼叫一次 SQL 指令去拿資料，所以當資料量一大的時候，會產生效能上的問題。

```ruby
@books.each do |book|
  book.author
end
```

<!-- more -->

## 環境設置

所以在效能測試之前，先來建立環境：新建一個專案，然後用 scaffold 產生 **author** 和 **book** 還有他們之間的關係。

```sh
rails new speed_test
cd speed_test
rails generate scaffold author name:string
rails generate scaffold book title:string author:references
```

在 `db/seeds.rb` 產生一些接下來要測試用的資料：一個作者有十本書。

```ruby
# db/seeds.rb
author = Author.create! name: "akii"
10.times do |i|
  Book.create! title: "book#{i}", author: author
end
```

migration 後開啟 rails server

```sh
rake db:migrate db:seed
rails s
```

## 效能測試

這是在 books controller 裡面，用 scaffold 產生的 index 如下：

```ruby
# app/controllers/books_controller.rb
def index
  @books = Book.all
end
```

然後用瀏覽器開啟 `localhost:3000/books` 頁面，可以看到 rails server 會產生以下的東西。

```sql
# rails console
Started GET "/books" for ::1 at 2016-11-21 14:10:10 +0800
  ActiveRecord::SchemaMigration Load (0.1ms)  SELECT "schema_migrations".* FROM "schema_migrations"
Processing by BooksController#index as HTML
  Rendering books/index.html.erb within layouts/application
  Book Load (0.1ms)  SELECT "books".* FROM "books"
  Author Load (0.1ms)  SELECT  "authors".* FROM "authors" WHERE "authors"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Author Load (0.1ms)  SELECT  "authors".* FROM "authors" WHERE "authors"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Author Load (0.1ms)  SELECT  "authors".* FROM "authors" WHERE "authors"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Author Load (0.1ms)  SELECT  "authors".* FROM "authors" WHERE "authors"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Author Load (0.1ms)  SELECT  "authors".* FROM "authors" WHERE "authors"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Author Load (0.1ms)  SELECT  "authors".* FROM "authors" WHERE "authors"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Author Load (0.1ms)  SELECT  "authors".* FROM "authors" WHERE "authors"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Author Load (0.1ms)  SELECT  "authors".* FROM "authors" WHERE "authors"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Author Load (0.1ms)  SELECT  "authors".* FROM "authors" WHERE "authors"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Author Load (0.1ms)  SELECT  "authors".* FROM "authors" WHERE "authors"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Rendered books/index.html.erb within layouts/application (32.9ms)
Completed 200 OK in 216ms (Views: 202.9ms | ActiveRecord: 1.8ms)
```

在 `views/book/index.html.erb` 裡面的 **@books.each**，每次有需要 **author** 的時候，都會重新使用 SQL 指令抓取 **author** 的內容，所以每個 **author** 的 id 也會不同。

```
# localhost:3000/books
Title	         Author
book0	#<Author:0x007f92b10aa618>
book1	#<Author:0x007f92b013d400>
book2	#<Author:0x007f92b0a76198>
book3	#<Author:0x007f92af9e03d8>
book4	#<Author:0x007f92af9502d8>
book5	#<Author:0x007f92b09f4d28>
book6	#<Author:0x007f92b0907230>
book7	#<Author:0x007f92b00fdb98>
book8	#<Author:0x007f92b002ea00>
book9	#<Author:0x007f92af8d5858>
```

現在把在 **all** 後面加上 `includes(:author)` 表示順便幫我們把 **author** 的東西也一起抓下來。

```ruby
# app/controllers/books_controller.rb
def index
  @books = Book.all.includes(:author)
end
```

所以重新開啟 rails server，然後載入 `localhost:3000/books` 頁面，就會發現 SQL 指令會少了好幾行。

```sql
# rails console
Started GET "/books" for ::1 at 2016-11-21 14:09:55 +0800
  ActiveRecord::SchemaMigration Load (0.1ms)  SELECT "schema_migrations".* FROM "schema_migrations"
Processing by BooksController#index as HTML
  Rendering books/index.html.erb within layouts/application
  Book Load (0.2ms)  SELECT "books".* FROM "books"
  Author Load (0.1ms)  SELECT "authors".* FROM "authors" WHERE "authors"."id" = 1
  Rendered books/index.html.erb within layouts/application (23.2ms)
Completed 200 OK in 205ms (Views: 192.0ms | ActiveRecord: 1.3ms)
```

而且每個 **author** 的 id 都會是一樣的

```
# localhost:3000/books
Title	         Author
book0	#<Author:0x007fcee42c2238>
book1	#<Author:0x007fcee42c2238>
book2	#<Author:0x007fcee42c2238>
book3	#<Author:0x007fcee42c2238>
book4	#<Author:0x007fcee42c2238>
book5	#<Author:0x007fcee42c2238>
book6	#<Author:0x007fcee42c2238>
book7	#<Author:0x007fcee42c2238>
book8	#<Author:0x007fcee42c2238>
book9	#<Author:0x007fcee42c2238>
```

只有 10 比資料效能提升可能不太明顯，但是如果 **books** 有 1000 筆資料，這樣就會有明顯的差異了：

```sql
Rendered books/index.html.erb within layouts/application (812.6ms)
Completed 200 OK in 968ms (Views: 887.0ms | ActiveRecord: 69.6ms)
```

```sql
Rendered books/index.html.erb within layouts/application (202.5ms)
Completed 200 OK in 359ms (Views: 344.5ms | ActiveRecord: 3.3ms)
```

## 注意

如果只想要拿一筆資料 find、find_by 之類的，寫法如下：

```ruby
Book.includes(:author).find_by_name("akiicat")

# Wrong
# Book.find_by_name("akiicat").includes(:author)
```
