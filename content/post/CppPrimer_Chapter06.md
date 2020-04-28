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



本章的重要内容有函数重载、默认实参，内联函数、constexpr函数以及函数指针。

<!--more-->

## 函数基础

一个典型的函数定义包含以下部分：返回类型、函数名字、由0个或多个形参组成的列表及函数体。

### 局部对象

在C++语言中，名字有作用域，对象有生命周期。形参和在函数体内部定义的变量统称为局部变量。

#### 自动对象

对于普通局部变量对应的对象来说，当函数的控制路径经过变量定义语句时创建该对象，当到达定义所在的块末尾时销毁它。这种只存在于块执行期间的对象称为自动对象，形参是一种自动对象。

#### 局部静态对象

某些时候，有必要令局部变量的生命周期贯穿函数调用及之后的时间。可以将局部变量定义成static类型从而获得这样的对象。**局部静态对象在程序的执行路径第一次经过对象定义语句时初始化，并直到程序终止才被销毁。**

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

#### ❗不要返回局部对象的引用或指针

```cpp
const string &manip(){
    string ret;
    if (!ret.empty())
        return ret;
    else return "Empty";
}
```

上面这个例子犯了一个明显的错误，它返回了局部兑现给的引用，局部对象在函数结束时就被销毁了，所以返回局部对象的引用导致的结果会是未定义的。

💡要想确保返回值安全，我们不妨提问：引用所引的时在函数之前已经存在的哪个对象。

#### 列表初始化返回值

C++11新标准规定，函数可以返回花括号包围的值的列表。

```cpp
vector<string> process(){
    if (expected.empty())
        return {};
    else if (expected == actual)
        return {"OK", "functionX"};
    else return {expected, actual, "Bad"};
}
```

#### 递归

如果一个函数调用了它自身，不管这种调用时直接的还是间接的，都称该函数为递归函数（recursive function）.例子：计算阶乘

```cpp
int factorial(int val) {
	if (val > 1)
		return factorial(val-1) * val;
	return 1;
}
```

在递归函数中，一定要有某条**路径**是不包含递归调用的，否则函数将永远的“递归”下去。

### 返回数组指针

 因为数组不能被拷贝，所以函数不能返回数组。不过，函数可以返回数组的指针或引用。虽然从语法上来说，要想定义一个返回数组的指针或引用的函数比较繁琐，但是有一些方法可以简化这个任务，其中最直接的方法是使用类型别名。

```cpp
typedef int arrT[10];           // arrT是一个类型别名，它表示的类型是含有10个整数的数组
using arrT = int[10];           // 同上
arrT* func(int i);              // func返回一个指向含有10个整数的数组指针
```

#### 声明一个返回数组指针的函数

```cpp
int (*func(int i))[10];
```

#### 使用尾指返回类型(C++11)

```cpp
auto func(int i) -> int (*)[10];
```

#### 使用decltype

```cpp
int odd[] = {1, 3, 5, 7, 9};
int even[] = {0, 2, 4, 6, 8};
decltype() *arrPtr(int i){
    return (i % 2) ? &odd : &even;
}
```

## 函数重载

如果同一作用域内的几个**函数名字相同但形参列表不同**，我们称之为**重载函数（overload function）**。

#### 定义重载函数

```cpp
Record lookup(const Account&);
Record lookup(const Phone&);
Record lookip(const Name&);
```

❗不允许两个函数除了返回类型外其他所有的要素都相同。

#### 重载和const对象

由于顶层const不影响传入函数的对象（会被消除），而底层const则会保留，所以：

```cpp
int func(const int i);
int func(int i);                // ⛔错误，重复
int func(const int *p);
int func(int *p);               // ✔正确
```

#### const_cast和重载

const_cast用于修改对象的底层const，其在重载函数的情境中最有用。例子：这里有一个比较两个常量字符串长短的函数，如果我们想要将其用到非常量对象，但是又不想重写函数，则可以：

```cpp
const string &shorterString(const string &s1, const string &s2) {
	return s1.size() <= s2.size() ? s1 : s2;
}

// 重载
string &shorterString(string &s1, string &s2) {
	auto &r = shorterString(const_cast<const string&>(s1), const_cast<const string&>(s2));
	return const_cast<string&>(r);
}
```

### 重载与作用域

当我们调用一个函数时，编译器首先寻找对该函数名的声明，一旦在当前作用域中找到了所需的名字，编译器就会忽略掉外层作用域中的同名实体。剩下的工作就是检查函数调用是否有效。即重载函数的声明应该放在同一个作用域中。

```cpp
void print(const string &);
void print(double);
void print(int);
void fooBar(int ival){
    void print(int);        // 新作用域，隐藏了其他的print
    print(ival);            // 正确
	print(3);               // 正确
	print(3.14);            // 正确，但是double会被转化为int然后调用的是print(int)
    print("nishuonimane");  // 错误，print(const string &) 被隐藏了
}
```

## 特殊用途语言特性

**默认实参、内联函数和`constexpr`函数**，这三种函数相关的语言特性，对大多数程序都很有用。

### 默认实参

```cpp
typedef string::size_type sz;
string screen(sz ht = 24, sz wid = 80, char background = ' ');
```

#### 使用默认实参调用函数

![](https://raw.githubusercontent.com/DereenMec/pics/master/img/20200428130850.png)

注意第四行，我们传入的'2'，实际上初始化了形参ht，这与我们想要的不同（background）。

#### 默认实参声明

对于函数的声明来说，通常一个函数只声明一次，但多次声明一个函数也是合法的。不过需要注意的是，**在给定的作用域中一个形参只能被赋予一次默认实参。**

```cpp
string screen(sz, sz, char background = ' ');   // 首次声明 
string screen(sz, sz, char = '');               // 错误，background被赋予2次默认实参
string screen(sz ht = 24, sz wid = 80, char);   // 正确
```

💡一旦某个形参被赋予了默认实参，则它后面的所有形参都要被赋予默认实参。即把有默认实参的形参放在函数声明参数列表的后面。

### 内联函数和constexpr函数

上文中有一个比较两个字符串，返回较短的一个的函数：

```cpp
const string &shorterString(const string &s1, const string &s2) {
	return s1.size() <= s2.size() ? s1 : s2;
}	
```

把这种规模较小的操作定义成函数有很多好处，主要包括：

- 阅读和理解其含义比较简单
- 使用函数调用可以确保行为的统一
- 在修改计算过程时较为便捷
- 函数可以被其他应用重复利用，省去了程序员重新编写的代价

同时，也有一个潜在的缺点：**调用函数一般比求等价表达式的值要慢一些。**在大多数机器上，一次函数调用其实包含着一系列工作：调用前要先保存寄存器，并在返回时恢复；可能需要拷贝实参；程序转向一个新的位置继续执行。

#### 内联函数可避免函数调用的开销

将函数定义为内联函数，通常就是将它在每个调用点上“内联地”展开。例如shorterString函数：

`cout << shorterString(s1, s2) << endl;`

将被展开为

`cout << (s1.size() <= s2.size() ? s1 : s2) << endl;`

要将一个函数声明为内联函数，只需在其前面加上关键字`inline`。

#### `constexpr`函数

`constexpr`函数是指能用于常量表达式[^2]地函数。定义`constexpr`函数地方法与其他函数类似，不过需要遵循几项约定：函数地返回类型及所有形参地类型都得是字面值类型；而且函数体中必须有且只有一条`return`语句。

```cpp
constexpr int new_size() { return 42; }
```

💡`constexpr`函数不一定返回常量表达式。

```cpp
constexpr size_t scale(size_t cnt) { return new_size() * cnt; }
```

上面地函数中，当`scale`的实参是常量表达式时，它的返回值时是常量表达式，反之则不然。

#### 把内联函数和constexpr的定义和声明都放在头文件中

和其他函数不一样，内联函数和constexpr函数可以在程序中多次定义。不过，对于某个给定的内联函数或者constexpr函数来说，它的多个定义必须完全一致。基于这个原因，内联函数和constexpr函数通常定义在头文件内。

### 调试帮助

C++程序员有时会用到一种类似头文件保护的技术，以便有选择地执行调试代码。基本思想是，程序可以包含一些用于调试地代码，但是这些代码只是在开发程序时使用。当应用程序编写完成准备发布时，要先屏蔽掉调试代码。这种方法用到两项功能：**assert**和**NDEBUG**。

#### assert预处理宏

assert是一种预处理宏，它的行为有点类似于内联函数。assert宏使用一个表达式作为它地条件：`assert(expr);`首先对expr求值，如果表达式为假，assert输出信息并终止程序地执行。如果表达式为真，assert什么也不做。在包含`cassert`头文件后，我们应该使用`assert`而不是`std::assert`，也不需要为assert提供using声明。

**assert宏常用于检查“不能发生”的条件。**

#### NDEBUG预处理变量

assert的行为依赖于一个名为NDEBUG的预处理变量的状态。如果定义了NDEBUG，则assert什么也不做。

## 函数匹配

函数匹配分为下面几步：

- 选定本次调用对应的重载函数集，集合中的函数称为**候选函数**。 
- 考察本次调用提供的实参，然后从候选函数中选出能被这组实参调用的函数，这些新选出的函数称为可行函数。
- 寻找最佳匹配

### 实参类型转换

为了确定最佳匹配，编译器将实参类型到形参类型的转换划分成几个等级，具体排序如下：

1. 精确匹配
2. 通过const转换实现的匹配。
3. 通过类型提升实现的匹配
4. 通过算术类型转换或指针转换实现的匹配
5. 通过类类型转换实现的匹配

## 函数指针

函数指针指向的是函数而非对象。和其他指针一样，函数指针指向某种特定类型。**函数的类型由它的返回类型和形参类型共同决定，与函数名无关。**

```cpp
bool lengthCompare(const string &, const string &);
```

上面这个函数的类型是`bool (const string &, const string &)`，要创建一个指向该类型的函数指针，只需要用指针替代函数名即可。

```cpp
bool (*pf)(const string &, const string &);
```

#### 使用函数指针

当我们把函数名作为一个值使用时，该函数自动地转换成指针。例如：

```cpp
pf = lengthCompare;
pf = &lengthCompare;
```

上面两条赋值语句都是正确的，取地址符是可选的。要使用函数指针，只需要：

```cpp
bool b1 = pf("Hello", "Nice");
bool b2 = (*pf)("Hello", "Nice");
```

上面的两条语句都是可以的。

#### 函数指针形参

和数组类似，虽然不能定义函数类型的形参，但是形参可以是指向函数的指针。

```cpp
void useBigger(const string &s1, const string &s2, bool pf(const string &, const string &));                    // 第三个形参是函数类型，会自动转换成指向函数的指针
void useBigger(const string &s1, const string &s2, bool (*pf)(const string &, const string &));             // 显式地将第三个参数定义成指向函数地指针
```

使用时我们可以直接将函数名传入，此时他会自动转换成指针，`useBigger(s1, s2, lengthCompare);`

#### 返回指向函数的指针

和数组类似，虽然不能返回一个函数，但是能返回指向函数的指针。**但是与上面不同的是，我们必须把返回类型写成指针形式，编译器不会自动地将函数返回类型当成对应的指针类型处理。**要想声明一个返回函数指针的函数，最简单的方法是使用类型别名。

```cpp
using F = int(int *, int);           // 函数类型
using PF = int(*)(int *, int);       // 指向函数的指针
```

使用：

```cpp
PF f1(int);          // 正确，PF是指向函数的指针
F f1(int);           // 错误，F是函数类型，不能作为返回类型
F *f1(int);          // 正确
int (*)(f1(int))(int *, int);         // 正确
```

和数组相同，也可以使用尾指返回类型

```cpp
auto f1(int) -> int (*)(int *, int);
```

#### 将auto和decltype用于函数指针类型

如果我们明确地知道返回地函数是哪一个，就能使用decltype简化书写函数指针返回类型地过程。

```cpp
string::size_type sumLength(const string &, const string &);
string::size_type largeLength(const string &, const string &);
decltype(sumLength) *getFcn(const string &);
```

**需要明确地一点是，使用`decltype(函数名)`返回地是函数类型而不是指向函数地指针，我们需要自行加上（*）号。**

[^1]: 所谓临时量对象就是当编译器需要一个空间来暂存表达式的求值结果时临时创建的一个未命名的对象。C++程序员常把临时量对象称为临时量
[^2]: 常量表达式（const expression）是指值不会改变并且在编译过程就能得到计算结果的表达式。显然，字面值属于常量表达式。用字面值初始化的const对象也是常量表达式。