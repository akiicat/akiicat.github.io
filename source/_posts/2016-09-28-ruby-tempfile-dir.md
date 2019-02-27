---
title:  Ruby 建立不存在的目錄 與 TempFile 的路徑
date:   2016-09-28 05:18:03 +0800
categories:
- Programming
tags:
- Ruby
---

## Root Path

執行 ruby 程式的當前位置

```ruby
Dir.pwd
# => "/Users/akii"
```

## Exec File

執行 ruby 程式的檔案

```ruby
__FILE__
# => "test.rb"
```

## Create Dir If Not Exists

如果當前目錄下不存在 `tmp` 的資料夾，則創立一個。

```ruby
tmpdir = "./tmp"
FileUtils.mkdir_p(tmpdir) unless File.exists?(tmpdir)
```

<!-- more -->

## TempFile

查詢 TempFile 的路徑

```ruby
Dir.tmpdir
# => "/var/folders/4z/k5tngj853j140y54xb11_0h00000gn/T"
```

如果上面暫存檔的路徑不是你想要的話，可以在 TempFile.new 的時候給定暫存檔的路徑。

```ruby
require 'tempfile'

file = Tempfile.new([key, '.txt'], tmpdir)
file.write('hello world')
file.close
file.unlink
```
