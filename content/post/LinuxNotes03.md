---
title: "LinuxNotes.管道符、重定向与环境变量"
date: 2019-09-06T12:46:25+08:00
lastmod: 2019-09-06T12:46:25+08:00
draft: false
tags: [Linux]
categories: [Linux 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

## 输入输出重定向

简而言之，输入重定向是指把文件导入到命令中，而输出重定向则是指把原本要输出到屏幕的数据信息写入到指定文件中。在日常的学习和工作中，相较于输入重定向，我们使用输出重定向的频率更高，所以又将输出重定向分为了标准输出重定向和错误输出重定向两种不同的技术，以及清空写入与追加写入两种模式。

- 标准输入重定向（STDIN，文件描述符为0）：默认从键盘输入，也可从其他文件或命令中输入
- 标准输出重定向（STDOUT，文件描述符为1）：默认输出到屏幕
- 错误输出重定向（STDERR，文件描述符为2）：默认输出到屏幕

### 输入重定向中用到的符号及其作用

| 运算符                 | 作用                                            |
| ---------------------- | ----------------------------------------------- |
| 命令 < 文件            | 将文件作为命令的标准输入                        |
| 命令 << 分界符         | 从标准输入中读入，直到遇见分界符才停止          |
| 命令 < 文件 1 > 文件 2 | 将文件 1 作为命令的标准输入并将标准输出到文件 2 |

### 输出重定向中用到的符号及其作用

| 运算符                              | 作用                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| 命令 > 文件                         | 将标准输出重定向到一个文件中（清空原有文件的数据）           |
| 命令 2> 文件                        | 将错误输出重定向到一个文件中（清空原有文件的数据）           |
| 命令 >> 文件                        | 将标准输出重定向到一个文件中（追加到原有内容的后面）         |
| 命令 2>> 文件                       | 将错误输出重定向到一个文件中（追加到原有内容的后面）         |
| 命令 >> 文件 2>&1 或 命令  &>> 文件 | 将标准输出与错误输出共同写入到文件中（追加到原有内容的后面） |

💡对于重定向中的标准输出模式，可以省略文件描述符1不写，而错误输出模式的文件描述符2必须要写。

例子：通过标准输出重定向将`man bash`命令原本要输出到屏幕的信息写入到文件`bashHelp.txt`中。

```bash
[linuxprobe@linuxprobe Desktop]$ man bash > bashHelp.txt
[linuxprobe@linuxprobe Desktop]$ cat bashHelp.txt 
BASH(1)                     General Commands Manual                    BASH(1)

NAME
       bash - GNU Bourne-Again SHell

SYNOPSIS
       bash [options] [file]

COPYRIGHT
       Bash is Copyright (C) 1989-2011 by the Free Software Foundation, Inc.
......省略......
```

下面这个例子说明标准输出和错误输出如何使用

```bash
[linuxprobe@linuxprobe Desktop]$ ls -l bashHelp.txt 2> bashHelp.txt 
-rw-rw-r--. 1 linuxprobe linuxprobe 0 Mar 27 12:48 bashHelp.txt
[linuxprobe@linuxprobe Desktop]$ cat bashHelp.txt 
[linuxprobe@linuxprobe Desktop]$ ls -l bashHelp.txt &> bashHelp.txt 
[linuxprobe@linuxprobe Desktop]$ cat bashHelp.txt 
-rw-rw-r--. 1 linuxprobe linuxprobe 0 Mar 27 12:48 bashHelp.txt
```

**输入重定向使用:**

```bash
## 统计bashHelp.txt 中的行数
[linuxprobe@linuxprobe Desktop]$ wc -l < bashHelp.txt 
1
```

## 管道命令符

执行格式为"命令A|命令B"。命令符的作用也可以用一句话来概括“把前一个命令原本要输出到屏幕的数据当作是后一个命令的标准输入”。

```bash
## 上一个例子的另一种方法
[linuxprobe@linuxprobe Desktop]$ cat bashHelp.txt | wc -l
1
```

通过匹配关键词`/sbin/nologin`找出所有被限制登录系统的用户。然后统计数目。[grep](https://www.notion.so/dereen/Linux-76b8e07b6813442d89135094ce800c57#150f314efb9b4bc0bee23db3b5658004)操作点这里。

```bash
[linuxprobe@linuxprobe Desktop]$ grep "/sbin/nologin" /etc/passwd | wc -l
33
```

**在修改用户密码时，通常都需要输入两次密码进行确认，通过在编写脚本时把passwd 命令和—stdin 参数相结合，就可以用一条命令来完成密码重置操作：**

```bash
[root@linuxprobe ~]## echo "linuxprobe" | passwd --stdin root
Changing password for user root.
passwd: all authentication tokens updated successfully.
```

## 命令行的通配符

- - 匹配零个或多个字符
- ? 匹配单个字符
- [0-9] 匹配0~9之间任一字符

## 常用的转义字符

- 反斜杠（\）：使反斜杠后面的一个变量变为单纯的字符串
- 单引号（''）：专一其中所有的变量为单纯的字符串
- 双引号（""）：包留其中的变量属性，不进行转义处理
- 反引号（``）：把其中的命令执行后返回结果

```bash
[root@linuxprobe ~]## PRICE=5
[root@linuxprobe ~]## echo $PRICE
5
[root@linuxprobe ~]## echo "Price is $PRICE"
Price is 5
[root@linuxprobe ~]## echo "Price is $$PRICE"
## 这里的输出是因为$$显示当前进程的ID号码
Price is 8240PRICE
[root@linuxprobe ~]## echo "Price is \\$$PRICE"
Price is $5
[root@linuxprobe ~]## echo `uname -a`
Linux linuxprobe.com 3.10.0-123.el7.x86_64 #1 SMP Mon May 5 11:16:57 EDT 2014 x86_64 x86_64 x86_64 GNU/Linux
```

## 重要的环境变量

变量是计算机系统用于保存可变值的数据类型。在 Linux 系统中，变量名称一般都是大写的，这是一种约定俗成的规范。我们可以直接通过变量名称来提取到对应的变量值。 Linux 系统中的环境变量是用来定义系统运行环境的一些参数，比如每个用户不同的家目录、邮件存放位置等。

Linux 执行命令经过以下4个步骤：

1. 判断用户是否以绝对路径或相对路径的方式输入命令（如/bin/ls），如果是的话，直接执行；
2. Linux 系统检查用户输入的命令是否为“别名命令”，即使用alias 命令创建的形如“alias 别名=命令”的命令。如果要取消一个别名命令，则“unalias 别名”。rm 其实就是一个别名命令，Linux 为了防止用户误删除文件，将rm -i命令重命名为rm。
3. Bash 解释器判断用户输入的是内部命令还是外部命令。内部命令是解释器内部的指令，会被直接执行；而用户绝大部分时间输入的是外部命令，这些命令进入下一步继续处理。我们可以使用“type 命令名称”来判断用户输入的命令是内部命令还是外部命令。
4. 系统在多个路径中查找用户输入的命令文件，而定义这些路径的变量叫作PATH，作用是告诉Bash 解释器待执行的命令可能存放的位置，然后Bash 解释器就会去这些位置中逐个查找。PATH 是由多个路径值组成的变量，每个路径值之间用冒号间隔，对这些路径的增加和删除操作将影响到 Bash 解释器对Linux 命令的查找。

![1e54b4a4-62ec-4edb-9a1b-cc1005efde18.png](https://storage.live.com/items/55AB66419C029DA!4107?authkey=AFBtWHxjrNHYZLw)

### Linux 系统中最重要的 10 个环境变量

| 变量名称     | 含义                             |      |
| ------------ | -------------------------------- | ---- |
| HOME         | 用户的主目录（即家目录）         |      |
| SHELL        | 用户在使用的 Shell 解释器名称    |      |
| HISTSIZE     | 输出的历史命令记录条数           |      |
| HISTFILESIZE | 保存的历史命令记录条数           |      |
| MAIL         | 邮件保存路径                     |      |
| LANG         | 系统语言、语系名称               |      |
| RANDOM       | 生成一个随机数字                 |      |
| PS1          | Bash 解释器的提示符              |      |
| PATH         | 定义解释器搜索用户执行命令的路径 |      |
| EDITOR       | 用户默认的文本编辑器             |      |

上面的变量中，相同的变量会因为用户身份的不同而具有不同的值。

**其实变量是由固定的变量名与用户或系统设置的变量值两部分组成的，我们完全可以自行创建变量，来满足工作需求。**

```bash
[linuxprobe@linuxprobe ~]$ mkdir /home/linuxprobe/testdir
[linuxprobe@linuxprobe ~]$ WORKDIR=/home/linuxprobe/testdir
[linuxprobe@linuxprobe ~]$ cd $WORKDIR
[linuxprobe@linuxprobe testdir]$ pwd
/home/linuxprobe/testdir
```

但是这样的变量不具有全局性，作用范围也有限，默认情况下不能被其他用户使用。如果工作需要，可以使用export 命令将其提升为全局变量。

```bash
[linuxprobe@linuxprobe ~]$ export WORKDIR
```