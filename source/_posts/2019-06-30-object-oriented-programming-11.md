---
title: C++ 物件導向 - 抽象 Abstraction
tags:
  - C++
  - Object-oriented-programming (OOP)
  - Abstraction
categories:
  - Programming
date: 2019-06-30 23:52:14
---


抽象是一種設計 programming 的技術，需要將**介面 (interface)** 和**實作 (implementation)** 的分離。

> Abstraction is a programming (and design) technique that relies on the separation of interface and implementation.

可以想像成在設計 API 的介面，把介面透露給外部的知道，隱藏實作細節。

在 C++ 只需要把介面放在關鍵字 `public` 底下：

```c++
#include <iostream>
using namespace std;

class Adder {
   public:
      // constructor
      Adder(int i = 0) {
         total = i;
      }
      
      // interface to outside world
      void addNum(int number) {
         total += number;
      }
      
      // interface to outside world
      int getTotal() {
         return total;
      };
      
   private:
      // hidden data from outside world
      int total;
};

int main() {
   Adder a;
   
   a.addNum(10);
   a.addNum(20);
   a.addNum(30);

   cout << "Total " << a.getTotal() <<endl;  // 60
   return 0;
}
```

在設計時，必須分別讓介面跟實作互相獨立，在更改底層實作細節時，介面仍然可以保持不變。

## Reference

- [abstraction](https://whatis.techtarget.com/definition/abstraction)
- [Data Abstraction in C++](https://www.tutorialspoint.com/cplusplus/cpp_data_abstraction.htm)