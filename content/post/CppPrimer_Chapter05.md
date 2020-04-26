---
title: "CppPrimer05.语句"
date: 2020-04-24T13:48:42+08:00
lastmod: 2020-04-24T13:48:42+08:00
draft: false
tags: [C++, 学习笔记]
categories: [C++ Primer 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---



本章主要介绍条件语句、循环语句、跳转语句和Try语句块以及异常处理。

<!--more-->

## 条件语句

### **if语句**

```cpp
if (condition) {
		/*...*/
} else if (condition) {
		/*...*/
} else {
		/*...*/
}
```

这部分需要注意的是，下面这个例子中，else匹配的是第二个if，虽然从缩进看起来好像是应该匹配第一个if。

```cpp
if (grade % 10 >= 3)
		if (grade % 10 > 7)
				lettergrade += '+';
else 
		lettergrade += '-';
```

这段代码本来是想给成绩末尾是0，1，2的添加”-“，给成绩末尾是8，9，10的添加”+“。但是实际的代码运行结果会是：成绩末尾是8，9，10的添加”+“，成绩末尾是3，4，5，6，7的却添加了”-“。**修正程序只需添加花括号：**

```cpp
if (grade % 10 >= 3){
		if (grade % 10 > 7)
				lettergrade += '+';
}
else 
		lettergrade += '-';
```

**完整程序：**

```cpp
const vector<string> scores = {"F", "D", "C", "B", "A", "A++"};
int grade = 92;
string letter_grade;

if (grade < 60) {
    letter_grade = scores[0];
} else letter_grade = scores[grade / 10 - 5];
if (grade % 10 >= 3) {
    if (grade % 10 > 7) letter_grade += '+';
} else
    letter_grade += '-';
cout << letter_grade << endl;
```

### switch语句

```cpp
switch (ch) {
		case 1:
				/*...*/
		case 2:
				/*...*/
		default:
				/*...*/
}
```

这里值得注意的有：

- case关键字后面的必须为整型常量表达式。如`case 3.14`和`case ival`都不可。
- 一般不要省略case分支最后的break语句。如果没写break语句，最好加一段注释说清楚程序的性能。
- 不得在case中进行变量的初始化。

## 迭代语句

### while语句

当不确定到底要迭代多少次时，使用while语句比较合适。

```cpp
while (condition) {
	/*...*/
}
```

### do while语句

do while语句不管条件为真为假，先执行一次循环体内的语句。

### for语句

```cpp
for (init-statemen; condition; expression){
    /*...*/
}
```

其中，for循环括号内的每个部分都可以设置多重语句：

```cpp
int cnt = 0;
for (int i = 1, j = 10; i < j && j != 16; ++j, i += 2) {
++cnt;
}
cout << cnt << endl;     // 输出6
```

### 范围for循环

```cpp
for (auto i : vI){
	cout << i << endl;
}
```

## 跳转语句

跳转语句终端当前的执行过程。C++语言提供了4种跳转语句：break、continue、goto和return。

- break：负责终止离他最近的while、do while、for或switch语句，并从这些语句之后的第一条语句开始继续执行。
- continue：终止最近的循环中的后续语句，并继续执行下个循环。
- goto：从goto语句无条件跳转到同一函数内的另一条语句。语法位：`goto label; label:/*...*/`

## try语句块和异常处理

在C++语言中，异常处理包括：

- throw表达式，异常检测部分使用throw表达式来表示它遇到了无法处理的问题。**我们说throw引发了异常。**
- try语句块，异常处理部分使用try语句块处理异常。try语句块以关键字try开始，并以一个或多个catch子句结束。因为catch语句处理异常，**因此其常被称为异常处理代码。**
- 一套异常类，用于在throw表达式和相关的catch子句之间传递异常的具体信息。

```cpp
Sales_item item1, item2;
while (cin >> item1 >> item2) {
    try {
        if (item1.isbn() != item2.isbn()) {
            throw runtime_error("Data must refer to same ISBN.");
        }
        cout << item1 + item2 << endl;
    } catch (runtime_error err) {
        cout << err.what() << "\nTry again? Enter y or n" << endl;
        char c;
        cin >> c;
        if (!cin || c == 'n') break;
    }
}
```

其中，`runtime_error	`是标准库异常类型的一种，定义在`stdexcept`头文件中，`what()`是其成员函数，返回的是初始化一个具体对象时所用的string对象的副本。

### 标准异常

C++标准库定义了一组类，用于报告标准库函数遇到的问题。这些异常也可以在用户编写的程序种使用，它们分别在以下四个头文件中：

- exception头文件定义了最通用的异常类exception。它只报告异常的发生，不提供任何额外信息。
- stdexcept头文件定义了常用的异常类，详细信息见后面的表格。
- new头文件定义了bad_alloc异常类型。
- type_info头文件定义了bad_cast异常类型。

|       异常       |                      含义                      |
| :--------------: | :--------------------------------------------: |
|    exception     |                  最常见的问题                  |
|  runtime_error   |          只有在运行时才能检测出的问题          |
|   range_error    |  运行时错误：生成的结果超出了有意义的值域范围  |
|  overflow_error  |              运行时错误：计算上溢              |
| underflow_error  |              运行时错误：计算下溢              |
|   logic_error    |                  程序逻辑错误                  |
|   domain_error   |        逻辑错误：参数对应的结果值不存在        |
| invalid_argument |               逻辑错误：无效参数               |
|   length_error   | 逻辑错误：试图创建一个超出该类型最大长度的对象 |
|   out_of_range   |       逻辑错误：使用一个超出有效范围的值       |

标准库异常类只定义了几种运算，包括创建或拷贝异常类型的对象，以及位异常类型的对象赋值。

❗我们只能以默认初始化的方式初始化exception、bad_alloc和bad_cast对象，不允许位这些对象提供初始值。其他异常类型的行为则恰好相反：应该使用string对象或C风格字符串初始化这些类型的对象，但是不允许使用默认初始化的方式。

异常类型只定义了一个名为what 的成员参数，该函数没有任何参数，返回值是一个指向C风格字符串的`const char*`。该字符串的目的是提供关于异常的一些文本信息。what函数返回的C风格字符串的内容和异常对象的类型有关。如果异常类型有一个字符串初始值，则what返回该字符串，对于其他无初始值的异常类型来说，what返回的内容由编译器决定。