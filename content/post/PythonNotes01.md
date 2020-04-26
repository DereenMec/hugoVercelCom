---
title: "PythonNotes.正则表达式"
date: 2018-09-24T12:46:25+08:00
lastmod: 2018-09-24T12:46:25+08:00
draft: false
tags: [Python, 正则表达式]
categories: [Python 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

## 常用缩写字符及其含义表格查询

| 缩写字符分类 | 含义                                                   |
| :----------: | :----------------------------------------------------- |
|      \d      | 0-9的任意数字                                          |
|      \D      | 除0-9的数字以外的任何字符                              |
|      \w      | 任何字母、数字或下划线字符（可以认为是匹配“单词”字符） |
|      \W      | 除字母、数字和下划线意外的任何字符                     |
|      \s      | 空格、制表符或换行符（可以认为是匹配“空白”字符）       |
|      \S      | 除空格、制表符或换行符的任何字符                       |
|      ?       | 问号紧跟于分组后表示前面分组的内容是可有可无的         |
|      *       | 星号紧跟于分组后表示前面分组的内容匹配0次或多次        |
|      +       | 加号紧跟于分组后表示前面分组的内容匹配至少1次          |
|      \|      | 管道匹配多个分组                                       |
|      {}      | 花括号内加数字表示匹配特定字数                         |
|      .       | 句点表示匹配任何字符（除了换行）                       |
## 在Python中使用正则表达式的步骤

在Python中使用正则表达式主要有下面几个步骤：

1. 用`import re`导入正则表达式模块；
2. 用`re.compile()`函数创建一个Regex 对象；
3. 向Regex 对象的`search()`或`findall()`方法传入目标字符串；
4. 调用Matches 对象的`group()`方法，返回实际匹配文本的字符串。

## Python正则表达式详细说明

### 通过在字符串的第一个引号之前加r来向`re.compile()`传递原始字符串

Python中转义字符使用倒斜杠"\\"。假如我们需要打印出"\n"，则需要输入转义字符\\\，才能打印出一个倒斜杠。所以"\\\n"表示一个倒斜杠加上一个小写的n。但是，通过在字符串的第一个引号之前加上r，可以将该字符串标记为原始字符串，它不包括转义字符。

### 利用括号分组

假如我们想要匹配下面文本中的电话号码，并且将区号从中分离。则我们可以添加括号在正则表达式中创建“分组”：`(\d\d\d)-(\d\d\d\d\d\d\d\d)`。然后使用`group()`匹配对象方法，从第二个分组中获取需要的文本。正则表达式字符串中的第一对括号是第一组，第二对括号是第二组。向`group()`匹配对象方法传入整数1或2，就可以取得匹配文本的不同部分。向`group()`方法传入0或不传参数，将返回整个匹配的文本,`groups()` 方法可以一次获取所有的分组。**（注意groups()和group(0)的差异）**例如：

```python
>>> import re
>>> phoneNumRegex = re.compile(r"(\d\d\d)-(\d\d\d\d\d\d\d\d)")
>>> mo = phoneNumRegex.search("My number is 029-88888888. ")
>>> mo.group(1)
'029'
>>> mo.group(2)
'88888888'
>>> mo.group(0)
'029-88888888'
>>> mo.group()
'029-88888888'
>>> mo.groups()
('029', '88888888')
```

### 用管道匹配多个分组

字符“|”称为“管道”。希望匹配许多表达式中的一个时，就可以使用它。例如，正则表达式`r"Kitty|Tiny"`将匹配“Kitty”或“Tiny”。如果二者都在文本中，则第一个出现的将作为Match对象返回。例如：

```python
>>> nameRegex = re.compile(r"Kitty|Tiny")
>>> mo1 = nameRegex.search("Tiny and Rose and Kitty. ")
>>> mo1.group()
'Tiny'
```

假如我们希望匹配Batman、Batwoman、Batcar、Batmobile中的任意一个，因为这些单词都是以Bat开始，所以如果只指定一次前缀就会很方便。例如：

```python
>>> nameRegex_2 = re.compile(r"Bat(man|woman|car|mobile)")
>>> mo2 = nameRegex_2.search("Batt、Batmobile、Batwoman、Batcar、Batman")
>>> mo2.group()
'Batmobile'
>>> mo2.group(1)
'mobile'
```

### 问号紧跟于分组后表示前面分组的内容是可有可无的

例如在匹配电话号码的例子中，假如遇到没有区号的电话号码，前面例子中的代码是无法匹配的，我们可以通过在分组后加问号来解决。例如：

```python
>>> phoneNumRegex_2 = re.compile(r"(\d\d\d-)?(\d\d\d\d\d\d\d\d)")
>>> mo = phoneNumRegex_2.search("My phone number is 12356897. ")
>>> mo.group()
'12356897'
```

### 星号紧跟于分组后表示前面分组的内容匹配0次或多次

简单，不用解释，例如：

```python
>>> emRegex = re.compile(r"e(m)*")
>>> mo = emRegex.search("emmmmmmmmmmm fuck trump. ")
>>> mo.group()
'emmmmmmmmmmm'
```

### 加号紧跟于分组后表示前面分组的内容匹配至少1次

简单，不用解释，例如：

```python
>>> emRegex = re.compile(r"e(m)+")
>>> mo = emRegex.search("e fuck trump. ")
>>> mo.group()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'NoneType' object has no attribute 'group'  # 没找到
>>> mo = emRegex.search("em fuck trump. ")
>>> mo.group()
'em'
```

### 用花括号匹配特定次数

如果想要查找一个分组重复特定次数的文本，则在该分组后紧跟一个花括号，其中的数字表示重复的次数。例如：(ha){3}匹配字符串“hahaha”，(ha){1,3}匹配字符串“ha”、“haha”或“hahaha”。这个花括号里面的规则和数组切片的那个规则是一样的，如{3,}将匹配3次及以上实例；{,5}将匹配0到5次实例。例如：

```python
>>> haRegex = re.compile(r"(ha){1,}")
>>> mo = haRegex.search("hahahahahahahahahahah. ")
>>> mo.group()
'hahahahahahahahahaha'
```

### 用`.*`来匹配除了换行符之外的任意多个字符

.可以匹配任意字符（除了换行符），*表示之前分组重复0及以上次数。则`.*`组合可以匹配任意字符串（除换行符）。

### 贪心和非贪心匹配

在前面的例子中，`(ha){1,}`可以匹配1个或其他数量个ha，但为什么会返回最长的。这是因为Python的正则表达式默认是贪心的，这表示在有二义的情况下，他们匹配最长的字符串。要想让他返回最短的那个，即将他变成非贪心的，则需要在花括号后加一个问号，**这里注意区别前述中分组后加问号是表示可有可无的意思。**例如：

```python
>>> haRegex = re.compile(r"(ha){1,}?")
>>> mo = haRegex.search("hahahahahahahahahahah. ")
>>> mo.group()
'ha'
```

### `search()`方法返回第一个匹配到的对象，`findall()`方法返回所有匹配到的字符串

`search()`方法返回一个Match对象，这个对象拥有`group()`方法；`findall()`方法直接返回一个列表，而列表则没有`group()`方法。如下：

```python
>>> import re
>>> phoneNumRegex = re.compile(r"(\d\d\d-)?(\d\d\d\d\d\d\d\d)")
>>> mo = phoneNumRegex.search("Home: 029-88618871, Office: 010-36758954. ")
>>> mo
<_sre.SRE_Match object; span=(6, 18), match='029-88618871'>
>>> mo.group()
'029-88618871'
>>> mo_2 = phoneNumRegex.findall("Home: 029-88618871, Office: 010-36758954. ")
>>> mo_2
[('029-', '88618871'), ('010-', '36758954')]
>>> mo_2.group()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'list' object has no attribute 'group'
```

总结：

1. 如果调用在一个没有分组的正则表达式上，例如`\d\d\d-\d\d\d\d\d\d\d\d`，方法`findall()`将返回一个匹配字符串的列表，例如['029-88618871','010-36758954'];
2. 如果调用在一个有分组的正则表达式上，例如`(\d\d\d-)?(\d\d\d\d\d\d\d\d)`，方法`findall()`将返回一个字符串的元组的列表，每个分组对应元组中的一个字符串，例如：[('029-', '88618871'), ('010-', '36758954')]。

### 建立自己的字符分类

如果觉得想要匹配的字符由上面的缩写（`\s\d\w`等）来表示太宽泛，可以用方括号定义自己的字符分类。如[abcdeABCDE]或[a-eA-E]将匹配“abcdeABCDE”中的任意字符一次。**值得注意的是，方括号中普通的正则表达式符号不会被解释，这就意味着在匹配"."时不需要输入"\\."等。**

通过在左方括号后紧跟一个^字符，就可以得到“非字符类”。如[^a-eA-E]就匹配所有除了“abcdeABCDE”以外的任意字符一次。

### 插入字符和美元字符

在正则表达式的开始处使用插入符号（^），表明匹配必须发生在被查找文本的开始除。类似的，在正则表达式的末尾加上美元符号（$），表示该字符串必须以这个正则表达式的模式结束才可以匹配到。二者可以同时使用。例如：

```python
helloRegex = re.compile(r"^hello")
>>> mo = helloRegex.search("oh, hello!")
>>> mo_2 = helloRegex.search("hello, Kitty.")
>>> mo == None
True
>>> mo_2
<_sre.SRE_Match object; span=(0, 5), match='hello'>
```

### 使用`sub()`方法替换字符串

正则表达式不仅能够找到字符串，还能够捎带着替代找到的字符串，Regex对象的`sub()`方法需要传入两个字符串参数。第一个参数用来取代查找到的字符串，第二个参数为要查找的字符串。例如：

```python
>>> findXiamenRegex = re.compile(r"Xiamen")
>>> findXiamenRegex.sub("Xi'an", "Xiamen is the capital of Shaanxi.")
"Xi'an is the capital of Shaanxi."
```

### 通过传入`re.IGNORECASE`、`re.DOTALL`和`re.VERBOSE`来忽略大小写、匹配换行符和多行输入正则表达式

向`re.compile()`中传入re.IGNORECASE或re.I参数可以在匹配时忽略大小写；传入re.DOTALL参数来使句点能够匹配换行符；传入re.VERBOSE来进行复杂正则表达式的多行输入。

**`re.compile()`不支持同时传入多个以上的3个参数，我们可以使用管道符号"|"来进行多个参数的传入。例如`re.compile(r"fool", re.I|re.DOTALL|re.VERBOSE)`。**

## 小项目：提取剪贴板中的手机号码、座机号码和邮件地址，并转换为规范格式并重新写回剪贴板（做完就啥都懂了）

这个项目可以分为以下几步：

1. 从剪贴板取得文本；
2. 找出文本中所有的手机号码、座机号码和邮件地址；
3. 将它们粘贴回剪贴板。

首先我们通过网络搜索得知，国内座机号码区号位3-4位，且均位0开头；国内手机号码则都是1开头的，有的可能含有86或+86前缀。

代码如下：

```python
#! python3
# phoneAndEmail.py - Finds phone numbers and email addresses on the clipboard.

import pyperclip
import re

# create phone number regex

phone1_Regex = re.compile(r'''(
    (\+)?
    (86)?
    (1\d{2})
    (-|\s)?
    (\d{8})
    (\D)
)''', re.VERBOSE)

phone2_Regex = re.compile(r'''(
    (0\d{2,3})?
    [-]?
    (\d{8})
    (\-\d{1,4})?
)''', re.VERBOSE)

# create email regex
emailRegex = re.compile(r'''(
    [a-zA-Z0-9._%+-]+
    @
    [a-zA-Z0-9.-]+
)''', re.VERBOSE)

# Find matches in clipboard text.
text = str(pyperclip.paste())
matches = []

for groups in phone1_Regex.findall(text):
    phoneNum = groups[3] + groups[5]
    matches.append(phoneNum)

for groups in phone2_Regex.findall(text):
    phoneNum = groups[1] + groups[2]
    if groups[3] is not None:
        phoneNum += groups[3]
    matches.append(phoneNum)

for groups in emailRegex.findall(text):
    matches.append(str(groups))

# Copy results to the clipboard.
if len(matches) > 0:
    pyperclip.copy("\n".join(matches))
    print("Copied to clipboard: ")
    print("\n".join(matches))
else:
    print("No phone numbers or email address found. ")

```

复制一段含有手机号码和电子邮件地址的文本，然后运行程序试试吧。

## 更多Python正则表达式

更多Python正则表达式内容见：[官方文档](https://docs.python.org/zh-cn/3/library/re.html)

> [Python学习笔记]系列是我在学习《Python编程快速上手——让繁琐工作自动化(Automate The Boring Stuff With Python)》这本书时的学习笔记。通过自己再手敲一遍概念和代码，方便自己记忆和日后查阅。如果对你有帮助，那就更好了！