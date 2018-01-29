---
title:  "Ruby on Rails callback 筆記"
date:   2017-01-11 21:55:08 +0800
---

## 可用的 callback

以下是 Active Record 可用的 callback，依照執行順序排序：

### 新建物件

```ruby
before_validation
after_validation
before_save
around_save
before_create
around_create
after_create
after_save
after_commit/after_rollback
```

### 更新物件

```ruby
before_validation
after_validation
before_save
around_save
before_update
around_update
after_update
after_save
after_commit/after_rollback
```

<!--excerpt-->

### 刪除物件

```ruby
before_destroy
around_destroy
after_destroy
after_commit/after_rollback
```

## 執行 callback

以下方法會觸發 callback：

```ruby
create
create!
decrement!
destroy
destroy!
destroy_all
increment!
save
save!
save(validate: false)
toggle!
touch
update_attribute
update
update!
valid?
```

另外 after_find 由下列查詢方法觸發：

```ruby
all
first
find
find_by
find_by_*
find_by_*!
find_by_sql
last
```

after_initialize 在每次 Active Record 物件實體化時觸發。

## 略過 callback

驗證可以略過， callback同樣也可以。使用下列方法來略過 callback：

```ruby
decrement
decrement_counter
delete
delete_all
increment
increment_counter
toggle
update_column
update_columns
update_all
update_counters
```

小心使用這些方法，因為 callback裡可能有重要的業務邏輯。沒弄懂 callback的用途，便直接跳過可能會導致存入不合法的資料。

## 條件式 callback

callback 和 validate 一樣，也可以在滿足給定條件時才執行。條件透過 :if、:unless 選項指定，接受 Symbol、String、Proc 或 Array

```ruby
class Comment < ActiveRecord::Base
  after_create :send_email_to_author, if: :author_wants_emails?,
      unless: Proc.new { |comment| comment.article.ignore_comments? }
end
```

- [Active Record Callback](http://rails.ruby.tw/active_record_callbacks.html)
