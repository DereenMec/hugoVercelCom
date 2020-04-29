---
title: "CppPrimer07.类"
date: 2020-04-28T11:20:09+08:00
lastmod: 2020-04-28T11:20:09+08:00
draft: true
tags: [C++, 学习笔记]
categories: [C++ Primer 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

本章的重要内容：类、构造函数、友元、可变数据成员、explicit抑制隐式类型转换、类的静态成员。

<!--more-->

类的基本思想是数据**抽象（data abstraction）**和**封装（encapsulation）**。数据抽象是一种依赖于接口（interface）和实现（implementation）的编程技术。类的接口包括用户执行的操作；类的实现则包括类的数据成员、负责接口实现的函数体以及定义类所需的各种函数。

封装实现了类的接口和实现的分离，也就是说用户只能访问接口，不能访问实现部分。

本章的最终目的是设计一个`Sale_item`类，令其支持各种操作。这里直接给出代码：

```cpp
#pragma once
#include <iostream>
#include <string>
#include <utility>

class Sales_item {

    friend std::istream& operator>>(std::istream&, Sales_item&);
    friend std::ostream& operator<<(std::ostream&, const Sales_item&);
    friend bool operator<(const Sales_item&, const Sales_item&);
    friend bool operator==(const Sales_item&, const Sales_item&);

public:
    // constructors are explained in section 7.1.4, pages 262 - 265
    // default constructor needed to initialize members of built-in type
    Sales_item() = default;
    explicit Sales_item(std::string book) : bookNo(std::move(book)) {}
    explicit Sales_item(std::istream& is) { is >> *this; }

public:
    // operations on Sales_item objects
    // member binary operator: left-hand operand bound to implicit this pointer
    Sales_item& operator+=(const Sales_item&);

    // operations on Sales_item objects
    std::string isbn() const { return bookNo; }
    double avg_price() const;
    // private members as before
private:
    std::string bookNo;      // implicitly initialized to the empty string
    unsigned units_sold = 0; // explicitly initialized
    double revenue = 0.0;
};

// used in chapter 10
inline bool compareIsbn(const Sales_item& lhs, const Sales_item& rhs)
{
    return lhs.isbn() == rhs.isbn();
}

// nonmember binary operator: must declare a parameter for each operand
Sales_item operator+(const Sales_item&, const Sales_item&);

inline bool operator==(const Sales_item& lhs, const Sales_item& rhs)
{
    // must be made a friend of Sales_item
    return lhs.units_sold == rhs.units_sold && lhs.revenue == rhs.revenue &&
           lhs.isbn() == rhs.isbn();
}

inline bool operator!=(const Sales_item& lhs, const Sales_item& rhs)
{
    return !(lhs == rhs); // != defined in terms of operator==
}

// assumes that both objects refer to the same ISBN
Sales_item& Sales_item::operator+=(const Sales_item& rhs)
{
    units_sold += rhs.units_sold;
    revenue += rhs.revenue;
    return *this;
}

// assumes that both objects refer to the same ISBN
Sales_item operator+(const Sales_item& lhs, const Sales_item& rhs)
{
    Sales_item ret(lhs); // copy (|lhs|) into a local object that we'll return
    ret += rhs;          // add in the contents of (|rhs|)
    return ret;          // return (|ret|) by value
}

std::istream& operator>>(std::istream& in, Sales_item& s)
{
    double price;
    in >> s.bookNo >> s.units_sold >> price;
    // check that the inputs succeeded
    if (in)
        s.revenue = s.units_sold * price;
    else
        s = Sales_item(); // input failed: reset object to default state
    return in;
}

std::ostream& operator<<(std::ostream& out, const Sales_item& s)
{
    out << s.isbn() << " " << s.units_sold << " " << s.revenue << " " << s.avg_price();
    return out;
}

double Sales_item::avg_price() const
{
    if (units_sold)
        return revenue / units_sold;
    else
        return 0;
}
bool operator<(const Sales_item&, const Sales_item&)
{
    return false;
}

```

## 常量成员函数

C++允许把const关键字放在成员函数的参数列表之后，此时，紧跟在参数列表后面的const表示this（一般是隐式的）是一个指向常量对象的指针。像这样使用const的成员函数称为常量成员函数（const member function）。

```cpp
std::string isbn() const { return bookNo; }
```

## 类相关的非成员函数

如果函数在概念上不属于类但是不定义在类中，则它一般应与类声明在同一个头文件中。例如：

```cpp
inline bool compareIsbn(const Sales_item &lhs, const Sales_item &rhs) 
{ return lhs.isbn() == rhs.isbn(); }
```

## 构造函数

每个类都分别定义了它的对象被初始化的方式，类通过一个或几个特殊的成员函数来控制其对象的初始化过程，这些函数叫做构造函数（constructor）。类可以包含多个构造函数，和其他重载函数差不多，不同的构造函数之间必须在参数数量或参数类型上有所区别。

### 默认构造函数

类通过一个特殊的无需任何实参的构造函数来进行默认初始化，这个构造函数称为默认构造函数。如果一个构造函数为所有参数都提供了默认实参，则它实际上也定义了默认构造函数。

### 合成的默认构造函数

由编译器为我们隐式的创建的默认构造函数称为合成的默认构造函数（synthesized default constructor）。

❗只要当类中没有定义任何构造函数时，编译器才会为我们创建合成的默认构造函数。**也就是说，只要我们在类中定义了一个构造函数，就必须定义默认构造函数。**

💡在C++11新标准中，如果我们需要默认的行为，那么可以通过在参数列表后面写上`=default`来要求编译器生成构造函数。

### 委托构造函数

一个委托构造函数使用它所属类的其他构造函数执行它自己的初始化过程，或者说它把它自己的一些职责委托给了其他构造函数。

```cpp
class Sales_data{
public:
    Sales_data (std::string s, unsigned cnt, double price) : bookNo(s). units_sold(cnt), revenue(cnt * price) {}
    Sales_data () : Sales_data("", 0, 0) {}
    Sales_data (std::string s) : Sales_data(s, 0, 0) {}
    Sales_data (std::istream &is) : Sales_data() { read(is, *this); }
}
```

上面的除了第一个构造函数，都是委托构造函数。

## 友元

类可以允许其他类或者函数访问它的非公有成员，方法是令其他类或函数成为它的友元（friend）。

```cpp
friend std::istream& operator>>(std::istream&, Sales_item&);
friend std::ostream& operator<<(std::ostream&, const Sales_item&);
friend bool operator<(const Sales_item&, const Sales_item&);
friend bool operator==(const Sales_item&, const Sales_item&);
```

💡友元的声明仅仅指定了访问的权限，而非一个通常意义上的函数声明。如果我们希望类的用户能够调用某个友元函数，那么我们就必须在友元生命之外再对函数进行一次声明。

**下一个例子：**

## 可变数据成员

有时会发生这样一种情况，我们希望能够修改类的某个数据成员，即使是在一个const成员函数内。可以通过再变量的声明中加入mutable关键字做到这一点。

```cpp
class Test{
private:
    mutable int i = 0;
    void some_member() const {
        ++i;
    }
}
```

## 从const成员函数返回*this

一个const成员函数如果以引用的形式返回*this，那么它的返回类型将是常量引用。

## 隐式的类类型转换

C++中，编译器只允许一步隐式类型转换，例如`item.combine()`方法需要传入的是一个`Sales_item`对象，但是如果我们给它传入一个字符串字面值常量`item.combine("999-999-9999");`。

- `const char[]`会先转换为`string`
- `string`再转换成`Sales_item`类

有两步转换，所以`item.combine("999-999-9999")`会报错。需要改为`item.combine(std::string("999-999-9999"))`

### 用explicit抑制构造函数定义的隐式转换

关键字explicit只对一个实参的构造函数有效。需要多个实参的构造函数不能使用隐式转换，所以无需将这些构造函数指定为explicit的。

❗explicit只能用于直接初始化，不适用于拷贝初始化。

## 聚合类

聚合类（aggregate class）使得用户可以直接访问其成员，并且具有特殊的初始化语法形式。聚合类满足下列条件：

- 所有成员都是public的
- 没有定义任何构造函数
- 没有类内初始值
- 没有基类，也没有virtual函数

例子：

```cpp
struct Data {
    int ival;
    string s;
}
```

初始化一个聚合类：

```cpp
Data val1 = { 0, "Anc" };
```

💡如果列表中元素的个数少于类成员个数，那么靠后的成员会被值初始化。

## 字面值常量类

**数据成员都是字面值类型的聚合类是字面值常量类。如果一个类不是聚合类，但它符合下列要求，则它也是一个字面值常量类：**

- 数据成员必须都是字面值类型
- 类必须至少含有一个constexpr函数
- 如果一个数据成员含有类内初始值，则内置类型成员的初始值必须是一条常量表达式；或者如果成员属于某种类类型，则初始值必须使用成员自己的constexpr构造函数
- 类必须使用析构函数的默认定义，该成员负责销毁类的对象

```cpp
class Debug {
public:
    constexpr Debug(bool b = true) : hw(b), io(b), other(b) { }
    constexpr Debug(bool h, bool i, bool o) : hw(h), io(i), other(o) { }
    constexpr bool any() { return hw || io || other; }
    void set_io(bool b) { io = b };
    void set_hw(bool b) { hw = b };
    void set_other(bool b) { other = b };
private:
    bool hw;
    bool io;
    bool other;
}
```

## 类的静态成员

类的静态成员存在于该类的任何对象之外，对象中不包含任何与静态数据成员有关的数据。一般来说，我们不能在类的内部初始化静态成员。相反的，必须在类的外部定义和初始化每个静态成员，且每个静态成员只能被定义一次。而且我们不能在static函数体内使用this指针。

```cpp
class Account {
public:
    void calculate() { amount += amount * interestRate; }
	static double rate() { return interestRate; }
    static void rate(double);
private:
    std::string owner;
    static double interestRate;
    static double initRate();
}
```

