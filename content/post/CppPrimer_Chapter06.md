---
title: "CppPrimer06.函数"
date: 2020-04-25T11:20:09+08:00
lastmod: 2020-04-25T11:20:09+08:00
draft: true
tags: [C++, 学习笔记]
categories: [C++ Primer 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---



<!--more-->

## 函数基础

一个典型的函数定义包含以下部分：返回类型、函数名字、由0个或多个形参组成的列表及函数体。

### 局部对象

在C++语言中，名字有作用域，对象有生命周期。形参和在函数体内部定义的变量统称为局部变量。

#### 自动对象

对于普通局部变量对应的对象来说，当函数的控制路径经过变量定义语句时创建该对象，当到达定义所在的块末尾时销毁它。这种只存在于块执行期间的对象称为自动对象，形参是一种自动对象。

#### 局部静态对象

某些时候，有必要令局部变量的生命周期贯穿函数调用及之后的时间。可以将局部变量定义成static类型从而获得这样的对象。**局部静态对象在程序的执行路径第一次经过对象定义语句时初始化，并知道程序终止才被销毁。**

```cpp
size_t count_calls(){
    static size_t cnt = 0;
    return ++cnt;
}

int main() {
    for (int i = 0; i <= 10; ++i){
        count_calls();
    }
    cout << count_calls() << endl;           // 输出12
}
```

### 函数声明

💡建议在头文件中进行函数声明，而在源文件中进行函数的定义，源文件应该包含头文件。

### 分离式编译

大多数编译器提供了分离式编译每个文件的机制，这一过程通常会产生一个后缀名是`.obj`（Windows）或`.o`（Unix）的文件，后缀名的含义是该文件包含对象代码。

## 参数传递

当形参是引用类型时，我们说它对应的实参被**引用传递**或者**函数被传引用调用**。当实参的值被拷贝给形参时，形参和实参是两个相互独立的对象。我们说这样的实参是被**值传递**或者**函数被传值调用**。

### 传值参数

- 在C++语言中，建议使用引用类型的形参代替指针；
- 如果函数无需改变引用形参的值，最好将其声明为常量引用；

```cpp
void func(int &a){/*...*/}     // 引用传递
void func(int a){/*...*/}      // 值传递
```

### const形参和实参

在形参初始化的时候，顶层const会被忽略：

```cpp
int func(int val){
    return ++val;
}

int main() {
    const int i = 20;
    cout << func(i) << endl;       // 输出21
}
```

💡尽量使用常量引用。

### 数组形参

数组有两个为人熟知的特点：**不可拷贝和数组名可以转化为指针，**所以我们无法以值传递的方式使用数组参数，**当我们把数组传递给函数时，时加上传递的是指向数组首元素的指针。**

因为数组是以指针的形式传递给函数的，因此一开始函数并不知道数组的尺寸，调用者应该为此提供一些额外的信息。管理指针形参有三种常用的技术：

#### 使用标记指定数组长度

例如将字符数组的最后一个元素置为`\0`，即C风格字符串。

#### 使用标准库规范

即传递指向数组首元素和尾后元素的指针，这种技术受标准库技术的启发。

#### 显示传递一个表示数组大小的形参

即专门定义一个表示数组大小的形参。

```cpp
char cs[] = {'H', 'a', 'p', 'p', 'y', '\0'};     // 方法1
char* beg = &cs[0], *end = &cs[5];
void print(const char *beg, const char *end);    // 方法2
void print(const char[], size_t size);           // 方法3
```

#### 数组形参和const

上面的例子中将，数组形参定义成了一个底层const，这就意味着不能通过传入的指针来修改数组中元素的值。

#### 数组引用形参

```cpp
void print(int (&arr)[10])
{
	for (auto elem ; arr)
		cout << elem << endl;
}
```

❗值得注意的是，`&arr`两端的括号必不可少，`f(int &arr[10])`将arr声明成了引用的数组，`f(int (&arr)[10])`正确，为数组的引用。

#### 传递多维数组

和所有数组一样，当将多维数组传递给函数时，真正传递的是指向数组首元素的指针。而数组的第二维（以及后面所有维度）的大小都是数组类型的一部分，**不能省略**。

```cpp
void print(const int (*matrix)[10], int rowSize) {
	/*...*/;
}
```

❗值得注意的是，`*matrix`两端的括号必不可少，`print(int *matrix[10])`将arr声明成了10个指针构成的数组，`print(int (*matrix)[10])`正确，为指向10个元素大小的数组的指针。

### main处理命令行选项

```cpp
int main(int argc, char *argv[]){
	/*...*/
}
int main(int argc, char **argv){
	/*...*/
}
```

当我们执行命令`main -d -o ofile data()`后，argv的第一个元素指向程序的名字或一个空字符串，接下来的元素一次传递给命令行提供的实参。最后一个指针之后的元素值保证为0。在此例中：

```cpp
#include <iostream>

using namespace std;

int main(int argc, char **argv) {
	for (int i = 0; i < argc; ++i) {
		cout << argv[i] << endl;
	}
}

依次输出
C:\dev\cppseries\LearnCpp\Debug\HelloWorld.exe
-d
-o
ofile
data()
```

### 含有可变形参的函数

为了编写能处理不同数量实参的函数，C++11新标准提供了两种主要的方法：

- 如果所有的实参类型相同，可以传递一个名为`initializer_list`的标准库类型；
- 如果实参的类型不同，我们可以编写一种特殊的函数，也就是所谓的可变参数模板（见后文）。

#### initializer_list形参

initializer_list类型定义在同名的头文件中，它提供的操作如下表所示：

| 用法                                      | 含义                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| `initializer_list<T> lst;`                | 默认初始化T元素类型的空列表                                  |
| `initializer_list<T> list{a, b, c, ...};` | lst的元素数量和初始值一样多，lst的元素是对应初始值的副本；列表中的元素是const |
| `lst2 = lst;`或`lst2(lst)`                | 拷贝或赋值一个`initializer_list`对象不会拷贝列表中的元素；拷贝后原始列表和副本共享元素 |
| `lst.size()`                              | 列表中的元素数量                                             |
| `lst.begin()`                             | 返回指向lst首元素的指针                                      |
| `lst.end()`                               | 返回指向lst尾后位置的指针                                    |

例子：

```cpp
void error_msg(initializer_list<string> il) {
	for (auto &i : il)
		cout << i << " ";
	cout << endl;
}

int main() {
	string expected = "Good", actual = "Bad";
	if (expected != actual)
		error_msg({ "Error, ", expected, "!=", actual, "." });
	else error_msg({ "Correct." });
}
```

**和vector不同的是，initializer_list元素的类型永远是const，我们无法改变initializer_list中元素的值。**

#### 省略符形参

```cpp
void foo(parm_list, ...)
```

省略符形参（...）是为了便于C++程序访问某些特殊的C代码而设置的，这些代码使用了名为varargs的C标准库功能。

❗省略符形参应该**仅仅用于C和C++通用的类型**。特别应该注意的是，大多数类型的对象在传递给省略符形参时都无法正确拷贝。

## 返回类型和return语句

return语句终止当前正在执行的函数并将控制权返回到调用该函数的地方。

### 无返回值的函数

返回void的函数不要求非得有return语句，因为这类函数的最后一句后面会隐式地执行return。

### 有返回值的函数

只要函数返回类型不是void，则该函数内的每条return语句必须返回一个值。return语句返回值的类型**必须与函数的返回类型相同，或者能隐式地转换成函数的返回类型**。

#### 值是如何被返回的

返回一个值的方式和初始化一个变量或形参的方式完全一样：返回的值用于初始化调用点的一个临时量[^1]，该临时量就是函数调用的结果。



[^1]: 所谓临时量对象就是当编译器需要一个空间来暂存表达式的求值结果时临时创建的一个未命名的对象。C++程序员常把临时量对象称为临时量