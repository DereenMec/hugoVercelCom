---
title: "PythonNotes.调试"
date: 2018-10-02T12:46:25+08:00
lastmod: 2018-10-02T12:46:25+08:00
draft: false
tags: [Python]
categories: [Python 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

编码占了编程工作量的90%，调试占了另外90%，这是一个流传着的笑话。调试在编程中占有很大的分量，即使专业的程序员也一直在制造缺陷。

## 抛出异常

**抛出异常相当于是说：“停止运行这个函数中的代码，将程序执行转到except 语句”。**抛出异常使用raise 语句，在代码中，raise 语句包含以下部分：

- raise 关键字；
- 对Exception 函数的调用；
- 传递给Exception 函数的字符串，包含有用的出错信息。

**通常是调用该函数的代码知道如何处理异常，而不是该函数本身。所以常常会看到raise 语句在一个函数中，try 和except 语句在调用该函数的代码中。**例如：

```python
def boxPrint(symbol, width, height):
    if len(symbol) != 1:
        raise Exception('symbol must be a single character string.')
    if width <= 2:
        raise Exception('Width must be greater than 2.')
    print(symbol * width)
    for i in range(height - 2):
        print(symbol + (' ' * (width - 2)) + symbol)
    print(symbol * width)


for sym, w, h in (('*', 4, 4), ('0', 20, 5), ('x', 1, 3), ('ZZ', 3, 3)):
    try:
        boxPrint(sym, w, h)
    except Exception as err:
        print('An exception happened: ' + str(err))
```

这个代码的功能是调用一个名为`boxPint()`的函数，该函数的功能是打印一个盒子，其使用的字符为symbol ，形状为width * height 。如果出现参数不合法，则抛出异常，运行上面的代码，其输出如下：

```
****
*  *
*  *
****
00000000000000000000
0                  0
0                  0
0                  0
00000000000000000000
An exception happened: Width must be greater than 2.
An exception happened: symbol must be a single character string.
```

## 取得反向跟踪的字符串

Python 遇到错误时产生的错误信息称为“反向跟踪”。反向跟踪包含了出错消息、导致该错误的代码行号和导致该错误的函数调用的序列。这个序列称为“调用栈”。例如：

```python
def spam():
    bacon()


def bacon():
    raise Exception('This is an err message.')


spam()
```

运行上面的代码，输出如下：

```
Traceback (most recent call last):
  File "f:\Learn\Automate-the-Boring-Stuff-with-Python-Solutions\p10_debug\temp_pracitce.py", line 9, in <module>
    spam()
  File "f:\Learn\Automate-the-Boring-Stuff-with-Python-Solutions\p10_debug\temp_pracitce.py", line 2, in spam
    bacon()
  File "f:\Learn\Automate-the-Boring-Stuff-with-Python-Solutions\p10_debug\temp_pracitce.py", line 6, in bacon
    raise Exception('This is an err message.')
Exception: This is an err message.
```

通过反向追踪，可以看到该错误发生在第6行，在bacon() 函数中。这次特定的bacon() 调用来自第二行，在spam() 函数中，它又是在第9行被调用的。

如果我们想让程序在出现异常时继续运行，则可以将反向跟踪信息写入一个日志文件，并让程序继续运行。稍后，在准备调试程序时，再检查该日志文件。

```python
import traceback

try:
    raise Exception("This is an err message.")
except Exception:
    errFile = open('errInfo.txt', 'w')
    errFile.write(traceback.format_exc())
    errFile.close()
    print('The traceback info was written the errInfo.txt.')
```

检查该日志文件内容：

```
Traceback (most recent call last):
  File "f:\Learn\Automate-the-Boring-Stuff-with-Python-Solutions\p10_debug\temp_pracitce.py", line 4, in <module>
    raise Exception("This is an err message.")
Exception: This is an err message.
```

## 断言

断言保证特定的条件正确，针对的是程序员的错误，而不是用户的错误。对于那些可以恢复的错误（诸如文件没有找到，或用户输入了无效的数据），请抛出异常，而不是用assert 语句检测它。

```python
states = "open"
assert states == "open", "The states must be open."
states = "others"
assert states == "open", "The states must be open."
```

运行上面的语句，输出如下：

```
Traceback (most recent call last):
  File "f:\Learn\Automate-the-Boring-Stuff-with-Python-Solutions\p10_debug\temp_pracitce.py", line 4, in <module>
    assert states == "open", "The states must be open."
AssertionError: The states must be open.
```

### 在交通灯模拟中使用断言

假定你在编写一个交通信号灯的模拟程序。代表路口信号灯的数据结构是一个字典，以“ns”和”ew“为键，分别表示南北向和东西向的信号灯。这些键的值可以是“yellow”，“green”或“red”其中之一。

```python
fStreet_1 = {'ns': 'green', 'ew': 'red'}
gStreet_5 = {'ns': 'red', 'ew': 'green'}
```

上面的两个字典中存储了f 街第1路口和g 街第5路口的实时红绿灯数据，假如我们要编写一个函数，它接收一个路口参数，进行红绿灯的切换，该函数如下：

```
def switchLights(stoplight):
    for key in stoplight.keys():
        if stoplight[key] == 'green':
            stoplight[key] = 'yellow'
        elif stoplight[key] == 'yellow':
            stoplight[key] = 'red'
        elif stoplight[key] == 'red':
            stoplight[key] = 'green'
            
            
switchLights(fStreet_1)
```

运行这段代码，不会出现任何报错，但是这段代码明显是有问题的，即有可能出现一个方向是绿灯，一个方向是黄灯。为了保证不会出现这种路口失控的情况，我们可以添加一个断言，确保始终有一个路口的信号灯是红灯。我们可以添加下面这个断言：

`assert 'red' in stoplight.values(), "Neither light is red! " + str(stoplight) `

这样再运行程序就会报错并崩溃：

```
Traceback (most recent call last):
  File "f:\Learn\Automate-the-Boring-Stuff-with-Python-Solutions\p10_debug\temp_pracitce.py", line 15, in <module>
    switchLights(fStreet_1)
  File "f:\Learn\Automate-the-Boring-Stuff-with-Python-Solutions\p10_debug\temp_pracitce.py", line 13, in switchLights
    assert 'red' in stoplight.values(), "Neither light is red! " + str(stoplight)
AssertionError: Neither light is red! {'ns': 'yellow', 'ew': 'green'}
```

### 禁用断言

在运行Python时传入-O 参数，可以禁用断言。

## 日志

记日志是一种很好的方式，可以理解程序中发生的事，以及事情发生的顺序。Python 的logging 模块使得我们可以很容易创建自定义的消息记录。这些日志消息将描述程序执行何时到达日志函数调用，并列出你指定的任何变量当时的值。另一方面，确实日志信息表明有一部分代码被跳过，从未执行。

### 使用日志模块

要启用logging 模块，只需将下面代码复制到程序顶部。

```python
import logging
logging.basicConfig(level=logging.DEBUG, format=' %(asctime)s - %(levelname)s - %(message)s')
```

来看一个程序，这个程序用来计算数字的阶乘：

```python
import logging
logging.basicConfig(level=logging.DEBUG, format=' %(asctime)s - %(levelname)s - %(message)s')
logging.debug('Start of program')


def factorial(n):
    logging.debug('Start of factorial(%s)' % (n))
    total = 1
    for i in range(1, n + 1):
        total *= i
        logging.debug('i is ' + str(i) + ', total is ' + str(total))
    logging.debug('End of factorial(%s)' % (n))
    return total


print(factorial(5))
logging.debug('End of program')
```

运行这个程序，输出如下：

```
 2019-08-14 16:09:09,950 - DEBUG - Start of program
 2019-08-14 16:09:09,950 - DEBUG - Start of factorial(5)
 2019-08-14 16:09:09,950 - DEBUG - i is 1, total is 1
 2019-08-14 16:09:09,950 - DEBUG - i is 2, total is 2
 2019-08-14 16:09:09,951 - DEBUG - i is 3, total is 6
 2019-08-14 16:09:09,951 - DEBUG - i is 4, total is 24
 2019-08-14 16:09:09,951 - DEBUG - i is 5, total is 120
 2019-08-14 16:09:09,951 - DEBUG - End of factorial(5)
120
 2019-08-14 16:09:09,952 - DEBUG - End of program
```

可以看到，`logging.debug()`在打印消息时，会自动加上时间戳和日志级别。

### 为什么不使用print() 调试

输入`import logging`和` logging.basicConfig(level=logging.DEBUG, format=' %(asctime)s - %(levelname)s - %(message)s')`可能有一点不方便。但是这样带来的好处是，只需在顶部加上一句`logging.disable(logging.CRITICAL)`就可以禁用所有日志输出。而使用`print()` 调试完在删除时，存在工作量大且可能误删正常`print()`语句的烦恼。

### 日志级别

“日志级别”提供了一种方式，按重要性对日志消息进行分类。5个日志级别如下表所示：

|   级别   |      日志函数      |                         描述                         |
| :------: | :----------------: | :--------------------------------------------------: |
|  DEBUG   |  logging.debug()   |                最低级别。用于小细节。                |
|   INFO   |   logging.info()   |       用于记录程序一般事件，确认一切工作正常。       |
| WARNING  | logging.warning()  | 用于表示可能的问题，不会阻止程序工作，但将来可能会。 |
|  ERROR   |  logging.error()   |         用于记录错误，将导致程序做某事失败。         |
| CRITICAL | logging.critical() |        最高级别，将导致程序崩溃完全停止工作。        |

当然，日志级别是一种建议，具体使用哪个日志函数，完全由程序员来决定。

`logging.basicConfig(level=logging.DEBUG)`中的`level=logging.DEBUG`可以控制日志的最低显示级别，如果在后期你只关注较高级别的消息，就可以设置`level=logging.ERROR`这样就只会显示ERROR 和CRITICAL 级别的消息了。例如：

```python
import logging
logging.basicConfig(level=logging.ERROR, format=' %(asctime)s - %(levelname)s - %(message)s')

logging.debug("This is a debug message.")
logging.info("I successfully be used.")
logging.warning("I should be int.")
logging.error("I must greater than 13.")
logging.critical("program crashed.")
```

运行上面代码，输出如下，可以看到ERROR 以下级别的日志消息都没有被输出了。

```
2019-08-15 11:25:29,254 - ERROR - I must greater than 13.
2019-08-15 11:25:29,254 - CRITICAL - program crashed.
```

### 禁用日志

在调试完成后，如果我们不再需要任何的日志消息，可以在头部添加`logging.disable(logging.CRITICAL)`这将禁止CRITICAL 及其以下级别的所有日志消息输出，即全部日志消息。通过给`logging.disable()`传入一个级别参数，将禁止该级别和更低级别的所有日志消息输出。

### 将日志记录到文件

除了将日志消息显示在屏幕上，还可以将他们写入文本文件。`logging.basicConfig()`函数接收filename 关键字作为参数，如下：

```
import logging
logging.basicConfig(filename='projectLog.txt', level=logging.ERROR, format=' %(asctime)s - %(levelname)s - %(message)s')
```

## IDLE的调试器

太简单了，此处不介绍。

> [Python学习笔记]系列是我在学习《Python编程快速上手——让繁琐工作自动化(Automate The Boring Stuff With Python)》这本书时的学习笔记。通过自己再手敲一遍概念和代码，方便自己记忆和日后查阅。如果对你有帮助，那就更好了！