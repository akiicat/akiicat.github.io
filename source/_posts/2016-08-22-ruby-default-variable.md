---
title:  Ruby 內建變數與內建常數
date:   2016-08-22 20:22:57 +0800
categories:
- Programming
tags:
- Ruby
---

## 指令輸出 <span>``</span>

可以直接使用 ``` `` ``` 符號，框住要行的指令，取得執行的結果，回傳形式為字串。範例為列出所有 `.rb` 結尾的檔案。

```ruby
files = `ls`
files.split.each do |file|
  print "#{file}\n" if file =~ /.rb$/i
end
```

<!-- more -->

## 內建變數
內建變數是 Ruby 預設運作方式的變數。這些內建變數雖然都以 `$` 開始的變數，但也有一些不是全域變數。有些全域變數如 `$<` 其實是 `ARGF` 的別名，這時候應盡量使用比較好懂的名稱。

- `$_`: 最後一次呼叫 `gets` 方法讀入的字串
- `$&`: 最後一次樣式比對成功的字串
- `$~`: 最後一次樣式比對的相關資訊
- <code>$`</code>: 最後一次樣式比對成功部份左方的字串
- `$'`: 最後一次樣式比對成功部份右方的字串
- `$+`: 最後一次樣式比對成功時，樣式中最後一個 `()` 所比對到的部份
- `$1`: 最後一次樣式比對成功時，樣式中每對 `()` 所比對到的部份，第 n 組 `()` 對應到 `$n`
- `$?`: 最後一個結束掉的 Child Thread 狀態
- `$!`: 最後一個發生之例外的相關資訊
- `$@`: 最後一個發生之例外的發生位置等資訊
- `$SAFE`: 安全膚級，預設是 0
- `$/`: 輸入紀錄分隔符號，預設是 `\n`
- `$\`: 輸出記錄分隔符號，預設是 `nil`
- `$,`: Array#join 的預設分隔字串，預設是 `nil`
- `$;`: Array#join 的預設分隔字串，預設是 `nil`
- `$.`: 輸入檔中最後讀入資料的行號
- `$<`: `ARGF` 的別名
- `$>`: `print` `puts` `p` 等方法預設的輸出對象，預設是 `STDOUT`
- `$0`: 現在所執行的 Ruby 指令稿名稱
- `$*`: `ARGV` 的別名
- `$$`: 現在所執行的 Ruby 的 Thread ID
- `$:`: require 讀取檔案時要尋找之目錄的陣列
- `$"`: require 讀取檔案時要尋找之檔案的陣列
- `$DEBUG`: 偵錯模式所指定的 flag，預設是 `nil`
- `$FILENAME`: `ARGF` 現在所讀取的檔名
- `$LOAD_PATH`: `$:` 的別名
- `$stdin`: 標準輸入，預設是 `STDIN`
- `$stdout`: 標準輸出，預設是 `STDOUT`
- `$stderr`: 標準錯誤輸出，預設是 `STDERR`
- `$VERBOSE`: 設定為警告模式的 flag，預設是 `nil`

### $: $LOAD_PATH
`$:` 與 `$LOAD_PATH` 相同，用來表示程式庫的路徑。也就是指定 Ruby 在尋找程式庫時，要從哪裡、以什麼順序搜尋？

### $! $@
用在例外處理，`$@` 紀錄例外發生的位置，而 `$!` 紀錄例外相關的資訊。

### $1 ... $9
從常規表示式中比對成功取出的值。

## 內建常數

- `STDIN`: 標準輸入
- `STDOUT`: 標準輸出
- `STDERR`: 標準錯誤輸出
- `DATA`: 用來存取 `__END__` 後方資料的檔案物件
- `ENV`: 環境變數的 Hash
- `ARGF`: 引數或標準輸入所建立的虛擬檔案物件
- `ARGV`: 命令列引數的陣列
- `RUBY_VERSION`: Ruby 執行環境的版本
- `RUBY_RELEASE_DATE`: Ruby 執行環境的釋出日期字串
- `RUBY_PLATFORM`: 現在運作的環境 OS CPU 字串


### Ruby Info

```ruby
p RUBY_VERSION            # "2.3.1"
p RUBY_RELEASE_DATE       # "2016-04-26"
p RUBY_PLATFORM           # "x86_64-darwin15"
```

`DATA` `__END__` 的用法

```ruby
data = DATA.read
print data

__END__
Text At Here Will Not Be Exec.
寫在這裡的字串不會被當作程式的一部份
而可以直接當作資料使用
```
