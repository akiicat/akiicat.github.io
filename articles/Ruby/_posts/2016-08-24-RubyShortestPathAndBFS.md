---
title:  "Ruby 用 BFS 找最短路徑"
date:   2016-08-25 02:35:12 +0800
---

## 說明
一個二維陣列，裡面有四個東西 `s` `g` `p` `w`，分別代表的意義如下

- `s`: `start` 開始，只能有一個
- `g`: `goal` 終點，只能有一個
- `p`: `path` 路徑，可以走訪過的地方
- `w`: `wall` 牆壁，無法行走

要從 `s` 開始的點經過 `p` 走到 `g` 的地方，`w` 是障礙物無法經過的地方路徑，設法找出路徑的最短距離。

### 操作

```ruby
arr = [                                     # 隨便一個只有 s g p w 在裡面的二維陣列。
  ["p", "s", "p", "w"],
  ["p", "p", "w", "p"],
  ["p", "w", "w", "p"],
  ["p", "w", "w", "g"],
  ["p", "p", "p", "p"],
]

block = Block.new(arr)                      # 操作
p bfs = block.bfs

```
### 輸出結果
跑過 `bfs` 這個方法後，從原點能走過的路徑都會標示距離。

```sh
(4, 5)
[1, 0, 1, w]
[2, 1, w, p]
[3, w, w, p]
[4, w, w, g]
[5, 6, 7, 8]
```

如果要找終點最近距離的話，只需要找終點周圍最小的數值。因為起點是 0 所以最後再加上 1。

```ruby
goal   = block.key("g")                     # => [3, 3]
nearby = bfs.around(goal).map { |i|
  (Numeric === bfs[i]) ? bfs[i] : nil       # 把所有周圍的文字轉成 nil
}.compact                                   # 刪除所有 nil 值 => [8]

if nearby.empyt?
  puts "Fail"                               # 若無法到達則印出 Fail
else
  puts nearby.min + 1                       # 最短距離 => 9
end
```

<!--excerpt-->
## 內部運作
個人比較喜歡用 `Hash` 來操作，至於每個方法在做什麼事情如下：

- `initialize`: 將這個 Class 初始化，當然就是傳入二維陣列。
- `around`: 這個方法主要就是用來連接周圍的點用的，給他一個點能找到周圍其他點的位置，在資料結構上也就是用來連接周圍的東西用的。
- `bfs`: 廣度優先搜尋來找到目標，從起點加入序列，然後慢慢從周圍推進取列找起到目標的位置。
- `to_a`: 轉換成二維陣列
- `inspect`: 用來顯示這個 Class 的方法。

```ruby
class Block < Hash
  attr_accessor :m, :n

  def initialize(arr)               # 初始化 傳入一個二維陣列轉存成 Hash 且紀錄陣列的長寬
    @m, @n = arr.first.length, arr.length
    arr.each_with_index do |row, x|
      row.each_with_index do |value, y|
        self.store( [x, y], value)
      end
    end
  end

  def around(key)                   # 回傳周圍且值不為 nil 的 key ，型態陣列
    keys = [
      [key[0]     , key[1] + 1],    # top
      [key[0] + 1 , key[1]    ],    # right
      [key[0]     , key[1] - 1],    # button
      [key[0] - 1 , key[1]    ],    # left
    ]
    keys.map { |key| (self[key]) ? key : nil }.compact
  end

  def bfs                           # 回傳 Block 且標示每個走訪過後的值
    block = self.dup

    from  = block.key("s")          # 開始的位置
    to    = block.key("g")          # 結束的位置

    block[from] = 0                 # 令原點為 0
    queue = [from]                  # 加入 bfs 的序列

    queue.each_with_index do |q, i|
      around(q).each do |key|       # 走訪周圍的點
        if  block[key] == "p"       # 如果還沒走過，標記距離然後推入序列
          block[key] = block[q] + 1
          queue.push(key)
        end
      end
    end
    return block
  end

  def to_a                      # 轉換成陣列
    arr = Array.new
    @n.times do |i|
      row = Array.new
      @m.times do |j|
        key = [i, j]
        row.push(self[key])
      end
      arr.push(row)
    end
    return arr
  end

  def inspect                    # 顯示用
    str = "(%d, %d)\n" % [@m, @n]
    to_a.each do |row|
      str << row.to_s.tr('\"\'', '') + "\n"
    end
    return str
  end
end
```
