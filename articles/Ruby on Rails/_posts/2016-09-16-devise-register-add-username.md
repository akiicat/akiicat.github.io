---
title:  "Ruby on Rails Devise 與 註冊時輸入使用者名稱"
date:   2016-09-16 03:08:34 +0800
---

## 註冊時輸入使用者名稱

在產生 `rails generate devise:views users` 後，然後在註冊的地方增加 username 的欄位。

```erb
# app/views/users/registrations/new.html.erb
<div class="field">
  <%= f.label :username %><br />
  <%= f.text_field :username, autofocus: true %>
</div>
```

在 controller 增加允許使用者欄位的驗證。

```ruby
# app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
  before_action :configure_permitted_parameters, if: :devise_controller?

  protected

  def configure_permitted_parameters
    added_attrs = [:username, :email, :password, :password_confirmation, :remember_me]
    devise_parameter_sanitizer.permit :sign_up, keys: added_attrs
    devise_parameter_sanitizer.permit :account_update, keys: added_attrs
  end
end
```

<!--excerpt-->

選擇性：限制使用者名稱的字元，不能輸入 email 當作 username。

```ruby
# app/models/user.rb
# Only allow letter, number, underscore and punctuation.
validates_format_of :username, with: /^[a-zA-Z0-9_\.]*$/, :multiline => true
```
