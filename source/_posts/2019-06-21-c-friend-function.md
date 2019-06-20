---
title: C++ Friend Class and Function 
tags:
  - C++
  - Friend
categories:
  - Programming
date: 2019-06-21 00:19:15
---


## Friend

將另一個 class 設成 firend，可以讓該 class 存取自己 private data，參考底下的範例：

A 將 B 設成 firend，則物件 B 裡 private、protected、public function 都可以存取該物件 A 裡的 private、protected、public 的資料。

```c++
#include <iostream> 
class A { 
private: 
    int a; 
public: 
    A() { a=0; } 
    friend class B;     // Friend Class 
}; 
  
class B { 
private: 
    int b; 
public: 
    void showA(A& x) {
        // In B, you can access private member of A by adding friend
        std::cout << "A::a = " << x.a;   
    } 
}; 
  
int main() { 
   A a; 
   B b; 
   b.showA(a);
   return 0; 
} 
```

上面的程式碼代表著 A 的 private data 可以給 B 使用。

A 將某個 function 設成 firend，則該 function 都可以存取該物件 A 裡的 private、protected、public 的資料。

```c++
#include <iostream>
class A { 
    int a; 
public: 
    A() {a = 0;} 
    friend void showA(A&); // global friend function 
}; 
  
void showA(A& x) { 
    // In this function, you can access private member of A by adding friend
    std::cout << "A::a=" << x.a; 
} 
  
int main() { 
    A a; 
    showA(a); 
    return 0; 
}
```

上面的程式碼代表著 A 的 private data 可以給 showA function 使用。

friend function 可以放在 class 的任何地方，不受關鍵字 private、protected、public 的限制。

## Reference

- [Friend class and function in C++](https://www.geeksforgeeks.org/friend-class-function-cpp/)