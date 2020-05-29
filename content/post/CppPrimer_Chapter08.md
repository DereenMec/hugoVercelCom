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

确定一个流对象的状态最简单的方法就是将他当作条件使用，例如`while(cin >> s)`。**将流作为条件使用，只能告诉我们流是否有效，而无法告诉我们具体发生了什么。**

IO库定义了一个与机器相关的iostate类型，它可以配合上表中IO库定义的四个常量进行位运算来判断流的具体状态，使用方法类似于之前我们遇到的[quiz练习](https://happy.dereen.top/post/cppprimer_chapter04/#%E4%BD%8D%E4%B8%8E-%E4%BD%8D%E6%88%96-%E4%BD%8D%E5%BC%82%E6%88%96%E8%BF%90%E7%AE%97%E7%AC%A6)。例子：

```cpp
auto old_state = cin.rdstate();           // 记住cin的当前状态
cin.clear();                              // 使cin有效
process_input(cin);                       // 使用cin
cin.setstate(old_state);                  // 将cin置为原始状态
// 复位failbit和badbit，其他位保持不变
cin.clear(cin.rdstate() & ~cin.failbit & ~cin.badbit);
```

### 管理输出缓冲

每个输出流都管理一个缓冲区，用来保存程序读写的数据。导致缓冲刷新的原因有很多：

- 程序正常结束，作为main函数的return操作的一部分，缓冲刷新被执行；
- 缓冲区满了；
- 使用操纵符endl来显式的刷新缓冲区；
- 在每个输出操作之后，我们可以用操作符unitbuf设置流的内部状态，来清空缓冲区。默认情况下，cerr是设置unitbuf的，所以写到cerr 的内容都会被立即输出；
- 一个输出流可能被关联到另一个流。在这种情况下，当读写另一个流时，这个输出流也会被刷新，例如cout是被关联到cerr和cin 的。当我们调用cerr和cin时，cout就会被刷新。

#### 刷新输出缓冲区

除了endl，IO库中还有两个类似的操纵符：flush和ends。flush刷新缓冲区且不输出任何额外字符。ends输出内容和一个额外的空格。

```cpp
cout << "Ni" << flush;
cout << "Ni" << ends;
```

#### unitbuf操纵符

如果想在每次输出操作后都刷新缓冲区，可以使用unifbuf操纵符，使用完后使用nounitbuf重置。

```cpp
cout << unitbuf;
cout << " sd sd";           // 会立即输出
cout << nounitbuf;
```

#### 关联输入和输出流

当一个输入流被关联到一个输出流时，任何试图从输入流读取数据的操作都会首先刷新关联的输出流。我们使用tie方法来关联两个流。tie有两个重载的版本：一个版本不带参数，返回指向输出流的指针。如果本对象当前已经被关联到一个输出流，则返回的就是指向这个输出流的指针，如果对象未关联到流，则返回的是空指针。tie的第二个版本接受一个指向ostream的指针，将自己关联到ostream。

```cpp
cin.tie(&cout); 	// 仅用来展示标准库将cin关联到cout
// old_tie中保存了之前被关联的输出流
ostream * old_tie = cin.tie(nullptr);
cin.tie(&cerr);		// 将cin关联到cerr
cin.tie(old_tie);	// 将cin关联到cout
```

💡每个输入流最多关联到一个输出流，但一个输出流可以关联多个输入流。

## 文件输入输出

头文件fstream定义了三个类型来支持文件IO：

- ifstream从一个给定文件读取数据
- ofstream向一个给定文件写入数据
- fstream针对给定文件进行读写数据

**除了上一个表格中的内容通用之外，fstream还额外定义了下面的方法。**

|           方法           |                             含义                             |
| :----------------------: | :----------------------------------------------------------: |
|     `fstream fstrm`      |                    创建一个未绑定的文件流                    |
|    `fstream fstrm(s)`    | 创建一个fstream，并打开名为s的文件。s可以是string类型或指向c风格字符串的指针。默认的文件模式mode依赖于fstream的类型 |
| `fstream fstrm(s, mode)` |                与上面相同，但制定了打开的mode                |
|     `fstrm.open(s)`      |            打开名为s的文件，并将文件与fstrm绑定。            |
|     `fstrm.close()`      |                    关闭与fstrm绑定的文件                     |
|    `fstrm.is_open()`     |  返回一个bool指，指出fstrm关联的文件是否成功代开且尚未关闭   |

