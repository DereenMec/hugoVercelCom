---
title: "Chapter04.语句"
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