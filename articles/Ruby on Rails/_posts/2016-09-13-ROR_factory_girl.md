---
title:  "Ruby on Rails 用 Factory Girl 自動產生 rspec 測資"
date:   2016-09-13 22:48:38 +0800
---

## 安裝

在 `Gemfile` 加上以下的部分，然後執行 `bundle`。

```ruby
# Gemfile
group :development, :test do
  gem 'factory_girl_rails', '~> 4.0'
end
```

## 設定

在 `spec/support/factory_girl.rb` 新增一個檔案，且加入以下的東西。

```ruby
# RSpec
## spec/support/factory_girl.rb
require 'factory_girl_rails'

RSpec.configure do |config|
  config.include FactoryGirl::Syntax::Methods
end
```

然後在 `spec/spec_helper.rb` 的最前面加入以下行，使用 factory_girl 的設定檔。

```ruby
## spec/spec_helper.rb
require 'support/factory_girl'
```

<!--excerpt-->

## 使用

建立 FactoryGirl 的測資填寫在以下四個地方的皆可，這裡我是寫在 `spec/factories/*.rb` 這個地方當做範例。

```
test/factories.rb
spec/factories.rb
test/factories/*.rb
spec/factories/*.rb
```

### 不重複

假設 user 有兩個欄位 name 和 email。

```ruby
# db/migrate/xxxxxxxxxxxxxx_create_users.rb
class CreateUsers < ActiveRecord::Migration[5.0]
  def change
    create_table :user do |t|
      t.string :name
      t.string :email
    end
  end
end
```

而現在要用 `sequence` 來產生不重複的測資，然後在需要的地方使用 `generate(:name)` 呼叫。

```ruby
# spec/factories/user.rb
FactoryGirl.define do
  sequence :email do |n|
    "cat#{n}@example.com"
  end
  sequence :name do |n|
    "name#{n}"
  end

  factory :user do
    name { generate(:name) }
    email { generate(:email) }
  end
end
```

使用 `create(:user)` 就能在 rspec 中使用新增 user。

```ruby
# in your rspec files
user = create(:user)
```

### 關聯

假設現在使用者 User 有很多文章 Post。

```ruby
# app/models/user.rb
class User < ApplicationRecord
  has_many :posts, dependent: :destroy
end

# app/models/post.rb
class Post < ApplicationRecord
  belongs_to :user
end
```

#### 單一關係

雖然 User 跟 Post 是一對多的關係，但是在新增 Post 的時候會再新增一個 User
來關聯，所以最後每一篇 Post 只會有一個 User，而且每篇 Post 的 User 都是不同的人。

```ruby
# spec/factories/user.rb
FactoryGirl.define do
  # user factory without associated posts
  factory :user do
    name 'Akiicat'
  end

  # post factory with a `belongs_to` association for the user
  factory :post do
    title 'Post Title'
    user                      ## 這樣就能關聯到 User 了
  end
end

```

用 FactoryGirl 關聯新增 Post 的時候，會再新增一個 User 來關聯，不會使用已存在的 User。

```ruby
# in your rspec files
user = create(:user)
post = create(:post)      ## post.user.id != user.id
```

#### 一對多

若想要讓一個使用者能新增多篇文章。

```ruby
# spec/factories/user.rb
FactoryGirl.define do

  factory :user do
    name 'Akiicat'

    # 在新增 user 後會在新增 posts
    factory :user_with_posts do
      # posts_count 在 transient 裡面宣告，可以使用 `evaluator` 將值取出
      transient do
        posts_count 5       # 新增 5 篇文章
      end

      # 在 after(:create) 呼叫會傳入兩個值：
      # 第一個值為 user
      # 第二個值為 evaluator
      # create_list：
      # 第一個三數是新增 post 這個東西
      # 第二個參數是新增 post 的數量
      # 第三個三數確保 user 能夠關聯到 posts
      after(:create) do |user, evaluator|
        create_list(:post, evaluator.posts_count, user: user)
      end
    end
  end

  factory :post do
    title 'Post Title'
    user
  end
end
```

```ruby
user = create(:user, :user_with_posts)
user.posts.count        # => 5
```

### 不同類別

下面的範例是 post 預設的 status 是 `public`，而使用 trait 可以產生不同的 status。

```ruby
# spec/factories/user.rb
FactoryGirl.define do
  factory :post do
    status "public"

	trait :post_public do
      status "public"
    end

    trait :post_private do
      status "private"
    end
  end
end
```

```ruby
create(:post).status                  # => "public"
create(:post, :post_private).status   # => "private"
```

[Github](https://github.com/thoughtbot/factory_girl_rails)
