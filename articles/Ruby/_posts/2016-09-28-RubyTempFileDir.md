---
title:  "Ruby 建立不存在的目錄 與 TempFile 的路徑"
date:   2016-09-28 05:18:03 +0800
---

## Root Path

執行 ruby 程式的當前位置

```ruby
Dir.pwd
```

## Exec File

執行 ruby 程式的檔案

```ruby
__FILE__
```

## Create Dir If Not Exists

如果目錄不存在，則創立一個。

```ruby
tmpdir = "./tmp/somepath"
FileUtils.mkdir_p(tmpdir) unless File.exists?(tmpdir)
```

<!--excerpt-->

## TempFile
查詢 TempFile 的路徑

```ruby
Dir.tmpdir
# => "/var/folders/4z/k5tngj853j140y54xb11_0h00000gn/T"
```

設定 TempFile 的路徑，與副檔名

```ruby
require 'tempfile'

file = Tempfile.new([key, '.txt'], tmpdir)
file.write('hello world')
file.rewind
file.read
file.close
file.unlink
```
