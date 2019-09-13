---
title: C++ 物件導向 - 繼承 Inheritance
tags:
  - C++
  - Object-oriented-programming (OOP)
  - Inheritance
categories:
  - Programming
date: 2019-06-30 23:54:55
---


### 繼承 Inheritance

在定義物件時，**子類 (subclass)** 可以繼承一個或多個物件 classes。

> Inheritance is the concept that when a class of objects is defined, any subclass that is defined can inherit the definitions of one or more general classes

他們之間的關係是 **IS-A** 的關係，像是：夠**是 IS-A** 動物、貓**是 IS-A** 動物。

## Base and Derived Classes

- Base Class：是存在的一個 class
- Derived Class：從 base class 繼承下來的 class

在 C++ 要把 base class 繼承給 derived class 的表達式如下，derived class 可以繼承多個 base-class：

```
class derived-class: access-specifier base-class, access-specifier base-class...
```

access-specifier 關鍵字必須是要 **public**, **protected**, 或 **private** 的其中一個，通常會使用 **public**，如果不指定的話預設會是 **private**

## Access Control

Derived class 可以存取 base class 所有**非 private** 的成員

> A derived class can access all the non-private members of its base class.

下面的表格說明了，誰能夠存取到 base class 的哪個權限：

| Access          | public | protected | private |
| --------------- | ------ | --------- | ------- |
| Same class      | yes    | yes       | yes     |
| Derived classes | yes    | yes       | no      |
| Outside classes | yes    | no        | no      |

Derived class 繼承所有 base class 方法，但以下幾點不會被繼承：

- Constructors, destructors and copy constructors of the base class.
- Overloaded operators of the base class.
- The friend functions of the base class.

## 繼承型態

剛剛前面講到的 access-specifier 關鍵字必須是要 **public**, **protected**, 或 **private** 的其中一個，通常我們會使用 **public** 公開繼承

- **公開繼承 public inheritance**：將 base class 的**公開**成員會繼承到 derived class 的**公開**範圍裡面。將 base class 的**保護**成員會繼承到 derived class 的**保護**範圍裡面。base class 的**私人**成員則不繼承。
- **保護繼承 protected inheritance**：將所有 base class 的**公開和保護**成員會繼承到 derived class 的**保護**範圍裡面。
- **私人繼承 private inheritance**：將所有 base class 的**公開和保護**成員會繼承到 derived class 的**私人**範圍裡面。

## Example Code

```c++
#include <iostream>
 
using namespace std;

// Base class
class Shape {
   public:
      void setWidth(int w) {
         width = w;
      }
      void setHeight(int h) {
         height = h;
      }
      
   protected:
      int width;
      int height;
};

// Derived class
class Rectangle: public Shape {
   public:
      int getArea() { 
         return (width * height); 
      }
};

int main(void) {
   Rectangle Rect;
 
   Rect.setWidth(5);
   Rect.setHeight(7);

   // Print the area of the object.
   cout << "Total area: " << Rect.getArea() << endl;  // 35

   return 0;
}
```



## Reference

- [inheritance](https://whatis.techtarget.com/definition/inheritance)
- [C++ Inheritance](https://www.tutorialspoint.com/cplusplus/cpp_inheritance.htm)