---
title: C++ 物件導向 - 封裝 Encapsulation
tags:
  - C++
  - Object-oriented-programming (OOP)
  - Encapsulation
categories:
  - Programming
date: 2019-06-30 23:52:49
---


封裝是一個物件導向的概念，它將**屬性** (data) 和**操作屬性的行為** (function) 綁在一起，同時避免外部的干擾和誤用。

> Encapsulation is an Object Oriented Programming concept that binds together the data and functions that manipulate the data, and that keeps both safe from outside interference and misuse.

**data hiding** 能夠將不需要的資料隱藏起來，減少外部干擾和誤用。

C++ 提供三種成員 **public**, **protected** and **private**，可以使用 `private` 把不需要透露給外部的資料隱藏起來，讓外部無法存取。

```c++
class Box {
   public:
      double getVolume(void) {
         return length * breadth * height;
      }

   private:
      double length;      // Length of a box
      double breadth;     // Breadth of a box
      double height;      // Height of a box
};
```

另外 {% post_link c-friend-function 'Friend Class and Function' %} 有助於減少封裝，想法是讓每個物件的實作細節盡可能隱藏在其他物件中。

## Reference

- [encapsulation](https://searchnetworking.techtarget.com/definition/encapsulation)
- [Data Encapsulation in C++](https://www.tutorialspoint.com/cplusplus/cpp_data_encapsulation.htm)