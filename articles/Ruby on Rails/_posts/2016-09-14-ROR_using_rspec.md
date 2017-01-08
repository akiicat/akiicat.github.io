---
title:  "Ruby on Rails 中使用 Rspec"
date:   2016-09-14 18:13:32 +0800
---

## Gemfile

```ruby
group :development, :test do
  gem 'rspec-rails'
end
```

然後執行 `bundle`

## Initialize

建立新的 rspec 檔

```sh
rails generate rspec:install
```

<!--excerpt-->

## Run

執行測試檔

```sh
# Rails
rake spec

# Run only model specs
rake spec:models

# Run only model specs
bundle exec rspec spec/models

# Run only specs for AccountsController
bundle exec rspec spec/controllers/accounts_controller_spec.rb
```

## Spec Files Template

```ruby
# spec/models/user.rb
require "rails_helper"

RSpec.describe User, :type => :model do
  it "orders by last name" do
    lindeman = User.create!(first_name: "Andy", last_name: "Lindeman")
    chelimsky = User.create!(first_name: "David", last_name: "Chelimsky")

    expect(User.ordered_by_last_name).to eq([chelimsky, lindeman])
  end
end
```
