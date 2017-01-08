---
title:  "Ruby 直接儲存 Tempfile 的方法"
date:   2016-11-22 02:02:39 +0800
---

先建立一個 Tempfile，然後隨便輸入一些資料進去。

```ruby
require 'tempfile'

f = Tempfile.new ''
f.puts("akii")
f.close
```

Tempfile 在程式結束的時候會 `f.unlink` 刪除檔案，所以在結束之前可以使用 `File.rename` 移動檔案，讓檔案逃脫免於被刪除的命運。

```ruby
# File.rename("afile", "afile.bak")
File.rename(f, "cat.txt")
```

<!--excerpt-->

需要注意的是，移動完檔案之後就不能再回去使用之前的 Tempfile，否則會產生 `IOError` 的錯誤訊息。也就是為什麼 `File.rename` 過後，關閉程式不會刪除檔案的原因。

```ruby
f.open
# => IOError
```

最後測試儲存後的內容

```ruby
File.read("cat.txt")
# => "akii\n"
```
