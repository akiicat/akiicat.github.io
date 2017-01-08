---
title:  "Ruby OptionParser Usage"
date:   2016-10-02 02:57:15 +0800
---

## OptionParser

這個東西在 python 應該是 setup script，而在 ruby 不知道叫什麼 google 了老半天終於被我找到了。用法上很簡單大概看 ruby doc 的範例就會使用了。

```ruby
require 'optparse'
require 'optparse/time'

options = {}
OptionParser.new do |opts|
  opts.banner = "Usage: example.rb [options]"

  opts.on("-v", "--[no-]verbose", "Run verbosely") do |v|
    options[:verbose] = v
  end

  # 傳入參數
  opts.on("-nNAME", "--name=NAME", "pass NAME arg with assignment") do |v|
    options[:verbose] = v
  end

  # 傳入參數
  opts.on("-r", "--require LIB", "pass LIB arg") do |v|
    options[:verbose] = v
  end

  # 檢查型態
  opts.on("-t", "--time [TIME]", Time, "pass TIME arg and check type") do |v|
    options[:verbose] = v
  end
end.parse!

p options
p ARGV
```

<!--excerpt-->

將 OptionParser 封裝

```ruby
require 'optparse'

Options = Struct.new(:name)

class Parser
  def self.parse(options)
    args = Options.new("world")

    opt_parser = OptionParser.new do |opts|
      opts.banner = "Usage: example.rb [options]"

      opts.on("-nNAME", "--name=NAME", "Name to say hello to") do |n|
        args.name = n
      end
    end

    opt_parser.parse!(options)
    return args
  end
end

options = Parser.parse %w[--name=akiicat]   # => #<struct Options name="akiicat">
options = Parser.parse %w[-nakiicat]        # => #<struct Options name="akiicat">
options.name                                # => 'akiicat'
```

[Ruby Doc 2.3.1 OptionParser](http://ruby-doc.org/stdlib-2.3.1/libdoc/optparse/rdoc/OptionParser.html)
