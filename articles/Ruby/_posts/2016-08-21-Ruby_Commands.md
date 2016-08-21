---
title:  "Ruby 命令列選項 command line"
date:   2016-08-21 22:31:47 +0800
---

## 偵錯、運作確認
### -c: 語法檢查
...檢查現在執行的 Ruby 指令稿語法是否正確。並不會實際執行程式

### -d: 設定偵錯模式
讓偵錯用變數 `$DEBUG` 生效。指定 `-d` 時， `$DEBUG` 的値會為 `true`。所以可以在程式裡加上這樣的敘述︰

```ruby
print some_var if $DEBUG
```

程式裡的這行敘述會有以下運作情形︰

- 平常不會執行
- 只有在偵錯模式下印出 some_var 的値

### -s: 在引數對指令稿指定變數開關
加有 `-s` 引數時，就可以對指令稿指定 `-foo` 這樣的選項，將變數 `$foo` 設定為 `true`。

```ruby
# sample.rb
puts "$foo: #{$foo}"
puts "$bar: #{$bar}"
puts "$baz: #{$baz}"

$ ruby -s sample.rb -foo -bar
$foo: true
$bar: true
$baz:
```

### -w: 設定警告模式
平常不顯示瑣碎的警告都會顯示出來。

```ruby
# warning_sample.rb
class WarningTest
  def initialize
    @test = "test."
  end
  def test
    print @tset       # 不小心寫成 @tset
  end
end

w = WarningTest.new
w.test
```

```shell
$ ruby warning_sample.rb
nil

$ ruby -w warning_sample.rb
warning_sample.rb:6: warning: instance variable @tset not initialized
nil
```

### -W[Level]: 指定警告層級模式
警告模式有三個層級:

- `-W0`: 不輸出警告
- `-W1`: 只輸出重要警告
- `-W2`: 輸出所有警告
- `-W`: 輸出所有警告，同 `-W2`

## 取得資訊
### --copyright: 顯示著作權
顯示 Ruby 的著作權資訊。

```shell
$ ruby --copyright
ruby - Copyright (C) 1993-2015 Yukihiro Matsumoto
```

### --version: 顯示版本
顯示現在使用的 Ruby 的版本。因為不同版本有所差異，所以在回報問題的時候應該要把 ruby 的版本一起送出。

```shell
$ ruby --version
ruby 2.2.3p173 (2015-08-18 revision 51636) [x86_64-darwin14]
```

### --verbose: 顯示版本和警告
縮寫為 `-v`，會先向 `--version` 顯示版本，再接著以警告模式 `-w` 執行程式。

### --help: 顯示輔助說明
縮寫為 `-h`，顯示指定的說明。


## 單行程式 one-liner
### -e: 執行單行程式
能夠直接將 command 當作指令執行。

```shell
$ ruby -e 'puts "Hello World!"'
Hello World!
```

### -0 [octal]: 指定行末字元
預設的行末字元是換行字元 `\n`。

### -a: 自動切割模式
自動對輸入行作 split，將結果存入陣列 `$F`。可以與選項 `-n` `-p` 並用。

### -Fpattern: 設定自動切割模式的分隔樣式
在自動切割模式 `-a` 下，指定用來 split 輸入行的 pattern。下面範例會以 `:` 來 split 檔案 `/etc/passwd` 的內容。

```shell
$ ruby -anF: -e 'p $F' /etc/passwd
["nobody", "*", "-2", "-2", "Unprivileged User", "/var/empty", "/usr/bin/false\n"]
["root", "*", "0", "0", "System Administrator", "/var/root", "/bin/sh\n"]
...
```

### -i[extension]: 編輯引數所指定的檔案
讀取引數所指定的檔案內容，並將以指令稿輸出的結果取代掉檔案的內容。想要將原始檔案備份時，可以在 `extension` 指定副檔名。下面的範例是在 a.txt 每行前面加上 4 位數的行號，並將原始檔改名為 a.txt.bak

```shell
# a.txt
hello world
hello ruby
```
```shell
$ ruby -i.bak -np -e '$_.sub!(/^/, "%4d " % $.)' a.txt
```
```shell
# a.txt
   1 hello world
   2 hello ruby

# a.txt.bak
hello world
hello ruby
```

### -Idirectory: 追加 $LOAD_PATH
在 $LOAD_PATH 前方加上 directory。

```shell
ruby -Ilib example.rb
```

### -l: 指定行末處理
自動刪除行尾的換行字元，並在輸出時自動補回去。

### -n: 迴圈執行程式碼
將程式放在下面這個迴圈執行。

```ruby
while gets()
  ...
end
```

gets 方法所讀入的每一行可從變數 `$_` 取得。下面這個可以像 `grep` 指令一樣只印出符合樣式的部分。

```shell
$ ruby -n -e 'print if /ruby/ =~ $_' a.txt
hello ruby
```

當 print 沒有指定引數時，會印出 `$_` 的內容。

### -p: 迴圈執行程式碼並自動輸出
與 `-n` 選項一樣在 while 迴圈內執行程式碼。`-p` `-n` 的差別在於 `-p` 每個迴圈最後會自動輸出 `$_` 的內容。

### -rlibrary: 執行前讀取程式碼
在執行程式碼前執行 `require "library"`

## 其他
### -K[kcode]: 設定 `$KCODE`
指定編碼方式。

- `-Ku`: UTF8
- `-Kn`: 不指定

### -C[directory]: 改變目錄
執行程式前先改變工作目錄。
