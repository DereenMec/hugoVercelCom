---
title: "PythonNotes.爬虫"
date: 2018-10-06T12:46:25+08:00
lastmod: 2018-10-06T12:46:25+08:00
draft: false
tags: [Python, 爬虫]
categories: [Python 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---
要使用Python 抓取网页，首先我们要学习下面四个模块：

| 包             | 作用                                                     |
| -------------- | -------------------------------------------------------- |
| webbrowser     | 打开浏览器获取指定页面；                                 |
| requests       | 从因特网下载文件和网页；                                 |
| Beautiful Soup | 解析HTML，即网页编码的格式；                             |
| selenium       | 启动并控制一个Web 浏览器，能够填写表单，并模拟鼠标点击。 |

## 小项目：利用webbrowser 模块的bilibiliSearch.py

webbrowser 模块的`open()`函数可以启动一个新浏览器，打开指定的URL。

```python
import webbrowser
webbrowser.open("https://bilibili.com")
```

例如运行上面的这段代码，系统就会启动一个新的标签页，打开B站了。但是，这也是webbrowser 模块唯一能做的事了。

假如我们要实现一个功能：使用Bilibili 搜索指定或剪贴板中的文字。那我们的程序需要做到：

- 从命令行参数或剪贴板中取得要搜索的内容；
- 打开Web 浏览器，指向搜索结果。

### 弄清指定地点的Bilibili Search Url 地址

我们首先在B站手动搜索“孙笑川 鬼畜”，可以发现地址栏的Url 网址为：“[https://search.bilibili.com/all?keyword=孙笑川%20鬼畜](https://search.bilibili.com/all?keyword=孙笑川%20鬼畜)”，于是我们可以发现，只要将要搜索的内容放在keyword= 后即可，多个关键字用空格分开，在网址中为`20%`表示。

### 处理命令行参数

为了从命令行参数中导入要搜索的内容，我们需要导入sys 包，并使用`sys.argv`来取得其内容。

### 处理剪贴板内容

使用pyperclip 包中的`pyperclip.paste()`函数来取得剪贴板中的内容。

于是，整个程序的代码如下：

```python
import webbrowser, sys, pyperclip

if len(sys.argv) > 1:
    # Get address from command line.
    address = ' '.join(sys.argv[1:])
else:
    # Get address from clipboard.
    address = pyperclip.paste()

webbrowser.open("https://search.bilibili.com/all?keyword="+address)
```

## 用requests 模块从Web 下载文件

`requests.get()`函数接收一个要下载的Url 地址字符串，返回一个Response 对象，其中包含了Web 服务器对你的请求做出的响应。

```python
>>> import requests
>>> res = requests.get('http://www.gutenberg.org/cache/epub/1112/pg1112.txt')
>>> type(res)
<class 'requests.models.Response'>
>>> res.status_code == requests.codes.ok
True
>>> len(res.text)
179378
>>> requests.codes.ok
200
>>> print(res.text[:300])    
﻿The Project Gutenberg EBook of Romeo and Juliet, by William Shakespeare


*******************************************************************
THIS EBOOK WAS ONE OF PROJECT GUTENBERG'S EARLY FILES PRODUCED AT A
TIME WHEN PROOFING METHODS AND TOOLS WERE NOT WELL DEVELOPED. THERE
IS AN IMPROVED
```

该对象包含若干属性，`status_code`称为状态码，该属性值可以查看这次请求是否成功，`text`属性中则包括了该网页中的文本信息。值得一提的是，访问成功的状态码为200，一般来说，其他均为失败。

我们可以在访问失败时使用`raise_for_status()`方法抛出异常，使程序停止。如果我们只希望抛出异常，不希望程序停止，则可以使用`try`和`except`包裹该语句。

```python
>>> res = requests.get("http://www.donotexists777.com")
>>> res.status_code
404
>>> res.raise_for_status()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "D:\Anaconda3\envs\mlbook\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 404 Client Error: Not Found for url: http://miwifi.com/diagnosis/index.html
```

## 将下载的文件保存至硬盘

```python
>>> import requests
>>> res = requests.get('http://www.gutenberg.org/cache/epub/1112/pg1112.txt')
>>> res.raise_for_status()
>>> playFile = open('RomeoAndJuliet.txt', 'wb')
>>> for chunk in res.iter_content(100000):
...     playFile.write(chunk)
...
100000
79380
>>> playFile.close()
```

使用标准的`open()`和`write()`方法即可保存Web 页面至本地文件中，在这里使用wb 参数视为了以二进制的方式打开文件，并且使我们能够写入该文本中的“Unicode编码”。

为了减少内存的使用量，我们使用`iter_content()`方法分批次将文本写入本地文档。

## 使用BeautifulSoup 模块解析HTML

不使用政策表达式解析Html 格式的原因在于，Html 的格式可以有许多不同的方式，并且仍然被认为是有效的Html ，但尝试用正则表达式来捕捉所有这些可能的变化，将非常繁琐，并且容易出错。BeautifulSoup 模块则不容易导致缺陷。

### 从HTML 创建一个BeautifSoup 对象

bs4.BeautifulSoup() 函数调用时需要一个字符串，其中包含将要解析的HTML。bs4.BeautifulSoup() 函数返回一个BeautifulSoup 对象。

```python
>>> import requests, bs4
>>> res = requests.get('http://www.baidu.com')
>>> res.raise_for_status()
>>> baiduSoup = bs4.BeautifulSoup(res.text)
>>> type(baiduSoup)
<class 'bs4.BeautifulSoup'>
```

### 使用select() 方法寻找元素

 针对要寻找的元素，调用`select()`方法，传入一个字符串作为CSS“选择器”，这样就可以取得Web 页面元素。

```html
<!-- This is the example.html example file. -->

<html><head><title>The Website Title</title></head>
<body>
<p>Download my <strong>Python</strong> book from <a href="http://
inventwithpython.com">my website</a>.</p>
<p class="slogan">Learn Python the easy way!</p>
<p>By <span id="author">Al Sweigart</span></p>
</body></html>
```

来看上面的一个例子，将上面这段代码保存为“example.html”，并保存在当前文件夹下。

```python
>>> from bs4 import BeautifulSoup
>>> exampleFile = open('./example.html')
>>> exampleSoup = BeautifulSoup(exampleFile.read(), features='html.parser')
>>> elems = exampleSoup.select('#author')
>>> type(elems)
<class 'list'>
>>> len(elems)
1
>>> type(elems[0])
<class 'bs4.element.Tag'>
>>> elems[0].getText()
'Al Sweigart'
>>> str(elems[0])
'<span id="author">Al Sweigart</span>'
>>> elems[0].attrs
{'id': 'author'}
```

## 小项目

爬个动漫排行- -

为了某些需要登陆才能查看到的番我们需要先关闭所有浏览器，然后在cmd输入：

```
"C:\Program Files (x86)\Microsoft\Edge Beta\Application\msedge.exe" --remote-debugging-port=8888
```

接下来：

```python
from selenium import webdriver
import time
"""
First, run:
"C:\Program Files (x86)\Microsoft\Edge Beta\Application\msedge.exe" --remote-debugging-port=8888
in the cmd.
"""
options = webdriver.ChromeOptions()
options.debugger_address = "127.0.0.1:" + '8888'
options.binary_location = r"C:\Program Files (x86)\Microsoft\Edge Beta\Application\msedge.exe"
chrome_driver_binary = r"D:\APP\MicrosoftWebDriver.exe"
driver = webdriver.Chrome(chrome_driver_binary, chrome_options=options)
for i in range(1, 100):
    url = "https://bangumi.tv/anime/tag/%E6%90%9E%E7%AC%91?page=" + str(i)
    driver.get(url)
    time.sleep(5)  # Let the user actually see something!
    filepath = str(i) + '.html'
    with open(filepath, 'wb') as f:
        f.write(driver.page_source.encode("utf-8", "ignore"))
        print(filepath + "写入成功！")
        f.close()
```

上面的是用来把网页文件下载到本地的0 0

下面的用来解析的- -

```python
from bs4 import BeautifulSoup
import re

dateRegex = re.compile(r"(\d{4})\D+(\d{1,2})\D+(\d{1,2})")	# 规范化日期
with open('topAnime.txt', 'a', encoding="utf-8") as f:
    for i in range(1, 77):
        filepath = str(i) + '.html'
        soup = BeautifulSoup(open(filepath, encoding="utf-8"), 'html.parser')
        nameList = soup.find_all(name="h3")
        for name in nameList:
            link = "https://bangumi.tv" + name.contents[1]["href"]
            f.writelines('[' + name.contents[1].string.strip('\n') + ']' + '(' + link + ')')
            if len(name) >= 4:
                f.writelines("\t" + name.contents[3].string.strip('\n'))
            else:
                f.writelines("\tNone")
            for sibling in name.next_siblings:
                try:
                    if sibling.attrs["class"] == ['info', 'tip']:
                        # f.writelines("\tinfo: " + sibling.string.strip())
                        date = dateRegex.search(sibling.string.strip())
                        try:
                            f.writelines("\t" + date[1] + '-' + date[2] + '-' + date[3])
                        except TypeError:
                            continue
                    if sibling.attrs["class"] == ['rateInfo']:
                        try:
                            f.writelines("\t" + sibling.contents[3].string.strip('\n'))
                        except IndexError:
                            f.writelines("\t0")
                            continue
                except AttributeError:
                    continue
            f.writelines("\n")
f.close()
# timeList = soup.find_all(attrs={"class": "info tip"})
# for time in timeList:
#     f.writelines(time.string)
```

最终的结果这里就不展示了- -