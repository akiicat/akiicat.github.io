---
title:  "Ruby on Rails 比較 scope 與 namespace 和參數之間的差別"
date:   2017-03-06 14:52:17 +0800
---

```
Prefix Verb   URI Pattern                  Controller#Action
                                    | 輔助方法 | 路徑 | Controller#動作
                                    | x       | x   | x
scope :api                          | x       | api | x
scope as: :api                      | api     | x   | x
scope path: :api                    | x       | api | x
scope module: :api                  | x       | x   | api

# scope :v1, as: :api               | api     | v1  | x
# scope :v1, module: :api           | x       | v1  | api
# scope as: :api, module: :api      | api     | x   | api
# scope :v1, as: :api, module: :api | api     | v1  | api

namespace :v1                       | v1      | v1  | v1
namespace :v1, as: :api             | api     | v1  | v1
namespace :v1, path: :api           | v1      | api | v1
namespace :v1, module: :api         | v1      | v1  | api

namespace :v1, as: ''               | do not use
namespace :v1, path: ''             | v1      | x   | v1
namespace :v1, path: '/'            | v1      | x   | v1
namespace :v1, module: ''           | error
```
