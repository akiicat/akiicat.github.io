---
title:  "Ruby Regexp MatchData"
date:   2017-03-06 14:49:35 +0800
---

<!--excerpt-->

https://stackoverflow.com/questions/22827566/ruby-parameters-by-reference-or-by-value

# reference types
# 跟
# pass by reference
# 這兩的東西很讓人困擾，但實際上並沒有太大的差別

# references and values
# values that are reference

# 如果是 pass by reference 的話，那 function 內的參數(parameters)會指給某個剛要傳入變數(variable)
# 而且你對他做任何的修改時，都會影響到原本的變數
#
# 可以看下面的例子
# Ruby 不是 pass by reference，那他到底是什麼

def inc(val)
  val += 1
end

a = 1
inc a
puts a

# 如果 Ruby 是 pass-by-reference 傳指的話，那他應該會印出 2，因為 `val += 1` 會增加 a 的值
# 但他不是會印出 2 ，這之中發生了什麼事了。
# 因為 val 並不是指向 a 的變數，val 他是一個獨立的變數，而且跟 a 有著同樣的值。
#
# 等一下，ruby 不是物件導向的語言嗎，所有看到的東西都是物件，那物件在傳遞的時候不是應該要 passed by reference 嗎？ Right?
# 不對

def change_string(str)
  str[0] = "NOOOO"
  str << " I can insult you all you want"
  str << " because you'll never see this"
  str << " because I'm going to replace the whole string!"
  str << " Haha you smell bad!"
  str = "What? I didn't say anything." # I'm so sneaky
end

be_nice_to_me = "hello"
change_string(be_nice_to_me)
puts be_nice_to_me

# 如果是 pass-by reference 的話，經過 change_method 這個方法過後，最後都會因為這行
# `str = "What? I didn't say anything."`
# 替換掉原本的 be_nice_to_me 的值
# 很棒的他最後不會是 `What? I didn't say anything`.
# 所以他應該不是 pass by reference
#
# 但事實上，be_nice_to_me 的值卻改變了成 `hello I can ...`
# 如果 Ruby 不是 pass by reference 的話，他是怎麼做到的？
#
#
# 還記得開始有提到 reference types
# 這就是 Ruby 裡提到的物件
# reference type 是一個 type，他的值會指向某些東西
# 在這個例子當中，參數的值(variable's value) 是指給一個 "hello" 的 string
# 當你傳入這個 string，參數的值會是一個指摽 (is a reference)，而且會複製一份指標 (也就是有兩個變數同時指向這個 string)
# 所以現在有兩個 references (be_nice_to_me, str) 會指到相同的 object (string)
# 而不是 str 指到 be_nice_to_me
# 所以當你修改`物件`的時候，就會改變到原先的值，因為他們都指到相同的物件 (string)
# 但是當你修改`變數`的時候，你就沒辦法看到原先的物件了，因為他現在指到的是另外一個物件
#
# 所以 Ruby 是 pass by reference 還是 pass by value 呢？
# 答案是 pass by value
# 但所有的 value 都是 reference
