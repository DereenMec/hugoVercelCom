---
title: "CppPrimer08.IO库"
date: 2020-04-29T11:20:09+08:00
lastmod: 2020-04-29T11:20:09+08:00
draft: true
tags: [C++, 学习笔记]
categories: [C++ Primer 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---



<!--more-->

## IO类

标准库定义的IO类型如下表，它们分别在三个头文件中：

| 头文件   | 类型                                                         |
| -------- | ------------------------------------------------------------ |
| iostream | `istream`， `wistream`从流读取数据<br />`ostream`， `ostream`向流写入数据<br />`iostream`，`wiostream`读写流 |
| fstream  | `ifstream`，`wifstream`从文件读取数据<br />`ofstream`，`wofstream`向文件写入数据<br />`fstream`，`wfstream`读写文件 |
| sstream  | `istringstream`，`wistringstream`从string读取数据<br />`ostringstream`，`wostringstream`向string写入数据<br />`stringstream`，`wstringstream`读写string |

**这些IO类型之间是继承关系，所以这些类型之间有许多操作是通用的，例如<<、>>表达式，getline函数。**

### IO对象无拷贝或赋值

由于不能拷贝IO对象，因此我们不能将形参或返回类型设置为流类型。进行IO操作的函数通常以引用方式传递和返回流。**读写一个IO对象会改变其状态，因此传递和返回的引用不能是const的。**

### 条件状态

IO操作一个与生俱来的问题是可能发生错误，其中有的是可修复的有的发生在系统深处，炒出来应用程序可修正的范围。下标列出了IO类所定义的一些**函数和标志**，可以帮助我们访问和操纵流的**条件状态**。下表中的strm是上个表格中所列出的任意一种IO类型。

| 函数及标志          | 含义                                                         |
| ------------------- | ------------------------------------------------------------ |
| `strm::iostate`     | iostate是一种机器相关的类型，提供了表达条件状态的完整功能。  |
| `strm::badbit`      | 用来指出流已崩溃                                             |
| `strm::failbit`     | 用来指出一个IO操作失败了                                     |
| `strm::eofbit`      | 用来指出流到达了文件末                                       |
| `strm::goodbit`     | 用来指出流未处于错误状态。此值保证为0                        |
| `s.eof()`           | 如果流的eofbit置位，返回true                                 |
| `s.fail()`          | 如果流的failbit置位，返回true                                |
| `s.bad()`           | 如果流的badbit置位，返回true                                 |
| `s.good()`          | 如果流的goodbit置位，返回true                                |
| `s.clear()`         | 将流s中所有条件状态复位，将流的状态设置为有效。返回void      |
| `s.clear(flags)`    | 根据给定的flags标志位，将流s中对应条件状态位复位。flags的类型为strm::iostate。返回void |
| `s.setstate(flags)` | 根据给定的flags标志位，将流s中对应条件状态位置位。flags的类型为strm::iostate。返回void |
| `s.rdstate()`       | 返回流s的当前状态，返回值类型为strm::iostate                 |

