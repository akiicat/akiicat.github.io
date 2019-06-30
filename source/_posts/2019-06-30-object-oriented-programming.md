---
title: 物件導向程式設計 object-oriented-programming (OOP)
tags:
  - Object-oriented-programming (OOP)
  - Encapsulation
  - Abstraction
  - Inheritance
  - Polymorphism
categories:
  - Programming
date: 2019-06-30 23:55:02
---


物件導向程式設計 object-oriented-programming (OOP) 是一種**程式語言模型** (programming language model)，程式需要圍繞著**物件**而不是圍繞著 function 和邏輯。

> Object-oriented programming (OOP) is a programming language model in which programs are organized around data, or [objects](https://searchmicroservices.techtarget.com/definition/object), rather than functions and logic. An object can be defined as a data field that has unique attributes and behavior.

當你看到 class 跟 object 可以把它們看成是相同的東西，因為英文通常會寫成**一類物件 (a class of objects)**。

物件裡面可以包含兩個東西：

- 唯一的**屬性** unique attributes
- 操作屬性的**行為** behavior that manipulate it

物件導向裡面，行為又可以稱作為**方法 method** 

## 定義

物件導向有四個基本的原則，分別是：

### 封裝 Encapsulation

封裝是一個物件導向的概念，它將**屬性** (data) 和**操作屬性的行為** (function) 綁在一起，同時避免外部的干擾和誤用。**data hiding** 能夠把不需要透露給外部的資料隱藏起來，讓外部無法存取。

> Encapsulation is an Object Oriented Programming concept that binds together the data and functions that manipulate the data, and that keeps both safe from outside interference and misuse.

### 抽象 Abstraction

抽象是一種設計 programming 的技術，需要將**介面 (interface)** 和**實作 (implementation)** 的分離。簡單來說就是設計 API 的介面。在 C++ 需要把設計好的介面放在 `public` 底下。

> Abstraction is a programming (and design) technique that relies on the separation of interface and implementation.

### 繼承 Inheritance

在定義物件時，**子類 (subclass)** 可以繼承一個或多個物件 classes。

> Inheritance is the concept that when a class of objects is defined, any subclass that is defined can inherit the definitions of one or more general classes

### 多型 Polymorphism

在物件被繼承的時候，允許變數、函數或物件具有多於一種形式。相關的概念有 dynamic binding，在 C++ 裡面需要用到 `virtual`。

> Polymorphism is the characteristic of being able to assign a different meaning or usage to something in different contexts - specitically, to allow an entity such as a variable, a function or, an object to have more than one form.

## 比較 Encapsulation vs Abstraction

看完定義以後，會不會覺得封裝 (Encapsulation) 跟抽象 (Abstraction) 的定義有點類似：

- **封裝**強調的是綑綁 data 的機制，和使用這些 data 的 function

> **Data encapsulation** is a mechanism of bundling the **data**, and the **functions** that use them

- **抽象**強調的是暴露介面和隱藏實作細節的機制

> **Data abstraction** is a mechanism of exposing only the **interfaces** and hiding the **implementation** details from the user.

## Example Code

以下是程式碼範例：

- {% post_link object-oriented-programming-encapsulation '封裝 Encapsulation' %}
- {% post_link object-oriented-programming-abstraction '抽象 Abstraction' %}
- {% post_link object-oriented-programming-inheritance '繼承 Inheritance' %}
- {% post_link object-oriented-programming-polymorphism '多型 Polymorphism' %}

## Reference

- [object-oriented programming (OOP)](https://searchmicroservices.techtarget.com/definition/object-oriented-programming-OOP)
- [Data Encapsulation in C++](https://www.tutorialspoint.com/cplusplus/cpp_data_encapsulation.htm)

