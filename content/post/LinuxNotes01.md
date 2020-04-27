---
title: "LinuxNotes.常用命令"
date: 2019-09-01T12:46:25+08:00
lastmod: 2019-09-01T12:46:25+08:00
draft: false
tags: [Linux]
categories: [Linux 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

## 查看某个命令的帮助

man [命令参数] [命令对象]

### man 命令中常用按键以及用途

| 按键      | 用途                               |
| --------- | ---------------------------------- |
| 空格键    | 向下翻一页                         |
| PaGe down | 向下翻一页                         |
| PaGe up   | 向上翻一页                         |
| home      | 直接前往首页                       |
| end       | 直接前往尾页                       |
| /         | 从上至下搜索某个关键词，如“/linux” |
| ?         | 从下至上搜索某个关键词，如“?linux” |
| n         | 定位到下一个搜索到的关键词         |
| N         | 定位到上一个搜索到的关键词         |
| q         | 退出帮助文档                       |

### man 命令帮助信息的结构以及意义

| 结构名称    | 含义                     |
| ----------- | ------------------------ |
| NAME        | 命令的名称               |
| SYNOPSIS    | 参数的大致使用方法       |
| DESCRIPTION | 介绍说明                 |
| EXAMPLES    | 演示（附带简单说明）     |
| OVERVIEW    | 概述                     |
| DEFAULTS    | 默认的功能               |
| OPTIONS     | 具体的可用选项（带介绍） |
| ENVIRONMENT | 环境变量                 |
| FILES       | 用到的文件               |
| SEE ALSO    | 相关的资料               |
| HISTORY     | 维护历史与联系方式       |

## 常用系统工作命令

### echo 在终端输出字符串或变量提取后的值

echo [字符串|$变量]

### date 输出系统的日期或时间

date 命令后跟“+”号开始的参数，即可安装指定格式输出日期或时间

#### date 命令中的参数以及作用

| 参数 | 含义            |
| ---- | --------------- |
| %t   | 跳格[Tab 键]    |
| %H   | 小时（ 00～23） |
| %I   | 小时（ 00～12） |
| %M   | 分钟（ 00～59） |
| %S   | 秒（ 00～59）   |
| %j   | 今年中的第几天  |

用例：

```bash
date "+%Y-%m-%d %H:%M:%S"
2017-08-24 16:29:12
```

更改当前系统时间：

```bash
date -s "20170901 8:30:00"
Fri Sep 1 08:30:00 CST 2017
```

### wget 在终端中下载网络文件

wget [参数] 下载地址

#### wget 命令的参数以及作用

| 参数 | 作用                                 |
| ---- | ------------------------------------ |
| -b   | 后台下载模式                         |
| -p   | 下载到指定目录                       |
| -t   | 最大尝试次数                         |
| -c   | 断点续传                             |
| -p   | 下载页面内所有资源，包括图片、视频等 |
| -r   | 递归下载                             |

### ps 查看系统中的进程状态

ps [参数]

#### ps 命令的参数以及作用

| 参数 | 作用                               |
| ---- | ---------------------------------- |
| -a   | 显示所有进程（包括其他用户的进程） |
| -u   | 用户以及其他详细信息               |
| -x   | 显示没有控制终端的进程             |

### 进程状态缩写含义

- R（运行）： 进程正在运行或在运行队列中等待。
- S（中断）： 进程处于休眠中，当某个条件形成后或者接收到信号时，则脱离该状态。
- D（不可中断）： 进程不响应系统异步信号，即便用 kill 命令也不能将其中断。
- Z（僵死）： 进程已经终止，但进程描述符依然存在, 直到父进程调用 wait4()系统函数后将进程释放。
- T（停止）： 进程收到停止信号后停止运行。

### 命令参数合并注意事项

> 在 Linux 系统中的命令参数有长短格式之分，长格式和长格式之间不能合并，长格式和短格式之间也不能合并，但短格式和短格式之间是可以合并的，合并后仅保留一个-（减号）即可。另外 ps 命令可允许参数不加减号（-），因此可直接写成 ps aux 的样子。

### top 动态地监视进程活动与系统负载等信息

相当于Linux 版的windows 任务管理器

### pidof 查询某个指定服务进程的PID 值

pidof [参数] [服务名称]

```bash
pidof sshd
2144
```

### kill 终止指定PID 的服务进程

kill [参数] [进程PID]

killall 用于终止某个指定名称的服务所对应的全部进程

```bash
pidof httpd
13581 13580 13579 13578 13577 13576
killall httpd
pidof httpd
(nothing)
```

## 系统状态检测命令

### ifconfig 获取网卡配置与网络状态等信息

ifconfig [网络设备] [参数]

```bash
[linuxprobe@linuxprobe ~]$ ifconfig
eno16777728: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        ether 00:0c:29:dc:70:24  txqueuelen 1000  (Ethernet)
        RX packets 13  bytes 1800 (1.7 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 0  (Local Loopback)
        RX packets 2  bytes 140 (140.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2  bytes 140 (140.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

### uname 查看系统内核与系统版本等信息

uname [-a]，这里的-a 其实是all 的意思，即输出所有系统相关信息

### uptime 查看系统负载信息

```bash
[linuxprobe@linuxprobe ~]$ uptime
 17:11:04 up 32 min,  2 users,  load average: 0.05, 0.05, 0.05
```

后面的三个负载值是平均负载值，分别问最近1分钟、5分钟、15分钟内

### free 用于显示当前系统中内存的使用量信息

free [-h] 这里的h 是human 其实就是人性化显示

### who 用于查看当前登入主机的用户终端信息

who [参数]

### last 用于查看所有系统的登录记录

last [参数]

```bash
[linuxprobe@linuxprobe Desktop]$ last
linuxpro pts/0        :0               Thu Mar 26 17:15   still logged in   
linuxpro pts/0        :0               Thu Mar 26 16:39 - 17:12  (00:33)    
linuxpro :0           :0               Thu Mar 26 16:38   still logged in   
(unknown :0           :0               Thu Mar 26 16:38 - 16:38  (00:00)    
reboot   system boot  3.10.0-123.el7.x Thu Mar 26 16:38 - 19:06  (02:28)    
linuxpro pts/1        :0               Thu Mar 26 15:46 - 16:23  (00:36)    
linuxpro pts/0        :0               Thu Mar 26 15:43 - 15:48  (00:05)    
linuxpro pts/0        :0               Thu Mar 26 15:42 - 15:42  (00:00)    
linuxpro :0           :0               Thu Mar 26 15:42 - 16:23  (00:40)    
(unknown :0           :0               Thu Mar 26 15:42 - 15:42  (00:00)    
reboot   system boot  3.10.0-123.el7.x Thu Mar 26 23:42 - 16:23  (-7:-19)   
linuxpro pts/0        :0               Thu Mar 26 15:38 - 15:41  (00:02)    
linuxpro :0           :0               Thu Mar 26 15:38 - 15:41  (00:02)    
(unknown :0           :0               Thu Mar 26 15:38 - 15:38  (00:00)
```

但是，由于这些信息都是以日志文件的形式保存在系统中，因此黑客可以很容易地对内容进行篡改。千万不要单纯以该命令的输出信息而判断系统有无被恶意入侵！

### history 用于显示历史执行过的命令

history [-c] 如果使用-c 参数则会清除所有历史记录

可以通过更改`/etc/profile`文件中的HISTSIZE 变量值来更改最多存放的历史命令数量（默认为1000），还可以使用“!编码数字”的方式来重复执行某一次的命令。历史命令保存在用户主目录中的`.bash_history`文件中。

```bash
[linuxprobe@linuxprobe ~]$ history
    1  cat /etc/redhat-release 
    2  yum repolist all
    3  systemctl status foo
    4  man man
    5  cat /etc/redhat-release
[linuxprobe@linuxprobe ~]$ !6
ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
```

### sosreport 用于收集系统配置及架构信息并输出诊断文档，格式为 sosreport

```bash
[linuxprobe@linuxprobe ~]$ sosreport 

sosreport (version 3.0)

no valid plugins were enabled
```

## 工作目录切换命令

### pwd 显示用户当前所处目录

### cd 切换工作路径

### ls 用于显示目录中的文件信息

ls [选项] [文件]

- -a 查看全部文件（包括隐藏文件）
- -l 查看文件的属性、大小等详细信息
- 如果想要查看目录属性信息，需要额外添加-d 参数

## 文本文件编辑命令

### cat 用于查看纯文本文件（内容较少）

cat [选项] [文件]

-n 可以显示行号

### more 用于查看纯文本文件（内容较多）

more [选项] [文件]

### head 用于查看纯文本文件的前N行

head [选项] [文件]

```bash
head -n 20 /etc/profile
```

### tail 用于查看纯文本文件的后N行

tail [选项] [文件]

```bash
tail -n 20
```

tail 命令最强悍的功能是可以持续刷新一个文件的内容，当想要实时 查看最新日志文件时，这特别有用，此时的命令格式为“tail -f 文件名”

### tr 用于替换文本文件中的字符

tr [原始字符] [目标字符]

```bash
[linuxprobe@linuxprobe Desktop]$ cat test.md
ABCDEFG
abcdefg
[linuxprobe@linuxprobe Desktop]$ cat test.md | tr [a-z] [A-Z]
ABCDEFG
ABCDEFG
[linuxprobe@linuxprobe Desktop]$ cat test.md
ABCDEFG
abcdefg
```

### wc 用于统计指定文本的行数 字数 字节数

wc [参数] [文本]

#### wc 的参数以及作用

| 参数 | 作用         |
| ---- | ------------ |
| -l   | 只显示行数   |
| -w   | 只显示单词数 |
| -c   | 只显示字节数 |

### stat 用于查看文件的具体存储信息和时间等信息

stat [文件名]

### cut 用于按"列"提取文本字符

cut [参数] [文本]

```bash
## 按行提取
[linuxprobe@linuxprobe Desktop]$ head -n 2 /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
## 按列提取以冒号为分隔符的第一列内容
[linuxprobe@linuxprobe Desktop]$ cut -d: -f1 /etc/passwd
root
bin
daemon
adm
lp
sync
shutdown
halt
...
linuxprobe
```

### diff 用于比较多个文本文件的差异

diff [参数] [文件]

- —brief 简单比较两个文件是否相同
- -c 打印两个文件具体不同

```bash
[linuxprobe@linuxprobe Desktop]$ cat diff_A.txt 
Hello, how are you?
[linuxprobe@linuxprobe Desktop]$ cat diff_B.txt 
Hello, how you doing?
[linuxprobe@linuxprobe Desktop]$ diff --brief diff_A.txt diff_B.txt 
Files diff_A.txt and diff_B.txt differ
[linuxprobe@linuxprobe Desktop]$ diff -c diff_A.txt diff_B.txt 
*** diff_A.txt	2020-03-26 19:39:48.537585147 +0800
--- diff_B.txt	2020-03-26 19:40:11.476259281 +0800
***************
*** 1 ****
! Hello, how are you?
--- 1 ----
! Hello, how you doing?
```

## 文件目录管理命令

### touch 创建空白文件或设置文件的时间

touch [选项] [文件]

#### touch 命令的参数及其作用

| 参数 | 作用                       |
| ---- | -------------------------- |
| -a   | 仅修改“读取时间”（ atime） |
| -m   | 仅修改“修改时间”（ mtime） |
| -d   | 同时修改 atime 与 mtime    |

```bash
[linuxprobe@linuxprobe Desktop]$ ls -l test.md
-rw-rw-r--. 1 linuxprobe linuxprobe 16 Mar 26 19:27 test.md
[linuxprobe@linuxprobe Desktop]$ echo "Visit the LinuxProbe.com to learn linux skills" >> test.md
[linuxprobe@linuxprobe Desktop]$ ls -l test.md
-rw-rw-r--. 1 linuxprobe linuxprobe 63 Mar 26 20:02 test.md
[linuxprobe@linuxprobe Desktop]$ touch -d "2020-03-26 19:27" test.md
[linuxprobe@linuxprobe Desktop]$ ls -l test.md
-rw-rw-r--. 1 linuxprobe linuxprobe 63 Mar 26 19:27 test.md
```

### mkdir 用于创建空白的目录

mkdir [选项] [目录]

mkdir -p 可以来递归创建出具有嵌套叠层关系的文件目录

```bash
[linuxprobe@linuxprobe Desktop]$ mkdir -p 1/2/3
```

### cp 用于复制文件或目录

cp [选项] 源文件 目标文件

#### cp 命令的参数及其作用

| 参数 | 作用                                         |
| ---- | -------------------------------------------- |
| -p   | 保留原始文件的属性                           |
| -d   | 若对象为“链接文件”，则保留该“链接文件”的属性 |
| -r   | 递归持续复制（用于目录）                     |
| -i   | 若目标文件存在则询问是否覆盖                 |
| -a   | 相当于-pdr（ p、 d、 r 为上述参数）          |

### mv 剪切文件或重命名文件

用法参考上个

### rm 用于删除文件或目录

rm [选项] 文件

在 Linux 系统中删除文件时，系统会默认向您询问是否要执行删除操作，如果不想总是看到这种反复的确认信息， 可在 rm 命令后跟上-f 参数来强制删除。 另外， 想要删除一个目录，需要在 rm 命令后面一个-r 参数才可以， 否则删除不掉。

### dd 用于按照指定大小和个数的数据块来复制文件或转换文件

dd [参数]

#### dd 命令的参数及其作用

| 参数  | 作用                 |
| ----- | -------------------- |
| if    | 输入的文件名称       |
| of    | 输出的文件名称       |
| bs    | 设置每个“块”的大小   |
| count | 设置要复制“块”的个数 |

![e4c0ea51-df44-42f7-a565-cf49fbbc2014.png](https://storage.live.com/items/55AB66419C029DA!4099?authkey=AFBtWHxjrNHYZLw)

### file 用于查看文件的类型

file 文件名

## 打包与压缩

### tar 用于对文件进行打包或解压

tar [选项] [文件]

#### tar 命令的参数及其作用

| 参数 | 作用                   |
| ---- | ---------------------- |
| -c   | 创建压缩文件           |
| -x   | 解开压缩文件           |
| -t   | 查看压缩包内有哪些文件 |
| -z   | 用 Gzip 压缩或解压     |
| -j   | 用 bzip2 压缩或解压    |
| -v   | 显示压缩或解压的过程   |
| -f   | 目标文件名             |
| -P   | 保留原始的权限与属性   |
| -p   | 使用绝对路径来压缩     |
| -C   | 指定解压到的目录       |

### grep 用于在文本中执行关键词搜索

grep [选项] [文件]

| 参数 | 作用                                               |
| ---- | -------------------------------------------------- |
| -b   | 将可执行文件（ binary）当作文本文件（ text）来搜索 |
| -c   | 仅显示找到的行数                                   |
| -i   | 忽略大小写                                         |
| -n   | 显示行号                                           |
| -v   | 反向选择—仅列出没有“关键词”的行                    |

```bash
## 找出/etc/passwd 中没有"nologin" 的行并显示行号
[linuxprobe@linuxprobe ~]$ grep -ivn nologin /etc/passwd
1:root:x:0:0:root:/root:/bin/bash
6:sync:x:5:0:sync:/sbin:/bin/sync
7:shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
8:halt:x:7:0:halt:/sbin:/sbin/halt
38:linuxprobe:x:1000:1000:linuxprobe:/home/linuxprobe:/bin/bash
```

### find 用于按照指定条件来查找文件

find [查找路径] 寻找条件 操作

| 参数               | 作用                                                         |
| ------------------ | ------------------------------------------------------------ |
| -name              | 匹配名称                                                     |
| -perm              | 匹配权限（ mode 为完全匹配， -mode 为包含即可）              |
| -user              | 匹配所有者                                                   |
| -group             | 匹配所有组                                                   |
| -mtime -n +n       | 匹配修改内容的时间（ -n 指 n 天以内， +n 指 n 天以前）       |
| -atime -n +n       | 匹配访问文件的时间（ -n 指 n 天以内， +n 指 n 天以前）       |
| -ctime -n +n       | 匹配修改文件权限的时间（ -n 指 n 天以内， +n 指 n 天以前）   |
| -nouser            | 匹配无所有者的文件                                           |
| -nogroup           | 匹配无所有组的文件                                           |
| -newer f1 !f2      | 匹配比文件 f1 新但比 f2 旧的文件                             |
| --type b/d/c/p/l/f | 匹配文件类型（后面的字幕参数依次表示块设备、目录、字符设备、管道、链接文件、文本文件） |
| -size              | 匹配文件的大小（ +50KB 为查找超过 50KB 的文件，而-50KB 为查找小于50KB 的文件） |
| -prune             | 忽略某个目录                                                 |
| -exec …… {}\;      | 后面可跟用于进一步处理搜索结果的命令（下文会有演示）         |

下面的命令在整个文件系统中找出所有归属于linuxprobe 用户的文件并复制到/root/findresults 目录

```bash
find / -user linuxprobe -exec cp -a {} /root/findresults/ \\;
```