---
title:  "Ruby on Rails Debug ByeBug 使用"
date:   2016-08-10 19:32:54 +0800
---

## 使用
在程式碼中想要中斷的地方加入 `byebug`，程式執行到 `byebug` 這個位置則會停下來讓使用者 debug。而至於進入 Debug 頁面能幹麻，大致上跟 GDB Debug 差不多，一步一步的執行，然後檢查每一個參數的值。

```ruby
# 進入 byebug 頁面
[9, 18] in /Users/akiicat/someplace.rb
    9:     byebug
   10:
   11:     @message.save!
   12:
   13:
=> 14:     @path = conversation_path(@conversation)
   15:   end
   16:
   17:   private
   18:
(byebug) d @path
```

## 操作指令：

- `help`: 查看所有指令
- `list`: 列出程式碼，簡寫 `l`, `l-`, `l=`
- `next`: 執行到下一個 `end` 段落，簡寫 `n`
- `step`: 執行下一行，簡寫 `s`
- `display <params>`: 印出參數的值，簡寫 `disp <params>`
- `continue`: 退出 byebug 讓程式執行完，簡寫 `cont`
- `quit`: 退出 byebug 且結束 rails
