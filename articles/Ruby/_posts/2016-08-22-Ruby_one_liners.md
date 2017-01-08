---
title:  "Ruby One-Liners"
date:   2016-08-22 03:04:33 +0800
---

[From Here](https://gist.github.com/KL-7/1590797#file-one-liners-md)

### 翻轉每一行

輸入資料:

```sh
$ cat foo
qwe
123
bar
```
翻轉每一行的字:

```sh
$ ruby -e 'File.open("foo").each_line { |l| puts l.chop.reverse }'
ewq
321
rab
```

<!--excerpt-->

從 stdout 翻轉每一行的字:

```sh
cat foo | ruby -e 'while s = gets; puts s.chop.reverse; end'
```

翻轉所有 `ARGF == $<`:

```sh
cat foo | ruby -e 'ARGF.each_line { |l| puts l.chop.reverse }'
ruby -e 'ARGF.each_line { |l| puts l.chop.reverse }' foo
```

自動使用迴圈:

```sh
ruby -ne 'puts $_.chop.reverse' foo
```

每個迴圈最後自動輸出 `$_` 的內容:

```sh
ruby -pe '$_.chop!.reverse!; $_ += "\n"' foo
```

自動刪除行尾的換行字元:

```sh
ruby -lpe '$_.reverse!' foo
```

輸出新的檔案:

```sh
$ cat foo
qwe
123
bar
$ ruby -i -lpe '$_.reverse!' foo
$ cat foo
ewq
321
rab
```

### 縮排

像這樣:

```sh
cat example.rb | ruby -ne 'puts " " * 4 + $_'
```
```sh
ruby -ne 'puts " " * 4 + $_' example.rb
```

### 加上行號 (所有檔案)

```sh
ruby -ne 'puts "#$. #$_"' foo.rb bar.rb
```

### 加上行號 (單一檔案)

```sh
ruby -ne '$. = 1 if $<.pos - $_.size == 0; puts "#$. #$_"' foo.rb bar.rb
```

### 字數統計

```sh
ruby -ane 'w = (w || 0) + $F.size; END { p w }' exmpl.txt
```

### 刪除空白行

刪除文件中的所有連續的空白行，除了每個組中的第一個

```sh
ruby -ne 'puts $_ if /^[^\n]/../^$/'
```

### 取出註解

輸入:

```sh
$ cat comments.txt
some code
/*
  comment one
  comment two
*/
more code
/* one-line comment */
a bit more code
/*
  comment three
*/
even more code
```

動作:

```sh
$ ruby -ne 'puts $_ if ($_ =~ /^\/\*/)..($_ =~ /\*\/$/)' comments.txt
/*
  comment one
  comment two
*/
/* one-line comment */
/*
  comment three
*/
```
```sh
ruby -pe 'next unless ($_ =~ /^\/\*/)..($_ =~ /\*\/$/)' comments.txt
```

Homebrew flip-flop (from "The Ruby programming language" by David Flanagan, Yukihiro Matsumoto):

```ruby
# flip-flop.rb
$state = false
def flipflop(s)
  unless $state
    result = (s =~ /^\/\*$/)
    if result
      $state = !(s =~ /^\*\/$/)
    end
    result
  else
    $state = !(s =~ /^\*\/$/)
    true
  end
end

ARGF.each_line do |l|
  puts l if flipflop(l)
end
```

Try it:

```sh
ruby flip-flop.rb comments.txt
```

### 標記行末空格

```sh
ruby -lpe '$_.gsub! /(\s+)$/, "\e[41m\\1\e[0m"' example.rb
```

### 移除行末空格

```sh
ruby -lpe '$_.rstrip!' example.rb
```

### 每行出過 50 字則標記顏色

簡單的方法:

```sh
ruby -ne 'puts "#{$_}\e[31m#{$_.chop!.slice!(50..-1)}\e[0m"' example.rb
```

難懂但好傳遞參數:

```sh
ruby -e 'w = $*.shift; $<.each { |l| puts "#{l}\e[31m#{l.chop!.slice!(w.to_i..-1)}\e[0m" }' 50 example.rb
```

### 簡單的 REPL

最直接的一種方法:

```sh
ruby -e  'loop { puts eval(gets) }'
```

加上 `-n` 選項:

```sh
ruby -ne 'puts eval($_)'
```

`>> ` 提示符號，但第一行沒有:

```sh
ruby -ne 'print "#{eval($_).inspect}\n>> "'
```

修正:

```sh
ruby -ne 'BEGIN{print">> "}; print "#{eval($_).inspect}\n>> "'
```

REPL 結束時的回覆:

```sh
ruby -ne 'BEGIN{print">> "}; print "#{eval($_).inspect}\n>> "; END{puts "Bye"}'
```

支援 RuntimeError，但不包括 SyntaxError:

```sh
ruby -ne 'BEGIN{print">> "}; print "#{(eval($_) rescue $!).inspect}\n>> "; END{puts "Bye"}'
```

Rescue 所有錯誤 -- 拯救世界:

```sh
ruby -ne 'BEGIN{print">> "}; print "#{(begin; eval($_); rescue Exception; $!; end).inspect}\n>> "; END{puts "Bye"}'
```

# 額外

在每個檔案前加上版權:

```ruby
# copyright.rb
copyright = DATA.read

ARGF.each_line do |l|
  puts copyright if $. == 1
  puts l
end

__END__
####################################
# Copyright (C) 2012 Kirill Lashuk #
####################################
```

使用方法:

```sh
ruby copyright.rb foo.py > foo2.py
```

取代的方法:

```sh
ruby -i copyright.rb foo.py
```

選擇性的方法:

```ruby
# copyright2.rb
BEGIN { copyright = DATA.read }

puts copyright if $. == 1

__END__
####################################
# Copyright (C) 2012 Kirill Lashuk #
####################################

```

取代後且備份原始檔:

```sh
$ cat bar.py
print 'hello'
$ ruby -p -i.bak copyright2.rb bar.py
$ cat bar.py
####################################
# Copyright (C) 2012 Kirill Lashuk #
####################################

print 'hello'
$ cat bar.py.bak
print 'hello'
```

# Links

* [One-liners by Josh Cheek](https://github.com/JoshCheek/Play/blob/master/ruby-one-liners/Readme.md)
* [Predefined global variables in Ruby](http://www.zenspider.com/Languages/Ruby/QuickRef.html#19)
* [ARGF doc](http://www.ruby-doc.org/core-1.9.2/ARGF.html)
* [Obscure and Ugly Perlisms in Ruby](http://blog.nicksieger.com/articles/2007/10/06/obscure-and-ugly-perlisms-in-ruby)
