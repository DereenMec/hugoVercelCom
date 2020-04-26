---
title: "CppPrimer04.表达式"
date: 2020-04-24T13:31:42+08:00
lastmod: 2020-04-24T13:31:42+08:00
draft: false
tags: [C++, 学习笔记]
categories: [C++ Primer 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

本章最主要的内容有：左值和右值、求值顺序、**位运算符**、**类型转换**以及最后所附的**运算符优先级表**。

<!--more-->

## 基础

### 重载运算符

为已存在的运算符赋予另外一层含义，这种运算符称为**重载运算符（overloaded operator）。**例如IO库的<<,>>以及string和vector对象的加减乘除。

### 左值和右值

**当一个对象被用作右值的时候，用的是它的值（内容）；当对象被用作左值的时候，用的是对象的身份（在内存中的位置）。在需要右值的地方可以用左值来替代，但不能把右值当成左值使用。**

使用关键字decltype的时候，左值和右值有所不同。如果表达式的求值结果是左值，decltype作用于该表达式得到的是一个引用类型。举个例子，假定p的类型是int*，因为解引用运算符生成左值，所以decltype(*p)的结果是int&。另一方面，因为取地址符生成右值，所以decltype(&p)的结果是int**，即指向int型指针的指针。

### 求值顺序

在大多数情况下，不会明确指定求职的顺序，对于如下例子：

```cpp
int i = f1() * f2();
```

我们只知道，f1和f2会在执行乘法之前被调用，但是我们无法知道到底是f1和f2到底谁先被调用。**对于那些没有执行指定顺序的运算符来说，如果表达式指向并修改了同一个对象，将会引发错误并产生未定义的行为。**

```cpp
int i = 0;
cout << i << " " << ++i << endl;
```

**有4种运算符明确规定了运算对象的求值顺序。`&&`、`||`、`?:`和`,`运算符。**

### 赋值运算符

#### 赋值运算符满足右结合律

```cpp
int ival, jval;
ival = jval = 0;         // ival 和 jval 都被赋值为0
```

## 成员访问运算符

点运算符和箭头运算符都可用于访问成员，`ptr->mem`等价于`(*ptr).mem`

```cpp
string s = "Hello", *p = &s;
cout << p->size() << endl;
cout << (*p).size() << endl;        // 输出相同，均为5
```

❗由于解引用运算符`*`的优先级低于点运算符`.`，所以，上面第三行的括号是必须的。

## 条件运算符

```cpp
condition ? expr1 : expr2;
string s = (score < 60) ? "Yes" : "No";
```

### 嵌套条件运算符

```cpp
finalGrade = (grade > 90) ? "High pass" : (grade < 60) ? "fail" : "pass";
```

### 在输出表达式中使用条件运算符

条件运算符的优先级非常低，因此当一条长表达式中嵌套了条件运算子表达式时，通常要给它加上括号。

```cpp
cout << ((grade < 60) ? "fail " : "pass");     // 正确
cout << (grade < 60) ? "fail" : "pass";        // 输出1或者0
cout << grade < 60 ? "fail" : "pass";          // 错误，试图比较cout和60
```

## 位运算符

位运算符作用于**整数类型**的运算对象，并把运算对象看成是**二进制位**的集合。**位运算符提供检查和设置二进制位的功能。**

| 运算符  | 功能       | 用法                           |
| ------- | ---------- | ------------------------------ |
| ~       | 位求反     | ` expr                         |
| <<   >> | 左移、右移 | expr1 << expr2、expr1 >> expr2 |
| &       | 位与       | expr1 & expr2                  |
| ^       | 位异或     | expr1 ^ expr2                  |
| \|      | 位或       | expr1 \| expr2                 |

### 移位运算符

左移运算符在右侧插0，右移运算符如果是无符号类型则在左侧插0，带符号类型插入符号位的副本或0，视具体环境而定。

```cpp
unsigned char bits = 0233;
bits = bits << 1;
cout << bits << endl;          // 输出应该为6
```

首先，因为位操作是针对整型的，所以bits由unsigned char类型提升为整型字面值默认类型int；0223八进制转化为二进制为`10011011`，左移一位的结果为`00110110`，其十进制的数字为54，而char 54对应的字符就是6，所以输出为6。

### 位求反运算符

位求反运算符（`）将运算对象逐位求反后生成一个新值。

### 位与、位或、位异或运算符

- 位与，两个bit上都是1，结果才为1，否则位0；
- 位或，至少有一个bit上为1，结果就为1，否则位0；
- 位异或（不进位加法），两个bit上相同为0，不同为1；

例子：假设班级中有30个学生，老师每周都会对学生进行一次小测验，测验的结果只有通过和不通过两种。为了更好地追踪测验的结果，我们用一个二进制位代表某个学生在一次测验中是否通过，显然全班的测验结果可以用一个无符号long来表示。

```cpp
unsigned long quiz1 = 0；
```

教师必须可以对这个数的二进制位的每一位进行操作。例如序号位27的学生，为了达到这个目的，我们首先创建一个值，这个值只有第27位为1，其他位都是0，然后将这个值与quiz1进行**位或运算**，这样就能强行将quiz1的第27个bit设置为1，其他bit保持不变。

```cpp
quiz1 |= 1UL << 27;          // 生成一个值，只有第27个bit为1.
```

加入教师发现自己操作失误，需要将27位重新置为0，其他位保持不变，这时只需找一个特殊值，只有27位为0，其他位为1，然后与quiz1进行**位与操作**即可。

```cpp
quiz1 &= ~(1UL << 27);
```

如果我们要查询第27为同学在quiz1中的通过情况：

```cpp
bool status_27 = quiz1 & (1UL << 27);
```

❗理解位运算符的时候，千万不要把某一个bit的结果当作整体的输出结果。例如1001和1000的位与输出是1000转化为bool类型是true，并非false。

**移位运算符满足左结合律，且优先级低于加减乘除但高于关系运算符，赋值运算符和条件运算符。**

## sizeof运算符

sizeof运算符返回一个表达式或一个类型名字所占的字节数（byte）。sizeof运算符满足右结合律，返回值为1个size_t类型的**常量表达式**。sizeof运算对象有两种形式：

```cpp
sizeof (type);
sizeof expr;
```

在第二种形式中，sizeof返回的是表达式结果类型的大小。**与众不同的一点是，sizeof不计算其运算对象的值。**

💡因为sizeof不会实际求运算对象的值，所以即使sizeof的运算对象是一个无效的指针也不会有什么影响，在sizeof的运算对象中解引用一个无效的指针仍然安全。

C++11允许使用作用域运算符来获取类成员的大小：

```cpp
class TEST {
public:
    int a = 0;
} ;
cout << sizeof(TEST::a) << endl;       // 输出4
return 0;
```

sizeof运算符的结果部分依赖于其作用的类型，对指针执行返回指针本身大小，对引用执行返回被引用对象所占大小，**对string或vector对象进行运算返回固定大小，并非元素数目\*单元素大小。**

```cpp
string s = "Hi";
vector<int> vI = {1, 2};
cout << sizeof(s) << "  " << sizeof(vI) << endl;     // 输出32， 24
```

这里引申出求数组元素个数的操作：

```cpp
int a[] = {1, 2, 3, 4, 5};
int size_a = sizeof(a) / sizeof(*a);         // 5
```

## 逗号运算符

逗号运算符含有两个运算对象，按照从左到右的顺序依次求值。逗号运算符常常被用在for循环中，下面的例子进行降序赋值：

```cpp
vector<int> ivec = {1, 2, 3, 4, 5};
vector<int>::size_type cnt = ivec.size();
for (vector<int>::size_type ix = 0; ix != ivec.size(); ++ix, --cnt)
    ivec[ix] = cnt;
```

## 类型转换

### 隐式转换

自动完成，无需程序员介入；

#### 算术类型

```cpp
int a = 1 + 3.14;           // 3.14 先隐式转换为3然后再相加
```

#### 其他隐式类型转换

- 数组转换成指针；

```cpp
int ia[10];
int *p = ia;              // 数组名ia转换成指向数组首元素的指针
```

- 转换成布尔类型

```cpp
char *cp = get_string();
if (cp) /*...*/                // 如果指针cp不是0，则为真
if (*cp)  /*...*/              // 如果指针指向的不是空字符，则为真
```

- 转换成常量

```cpp
int a = 0;
cosnt int& ca = a;          // 非常量转换成const int 类型的引用
const int* cp = &a;         // 非常量的地址转换成const 的地址
```

- 类中自定义的转换

```cpp
while(cin >> a) /*...*/        // cin转换为布尔类型
```

### 显式转换

有时我们希望显式的将对象强制转换成另一种类型。例如，如果想在下面的代码中执行浮点数除法：

```cpp
int i, j;
double slope = i / j;
```

#### 命名的强制类型转换

```
cast-name(expression);
```

其中，type是转换的目标类型，而expression是要转换的值。如果type是引用类型，则结果是左值。cast-name是static_cast、dynamic_cast、const_cast和reinterpret_cast中的一种。

**static_cast，\**任何具有明确定义的类型转换，只要\**不包含底层const**，都可以使用static_cast。例如：

```cpp
double slope = static_cast<double>(i) / j;
```

static_cast对于编译器无法自动执行的类型转换也非常有用，例如将void*指针转换为它当初的类型：

```cpp
double d = 3.14;
void *p = &d;
double *dp = p;                         // 这行会报错，系统无法自动转换void*
double *dp = static_cast<double*>(p);   // 正确
```

**const_cast只能改变运算对象的底层const**

```cpp
const char *pc;
char *p = const_cast<char*>(pc);     // 正确，但是通过p写值是未定义的行为
```

#### reinterpret_cast（过于危险，不建议了解）

**❗尽量避免使用强制类型转换。**

#### 旧式的强制类型转换

```cpp
type (expr)          // 函数形式的强制类型转化
(type) expr          // C语言风格的强制类型转换
```

## 附录：运算符优先级表

![运算符优先级表](https://raw.githubusercontent.com/DereenMec/pics/master/img/_20200423125000.jpg)

