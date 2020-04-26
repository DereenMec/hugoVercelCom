---
title: "PythonNotes.组织文件"
date: 2018-09-28T12:46:25+08:00
lastmod: 2018-09-28T12:46:25+08:00
draft: false
tags: [Python]
categories: [Python 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

## `shutil` 模块

`shutil` 模块可以让我们很方便的在Python程序中复制、移动、改名和删除文件。

### 复制文件和文件夹

使用`shutil.copy()`来复制文件，该函数含两个参数，均为字符串格式的路径。当第二个参数为文件时，就将原文件复制过来并改名，当第二个参数为文件夹时，就将原文件复制至该文件夹下。

```python
>>> import shutil
>>> shutil.copy('oldFolder/hello.txt', 'newFolder')
'newFolder\\hello.txt'
>>> shutil.copy('oldFolder/hello.txt', 'newFolder/hello_2.txt')
'newFolder/hello_2.txt'
```

值得注意的是，在将文件复制至一个文件夹下时，如果这个文件夹不存在，程序就会误以为你要将原文件命名为第二个参数的名称，导致出现歧义。如下：

```python
>>> shutil.copy('oldFolder/hello.txt', 'newFolder')
'newFolder'
```

使用`shutil.copytree()`来复制文件夹及其包含的文件。用法如下：

```python
>>> shutil.copytree('oldFolder', 'oldFolder_backup')
'oldFolder_backup'
```

### 文件和文件夹的移动与改名

使用`shutil.move()`函数来移动文件或文件夹，用法与上一节的`shutil.copy()`函数大同小异。

```python
>>> shutil.move('oldFolder', 'newFolder')
'newFolder\\oldFolder'
>>> shutil.move('newFolder\\oldFolder\\hello.txt', 'oldFolder_backup\\hello_2.txt')
'oldFolder_backup\\hello_2.txt'
```

### 永久删除文件和文件夹

- 使用`os.unlink(path)`来永久删除一个文件；
- `os.rmdir(path)`来永久删除一个**空文件夹**；
- `shutil.rmtree(path)`来永久删除一个文件夹及其中所有内容。

为了确保不会因为输入有误错误删除重要文件，我们可以先运行下面的程序。

```python
import os
for filename in os.listdir():
    if filename.endswith('.txt'):
        # os.unlink(filename)
        print(filename)
```

再确认无误之后，再删除`print`行，并去除删除文件行代码的注释。

### 使用`send2trash` 模块安全删除

上一节中的三个方法都是永久的删除文件或文件夹，所以在使用起来可能会有风险。使用`send2trash`方法可以将文件或文件夹移入回收站，方便误删后的恢复。在使用`send2trash`方法前，我们先要安装它。

```python
>>> import send2trash
>>> dogList = open('dog.txt', 'w')
>>> dogList.write("I'm a cat.")
10
>>> dogList.close()
>>> send2trash.send2trash('dog.txt')
>>> send2trash.send2trash('oldFolder_backup')
```

## 遍历目录树

处理一个文件夹及其子文件夹下的所有文件可能会是一个复杂的操作，但是python 提供了一个函数`os.walk()`，方便了这个过程。该函数接收一个路径参数，返回该路径下的子文件夹名称及文件名称并层层遍历。

假如在当前文件夹下有一个文件夹cat 其文件目录如下：

![](https://i.loli.net/2019/07/30/5d3fa6df930f256683.png)

我们可以使用下面的代码来输出cat 文件夹下的所有子文件夹及文件名称字符串。

```python
import os

for folderName, subfolders, filenames in os.walk('.\\cat'):
    print('The current folder is ' + folderName)

    for subfolder in subfolders:
        print('SUBFOLDER OF ' + folderName + ': ' + subfolder)
    for filename in filenames:
        print('FILE INSIDE ' + folderName + ': ' + filename)

    print('')
```

输出结果如下：

```
The current folder is .\cat
SUBFOLDER OF .\cat: Libai
SUBFOLDER OF .\cat: Zhongfen

The current folder is .\cat\Libai
FILE INSIDE .\cat\Libai: head.jpg
FILE INSIDE .\cat\Libai: info.txt

The current folder is .\cat\Zhongfen
FILE INSIDE .\cat\Zhongfen: head.jpg
FILE INSIDE .\cat\Zhongfen: info.txt
```

## 使用`zipfile`模块处理压缩文件

我们将上一节中的cat 文件夹压缩为一个zip 压缩文件，在本节使用。

### 读取zip 文件

要读取zip 文件，首先创建一个ZipFile 对象。具体用法如下：

```python
>>> import zipfile
>>> import os
>>> catZip = zipfile.ZipFile('cat.zip')
>>> catZip.namelist()
['cat/', 'cat/Libai/', 'cat/Libai/head.jpg', 'cat/Libai/info.txt', 'cat/Zhongfen/', 'cat/Zhongfen/head.jpg',
'cat/Zhongfen/info.txt']
>>> libaiInfo = catZip.getinfo('cat/Libai/info.txt')
>>> libaiInfo.file_size
0
>>> libaiInfo.compress_size
0
```

### 从zip 文件中解压缩

ZipFile 对象的`extractall()`方法从ZIP 文件中解压缩所有文件和文件夹，放到当前工作目录中。

```python
>>> catZip.extractall()
>>> catZip.close()
```

ZipFile 对象的`extract()`方法从ZIP文件中解压出单个文件。

```python
>>> catZip.extract('cat/Libai/info.txt', 'D:\\')
'D:\\cat\\Libai\\info.txt'
```

其中第二个参数为解压缩后文件的位置，他将在后续被输出出来。

### 创建和添加到ZIP 文件

要创建一个压缩文件，必须以写模式打开ZipFile 对象，即传入“w”作为第二个参数，但这会删除压缩文件中原有的内容，若要向压缩文件中添加新内容，可以以“a”作为第二个参数打开。

```python
>>> newZip = zipfile.ZipFile('new.zip', 'w')
>>> newZip.write('cat/Libai/info.txt', compress_type=zipfile.ZIP_DEFLATED)
>>> newZip.close()
```

## 小项目

假定我们在做一个项目，它的文件保存在 ./cat 文件夹中。我们担心工作会丢失，所以希望为整个文件夹创建一个ZIP 文件，作为“快照”。希望保存不同的版本，希望ZIP 文件的文件名每次创建时都有所变化。例如cat_1.zip、cat_2.zip等。

```python
import zipfile
import os


def backup2zip(folder):
    # Backup the entire contents of "folder" into a ZIP file.
    folder = os.path.abspath(folder)  # make sure folder is absolute

    # Figure out the filename this code should use based on what files already exist.
    number = 1
    while True:
        zipFilename = os.path.basename(folder) + "_" + str(number) + '.zip'
        if not os.path.exists(zipFilename):
            break
        number = number + 1

    # Create the ZIP file.
    print("Create %s..." % (zipFilename))
    backupZip = zipfile.ZipFile(zipFilename, "w")

    # Walk the entire folder tree and compress the files in each folder.
    for foldername, subfolders, filenames in os.walk(folder):
        print("Adding files in %s..." % (foldername))
        # Add the current folder to ZIP file.
        backupZip.write(foldername)
    # Add all the files in this folder to the ZIP file.
    for filename in filenames:
        newBase = os.path.basename(folder) + '_'
        if filename.startswith(newBase) and filename.endswith('.zip'):
            continue
        backupZip.write(os.path.join(foldername, filename))
    backupZip.close()
    print('Done.')


backup2zip('./cat')
```

> [Python学习笔记]系列是我在学习《Python编程快速上手——让繁琐工作自动化(Automate The Boring Stuff With Python)》这本书时的学习笔记。通过自己再手敲一遍概念和代码，方便自己记忆和日后查阅。如果对你有帮助，那就更好了！